# 25-07-23 Modifiche alle query sul crm per tutti i clienti con attivo i Trigger Thankyou Letter

Lavoro tutto nel database postgres. B0C4MGliy3
Ho creato due script bash con l'aiuto di ChatGPT per la ricerca all'interno dei db sulle macchine 192.168.250.10 - 192.168.100.211 - 192.168.100.229 dei trigger attivi.

## Cerchiamo i trigger attivi
Il primo passaggio è stato quello di elencare i database presenti e salvarli su file txt.
```sh
psql -t -c "SELECT datname FROM pg_database WHERE datistemplate = false;" -h 0.0.0.0 -U postgres > elenco_database.txt
```
Quindi con una iterazione andiamo a cercare in tuti i database che hanno una relazione pubblica i trigger relativi alla ThankYou Letter

```sql
SELECT id, frequenza, descrizione FROM trigger WHERE descrizione LIKE '%you%';
UPDATE trigger SET frequenza=1 WHERE id=5 OR id=2;
SELECT id, frequenza,fk_comandisql_id, descrizione FROM trigger WHERE frequenza != 0 order by ID;
```
Con l'ultima query dell'elenco visualizzo anche il campo ID dei comandi SQL che deve essere pari a 116 equivalente al valore *Data*. Per i trigger attivi! In questo modo la query funziona correttamente.

## Evidenziamo la query da sostituire
Per tutti gli hotel con i trigger attivi, che sono stati raggruppati all'interno di un elenco csv, viene evidenziato, nel csv, se la query di invio è stata aggiornata.
All'interno dei vari db, per ogni hotel a cui accedo e controllo lo stato della query posso recuperare la query da sostituire e controllarla tramite
```sql
SELECT id,name,query FROM sm_list WHERE description LIKE '%you%';
```
modifico solo le queri NON SOL.

Il valore della query nuova da inserire è 
```sql
select distinct #wa_ordine_ext.mail# as email, wa_ordine_ext.data_ordine, wa_ordine_ext.periodo_dal, wa_ordine_ext.periodo_al,
 wa_ordine_ext.nome AS descrizione_cliente, split_part(#wa_ordine_ext.note#, ',', 4) as lingua_cliente, wa_ordine_ext.prenotante
from wa_ordine_ext where periodo_al = current_date and #wa_ordine_ext.mail# like '%@%' and split_part(#wa_ordine_ext.note#, ',', 4) <> 'I' 
and wa_ordine_ext.stato=8 and wa_ordine_ext.id_societa = 1
```
e lo faccio tramite un comando update ed il relativo campo id che ogni volta devo selezionare.
La query va adattata in base alla società ed alla nazione dei clienti. I parametri da modificare sono `wa_ordine_ext.id_societa = 1` e `split_part(#wa_ordine_ext.note#, '','', 4) <> ''I''`.
Quindi abbiamo la query per gli stranieri
```sql
update sm_list set query='select distinct #wa_ordine_ext.mail# as email, wa_ordine_ext.data_ordine, wa_ordine_ext.periodo_dal, wa_ordine_ext.periodo_al,
 wa_ordine_ext.nome AS descrizione_cliente, split_part(#wa_ordine_ext.note#, '','', 4) as lingua_cliente, wa_ordine_ext.prenotante
from wa_ordine_ext where periodo_al = current_date and #wa_ordine_ext.mail# like ''%@%'' and split_part(#wa_ordine_ext.note#, '','', 4) <> ''I''
and wa_ordine_ext.stato=8 and wa_ordine_ext.id_societa = 1' where id=;
```
e la query per i clienti Italiani
```sql
update sm_list set query='select distinct #wa_ordine_ext.mail# as email, wa_ordine_ext.data_ordine, wa_ordine_ext.periodo_dal, wa_ordine_ext.periodo_al,
 wa_ordine_ext.nome AS descrizione_cliente, split_part(#wa_ordine_ext.note#, '','', 4) as lingua_cliente, wa_ordine_ext.prenotante
from wa_ordine_ext where periodo_al = current_date and #wa_ordine_ext.mail# like ''%@%'' and split_part(#wa_ordine_ext.note#, '','', 4) = ''I''
and wa_ordine_ext.stato=8 and wa_ordine_ext.id_societa = 1' where id=;
```


### Controllo che le query siano inviate
Per controllare che le query siano inviate correttamente dobbiamo filtrare alcuni risultati e controllare dalla tabella delle partenze quando un cliente se ne va. 
Filtrando anche che sia stata inserita una mail e che nelle note sia presente la nazione di residenza.
```sql
SELECT "periodo_al", "nome",convert_from (decrypt_iv( decode(mail,'base64'),'12345678901234567890123456789012', 'passwordIV123456', 'aes-cbc'),'UTF8') AS email,convert_from (decrypt_iv( decode(note,'base64'), '12345678901234567890123456789012','passwordIV123456', 'aes-cbc'),'UTF8') AS note FROM "wa_ordine_ext" WHERE "periodo_al" = current_date AND "mail" != 'ekGTdXckCkTXyiqZ76T3ag==' ORDER BY "periodo_al" LIMIT 50
```

```sql
SELECT create_time, oid, * FROM public.sm_queue WHERE date(create_time) = current_date and (subject like '%you%' or subject like 'Grazie%') order by create_time desc;
```


Confrontanto questi risultati, con le effettive email inviate nella tabella sm_queue cercando la data in cui le email sono state create.

### Attenzione alla societa



## Modifichiamo la frequenza di invio delle lettere
Per modificare la frequenza di invio devo modificare la tabella 'trigger' impostando i valori in base a: 0 MAI - 1 GIORNALIERO - 5 OGNI MINUTO.
```sql
select id,descrizione,frequenza from trigger where descrizione like '%you%';

update trigger set frequenza = 1 where id =;

```
Per controllare se abbiamo fatto le cose in modo corretto, commiamo controllare

