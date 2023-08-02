# Adminer 
Adminer è una valida altenativa a PhpMyAdmin e permette di collegari.
Poichè presenta un'interfaccia semplice e non ha bisogno di particolare configurazioni può essere tranquillamente installato con il comando di base senza dover ricevere varibili di ambiente.

E' stato installato sulla OL8.8 con il comando e la variabile di default 0.0.0.0 per capire a quale server collegarsi. Ma non è necessaria poichè viene richiesta come variabile in fase di connessione. 
```sh
 podman create --name adminer -p 8100:8080 -e ADMINER_DEFAULT_SERVER=0.0.0.0 adminer
```

E' stata installata sulla OL9 con l'immagine di *dockette* poiche promette di occupare solo 22Mb
nella realtà l'immagine indicata da podman occupa 56.4MB
```sh
podman create --name adminerer -p 8100:8080 dockette/adminer
```
La connessione, con entrmabi i database, sia Mysql che Postgres funziona. La connessione a Mongodb non funziona perchè richiede l'autentificazione con User e Password.
Una volta impostato un utente con password mi è possibile accedere anche a MongoDb e navigare attraverso i dati.
Al momento non ho provato a lanciare delle query.
