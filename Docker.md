# Docker

<!-- TOC -->

- [Installiamo](#installiamo)
    - [Con o senza: cgroups V2 vs tmpfs](#con-o-senza-cgroups-v2-vs-tmpfs)
        - [L'errore](#lerrore)
        - [e la soluzione](#e-la-soluzione)
- [Controllare i container](#controllare-i-container)
    - [Avviare un container](#avviare-un-container)
    - [Stoppare un container](#stoppare-un-container)
    - [Cancelliamo un POD](#cancelliamo-un-pod)
    - [Controlliamo il log per vedere gli errori](#controlliamo-il-log-per-vedere-gli-errori)
    - [Aprima una porta dal container all'host](#aprima-una-porta-dal-container-allhost)
    - [Creiamo un commit](#creiamo-un-commit)
- [Modifichiamo un file su un container.](#modifichiamo-un-file-su-un-container)
    - [Accediamo alla bash come root](#accediamo-alla-bash-come-root)
    - [Import/export del file](#importexport-del-file)
- [Configurazione su OL9](#configurazione-su-ol9)
    - [Avvio automatico di podman come servizio.](#avvio-automatico-di-podman-come-servizio)
    - [I pod si riavviano?](#i-pod-si-riavviano)
    - [Creo i file service](#creo-i-file-service)
    - [Abilito l'esecuzione automatica dei pod](#abilito-lesecuzione-automatica-dei-pod)
- [Conclusioni](#conclusioni)
    - [Mysql](#mysql)
- [Postgres](#postgres)
- [MondoDB](#mondodb)

<!-- /TOC -->

**Docker su RH non è piu supportato. Esiste Podman**
Podman, sempre di Red Hat, è considerato il diretto successore e, facendo a meno di un demone centrale e dei privilegi di root, è in grado di allontanare molte preoccupazioni sulla sicurezza del suo predecessore. Per il resto, i due strumenti sono simili, anche se Podman deve ancora fare i conti con alcuni bug.

Con questa linea guida provo a configurare docker per essere utilizzato su una macchina virtuale.
Una risorsa interessante con buona documentazione è [Rootless Container](https://rootlesscontaine.rs)

## Installiamo
Prima installazioze quella di Docker. Installando docker viene installao **Podman**.
Possiamo seguire la [guida di oracle per l'installazione](https://oracle-base.com/articles/linux/podman-install-on-oracle-linux-ol8)

### Con o senza: cgroups V2 vs tmpfs
Per capire cosa sono iniziamo a leggere l'articolo [Introduzione ai cgroups](https://www.extraordy.com/cgroups-cosa-sono-e-come-sfruttarli-in-rhel-7/), perchè è importante capire quale metodo viene utilizzato sulla macchina scelta. Infatti una nota di RH dice che ["Rootless podman user cannot run..."](https://access.redhat.com/solutions/5913671) e quindi a differenza di come si gestisce in caso di non utilizzo dei Cgroup, se questi sono abilitati ed il container lo richiede perchè ha dei processi amministrativi, dobbiamo eseguire l'installazione come **root**.
L'articolo [rootless Podman di Matthew Heon](https://www.redhat.com/sysadmin/rootless-podman) lo spiega in maniera esaustiva.

#### L'errore
Lanciando una pull da utente normale si ottiene il seguente errore
```sh
Error: copying system image from manifest list: writing blob: adding layer with blob "sha256:faef57eae888cbe4a5613eca6741b5e48d768b83f6088858aee9a5a2834f8151": processing tar file(potentially insufficient UIDs or GIDs available in user namespace (requested 0:42 for /etc/gshadow): Check /etc/subuid and /etc/subgid if configured locally and run podman-system-migrate: lchown /etc/gshadow: invalid argument): exit status 1
```
#### e la soluzione
Alla fine ho eseguito due passaggi ed il risultato è andato a buon fine. Non so, se sarebbe bastato il secondo passaggio.
1. Ho inserito all'interno dei file `subuid` e `subgid` una linea relativa al mio utente, con i valori come i seguenti in base all'id
```sh
sysdatadmin:100000:65536
sysdat:165536:65536
casalegno:122720:65536
```
solo con questo passaggio il pull dava comunqu errore.
2. Ho quindi ho lanciato il comando di migrazione ed ho testato il pull con un container
```sh
podman system migrate
podman pull {alpine}
```
a questo punto il pull ha fatto il suo lavoro.

I **Croups** semplicemente servono a gestire le risorse della macchina (CPU, memoria, network I/O, disk I/O, ecc) e a profilare la loro allocazione ai processi in esecuzione. Per visionare quale tipologia di Cgroups è attivo posso scoprirlo lanciando il comando
```sh
stat -c %T -f /sys/fs/cgroup
```
**ROOT**
Negli esempi di prova ho utilizzato l'utente **root** per eseguire i comandi perchè trovo dei problemi con la creazione di symlink (per cambiare la posizione ove viene montato il volume) con la versione dei CGroup v2.

## Controllare i container
Per controllare quali container sono in esecuzione, possiamo utilizzare il seguente comando
```sh
docker container list
docker ps
podman ps -a ##visualizzo tutti anche quelli fermi
```
### Avviare un container
E' possibile avviare un container mentre lo si crea o crearlo senza lanciare il comando di avvio.
In questo secondo modo possiamo modificare i parametri del container prima che questo preconfiguri tutto.
Se invece eseguiamo tramite `run` un container in fase di creazione possiamo sfruttare l'opzione *-rm* che elimina automaticamente il container una volta terminata l'azione.  
```sh
podman run ... -rm ... {nome_container}
podman create ... {nome_container}
podman start {nome_container}
```

### Stoppare un container
Per fermare un container usiamo il comando stop per eliminarlo il comando rm
```sh
podman container stop {name/id}
```
il container puo essere riavviato semplicemente usando il comando *start* al posto dello stop e senza specificare l'opzione *container*.

### Cancelliamo un POD
Per cancellare un container uso il comando *rm*. Per eliminare tutti i container non utilizzati (non avviati) al momento uso *prune*.
Se invece voglio cancellare tutti i container, i pod, le immagini ed i volumi che non stanno lavorando, utilizzo *system*
```sh
podman rm {container}
podman prune {container}
podman system prune --all --force
podman system prune -af
```
Ho notato che *system prune* non elimina i volumi.
(OL9 - Cgroup V2) se non viene eseguito come **root** elimina solo i container. Bisogna eliminare a mano images e volumes.
I volumi montati in location separate non vengono riconosciuti.
Diciamo che in modalità *rootless* se voglio eliminare tutti i dati (che sono stati creati con quell'utente) posso cancellare la cartella `containers`.
```sh
rm -rf .local/share/containers/
```

### Controlliamo il log per vedere gli errori
Per controllare gli errori, Podman contiene un comando interno che permette di vedere i singoli errori direttamente da ogni container:
```sh
podman logs {container}
```

### Aprima una porta dal container all'host
Quando creo un container, se sulla stessa macchina girano piu container, devo cambiare la porta di connessione cosi da poter accedere esattamente a quel container.
Per farlo devo dichiarare la porta alla quale il client fa riferimento (ex 10000) e la porta che il server si aspetta (ex 3306):
```sh
-p 10000:3306
```
Se SELinux è disabilitato non ci sono blocchi per arrivare alla macchina 

### Creiamo un commit
Creare un commit significa creare una immagine del contenitore che indichiamo, da poterla cosi utilizzare come base, per creare altri contenitori.
```sh
podman commit <containeresistente> <nuovaimage>
```
il riavvio di un container è identico all'avvio classico semplicemente c'è il cambio di immagine dalla quale partire.


## Modifichiamo un file su un container.
All'interno di un docker, difficilmente troviamo un editor di testo per poter modificare dei file di configurazione. Se dobbiamo farlo, abbiamo due strade da seguire: diventare root ed installare l'editor, esportare il file modificarlo e reimportalo.

### Accediamo alla bash come root
Se il container è in esecuzione, possiamo accedere alla bash del container come utente di applicazione. Possiamo pero farlo anche come utente amministratore. Nel secondo caso abbiamo tutti i diritti per fare qualsiasi operazione.
```sh
podman exec -it <containerID> bash
podman exec -u 0 -it <containerID> bash #con l'opzione -u 0 accediamo come root.
```

### Import/export del file
Per non alterare il peso e le caratteristiche del container, possiamo scaricare, modificare e ricaricare il file cui siamo interessati. 
Per copiare il file dal container alla posizione in cui siamo
```sh
podman container cp <containerId>:/etc/mysql/my.cnf container-my.cnf
```
Una volta modificato il file, possiamo ricopiarlo nella cartella del container
```sh
podman container cp container-my.cnf <containerId>ysl:/etc/mysql/my.cnf
```

## Configurazione su OL9
Con l'uso dei CGroup2 l'utente base non puo avviare il POD se ha creato un SymLink dalla cartella nascosta *.local* a quella predefinita.
Con lo stesso concetto però ed utilizzando la stessa varibile in fase di dichiarazione posso creare il container facendo il mount direttamente nella cartella selezionata
```sh
podman create ... -v /var/lib/container/pg1:/var/lib/postgres/data ...
```
Il container viene creato ma non puo essere avviato: crun ritorna un errore sul file resolv.conf. L'errore è legato a crun ed i permessi per scrivere i symlink

**Il problema si risolve eseguendo podman con i permessi di root.**

### Avvio automatico di podman come servizio.
Questo intervento mi è stato richiesto dal reparto HW  e sto provando ad eseguirlo su una macchina sulla rete 192.168.100.136.
L'articolo da cui prendo spunto e che provo a seguire è [Configure a container to start automatically as a systemd service](https://www.redhat.com/sysadmin/container-systemd-persist-reboot) di A. Olivers du RedHat.com
E da tenere in considerazione anche la sezione di RedHat per [la gestione dei containers](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux_atomic_host/7/html/managing_containers/index)

### I pod si riavviano?
Nell'articolo l'autore crea un POD di httpd per eseguire un server apache. Noi avendo gia in funzione sulla macchina due pod con mysql e postgres proveremo ad eseguire quei due pod.

Come prima cosa vediamo come si comportano in fase di reboot della macchina.
I container al riavvio sono fermi, per poterli avere nuovamente in funzione devo farli partire sempre come utente **root**.

### Creo i file service
Una volta riavviati i container e controllato che anche se dopo essersi stoppati in malomodo riescono a ripartire correttamente, posso creare il file `.service`
Per farlo uso un comando interno di podman e lo faccio per ogni container che voglio venga eseguito automaticamente
```sh
 podman generate systemd --new --files --name postgres1
 podman generate systemd --files --name mysql1 ##si aspetta di trovare un container gia esistente
```
Questi due comandi creerano rispettivamente i file 

    container-mysql1.service
    container-postgres1.service

##### Nota sui file creati.
I file che vengono creati con questo comando, ogni volta che lanciamo il servizio, creano un uovo container con le stesse caratteristiche di quello precedente.
Ogni volta che lo stoppiamo, lo cancellano.
Vengono quindi creati e poi dismessi, ma non cancellati tutti gli overlay.

### Abilito l'esecuzione automatica dei pod
Il passo successivo quindi per abilitare l'esecuzione automatica e quella di copiare questi files nella cartella dei system service.
```sh
cp container-mysql1.service /etc/systemd/system/container-mysql1.service

cp container-postgres1.service /etc/systemd/system/container-postgres1.service
```
Copiati i file all'interno della cartella di system service li abilito al riavvio automatico.
```sh
systemctl enable container-mysql1.service
systemctl enable container-postgres1.service
```
Una volta che sono stati abilitati, questi servizi partiranno automaticamente al riavvio del sistema.
Per farli partire immediatamente e controllare se il servizio funziona possiamo spegnerli tramite podman e provare a riavviarli come servizio:
```sh
podman stop mysql1
systemct start container-mysql1.service
systemct status container-mysql1.service
```

## Conclusioni

I test eseguiti sui container hanno interessato i tre database che utilizziamo in Sisdat. Per ogni database ho creato una guida con tutte le informazioni acquisite durante i test.

### Mysql
I test sono stati [eseguiti e documentati](./Docker-MySql.md) sia su mysql 8.0.28 sia sulla versione 8.0.32
I risultati sono chiari: si possono eseguire i container con Mysql in entramebe le versioni. Ci sono alcune variabili d'ambiente da impostare perchè il PMS riesca a leggere e scrivere e si possono usarei file preparati da Angelo per sostituire il My.cnf.

## Postgres
Postgres 15.3 non ha dato particolari problemi se non in fase di ricerca delle variabili da utilizzare per la  creazione del contenitore. I dati si riscono a leggere e si riesce a scrivere all'interno senza particolari difficoltà.
L'importazione dei dati dai db gia esistenti non comporta nessun problema.

## MondoDB
Con mongodb il problema principale è stato quello di utilizzare una macchina con un kernel adattato. Lo stesso problema evidenziato nella versione non conteinerizzata. 