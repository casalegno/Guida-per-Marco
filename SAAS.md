# I computer e la rete aziendale SAAS

## Come sono definiti gli indirizzi IP delle varie reti

Esistono principalmente differenti schemi di rete.

La rete di Saronno, quindi della sede risponde alla rete **192.0.200.xxx**
Questa rete viene definita **trusted** è una rete vecchia, viaggia a 1Gb/s
All'interno di questa rete troviamo mediamente tutti i computer aziendali e gli altri dispositivi.

Sempre a Saronno è attiva una seconda rete, la **192.168.235.xxx** che viene definita **untrusted**
e questa è la rete pubblica dove possono accedere gli eventuali clienti e dalla quale vengono presi gli indirizzi delle macchine virtuali create localmente.

#### Aggiornamento del 10/06/2023
Sia dalla trusted che dalla untrasted si puo accedere all'altra e viceversa.

La rete con protocollo **192.168.10.xxx** dovrebbe nei prossimi mesi prendere il posto dell'attuale rete principale 0.200.
Al momento su questa rete è presente una macchina definita _documentale_ nella quale possiamo trovare tutte le varie documentazioni: 192.168.10.190


La rete di Milano dei NAS risponde a **192.168.100.xxx**
Questa rete viene definita **sass**. Qui troviamo tutte le macchine virtuali dei vari clienti che hanno il servizio in cloud.

L'ultima rete utilizzata in azienda ma ospitata da Irideos Farm è la numero  **192.168.250.xxx**  dove troviamo delle altre macchine virtuale di alcuni clienti che hanno il servizio in cloud, ma che adrà a breve sostituita.

### Risorse di varia importanza.

- 192.0.200.118 **LA MIA MACCHINA**
- Accedo alle macchine di Milano e di Irideo con i miei dati personali
- Stesso discorso per il SU c'è il gruppo sudo.
- 192.0.200.190 NAS dove vengono salvati tutti i backup dei computer della rete **trusted** 
- 190.0.200.80 Macchina dove vengono conservate le macchine virtuali da utilizzare per l'installazione: nella cartella   `\vm\Front\*.vmdk`  trovo l'immagine dei dischi.
- 192.168.10.190 Documentale, macchina con tutti i documenti dei vari reparti.
Ho accesso a Frontoffice e Doctur.
- 192.168.250.9 e la macchina che contiene i CRM Elite chiamata Webcheck-in l'url per raggiungere il crm helite è helite.syshotel-crm.it/q=ambiente/index.php
- 192.168.250.10 è la macchina che contiene i db per i CRM Elite.
- 192.168.100.222 è la macchina Delta con i CRM (con ssh non posso collegarmi a oracle9: ssh troppo vecchia) non in comunicazione con 168.100
- 192.168.100.211 e la macchina Delta DB
- 192.168.100.229 e la macchina dedicata a Due Torri.

- 192.0.200.140 Macchina di test con montato il CRM - Sto provando a fare gli aggiornamenti su questa macchina.
- 192.0.200.115 Macchina Master per l'rsync degli ambienti.
- aprendo il file \\192.168.10.190\doctur\Assistenza\Progetti\SituazioneServerSaasFront.xml trovo tutte le macchine in uso ai clienti
- 192.0.200.129 XSiges con installato e-booking3

- 192.168.30.1 - Macchina ufficiale con installato Git e tutti i repo
- 192.168.30.2 - Macchina ufficiale con installato Postgres 15
- 192.168.30.5 - Macchina ufficiale del **CRM 3.0**
- 192.168.30.6 - Macchina ufficiale con installato MongoDb6

## Server BI
Il servizio è gestito dai due dirimpettai, fanno analisi di tutti i dati che arrivano dai vari PMS e CRM.
Hanno un server dedicato 93.62.208.137 gestito direttamente da loro.
sysdatbi.sigesgroup.it:9503 la porta per accedere al loro serve.
Tendenzialmente non viene utilizzato ma il cliente puo richiedere di installare on primis un server per la gestione dei dati e viene fatto direttamente da Oracle.


## Server PMS
Per info su autorizzazioni lato pc chiedere a Bruno 666 o Daninele 328
E' installato il soft COBIAN per il Backup che viene eseguito autonomamente ogni dì.
Crontab.guru per impostare l'uso di cron.
```sh
Cat /etc/os-release
more
```
con `cut /etc/environement` vedo quale versione di Acucobol è in esecuzione e quali librerie richiede.





### Markdown
Css folder: `C:\Users\casalegno\AppData\Local\Programs\Microsoft VS Code\resources\app\extensions\markdown-language-features\media`


## MAC Address per indirizzo IP
**0.200**
F29383DDC1D8 - 192.0.200.98
080027D01D0B - 192.0.200.148
**168.235**
0800274C9030 - 192.168.235.120-155
08002781C8C0 - 192.168.235.39
080027EFAED1 - LocaleMarco - 192.168.235.11



