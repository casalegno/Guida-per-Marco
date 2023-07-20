# Docker con MySql
In questo articolo vediamo tutte le prove che ho fatto con Docker/Podman e con la connessione del PMS a MySql.

Possiamo ritenere completato il test di mysql con Docker.
**Vedi Conclusione!**

<!-- TOC -->

- [Mysql](#mysql)
    - [MySql 8.0.32](#mysql-8032)
    - [Mysql 8.0.28 DEPRECATO](#mysql-8028-deprecato)
    - [I dati dei DB](#i-dati-dei-db)
    - [Salviamo i dati da un'altra parte](#salviamo-i-dati-da-unaltra-parte)
    - [Configuriamo mysql per il primo avvio](#configuriamo-mysql-per-il-primo-avvio)
        - [Lower case table - initialize DEPRECATA](#lower-case-table---initialize-deprecata)
    - [Avviamo il container](#avviamo-il-container)
        - [Stoppiamo mysql nel container](#stoppiamo-mysql-nel-container)
- [Test di funzionamento su un database esistente DcHotel](#test-di-funzionamento-su-un-database-esistente-dchotel)
    - [Conclusione](#conclusione)

<!-- /TOC -->

## Mysql
Iniziamo con il dire che collegare il PMS a Mysql con un container è possibile e i database importati si leggono.
I passaggi seguiti sono in linea di massima quelli per installare mysql su una VM, con alcune precauzioni dovute all'uso dei contenitori.

### MySql 8.0.32
Dopo gli ultimi aggiornamenti siamo riusciti ad eseguire Mysql con la versione 8.0.32.
Quindi rifacciamo i test con questa versione.
```sh
podman create --name mysql1 -e MYSQL_ROOT_PASSWORD=Sysd@t21! -v mysql1_data:/var/lib/mysql -p 0.0.0.0:10001:3306 mysql:8.0.32 mysqld
```
È importante notare che per far funzionare correttamente questa versione con tutte le specifiche richieste da Geniu7 dobbiamo utilizzare i file `my.cnf` e `mysql-server.cnf` creati da Angelo, nelle rispettive cartelle

    /etc/my.cnf
    /etc/my.cnf.d/mysql-server.cnf

Una copia dei file è sulla sua home.
Una copia dei file è sulla 192.168.100.136



### Mysql 8.0.28 DEPRECATO
I test sono stati esugiti sulla versione seleziona di MySql  quindi installiamo mysql 8.0.28
```sh
docker pull mysql:8.0.28
```
Una volta scaricata l'immagine (sarebbe il caso di crearne una gia pronta all'uso)
prima di avviare il container con all'interno il docker dobbiamo creare il contenitore cosi da poterlo personalizzare.

In fase di creazione dobbiamo utilizzare gli stessi parametri usati nei test di avvio, cambia solo il comando
```sh
## comando di avvio/creazione automatica del container NON USARE
podman run --name mysql2 -e MYSQL_ROOT_PASSWORD=SYSDAT -v mysql2_data:/var/lib/mysql -p 127.0.0.1:1002:3306 -d mysql:8.0.28

### comando di creazione del container SENZA avvio
podman create --name mysql1 -e MYSQL_ROOT_PASSWORD=SYSDAT -v mysql1_data:/var/lib/mysql -p 0.0.0.0:10001:3306 mysql:8.0.28
```
Quindi avendo creato il container questo ha generato in una cartella presente nella path dell'utente il volume di riferimento. 

### I dati dei DB
Secondo l'esempio precedente la cartella che viene creata è *mysql1_data*
Su macchine OL8, ma principalmene penso usando **podman** i dati vengono salvati nella cartella 
```sh
$HOME/local/share/containers/storage/volumes/<id_container>/_data
## troveremo quindi
$HOME/.local/share/containers/storage/volumes/mysql1_data/_data
```
quello che a noi interessa ora è riuscire salvare i dati in una cartella in un'altra posizione
Ricordiamoci che due o piu *POD* (il nome con cui vengono chiamati i container creati da Podman) Non possono essere attivi contemporaneamente puntando agli stessi dati perchè il primo che viene avviato blocca l'accesso al secondo.

### Salviamo i dati da un'altra parte
Poichè **Podman** crea i file in una directory specifica dell'utente, possiamo spostarne la locazione una volta avviato il container. Una volta che ha creato la cartella dati, manualmente la vado a spostare ed al suo posto creo un link simbolico cosi da poter avere i dati salvati dove desidero.
Lo stesso discorso funziona se preparo prima la location dove mettere i dati: prima creo il link simbolico che vado poi a dichiarare con l'opzione `-v` durante la creazione del container.
Resta il fatto che quando creo la cartella o la sposto successivamente il container deve essere fermo.
```sh
cd ./local/share/containers/storage/volume/
cp <cartellamysql> /mysql_data/
rm -rf <cartellamysql>
ln -s /mysql_data/<cartellamysql>/ ./<cartellamysql>
```
al termine del passagio dei dati posso rifarpartire il container.

### Configuriamo mysql per il primo avvio
Per avviare mysql in realtà basta avviare il container e il demone andrà a popolare il volume con i dati di default.
Per poterlo utilizzare con una certa compatibilità doppiamo impostare un valore nel config.

Come primo passaggio [copiamo in locale il file di configurazione](./Docker.md#importexport-del-file) di mysql. Il file *my.cnf* lo dobbiamo utilizzare per alcune verisioni, mentre per altre dobbiamo usare il file *docker.cnf*.
La path completa è
```sh
mysql 8.0.28
/etc/mysql/conf.d/docker.cnf

mysql 8.0.32
/etc/my.cnf
```
Con Vi inseriamo
```sh
[mysqld]
...
lower_case_table_names=1
```
salviamo e reimportiamo nella stessa posizione il file.
Se abbiamo gia dei file all'interno del database non riusciremo ad aggiornare questa variabile anche se viene suggerito questo passaggio

#### Lower case table - initialize DEPRECATA
Un problema id accesso dei dati con Genius è probabilmente la variabile lower_case_table_names=1;
Reinizializziamo mysqld [una volta stoppato](#stoppiamo-mysql-nel-container) e dopo [aver copiato al suo interno](#importexport-del-file) il file di configurazione
```sh
sudo mysqld --defaults-file=/etc/mysql/my.cnf --initialize --lower_case_table_names=1 --user=mysql --console
```
per poter fare l'inizializzazione dobbiamo avere la cartella dati vuota. Se la

### Avviamo il container 
Fatta la modifica al file di configurazione, possiamo avviare il container che andrà a popolare i dati
```sh
podman start mysql1
```
Ora dovremo semplicemente continuare a configurare mysql come lo si faceva sulle macchine virtuali [seguendo la guida di Oralce9](./Mysql%20su%20Oracle9.md)

#### Stoppiamo mysql nel container
Per fermare il processo di mysql, possiamo usare il comando
```sh
docker exec CONTAINERNAME /usr/sbin/mysqld stop
docker exec CONTAINERNAME /usr/sbin/mysqld start
```
oppure se siamo loggati nel container, direttamente
```sh
/usr/sbin/mysqld stop
```

## Test di funzionamento su un database esistente DcHotel
Ho configurato la macchina sulla Vm 192.168.235.116 - Vm con CentOs7.
Ho aperto in contemporanea il file di log dedicato ad Easydoc che si trova al percorso `C:\Genius7\Easydoc\LogDOC.txt`.
Lo mantengo controllato tramite tramite il programma LiveLogViewer.

Genius ad ogni operazione restituisce un errore dovuto ad una query di selezione su indici.

    19/07/2023 11:17:53 Lanciata query select indici.*,tipodocumento,Icona,IDRAGGRUP,Descrizione,Mostracolonne from indici join tipidoc on TIPODOC=IDTIPO join causali on CAUSALE=KEYCAUSALI join raggrup on RAGGRUPPAMENTO=IDRAGGRUP  Where 1=1  And  Datadocumento ='2023-07-19' And ( Causale = 000000000000000004

Insieme a Dario abbiamo configurato ThinClient per la connessione ad un ambiente chiamato GranMilan con il database importato di DcHotel.
Nella data odierna, configurata dal pannello INST0228 alla voce *Data di lavoro* e *data ultima schiusura* abbiamo eseguito la generazione di una stampa generica utilizzando il pannello STPR2. 
Quindi abbimo eseguito il pannello EDNAV ed al suo interno abbiamo effettuato una ricerca con le impostazioni come da immagine seguente.

Se non impostiamo nessuna data o nessun valore all'interno dei campi nel pannello EDNAV otteniamo un errore di corrispondenza di MySql.

    Unable to convert MySQL date/time value to System.DateTime

Se invece inseriamo almeno un parametro, che possa essere una data o un oggetto riusciamo ad ottenere un risultato.
Confrontandomi con Angelo il problema è legato ad una variabile inserita da lui nella configurazione del db Mysql.
Il problema viene analizzato in questo [post su Stack Overflow](https://stackoverflow.com/questions/2934844/unable-to-convert-mysql-date-time-value-to-system-datetime)

### Conclusione
Anche inserendo questo parametro tramite il `mysql-server.conf` file, il problema persiste. Angelo ritiene si tratti di qualche configurazione che non viene pienamente letta dal gestionale, ma sulla quale si puo soprassedere.
