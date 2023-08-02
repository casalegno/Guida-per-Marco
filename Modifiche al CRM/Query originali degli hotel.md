# Dimora
select distinct wa_soggiorni.periodo_dal, wa_soggiorni.periodo_al,wa_soggiorni.id_soggetto,wa_soggiorni.stato_cliente,
persone.descrizione_cliente, #persone.email# as email, nazioni.cod_nazione,nazioni.lingua, lingue.codice from wa_soggiorni left join persone on persone.id = wa_soggiorni.id_soggetto left join nazioni on nazioni.id = persone.cod_nazione left join lingue on lingue.id = nazioni.lingua  where wa_soggiorni.periodo_al = current_date - integer '2' and wa_soggiorni.stato_cliente = '5' and #persone.email# like '%@%' and lingue.codice = 'I'
-----
# Demilan Straf
select distinct #wa_ordine.mail# as email, wa_ordine.data_ordine, wa_ordine.periodo_dal, wa_ordine.periodo_al, wa_ordine.nome AS descrizione_cliente, split_part(#wa_ordine.note#, ',', 4) as lingua_cliente from wa_ordine where periodo_al = current_date - integer '2' and #wa_ordine.mail# like '%@%' and split_part(#wa_ordine.note#, ',', 4)  <> 'I' and wa_ordine.stato = 8 and wa_ordine.id_societa=2
---
# Byron
select distinct #wa_ordine_ext.mail# as email, wa_ordine_ext.data_ordine, wa_ordine_ext.periodo_dal, wa_ordine_ext.periodo_al, wa_ordine_ext.nome AS descrizione_cliente, split_parT (#wa_ordine_ext.note#, ',', 4) as lingua_cliente, wa_ordine_ext.prenotante from wa_ordine_ext where wa_ordine_ext.periodo_al = current_date and #wa_ordine_ext.mail# like '%@%' and split_part(#wa_ordine_ext.note#, ',', 4) = 'I' and wa_ordine_ext.eliminato=0 and wa_ordine_ext.id_societa = 1
---
# ArtHotel
select distinct #wa_ordine.mail# as email, wa_ordine.data_ordine, wa_ordine.periodo_dal, wa_ordine.periodo_al, wa_ordine.nome AS descrizione_cliente,  wa_ordine.riferimento as riferimento, split_part(#wa_ordine.note#, ',', 4) as lingua_cliente from wa_ordine where periodo_al = current_date - integer '2' and #wa_ordine.mail# like '%@%' and split_part(#wa_ordine.note#, ',', 4)  <> 'I' and wa_ordine.stato = 8 AND #wa_ordine.mail# not like '%m.expediapartnercentral.com%'
---
# LEONDORO
select distinct #wa_ordine_ext.mail# as email, wa_ordine_ext.data_ordine, wa_ordine_ext.Periodo_dal, wa_ordine_ext.periodo_al, wa_ordine_ext.nome AS descrizione_cliente, split_part(#wa_ordine_ext.note#, ',', 4) as lingua_cliente, wa_ordine_ext.prenotante from wa_ordine_ext where periodo_al = current_date and #wa_ordine_ext.mail# like '%@%' and split_part(#wa_ordine_ext.note#, ',', 4) <> 'I' and wa_ordine_ext.eliminato=0 and wa_ordine_ext.id_societa = 1
---
# VIEST - VILLAPOGGIALE
SELECT DISTINCT #wa_ordine.mail# AS email, wa_ordine.periodo_dal, wa_ordine.periodo_al, wa_ordine.nome as descrizione_cliente, split_part(#wa_ordine.note#, ',', 4) as lingua_cliente, wa_ordine.riferimento FROM wa_ordine WHERE periodo_al = CURRENT_DATE - INTEGER '2' AND #wa_ordine.mail# LIKE '%@%' AND ( wa_ordine.stato = 1 or wa_ordine.stato = 6 or wa_ordine.stato = 8) AND split_part(#wa_ordine.note#, ',', 4)  = 'I' AND group_master = 0 order by 1
---WSW
# PIROVANO 
select distinct utente.utente as email, wa_ordine.data_ordine, wa_ordine.periodo_dal, wa_ordine.periodo_al, split_part(#wa_ordine.note#, ',', 1) AS descrizione_cliente, split_part(#wa_ordine.note#, ',', 3) AS lingua_cliente from wa_ordine left join wa_utente as utente on utente.id = wa_ordine.id_utente where periodo_al = current_date - integer '2' and utente.utente like '%@%' and utente.deleted=0 and wa_ordine.eliminato=0 and split_part(#wa_ordine.note#, ',', 3) <> 'I' and wa_ordine.id_societa = 1
----
# DALPOZZO
select distinct #wa_ordine.mail# as email, wa_ordine.data_ordine, wa_ordine.periodo_dal, wa_ordine.periodo_al, wa_ordine.nome AS descrizione_cliente, wa_ordine.riferimento as riferimento, split_part(#wa_ordine.note#, ',', 4) as lingua_cliente from wa_ordine where periodo_al = current_date - integer '2' and #wa_ordine.mail# like '%@%' and split_part(#wa_ordine.note#, ',', 4)  <> 'I' and wa_ordine.stato = 8