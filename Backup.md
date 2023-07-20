# Come eseguire i Backup della macchina

## Il vecchio metodo

Lo script da utilizzare per effettuare il backup è il file presente nella cartella `/u/usr/genius7/batch` ed il file è `DumbDb.sh`

### Configuriamo il file

	HOME="/u/usr/genius7"
	DATE=`date +%u`
	LOCAL_BACKUP_DIR="$HOME/backup"
	DB_AZ1="DEFAULT"
	DB_AZ2="RISTO"  //da remmare se non utilizzata
	#DB_AZ3="PROVA"
	DB_USER="root"
	DB_PASSWORD="SYSDAT"
	DB_HOST="127.0.0.1" inserire l'host della macchina

	SFTP_SERVER="xxx.xxx.xxx.xxx"
	SFTP_USERNAME=""
	SFTP_PASSWORD=""
	SFTP_UPLOAD_DIR=""

	LOG_FILE_MYSQLDUMP=$HOME/backup/backup-Mysql.log
	LOG_FILE_POSTGRESDUMP=$HOME/backup/backup-Postgresql.log
	LOG_FILE_MONGODBDUMP=$HOME/backup/backup-Mongodb.log

Per le righe dei database, bisogna remmar anche le righe riferite alle aziende non in uso

	#mysqldump -u $DB_USER  -p$DB_PASSWORD -h $DB_HOST --databases rdbedoc_indici rdbedoc_files rdbedoc_tabstampe  > $LOCAL_BACKUP_DIR/$DATE-$DB_AZ2.sql
	#/u/sysdat/bin/cryptotar.sh $LOCAL_BACKUP_DIR/$DATE-$DB_AZ2 $LOCAL_BACKUP_DIR/$DATE-$DB_AZ2.sql

	#mysqldump -u $DB_USER  -p$DB_PASSWORD -h $DB_HOST --databases  xxxx_indici xxxx_files xxxx_tabstampe  > $LOCAL_BACKUP_DIR/$DATE-$DB_AZ3.sql
	#/u/sysdat/bin/cryptotar.sh $LOCAL_BACKUP_DIR/$DATE-$DB_AZ3 $LOCAL_BACKUP_DIR/$DATE-$DB_AZ3.sql
	
In questo caso le righe riferite ad _azienda2_ ed _azienda3_

## Il nuovo metodo

Con il nuovo metodo, la cui descrizione è rintracciabile sull wiki
https://wiki.sigesgroup.it/?q=content/nuova-batch-salvataggio-database

Prima eseguire sempre il `postfix.sh`.

Quindi eseguire il `DumpDb.sh`.
In questo file tutte le variabili sono all'inizio.
Per effettuare una prova di Backup si puo all'interno del file, modificare il nome del cliente.
Una volta avviato lo script con il dato sbagliato, se non riesce a eseguire il mount perchè c'è un errore qualsiasi (tipicamente errore di folder) parte una mail di sicurezza all'assistenza Turismo.

All'interno del file DumpDb.sql a differenza della vecchia versione trovo una sola voce _DB_AZ_

	#!/bin/bash
	#Per info sulla batch chiedere a Guizza!
	###############  Definizione Variabili  ########################
	
	CLIENTE="vmcasalegno"
	HOME=/u/usr/genius7
	LOCAL_BACKUP_DIR=/media/nas
	DB_AZ="DEFAULT"
	DB_PREFIX="db"
	DB_USER=root
	DB_PASSWORD=SYSDAT
	DB_HOST=127.0.0.1
	
	POSTGRES_DB=crm
	SFTP_SERVER=192.0.200.250
	SFTP_USERNAME="sysdat"
	SFTP_PASSWORD="ferrari21"
	SFTP_UPLOAD_DIR=sysdat
	VERS="2.0"

	EMAIL_LOG=assistenza.turismo@sigesgroup.it

	######################################################
	#   NON MODIFICARE DOPO QUESTA RIGA
	######################################################
	....

per eseguire il backup di piu aziende traformo la variabile _DB_AZ_ in un array ed allo stesso modo trasformo la variabile _DB_PREFIX_ inserendo separati da uno spazio le diverse aziende ed i prefissi dei database relativi.

	DB_AZ="DEFAULT ARLECCHINO MIRAMARE"
	DB_PREFIX="db ar mm"

## Eseguire il Purge dei logs

Per eseguire il purge dei logs eseguo lo script seguente una volta eseguito l'accesso a mysql.

	$mysql -u $USER -p$PWD -h $HOST
	mysql> purge binary logs before '2023-12-25 14:30'


## Eseguire il backup di ambienti su macchine in rete.
Per eserguire il backup utilizziamo il comando `rsync` presente su tutte le distro, nel caso lo si installa.
Per eseguire un test il comando che ho lanciato è
```sh
rsync -RlPhva <origine> <utente@server:desinazione>


