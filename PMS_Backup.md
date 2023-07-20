## Eseguire il backup del server del cliente

Quando un cliente ha un server personale, bisogna mettere al sicuro i suoi dati.
Se la macchina è un server windows, dobbiamo per prima cosa installare **Cobian Reflector** ed andiamo ad impostare l'esecuzione del backup di tutta la macchina sul server NAS del cliente.

Quando imposto il backup dal cliente eseguo il bkp del disco .vmdk della macchina virtuale. Su Cobian nelle configurazione Generale del Task, devo assicurarmi che la voce _Usa copie shadow del volume_ sia flaggato.
La posizione standard del file della macchina virtuale su dispositivo windows è in _C://VM/*.vmdk_

Nel caso il cliente lo richieda, posso impostare il backup anche della parte utente del server facendo attenzione che non sia ridondante la copia della VM.
Per fare questo devo installare su tutti i pc client del cliente una versione di Cobian che backuppa direttamente sul NAS.

### Backup alternativi a Cobian	

- Tramite Cron per quel che riguarda i database con lo script Dumpdb.sh presente nella cartella /u/usr/genius7/batch/ 
- La procedura manuale viene eseguito dall'hotel in fase di chiusura della giornata lavorativa seguendo un Task prefissato di passaggi tra questo un backup interno di Genius7 (script pre-configurato). 