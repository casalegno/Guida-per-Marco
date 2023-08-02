# POSTGRESS
Per accedere al db postgres e piu facile riuscirci se sono un utente root.

<!-- TOC -->

- [Postgres dove e quando](#postgres-dove-e-quando)
- [Accediamo a Postgres](#accediamo-a-postgres)
        - [La password di default è B0C4MGliy3](#la-password-di-default-%C3%A8-b0c4mgliy3)
- [Controlliamo i database](#controlliamo-i-database)
    - [Database ordinati per dimensioni](#database-ordinati-per-dimensioni)
    - [Tabella ordinata per dimensioni](#tabella-ordinata-per-dimensioni)
- [Query di default per controllare se il db funziona](#query-di-default-per-controllare-se-il-db-funziona)
- [Installiamo da zero PostgeSQL](#installiamo-da-zero-postgesql)
    - [Configuriamo il database e l'utente](#configuriamo-il-database-e-lutente)
- [Encryption e connessione da psql < 10](#encryption-e-connessione-da-psql--10)
- [Comandi utili con Postgres](#comandi-utili-con-postgres)
    - [Modifica del db](#modifica-del-db)
- [Ottenere l'elenco dei DB in un file txt](#ottenere-lelenco-dei-db-in-un-file-txt)

<!-- /TOC -->

## Postgres dove e quando
Postgres è presente su due macchine: webcheckin e la macchina on-premis/saas del cliente. Questo perchè webcheckin la usa per la connessione al CRM (infatti la maggior parte dei db sono pieni di log di accesso) e per salvare i dai in html delle email inviate, mentre la seconda macchina contiene i dati relativi a marketing e ristorante.


## Accediamo a Postgres
Quindi diventato root posso accedere all'utente postgres con il comando [link](#abilitiamo-i-repository)
```sh
sudo -s
su - postgres 
```
Diventato infine utente postgress, me ne rendo conto se lancio il comando `$pwd` che restituisce _/var/lib/pgsq_ posso quindi accedere al database lanciando 
```sh
psql 
psql <database>
```
accedo quindi alla applicazione.

#### La password di default è B0C4MGliy3
Noto di aver effettuato l'accesso perchè la label è diventata `postgres=#`



Per selezionare il db del crm, uso `\c`; una volta che ho selezionato il database da utilizzare la label cambia con il nome del Db `NOMEDB=#`
Per visualizzare le tabelle uso `\dt` o `\dt+` per visualizzare piu dati.
Per uscire da postgres uso il `\q`

## Controlliamo i database
Possiamo controllare il peso dei vari db utilizzando il comando lista con il `+`

### Database ordinati per dimensioni
```sql
select t1.datname AS db_name,pg_size_pretty(pg_database_size(t1.datname)) as db_size from pg_database t1 order by pg_database_size(t1.datname) desc;
```

### Tabella ordinata per dimensioni
Con questa query posso vedere le dimensioni delle tabelle di un database in ordine decrescente.
```sql
select schemaname as "Schema", relname as "Table",
       pg_size_pretty(pg_relation_size(relid)) as data_size
from pg_catalog.pg_statio_user_tables
ORDER BY pg_relation_size(relid) desc;
```
Questa Select è importante perchè mi permette di controllare quanto spazio usato ha il database Postrges del cliente
```sql
SELECT relname as "Table",
 pg_size_pretty(pg_total_relation_size(relid)) As "Size", pg_size_pretty(pg_total_relation_size(relid) - pg_relation_size(relid)) as "External Size" FROM pg_catalog.pg_statio_user_tables 
ORDER BY pg_total_relation_size(relid) DESC;
```




## Query di default per controllare se il db funziona
Per eseguire una query di esempio per controllare se il database funziona uso:
```sql
select * from persone limit 10;
```
In alternativa uso il programma **PGADMIN**

<samp>
Questo è un elenco dei database presenti di base in postgres

|Nome      |Proprietario|Codifica| Ordinamento|Ctype       |Privilegi di accesso               |
|----------|------------|--------|------------|------------|-----------------------------------|
|crm       |postgres    | UTF8   |it_IT.UTF-8 |it_IT.UTF-8 |
|postgres  |postgres    | UTF8   |it_IT.UTF-8 |it_IT.UTF-8 |
|template0 |postgres    | UTF8   |it_IT.UTF-8 |it_IT.UTF-8 |=c/postgres + postgres=CTc/postgres|
|template1 |postgres    | UTF8   |it_IT.UTF-8 | it_IT.UTF-8|=c/postgres + postgres=CTc/postgres|
</samp>

Per creare un database o per cancellare un database esistente
```sql
create database "<database name>";
DROP DATABASE IF EXISTS <database name>;
```
Per eseguire il dump del database, ovvero una copia del database in un file esterno al db 
posso utilizzare il comando
```sh
pg_dump -U postgres <database name> > LOCATION/<database name>.db
psql <database name> < LOCATION/<database name> 
```
per l'upload dei file.

Per avere informazioni addizionali posso usare queste query:
To see where the data directory is, use this query.
```sql
show all; #vedo tutti i parametri di configurazione
show data_directory; #mostra la path di salvataggio dei dati.
```

## Installiamo da zero PostgeSQL
Se vogliamo installare da zero Postgres, possiamo seguire le [istruzioni sul sito ufficiale](https://www.postgresql.org/docs/current/index.html).
L'installazione è piuttosto basilare e non viene riscritta perchè potrebbe variare in base al package manager usato ed ai relativi repo.
### Configuriamo il database e l'utente
per installare postgres su Oracle9 quindi loggati come utente postgres ed accedi al db.
imposta una password per il db 
```sql
ALTER USER user_name WITH PASSWORD 'new_password';
### oppure
\password 
```
memorizzando la password che si inserisce.

In questo modo la password viene salvata ma il metodo di autentificazione rimane peer.
Modificare quindi il file pg_hba.conf all'interno della direcotry 
/var/lib/pgsql/15/data/pg_hba.conf
nelle righe:
```cfg
# TYPE  DATABASE        USER            ADDRESS                 METHOD
# "local" is for Unix domain socket connections only
#local   all             all                                     peer
local   all             postgres                                md5
# IPv4 local connections:
host    all             all             127.0.0.1/32            scram-sha-256
```
remmare la prima riga riferita a 'local' ed aggiungere una seconda come quella indicata
`local   all             postgres                                md5`
in questo modo si abilita l'accesso a Postgresql con l'utente postgres ed una password.

## Encryption e connessione da psql < 10
Se al server ci si deve collega da un server linux Centos7 e necessario fare attenzione che la modalità di encryption della password sia impostata a md5, per fare questo prima di andare a impostare la password con il comando “ALTER USER” bisogna modificare nel file `/var/lib/pgsql/15cd da	/data/postgresql.conf` il parametro *“password_encryption”* impostandolo a *“md5”* (password_encryption = md5) salvare e riavviare postgres (systemctl restart postgresql-15)

Per controllare l'attuale encryption bisogna lanciare il comando
```sql
show password_encryption;
```

## Comandi utili con Postgres
```sql
\l (\elle) #visualizzo i database
\c <database name> #scelgo il database da utilizzare
\d #elenco le tabelle
\dt+ #elenco le tabelle con info aggiuntive
\q #esco dal db
\! clear #per pulire la schermata
[CTRL+L] #per pulire la schermata
```
### Modifica del db
```sql
ALTER DATABASE db RENAME TO newdb;
```

## Ottenere l'elenco dei DB in un file txt
Esegui il comando psql per ottenere l'elenco dei database
 Utilizziamo l'opzione -t per ottenere solo i nomi dei database senza alcuna formattazione
 Utilizziamo l'opzione -c per specificare il comando SQL da eseguire
 Utilizziamo l'opzione -w per richiedere la password dell'utente del database in modo sicuro
```sql
psql -t -c "SELECT datname FROM pg_database WHERE datistemplate = false;" -h 0.0.0.0 -U postgres -w > dblist.txt
```







