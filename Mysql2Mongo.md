# Conversione database Mysql Mongo

Facciamo un test di conversione dei database da Mysql a Mongo. Come database di prova utilizziamo il DB dell'Hotel Flora
La struttura è composta da 3 database differenti:
- flmdbedoc_files: tot. righe 1.247 - 89.6 MiB - 
- flmdbedoc_indici: tot. righe 537 - 15 MiB
- flmdbedoc_tabstampe: tot. righe 0 - 16 KiB

Come si evince dalle dimensioni del DB andiamo a lavorare principalmente sul Db dei files per ottimizarlo e provare a spostarlo successivamente su Mongo.

## Mysql
Seguendo la guida di ottimizzazione delle tabelle presente su HTML.it come prima cosa analizziamo il DB _information_schema_ e controlliamo su questo db la corrispondenza tabella schema e nome tabella.

### Iniziamo ad eliminare le righe che non ci interessano
Ora possiamo iniziare ad eliminare tutte le tabelle che occupano dello spazio ma che in tutto questo periodo non son utilizzate.
```sql
SELECT table_schema,table_name,create_time,update_time from information_schema.tables 
WHERE table_schema not in ('information_schema','mysql') 
	and engine is not null and ((update_time < (now() - interval 1 day)) or update_time is NULL) 
LIMIT 5;
```
Con la query sopra indicata andiamo ad ottenere un risultato in cui si vedono tutte le tabelle che non anno ricevuto un update, quindi non sono state utilizzate. Nell'esempio viene indicato `interval 1 day` ma questa voce puo essere agiornata in `interval 1 month` oppure `interval 1 year` per modificare i valori.


##### Risorse Utilizzate
> https://www.forknerds.com/reduce-the-size-of-mysql/
> https://www.html.it/pag/65479/analisi-e-ottimizzazione-della-memoria/
> https://stackoverflow.com/questions/30635603/what-does-table-does-not-support-optimize-doing-recreate-analyze-instead-me
> https://dba.stackexchange.com/questions/14246/innodb-file-format-barracuda
> i database originali presenti sulla VM sono stati salvati in `/var/lib/phpMyAdmin/save`

## MongoDb
