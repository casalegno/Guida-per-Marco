# Importiamo i dati su Vm Mysql8.0

La macchina 192.168.100.136 è la macchina SaaS dedicata ai Db Mysql8.
Sto importando su questa macchina i db di prova di tre Hotel per testarne la compressione ed il carico della CPU durante le query sulla macchina.

**Nota**
Cio che ho scoperto in fase preliminare è che con MySQL8 le varibili dedicate ad `InnoDB_ File_Format` sono state deprecate e non sono quindi piu funzionanti.
Per impostare una compressione su una tabella bisogna assegnare il `ROW_FORMAT=COMPRESSED` ed il `FILE_BLOCK_SIZE=8192`

Questi sono i file che importo:
```
-rw-r--r--. 1 sysdatadmin sysdatadmin 28998253802 13 giu 16.50 bmdbedoc.sql
-rw-r--r--. 1 sysdatadmin sysdatadmin  5066020190 13 giu 14.12 ccodbedoc.sql
-rw-r--r--. 1 sysdatadmin sysdatadmin 27561692689 13 giu 15.11 codbedoc.sql
-rw-r--r--. 1 sysdatadmin sysdatadmin         512 16 giu 16.32 readme.md
```

## Passaggi
I passaggi eseguiti, da un database completamente pulito sono i seguenti:
- [Importo i database all'interno di MySql cosi come sono](#importo-i-dati)
- [Controllo il peso dei database e delle singole tabelle di un db](#dimensioni-delle-tabelle-e-dei-database)
- [Eseguo un check della tipologia di compressione dei dati](#check-della-di-tipologia-di-compessione-dati)
- [Creiamo una nuova tabella](#creo-la-nuova-tabella)
- [Trasferiamo i dati](#trasferiamo-i-dati-tra-le-tabelle)


### Importo i dati
Il metodo di importazione è piuttosto semplice e classico. Va fatto per ogni gruppo di DB utilizzato facendo attenzione che non si sovrascrivino.
```sh
mysql -u root -p < {nomedb}.sql
```
Terminato il processo di importazione controlliamo il peso di ogni singolo database [visualizzandoli in elenco](Mysql%20Compress.md#selectDBname)




### Dimensioni delle tabelle e dei database
Con questa query andiamo ad analizzare il peso di una singola tabella all'interno di un database. Posso elencare le tabelle e posso scegliere il database
```sql
SELECT
    table_name AS 'Table',
    data_length + index_length 'Size (Bytes)',
    ROUND(((data_length + index_length) / 1024 / 1024), 2) `Size (MB)`
FROM information_schema.tables
WHERE table_schema = "{pre}dbedoc_files";
```
oppure per vederle raggruppate[#selectDBname]()
```sql 
SELECT 
    table_schema "DB Name",
    Round(Sum(data_length + index_length) / 1024 / 1024, 1) "DB Size in MB"
 FROM information_schema.tables
 GROUP BY table_schema;
```
Facendo una query per vedere il peso delle tabelle, otteniamo questo risultato, dove possiamo vedere che il db flmdbedoc_files pesa circa 19GB.
```
+---------------------+---------------+
| DB Name             | DB Size in MB |
+---------------------+---------------+
| flmdbedoc_files     |       19830.5 |
| flmdbedoc_indici    |         100.6 |
| flmdbedoc_tabstampe |           1.0 |
| information_schema  |           0.0 |
| mysql               |           0.9 |
| performance_schema  |           0.0 |
+---------------------+---------------+
```

## Controllare una tabella
Per effettuare il controllo di una tabella ed aggiornare nel frattempo `table_schema` possiamo lanciare `analyze`.
```sql
ANALYZE TABLE <table>;
```

### Check della di tipologia di compessione dati.
Per controllare la tipologia di compressione dati di ogni singola tabella, devo accedere al database ove quella tabella è allocata.
All'interno di ogni database devo lanciare la query che restituisce il formato delle tabelle.
```sql
use {pre}odbedoc_files
use {pre}odbedoc_indici
use {pre}odbedoc_tabstampe
```
Ogni database, dedicato ad ogni hotel, ha il proprio prefisso.

La query da lanciare una volta eseguito l'accesso  è la seguente
```sql
SELECT table_name, row_format FROM information_schema.tables WHERE table_schema=DATABASE();
```
Otterremo un risultato simile al seguente:
```
+------------------+------------+
| TABLE_NAME       | ROW_FORMAT |
+------------------+------------+
| digitalizzazioni | Dynamic    |
| filesdati        | Dynamic    |
+------------------+------------+
```
 Controlliamo il peso delle tabelle di questo database


 ### Creo la nuova tabella
 A questo punto posso iniziare a creare una tabella che andrà a sostituire quella esistente, la chiamerò `nuova` e a questa tabella assegno i valori di compressione secondo il concetto 

 ```sql
 CREATE TABLE nuova LIKE filesdati; 
 ALTER TABLE nuova ROW_FORMAT=COMPRESSED KEY_BLOCK_SIZE=4; 
 ```
**Lanciando il secondo comando chiediamo la compressione 4x rispetto all'originale, senza creare una seconda tabella ove copiare i dati.**
Per comprendere come è strutturato i livello di compressione, basiamoci sullo scenario seguente.

#### SCENARIO
Abbiamo un Db Server con un Buffer Pool da 8Gb

Lanciamo la compressione con `KEY_BLOCK_SIZE=8`
8 e il 50.00% di 16(che è lo standatrd)
50.00% di 8G sono 4G
Otteniamo quindi un  innodb_buffer_pool_size da 12G (8G + 4G)

Lanciamo la compressione con `KEY_BLOCK_SIZE=4`
4 e il 20.00% di 16(che è lo standatrd)
25.00% di 8G sono 2G
Otteniamo quindi un  innodb_buffer_pool_size da 10G (8G + 2G)

Lanciamo la compressione con `KEY_BLOCK_SIZE=2`
2 e il 12.50% di 16(che è lo standatrd)
12.50% di 8G sono 1G
Otteniamo quindi un  innodb_buffer_pool_size da 9G (8G + 1G)

Lanciamo la compressione con `KEY_BLOCK_SIZE=1`
8 e il 06.25% di 16(che è lo standatrd)
06.25% di 8G sono 0.5G
Otteniamo quindi un  innodb_buffer_pool_size da 8.5G (8G + 0.5G)

MORAL OF THE STORY : The InnoDB Buffer Pool just needs additional breathing room when handling compressed data and index pages.

## Controlliamo la dimensione del Buffer Pool Size
InnoDB è senza dubbio l’engine MySQL più performante quando si tratta di gestire query di tipo SELECT. La sua configurazione prevede diversi parametri tra cui **innodb_buffer_pool_size**.
InnoDB Buffer Pool indica la dimensione di RAM da dedicare per la memorizzazione di indici, cache, strutture dati e tutto ciò che ruota attorno InnoDB.
È uno dei parametri più importanti della configurazione di MySQL ed il suo valore va impostato in funzione del quantitativo di memoria RAM disponibile e dei servizi che operano sul server.

## Trasferiamo i dati tra le tabelle

Spostiamo tutti i dati dalla tabella `filesdati` a `nuova` e rinominiamola.
Il comando di copia dei dati è piuttosto smeplice e prevede l'inserimento da una semplice select
```sql
INSERT INTO nuova SELECT * FROM filesdati;
```
Il processo impieghera il tempo  necessario in base alla dimensione del DB ed alla potenza della macchina che opera.
**Poichè ci possono essere problemi in fase di copia, può essere utile inserire un limite di query durante la copia della tabella:**
```sql 
INSERT INTO nuova SELECT * FROM filesdati limit 10000; // i primi 10000
INSERT INTO nuova SELECT * FROM filesdati limit 10000,10000; // poi altri 10000 partendo da 10000
INSERT INTO nuova SELECT * FROM filesdati limit 20000,10000; // poi altri 10000 partendo da 20000
```
con il valore di 10000 che incrementera di volta in volta in base alla quantità di query necessarie.

Prima di modificare le tabelle controlliamo che siano equivalenti almeno il numero delle righe
```sql
SELECT COUNT(*) FROM filesdati;
```

Rinominiamo le tabelle ed eventualmente droppiamo, cancelliamo, la tabella vecchia.
```sql
RENAME TABLE filesdati TO filesdati_old;
RENAME TABLE nuova TO filesdati;
DROP TABLE filesdati_old;
```




## Confronto i dati a fine lavoro

Questa è una tabella legata al peso della tabella piu.... importante **dbedoc_files.filesdati**

| Table                         | Size (Bytes) | Size (MB) |ROW_FORMAT  | K.B. SIZE |
|-------------------------------|--------------|-----------|----------  |           |
| ccodbedoc_file.filesdati_old  |   4149248000 |   3957.03 | DYNAMIC    |   8       |
| ccodbedoc_file.filesdati      |   1634238464 |   1558.53 | COMPRESSED |   4       |



