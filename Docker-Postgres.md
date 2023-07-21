# Postgres su Docker
I test su postgres sono terminati.
Il database su container funziona, si riesce ad aggiornare con il passaggio dei dati e si possono importare tranquillamente i db esistenti.

Creare un POD per Postgress è praticamente simile a crearlo per mysql.
```sh
podman create --name postgres1 -e POSTGRES_PASSWORD=B0C4MGliy3 -e POSTGRES_INITDB_ARGS=--auth-host=md5 -v pg1:/var/lib/postgresql/data -p 0.0.0.0:5432:5432 postgres:15.3
```
Quello che cambia nel nostro caso è l'immagine da cui prendere i dati per postgres poichè dobbiamo utilizzare PG 15.3 alpine.
Sul sito di Docker ho trovato due differenti immagini relative oltre a quella di default
- postgres:15.3
- postgres:15.3-alpine3.18
- postgres:15.3-alpine-rfhardened

Importante da ricordarsi che in caso di mount di un volume nominale, dobbiamo indicare la cartella `data` altrimenti non si riuscirà a montare correttamente il POD. La porta è la **5432** ed è obbligatorio inserire la password per l'accesso.

Provando ad eseguire direttamente il `run` con l'immagine ufficiale, sia che si provi a scaricarlo dai repository di docker, sia da quelli di oralce, ottengo un errore.
Facendo però prima la Pull della stessa immagine, si risce ad averla sul sistema quindi invece di dicharare il nome del programma e farlo scaricare prima di partire, gli indichiamo l'imageID cosi esegue il container partendo da quella immagine.

## Eseguiamo il container e facciamo il primo accesso
Avviare il container è basilare, basta infatti usare il comando start. Accedere a postgres possiamo farlo tramite il client `psql` del'host (se manca andrà installato) o tramite il client `psql` del container. Nel secondo caso il comando è piu articolato.
```sh
podman start postgres ##avviamo il container

psql -U postgres -h 0.0.0.0 -p 10003 ##accediamo tramite il client dell'host o remoto (dobbiamo conoscere la password)

podman exec -it postgres psql -U postgres ##accediamo tramite il client del container (non richiede password)
```

## Cambio di Encryption
*Questa parte è gia stata integrata nel codice di creazione del container*
Se accediamo da un server CentOS7 ad una macchina recente OL8 oppure OL9 dobbiamo variare la modalità di encryption utilizzata.
Il file da modificare per l'encryption è `postgresql.conf` e lo copiamo in locale, per modificarlo e ricopiaro nel container.
```sh
/var/lib/postgresql/data/postgresql.conf
```
Non capisco il motivo ma seguendo questa strada risulta che la cartella data è piena e non riesce a far partire il sistema.

#### La variabile di ambiente
Se invece quando creo il container lo creo con la varibile di ambiente indicata dalla guida, il metodo di criptaggio risulta corretto. Ogni variabile di ambiente deve essere preceduta dalla sua opzione
```sh
podman create ... -e POSTGRES_INITDB_ARGS=--auth-host=md5 ...
```


## Upgrade del database
Cercando una soluzione per quel che riguarda gli upgrade senza dover fare un dump ed un import, ho trovato un'immagine che contiene uno script per effettuare il passaggio dei dati. --Non riesco a farla funzionare tra major version--


### Da Postgres 9.2 a Postgres 14
Ho scaricato il db di dchotel da Postgres 9.2.24 dalla macchina SAAS 192.168.100.132 e dalla macchina Webcheckin 192.168.250.10.

##### Postgres 13.11
Ho creato un POD con postgres 13 ed ho caricato li database.
```sh
podman create --name postgress13.11 -e POSTGRES_PASSWORD=B0C4MGliy3 -e POSTGRES_INITDB_ARGS=--auth-host=md5 -v pg_dchotel:/var/lib/postgresql/data -p 0.0.0.0:10102:5432 postgres:13.11
```
Funziona, leggo, scrivo, modifico.

