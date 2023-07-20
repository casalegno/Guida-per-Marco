# Mongodb su Podman
Iniziamo il test con Mongodb 6.0 per Podman. 

Pensiamo a duplicare la macchina perche su questa ci sarà oltre al crm anche apache ed altro. Io vorrei avere tutto questo su una macchina pulita.
potrei sfruttare la 192.168.100.136 - [NON È POSSIBILE](#si-ripresenta-il-problema-del-kernel)
allora utilizziamo la macchina ufficiale di mongodb che ha gia installato in locale mongodb
effettivamente è la 192.168.100.137
Al momento lancio il comando di creazione del pod con queste caratteristiche
```sh
podman create --name mongo1 -v mongo1:/data/db -p 0.0.0.0:10017:27017 mongo:6.0.8
```
Il container viene creato ma non avviato

#### Si ripresenta il problema del kernel
Il container non si avvia poichè viene indicato il seguente errore

>WARNING: MongoDB 5.0+ requires a CPU with AVX support, and your current system does not appear to have that!
>see https://jira.mongodb.org/browse/SERVER-54407
>see also https://www.mongodb.com/community/forums/t/mongodb-5-0-cpu-intel-g4650-compatibility/116610/2
>see also https://github.com/docker-library/mongo/issues/485#issuecomment-891991814

In questo caso l'unica soluzione è cambiare la tipologia di macchina poichè non c'è modo di farla lavorare con questa combinazione. 

## Eseguiamo il dump di mongodb
Ho eseguito il dump di un db presente su mongo in locale ed importato con mongorestore

```sh
mongorestore --port 10017 -host 0.0.0.0 -d {database} path_db
```

docker exec <CONTAINER> sh -c 'exec mongodump --db somedb --gzip --archive' > dump_`date "+%Y-%m-%d"`.gz

## Accediamo a MongoDB su container
Dobbiamo sempre accedere indicando porta e host, tramite mongo shell
```sh
mongosh --port 10017 --host 0.0.0.0 
```