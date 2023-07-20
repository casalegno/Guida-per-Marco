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