##### Postgres 14
L'aggiornamento alla nuova versione non è possibile se non spostiamo la location del volume montato. Quindi la soluzione unica per poterlo risolvere è quella di fare il dump del database, stoppare la vecchia versione, avviare quella nuova che deve essere stata creata con gli stessi parametri ma volume differente
```sh
podman create --name postgress14 -e POSTGRES_PASSWORD=B0C4MGliy3 -e POSTGRES_INITDB_ARGS=--auth-host=md5 -v pg_dchotel14:/var/lib/postgresql/data -p 0.0.0.0:10102:5432 postgres:14
```
In questo caso il db viene reimportato correttamente, leggo, scrivo e modifico.

#### Il passaggio completo con Dump All
Posso eseguire il dump del database o di solo un db. Per farlo di tutti i db uso
```sh
pg_dumpall -U postgress -p 10102 -h 0.0.0.0 > postgresAll.db
```



### Dalla 12 alla 15 con il tool di Tianon
Ho scaricato il db di dchotel da Postgres 9.2.24 dalla macchina SAAS 192.168.100.132 e dalla macchina Webcheckin 192.168.250.10.
https://hub.docker.com/r/tianon/postgres-upgrade
L'ho caricato su POD *postgress12* con una configurazione simile a quella proposta da Tianon.

Secondo la logica proposta possiamo creare i POD in due modi differenti, ci stiamo riferendo alla path di mount dei volumi data.
Possiamo differenziare le versioni in due modi:

##### Monto in locale all'interno di subdirectory
Monto in locale in una subdir le differenti versioni creando prima del comando le cartelle necessarie ed indicando il percorso assoluto. In questo caso è necessario indicare la versione nella path del container
```sh
mkdir -p /mnt/postgres/12
mkdir -p /mnt/postgres/13

podman create --name postgress12 -e POSTGRES_PASSWORD=postgres -e POSTGRES_INITDB_ARGS=--auth-host=md5 -v /mnt/postgres/12:/var/lib/postgresql/12/data -p 0.0.0.0:10102:5432 postgres:12
podman create ... -v /mnt/postgres/13:/var/lib/postgresql/13/data ...
```

Il codice della transizione
```sh
podman run --rm -v /mnt/postgres:/var/lib/postgresql/data tianon/postgres-upgrade:12-to-13 --link
```

##### Monto in locale su diverse directory
Nella seconda versione invece monto i volumi in differenti cartelle con differenti nomi. In questo caso è necessario indicare la versione nella path del container
```sh
podman create --name postgress12 -e POSTGRES_PASSWORD=postgres -e POSTGRES_INITDB_ARGS=--auth-host=md5 -v postgres-12.0:/var/lib/postgresql/12/data -p 0.0.0.0:10102:5432 postgres:12
podman create ... -v postgres-13.0:/var/lib/postgresql/13/data
```
Il codice per la transizione è
```sh
podman run --rm -v postgres-12.0:/var/lib/postgresql/12.0/data 	-v postgres-13.0:/var/lib/postgresql/13.0/data tianon/postgres-upgrade:12-to-13
```

#### Creo la transizione di versione
La migrazione dei dati si puo applicare con un container che esegue il passaggio quindi viene rimosso. Infatti con l'opzione *--rm* indichiamo la rimozione automatica del container una volta che ha terminato la sua funzione 
**In tutti i casi i comandi sono da lanciare con i container fermi e con lo status delle nuove versioni in created** quindi senza averli mai eseguiti.

Lanciando il programma viene evidenziato un warning

	initdb: warning: enabling "trust" authentication for local connections
	You can change this by editing pg_hba.conf or using the option -A, or
	--auth-local and --auth-host, the next time you run initdb.

Viene chiesto di lanciare il server (presumo dall'interno del container)

	pg_ctl -D /var/lib/postgresql/13/data -l logfile start

Ed inoltre viene evidenziato questo errore

	could not open version file "/var/lib/postgresql/12/data/PG_VERSION": No such file or directory
	Failure, exiting

Lo stato del 13 è sempre a *created*. La cartella del mount è vuota, provo ad eseguire il pontainer. Il db resta vuoto segno che non ha terminato la trasizione e non ha copiato nulla.

Presumo, dovo aver riletto per l'ennesima volta la documentazione che il problema sia legato a `pg_upgrade` che viene usato come comando e che non permette la transizione dei db tra major version.





