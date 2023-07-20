# QUERY MYSQL PER ANALIZZARE LE TABELLE

## Dimensioni degli indici
Con questa query invece vediamo la dimensione degli indici
```sql
SELECT table_schema as database_name,
table_name 'Nome Tabella',
round(index_length/1024/1024,2) as 'Indici in MB'
FROM information_schema.tables
WHERE table_type = 'BASE TABLE'
     and table_schema not in ('information_schema', 'sys','performance_schema', 'mysql')
     and table_schema = '<db_name>'
ORDER BY 'Indici in MB' desc;
```

## Controllare il numero di righe di una tabella mysql
SELECT COUNT(*) FROM count_demos;


### Eseguiamo il passaggio dei dati

Eseguiamo tutti i passaggi per il trasferimento dei dati, creando una nuova tabella identica alla precedente, alla quale assegnamo il metodo compress e ne copiamo all'interno tutti i files.
```sql
CREATE TABLE nuova LIKE filesdati
```

## Abilitiamo quindi le colonne Compresse
#### Aggiornamento a MySQL 8
##### Nota
Cio che ho scoperto in fase preliminare è che con MySQL8 le varibili dedicate ad `InnoDB_ File_Format` sono state deprecate e non sono quindi piu funzionanti.
Per impostare una compressione su una tabella bisogna assegnare il `ROW_FORMAT=COMPRESSED` ed il `FILE_BLOCK_SIZE=8192`
The row format of a table determines how its rows are physically stored, which in turn can affect the performance of queries, DML operations, and disk usage.
 
- controlliamo quale tipo di _ROW_FORMAT_ sia utilizzato nelle tabelle del DB
    ```sql
     SELECT table_name, row_format FROM information_schema.tables WHERE table_schema=DATABASE();
    ```
- ne modifico il formato di compressione 
    ```sql
    ALTER TABLE nuova ROW_FORMAT=COMPRESSED KEY_BLOCK_SIZE=4;
    ```
- Controlliamo lo stato del DB Engine
    ```sql
    SHOW ENGINE INNODB status;
    ```
- copiamo i dati nella nuova tabella 

    ```sql
    INSERT INTO nuova SELECT * FROM filesdati;
    ```
    Il processo impieghera il tempo  necessario in base alla dimensione del DB ed alla potenza della macchina che opera.
    **Poichè ci possono essere problemi in fase di copia, può essere utile inserire un limite di query durante la copia della tabella:**
    ```sql 
    INSERT INTO nuova SELECT * FROM filesdati limit 5000; // i primi 5000
    INSERT INTO nuova SELECT * FROM filesdati limit 5000,5000; // poi a blocchi di 5000
    INSERT INTO nuova SELECT * FROM filesdati limit 10000,10000; // poi a blocchi di 10000
    ```

## Facciamo un controllo sui binlog.
I bin log sono dei file che vengono creati da MySql di default. Servono per registrare tutte le operazioni effettuate su Mysql e possono tornare utili in caso di replication.
Possono tranquillamente essere cancellati direttamente attraverso Mysql con una query. 
```sql
PURGE BINARY LOGS BEFORE NOW() - INTERVAL 3 DAY;
```
## Risultato
Dopo l'importazione dei dati il nuovo confronto tra le due tabelle compresse è il seguente:
```sql
SELECT     table_name AS 'Table',     data_length + index_length 'Size (Bytes)',     ROUND(((data_length + index_length) / 1024 / 1024), 2) `Size (MB)` FROM information_schema.tables WHERE table_schema = "flmdbedoc_files";


 SELECT     table_name AS 'Table',     data_length + index_length 'Size (Bytes)',     ROUND(((data_length + index_length) / 1024 / 1024), 2) `Size (MB)` FROM information_schema.tables WHERE table_schema = "flmdbedoc_files" AND table_name IN ("filesdati",'nuova');
 ```
