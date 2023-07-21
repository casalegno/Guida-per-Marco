# Mongodb
Mongo è un db NoSQL che gestisce per il CRM i documenti.
Infatti per funzionare e per visualizzare i docuementi salvati, nel CRM è necessario abilitare il modulo Documentale.

## Accediamo a mongodb
MongoDB lo troviamo in esecuzione al momento su WebcheckinDB la macchina con il 192.168.250.10 e per accedere dobbiamo usare il comando `mongo` che genera una connessione al db
```sh
mongo
mongosh ##dalla versione 6 di mongo
connecting to: mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb
```
Quindi visualizziamo i database presenti, ne scegliamo uno ed esco da MongoDB.
```sql
> show dbs;
...
...
> exit
```
Quindi eseguo il dump

## Dump e restore del database
Come indicato [nella guida ufficiale](https://www.mongodb.com/docs/manual/tutorial/backup-and-restore-tools/) il dump ed il restore del db sono semplici operazioni da effettuare.
Ci sono 2 diversi programmi che gestiscono il dump e 2 per il restore.
Mongodump e mongorestore lavorano con i file binari.
```sh
mongodump \
   --host=mongodb1.example.net \
   --port=3017 \
   --username=user \
   --password="pass" \
   --out=/opt/backup/mongodump-1

mongodump -d {nomedatabase} -o {posizionedeldump}
mongodump -d dbtest -o .
```
Il restore funziona indicativamente allo stesso modo
```sh
mongorestore -d {nomedatabase} --host={hostip} --port={11243}
```
è importante ricordarsi che quando importo un db devo indicare in quale database importare. Crea lui autonomamente il database se non esiste

## Abilitiamo il bindIp remoto
Per abilitare il bind remoto a MongoDB che solitamente accetta solo ed esclusivamente accessi da localhost dobbiamo modificare il file di configurazione e riavviare il demone.
```sh
vi /etc/mongod.conf
```
>#  bindIp: 127.0.0.1  # Listen to local interface only, comment to listen on all interfaces.
>bindIp: 0.0.0.0
```sh
systemctl restart mongod
```

## Cambiamo nome ad un databae
Per cambiare un nome ad un db dobbiamo, dalla versione 4 in avanti, eseguire il dump ed il restore.

## Cancellare un Database
Per cancellare un database dobbiamo per prima cosa accedere al database quindi lanciare il comando di drop
```sql
use {database}
switched to db {database}
db.dropDatabase()
```
ed in questo modo viene cancellato.


Error 500
192.168.100.137:10017: OP_QUERY is no longer supported. The client driver may require an upgrade. For more details see https://dochub.mongodb.org/core/legacy-opcode-removal