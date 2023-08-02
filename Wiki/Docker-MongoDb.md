# Mongodb su Podman
Iniziamo il test con Mongodb 6.0 per Podman.

<!-- TOC -->

- [Creiamo il container](#creiamo-il-container)
        - [Si ripresenta il problema del kernel](#si-ripresenta-il-problema-del-kernel)
- [Accediamo a MongoDB su container](#accediamo-a-mongodb-su-container)
        - [Errore del branch](#errore-del-branch)
    - [Importiamo ed esportiamo i dati.](#importiamo-ed-esportiamo-i-dati)
    - [Aggiornaimo i container](#aggiornaimo-i-container)
    - [Il file di configurazione](#il-file-di-configurazione)
- [Gli errori sui dati](#gli-errori-sui-dati)
- [Configuriamo un utente](#configuriamo-un-utente)

<!-- /TOC -->

Pensiamo a duplicare la macchina perche su questa ci sarà oltre al crm anche apache ed altro. Io vorrei avere tutto questo su una macchina pulita.
potrei sfruttare la 192.168.100.136 - [NON È POSSIBILE](#si-ripresenta-il-problema-del-kernel)
allora utilizziamo la macchina ufficiale di mongodb che ha gia installato in locale mongodb
effettivamente è la 192.168.100.137

## Creiamo il container
Al momento lancio il comando di creazione del pod con queste caratteristiche
```sh
podman create --name mongo1 -v mongo1:/data/db -p 0.0.0.0:27017:27017 mongo:6.0.8
```
Il container viene creato ma non avviato
La cartella interessata è la stessa valida per tutti gli altri container 

> /var/lib/containers/storage/volumes/

#### Si ripresenta il problema del kernel
Il container non si avvia poichè viene indicato il seguente errore

>WARNING: MongoDB 5.0+ requires a CPU with AVX support, and your current system does not appear to have that!
>see https://jira.mongodb.org/browse/SERVER-54407
>see also https://www.mongodb.com/community/forums/t/mongodb-5-0-cpu-intel-g4650-compatibility/116610/2
>see also https://github.com/docker-library/mongo/issues/485#issuecomment-891991814

In questo caso l'unica soluzione è cambiare la tipologia di macchina poichè non c'è modo di farla lavorare con questa combinazione. 

## Accediamo a MongoDB su container
Dobbiamo sempre accedere indicando porta e host, tramite mongo shell
```sh
mongosh --port 10017 --host 0.0.0.0 
```
l'accesso a MongoDb anche su container da terminale funziona senza problemi.
Sfortunatamente non ho un'altra macchina con installato la shell di mongo 6 per effettuare un test di connessione.

#### Errore del branch
Dal CRM continua a non collegarsi a Mongo6. Il problema è legato alla versione del branch in uso: al momento di effettuare le connessini siamo sul branch crm2.17
Basta spostarsi sul branch crm3.0

### Importiamo ed esportiamo i dati.
L'import e l'export dei dati è fattibile e da linea di comando si riesce ad eseguire senza difficoltà, dando come parametri quelli del container attivo.

Ho eseguito il dump di un db presente su mongo in locale ed importato con mongorestore
```sh
mongodump -port {porta} -host {host} -d {database} path_file
mongorestore --port 10017 -host 0.0.0.0 -d {database} path_db
```

### Aggiornaimo i container
Anche l'aggiornamento della versione e fattibile senza difficoltà sempre con il passaggio dell'export import.
I dati fanno il passaggio da una versione all'altra senza difficoltà.
Si potrebbe tramite modifiche varie utilizzare la stessa cartella di una versione per essere letta dall'altra ma cercando online ci sono difficoltà di conversione.

### Il file di configurazione
Mongo gestisce i parametri tramite un file di configurazione chiamato `monogd.conf`.
Nel container ufficilae il file da copiare ha un nome simile
```sh
podman cp mongo1:/etc/mongod.conf.orig .
```
Possiamo ad esempio abilitare l'autentificazione al db. Dopo aver creato l'utente e con il container fermo all'interno di questo file inseriamo la voce
```yaml
security:
  authorization: enabled
```

## Gli errori sui dati
Non avendo una macchina aggiornata con Genius7 ho collegato la macchina di test 192.0.200.140 al crm di test che sto utilizzando.
Quando il PMS tenta di scrivere sul database all'interno di docker restituisce un errore.
Anche quando il crm tenta di scrivere su database restituisce un errore ed è lo stesso che otteniamo con il PMS.
```sh
$scenario per DocsFileFactory non impostato
```
Questo errore è ancora da identificare ma puo essere ricollegato a Php (probabilmente).

Al contrario del PMS che riesce a collegarsi a mongo ed infatti crea il database *'docuemntale'*, il CRM riesce anche a salvare i dati all'interno del Db creando tutte le collections, nonostante l'errore sopra indicato.
Una volta che ha salvato i dati però non riesce piu a leggerli. Lo stesso problema si presenta nel momento in cui i dati vengono importati da un database esistente di qualche cliente ed importato.
```sh
Error 500
Object of class MongoDB\Model\BSONArray could not be converted to string
```
L'errore però viene evidenziato sia che si lavori con mongo installato sulla macchina sia con mongo installato nel container.

Lo stesso errore appare sia che lavoriamo di base su una macchina OL8 sia su OL9


## Configuriamo un utente
Per mettere in sicurezza mongo e per dare la possibilità di accedervi anche da un server esterno.
Creiamo un nuovo utente root
```sql
use admin
db.createUser(
  {
    user: "Admin",
    pwd: 'mongodb6', //pwd: passwordPrompt(), // or cleartext password
    roles: [
      { role: "userAdminAnyDatabase", db: "admin" },
      { role: "readWriteAnyDatabase", db: "admin" }
    ]
  }
)
```