| Table     | Size (Bytes) | Size (MB) |
|-----------|--------------|---------: |
| filesdati |  20793737216 |  19830.45 |
| nuova     |   7450238976 |   7105.10 |

### Test di compressione 
Sulla macchina locale operando sulla tabella originale `filesdati`, 
| Table         | Size (Bytes) | Size (MB) |
|---------------|--------------|---------: |
| nuova         |   7450238976 |   7105.10 |
| filesdati     |   7725457408 |   7367.57 |

ho portato il DB dai precedente **18830.45 Mb** agli attuali **7367.57 Mb**.
Il tempo di esecuzione è stato piuttosto ridotto:
iniziato il processo alle **15:25** terminato alle **16:40**

Come ha fatto notare Luca, questo livello di compressione puo portare ad un incremento di lavoro della macchina che deve processare piu passaggi per decomprimere i dati.
Dare una letta a https://www.percona.com/blog/mysql-blob-compression-performance-benefits/

## MySql dump compress.
Il fattore di compressione è intrinseco nell'uso di InnoDb. Facendo un mysqldump di un db compress otterremo un file della dimensione originale del database.


Alcune info importanti
https://serverfault.com/questions/358444/setting-mysql-innodb-compression-key-block-size

Facendo alcune prove
```
mysql> select * from INFORMATION_SCHEMA.INNODB_CMP;
+-----------+--------------+-----------------+---------------+----------------+-----------------+
| page_size | compress_ops | compress_ops_ok | compress_time | uncompress_ops | uncompress_time |
+-----------+--------------+-----------------+---------------+----------------+-----------------+
|      1024 |            0 |               0 |             0 |              0 |               0 |
|      2048 |            0 |               0 |             0 |              0 |               0 |
|      4096 |            0 |               0 |             0 |              0 |               0 |
|      8192 |      7029231 |         6352315 |          1437 |         339708 |              41 |
|     16384 |            0 |               0 |             0 |              0 |               0 |
+-----------+--------------+-----------------+---------------+----------------+-----------------+
5 rows in set (0.00 sec)

mysql> select * from INFORMATION_SCHEMA.INNODB_CMPMEM;
+-----------+------------+------------+----------------+-----------------+
| page_size | pages_used | pages_free | relocation_ops | relocation_time |
+-----------+------------+------------+----------------+-----------------+
|       128 |      11214 |          0 |        8434571 |               2 |
|       256 |          0 |         37 |              0 |               0 |
|       512 |          0 |         34 |              0 |               0 |
|      1024 |          0 |          2 |              0 |               0 |
|      2048 |          0 |        141 |              0 |               0 |
|      4096 |          0 |        298 |          96657 |               0 |
|      8192 |      15133 |          0 |        4121178 |               5 |
|     16384 |          0 |          0 |              0 |               0 |
+-----------+------------+------------+----------------+-----------------+
8 rows in set (0.00 sec)
```

## Mysql Disbilitare il Journal - Binary Log
Abilitare o disabilitare i Binary Log puo incidere o meno sulle prestazioni della CPU durante l'utilizzo di Mysql.
Un motivo per non disabilitare i Binary Log è legato alla sicurezza dei dati: in caso di errore MySql ripristina il funzionamento del DB appoggiandosi ai BinLog presenti.

## Mysql Single 
In mysql non è possibile creare un accesso in single mode, quindi permettere ad un solo utente alla volta la possibilità di leggere e scrivere.
Al limite è possibile impostare mysql in modalità readonly.
```sql
ALTER DATABASE <database> READ ONLY = 1;
```
In rete si trovano **informazioni relative a SQLServer** dove è possibile avere una situazione di 'Single User Mode' in cui un utente alla volta puo accedere al database.
```sql
#single mode
ALTER DATABASE <database_name> SET SINGLE_USER WITH ROLLBACK IMMEDIATE

#multiple mode
ALTER DATABASE <database_name> SET MULTI_USER;
```