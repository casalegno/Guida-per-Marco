DATABASES - Mysql, Postgres e MongoDb
==========

## MySql in SaaS
Le macchine presenti in Saas, quindi 198.162.100.131/132 ecc. salvano i dati relativi nel database  mysql che gira direttamente sulla stessa macchina.

Per accedere a questo database è necessario indicare la stringa completa con user, password ed host 
```sh
$mysql -u root -p -h 127.0.0.1
```
- $PWD=SYSDAT
- =nomedatabase #inserire il nome del database de utilizzare

una volta eseguito l'accesso

	SHOW DATABASES; #per mostrare i database presenti sul host
	USE nomedatabase; #per selezionare il database da utilizzare
	SHOW TABLES; # per mostrare l'elenco delle tabelle
	SELECT * FROM ..... WHERE LIMIT\G
	#quando faccio una select e richiedo una visualizzazione facilmente leggibile, devo inserire a fine riga il \G
	DESCRIBE {tabella}; # mostra la struttura di una tabella.
	ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';




------------------------------------------------------------------------------------
### Phpmyadmin

Se voglio installare phpmyadmin per potervi accedere dal browser del sistema host devo eseguire i seguenti passaggi:

	yum install phpmyadmin

quindi configurare il file presente in `/etc/httpd/conf.d/phpMyAdmin.conf` e sostituire il contenuto tra `<Directory>....</Directory>` con 

	<Directory /usr/share/phpMyAdmin/>
		AddDefaultCharset UTF-8
		<IfModule mod_authz_core.c>
		 # Apache 2.4
		 <RequireAny>
			#Require ip 127.0.0.1
			#Require ip ::1
			Require all granted
		 </RequireAny>
		</IfModule>
		<IfModule !mod_authz_core.c>
			# Apache 2.2
			Order Deny,Allow
			Deny from All
			Allow from 127.0.0.1
			Allow from ::1
		</IfModule>
	</Directory>

quindi vado a modificare il file `/etc/phpMyAdmin/config.inc.php` andando ad inserire come variabili per User e Password quelle di Mysql.

	...
	$cfg['Servers'][$i]['user']          = 'root';          // MySQL user
	$cfg['Servers'][$i]['password']      = 'SYSDAT';          // MySQL password (only needed
                                                    // with 'config' auth_type)
	...


-----------------------------------------------------------------------------------


---------------------------------------------------------------------------------
## MongoDB

MongoDB si differenzia da gli altri DB poichè si tratta di un database NoSql, non è composto da tabelle con righe e colonno ma è composto da _collections_ ovvero collezioni di documenti.
Un database NoSql significa che, a differenza di Mysql e PostgreSQL non supporta le query SQL per gestire i dati registrati.
Le collections  ed i documenti in cui MongoDB salva i dati hanno una struttura similare a Json. Ogni documento infatti, è formato da una serie di elementi e loo si puo vedere con un oggetto di Bson (Json).
I dati vengono passati in Json, tramutati in Bson e ritornati in Json per essere elaborati. Ogni elemento è composto da due campi strutturato come <code>campo:valore (field:value).</code>

**Bisogna fare attenzione poichè non ci sono schemi e strutture predefinite.**

Per quel che riguarda MongoDB e la sua installazione/aggiornamento, fare riferimento alla [guida ufficiale di Marco](./Mongodb.md)

