# MongoDB - Aggiornamento versioni

## I VALORI DI CONFIGURAZIONE DEI REPOSITORY DI MONGODB ED IL TEST SUL DATABASE LOCANDA CAVOUR

### MongoDB 3.2
Situazione di partenza
La versione di partenza della libreria di PHP alla quale MongoDB si appoggia e importantissima perchè al momento non si può aggiornare.
La tabella di compatibilità è visibile all'indirizzo 
https://www.mongodb.com/docs/drivers/php/?_ga=2.177649511.331656005.1685539167-13806316.1685539158#mongodb-compatibility. 
Nella seconda metà della pagina si vede la compatibilita della libreria con PHP e per sapere quale versione è in uso sulla macchina bisogna lanciare.
Massimo Porta suggerisce che la connessione tra questi due oggetti è irrilevante poichè il CRM si appoggia al Framework YII.
	
	php --ri mongo | grep Version
	php --re mongodb |grep version

al momento la situazione è la seguente:

	MongoDB extension version => 1.7.5
	libbson bundled version => 1.16.2
	libmongoc bundled version => 1.16.2
	libmongocrypt bundled version => 1.0.3

Poichè il CRM utilizza uno script dedicato per appoggiarsi a MongoDB perchè la versione ufficiale del connector tra Yii e MongoDB è presente solo da **Yii2** in avanti mentre noi utilizziamo **Yii 1.1.20** bisogna limitarsi ad utilizzare quella presente attualmente che **NON È AGGIORNABILE**
https://www.yiiframework.com/doc/api/2.0/yii-db-connection

### MongoDB 3.4	
Primo step nessun problema
### MongoDB 3.6
	[mongodb-org-3.6]
	name=MongoDB Repository
	baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
	gpgcheck=1
	enabled=1
	gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
	
Versione funzionante del database importato di cavour, funzionamento anche del crm. Test di controllo della presenza dei documenti e della loro visualizzazione

### MongoDB 4.0

	[mongodb-org-4.0]
	name=MongoDB Repository
	baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.0/x86_64/
	gpgcheck=1
	enabled=1
	gpgkey=https://www.mongodb.org/static/pgp/server-4.0.asc
	
Con la versione 4.0 installata continua a cadere la connessione al database.
Testiamo con l'aiuto di Bruno HW il cambio di classe per capire se la disconnessione è legata alla classe 235.
Al primo tentativo, dopo aver aggiornato con il nuovo IP il file di configurazione _/etc/mongodb.conf_ ed aver riavviato il demone, la macchina funziona!!!

I dati si vedo, si riescono a modificare, aggiornare eliminare.

### MongoDB 4.2	
Il db da terminale e con COMPASS funziona.
Se aggiorno la pagina del CRM la connessione a MONGODB salta Error 500.
Eseguito un _systemctl status mongod.service_ da un error 14 in partenza.
Risolto assegnando `chown mongod:mongod /tmp/mongodb-27017.sock`
Il CRM comunque fa saltare 
Aggiornato con adminCommand le CompatibilityVersion con un downgrade alla 4.0 ed il CRM ha ripreso a funzionare.
Il modulo Documentale viene visto, mi permette di visualizzare l'elenco dei documenti. Al primo tentativo di visualizzazione di un documento la connessione decade.
Eseguita piu volte la prova.
Ripristinato la CompatibilityVersion alla 4.2, il CRM ha ripreso a funzionare. 
Il modulo Documentale viene visto, mi permette di visualizzare l'elenco dei documenti.
Le query di filtro funzionano.
Richesta la visualizzazione di un documento, la connessione salta.
Riabilitato il modulo, richiesta la lista documenti, visualizzati piu documenti senza problemi.
Eliminato un documento - Modificato documento - Inserito Documento.

### MongoDB 4.4
Il db da terminale e con COMPASS funziona.
Se aggiornola pagina del CRM la connessione a MONGODB salta Error 500.
Riavviato il demone di httpd e il modulo documentale non è caduto.
Riesco a visualizzare i documenti e a lavorarci senza problemi.

**EXTRA**
Prima del passaggio alla nuova versione, poichè mi è stato fatto notare che potrebbe non funzionare, ho importato all'interno di MongoDB il database di altri hotel presenti su HeliteDb.
In particolare ho eseguito il dump di
- torrerinalda
- chisurelle
- gabbiano
- itacanusicaa
Tutti i Db funzionano e si vedono correttamente nel CRM

### MongoDB 5.0
Una volta installato mongodb 5 il demone non voleva partire. Errore 14/n/ad
L'errore è forse dovuto alla mancanza/cancellazione della directory dove vengono salvati i database.
In effetti poichè lanciando da console `mongod` lui ricarca la directory /data/db se questa esiste il demone entra in fuzione e MongoDB parte.
Eseguendo in questo modo il demone di MongoDB ho eseguito il restore dei 4 database precedentemente testati e tutti si vedono, si modificano e si gestiscono da CRM. Sempre con php 5.6.40

MongoDB 5.0 supporta fino ad CentOS/Oracle 8 NON supporta Oracle 9



### MongoDB 6.0
Con la nuova versione di MongoDb cambia il file /bin/mongo che divventa invece /usr/bin/mongosh. Il problema lo si puo risolvere con un alias.
Una volta avviato il demone di mongodb i database installati si vedono e si risce a navigare.
Il CRM non funziona.

MongoDB 6.0 Support CentOS/Oracle 6 fino ad CentOS/Oracle 9






CORSO MongoDB
---------------------------

- Seguito il corso di Mongodb su Youtube di Edoardo Midali (la base) che spiega le basi
https://www.youtube.com/playlist?list=PLP5MAKLy8lP_QKLouwhi-cKC_nDhYoPfR




