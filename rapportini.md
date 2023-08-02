# Rapportini di giornata

<!-- TOC -->

- [COME COMPILARE I RAPPORTINI PER OGNI GIORNO](#come-compilare-i-rapportini-per-ogni-giorno)
    - [Inseriamo il rapportino giornaliero](#inseriamo-il-rapportino-giornaliero)
    - [Lavoro per il cliente non a pagamento](#lavoro-per-il-cliente-non-a-pagamento)
        - [- #### Spostamento ambiente da Delta a Helite](#---spostamento-ambiente-da-delta-a-helite)
        - [- #### Aggionrnamento CRM](#---aggionrnamento-crm)
        - [- #### SAQ PMS](#---saq-pms)
        - [- #### SAQ WEB](#---saq-web)
        - [- #### Corso Software - Costi diretti](#---corso-software---costi-diretti)
        - [- #### Test e Studio MySql](#---test-e-studio-mysql)
        - [- #### Documentazione personale](#---documentazione-personale)
        - [- #### Documentazione WIki TU](#---documentazione-wiki-tu)
        - [- #### Documentazione WIki crm](#---documentazione-wiki-crm)
        - [- #### Lavori e test su CRM](#---lavori-e-test-su-crm)
        - [- #### Documentazione Wiki CRM](#---documentazione-wiki-crm)
        - [- #### Corso MongoDB](#---corso-mongodb)
        - [- #### Ferie e Permessi](#---ferie-e-permessi)
        - [- #### MODIFICHE AL CRM PER SVILUPPO](#---modifiche-al-crm-per-sviluppo)
        - [- #### MODIFICHE AL CRM CORREZIONI ERRORI](#---modifiche-al-crm-correzioni-errori)
        - [- #### LAVORO PER IL SOL](#---lavoro-per-il-sol)
        - [- #### SESSIONE BLOCCATTA](#---sessione-bloccatta)
- [STAMPA RAPPORTINI](#stampa-rapportini)
    - [Prima Stampa: RA3COLASV](#prima-stampa-ra3colasv)
    - [Seconda stampa: RA2](#seconda-stampa-ra2)
    - [Terza Stampa: RA5](#terza-stampa-ra5)
    - [Quarta Stampa: RA7LAS solo se necessario](#quarta-stampa-ra7las-solo-se-necessario)
    - [Quinta Stampa: RA3COLAS](#quinta-stampa-ra3colas)
- [CRM INSERIMENTO RAPPORTINO E RICHIESTE](#crm-inserimento-rapportino-e-richieste)
    - [Inserimento rapportino per invio al cliente](#inserimento-rapportino-per-invio-al-cliente)
    - [Scaricare i rapportini non sempre funziona](#scaricare-i-rapportini-non-sempre-funziona)
    - [Inserimento richieste per ferie permessi](#inserimento-richieste-per-ferie-permessi)

<!-- /TOC -->

## COME COMPILARE I RAPPORTINI PER OGNI GIORNO
Per compilare i rapportini bisogna aprire tramite putty il relativo programma ed inserire quindi il codice operatore/matricola 0386.
La psw è iniziali nome cognome maiuscolo. Tutti i comandi vanno dati con **UPPERCASE**
Con un doppio _INVIO_ visualizzo il menu quindi seleziono _Menu Rapportini_
Quindi nel nuovo menu eseguo un triplo _INVIO_ per selezionare _Gestione Rapportini_ e _Gestione dati per rapportini_
Entro in **RA1** il menu per la gestione dei rapportini giornalieri.
Da qui posso cliccando ancora _INVIO_ andiamo a scegliere se inserire il rapportino giornaliero o modificare un rapportino in base alla data.

### Inseriamo il rapportino giornaliero
Nella schermata che si apre inseriamo nuovamente il codice matricola 0386 e la data del giorno da gestire quindi con _INVIO_ o con _F3_ confermiamo e passiamo alla nuova schermata.

Bisogna ricordarsi che con il comando _F6_ si esegue la ricerca

Compiliamo quindi il rapportino andando a riepire i campi richiesti:

| Numero | Titolo | Input |
|--------|--------|-------|
|01|Data| Data odierna|
|02|Reparto| TU Front Office|
|03|Cliente|31993|
||Protocollo| Il protocollo da utilizzare|
||Attivita| COR per i corsi ... altrimenti quello che serve|
||Prodotto|AAFRXX SW Front non Assegnato|
|04|Citta|sede|
|05|Motivo Interno|ZZ - Descrizione del lavoro svolto|
|06|Ore lavorate|Mattina/Pausa/Pomeriggio|
|07|Uso Ticket[^1]|S|
|08|Ore Concordate|Solo in caso di Viaggio|
|09|Auto Ditta|--|
|10|Andata/Ritorno|--|
|12|Spese dipendente|--|
|13|Spese Ant. Ditta|--|
|14|km percorsi|--|
|15|Da Stampare separato|No|

Alla fine della compilazione posso salvare con _F3_ oppure duplicare il rapportino con _F5_ nel qual caso viene richiesta la data con la quale duplicare.
Poi tramite la funzione di ricerca dei rapportini per data posso andare a modificarlo.

Una volta tornato al menu iniziale con _ESC_ posso chiudere tutto con _F4_

[^1]:Posso utilizzare il Ticket se ho fatto almeno 5 ore di lavoro consecutive con 1/2 di pausa-

### Lavoro per il cliente non a pagamento

In generale le ore riguardanti attività di ore di test su nuovi sistemi operativi - linguaggi etc vanno inserite così:
REPARTO: reparto specifico
COMMESSA: commessa generica di prodotto
ATTIVITA’: SVT
PRODOTTO: generico

- #### Spostamento ambiente da Delta a Helite
    02) CRM 
    03) 28001 ANL FAHELI
    05) SPOSTAMENTO DA DELTA A HELITE
    06) 1 ora come minimo
- #### Aggionrnamento CRM
    02) CRM 
    03) 28XXX Codice preso da pianificazione - ERR FAHELI 
    05) AGGIORNAMENTO CRM
    06) 1 ora come minimo
- #### SAQ PMS
    02) TU 
    03) 19326 SYS-HOTEL ANL AAFRHT
    05) RIUNIONE SAQ PMS
- #### SAQ WEB
    02) CRM 
    03) 28001 ANL FAHELI
    05) RIUNIONE SAQ WEB MENSILE
- #### Corso Software - Costi diretti
    02) TU 
    03) 31993 COR AAFRXX
    05) CORSO <DA SPECIFICARE>
- #### Test e Studio MySql
    02) TU 
    03) 19326 SVT AAFRXX
    05) TEST E LAVORO PRATICO SU MYSQL
- #### Documentazione personale
    In linea generale la mia documentazione personale va inserita come DOC con ore separate.
- #### Documentazione WIki TU
    02) TU 
    03) 19326 DOC AAFRXX
    05) DESCRIZIONE DOCUMENTAZIONE
- #### Documentazione WIki crm
    02) CRM
    03) 28001 DOC FAHELI
    05) DESCRIZIONE DOCUMENTAZIONE
- #### Lavori e test su CRM
    02) CRM 
    03) 28001 SVT FAHELI
    05) ESERCIZI PRATICI E TEST VARI
- #### Documentazione Wiki CRM
    02) CRM 
    03) 28001 DOC FAHELI
    05) Documentazione wiki e varie
- #### Corso MongoDB
    02) CRM 
    03) 31993 COR FAHELI
    05) DESCRIZIONE LEZIONI
- #### Ferie e Permessi
    Teoricamente se inseriti nel crm dovrebbe crearli lui, se non lo fa, li inserisco a mano
    02) TU
    03) 31993 PER XX
    04) FERIE
    05) FERIE
    06) 9-13 14-18
    07) NO
- #### MODIFICHE AL CRM PER SVILUPPO
    02) CRM 
    03) 28001 SVI FAHEMA - Marketing FAHEWC - Webcheckin
    05) DESCRIZIONE ATTIVITA
- #### MODIFICHE AL CRM CORREZIONI ERRORI 
    02) CRM 
    03) 28001 ERR FAHEMA - Marketing FAHEWC - Webcheckin
    05) DESCRIZIONE ATTIVITA
- #### LAVORO PER IL SOL
    02) SOL
    03) 18298 ANL DABO
    05) DESCRIZIONE ATTIVITÀ



- #### SESSIONE BLOCCATTA
    ACCEDRERE CON UTENTE DIS ED INSERIRE IL MIO CODICE
    SCEGLIERE TUTTE
    ANCORA IL MIO CODICE CON PASSWORD




## STAMPA RAPPORTINI

A fine mese bisogna eseguire la stampa dei rapportini

Posso richiamare i comandi rapidi con F2

### Prima Stampa: RA3COLASV 
Con queto comando stampo a video un riepilogo di tutti i rapportini inseriti nel mese, per controllare se i dati inseriti sono corretti. 
Sarebbe buona norma eseguire questo passaggio tutte le settimane per assicurarsi che i rapportini siano inseriti in modo logico.
Anteprima di Stampa [X] - Stampante: [04 Laser Back.]
Stampa su carta [no] - [F11]
Mia Matricola [0386] - Data di inizio mese[010123]
Stampa (S/N): [S]
Codice cliente:[INVIO] (INIZIO-FINE)
Conferma [SI]
**Il programma raggruppa per protocollo tutti i lavori fatti**


### Seconda stampa: RA2
Anteprima di Stampa [X] - Stampante: [04 Laser Back.]
Stampa su carta [X] - [F11]
Matricola: [0386] - Data Inizio: [010623](primo giorno del mese)
[F3-Conferma]
Controllo il rapportino con tutti i giorni ed eventuali avvisi
[F6-STAMPA] (DUE COPIE - DA FIRMARE)

### Terza Stampa: RA5
Anteprima di Stampa [X] - Stampante: [04 Laser Back.]
Stampa su carta [X] - [F11]
Matricola: [0386] - Data Inizio: [010623](primo giorno del mese)
[Invio-Invio]
Controllo il riepilogo di tutte le ore suddivise per protocollo. 
[F6-STAMPA] (DA FIRMARE)

### Quarta Stampa: RA7LAS (solo se necessario)
Generazione Nota Spese
Eventuali spese che ho sostenuto e sono da inserire nei rapportini.0105
Generazione Calcolo Straordinari
Ti calcola gli straordina in base alle ore inserite nei rapportini giornalieri

### Quinta Stampa: RA3COLAS
Ripeto tutte la procedura gia eseguita in RA3COLASV
**Il programma stampa solo quello che ritiene necessario sulla stampate alle mie spalle**
La stampa non è garantita (potrebbe non stampare nulla)
(DUE COPIE)
  
 
## CRM INSERIMENTO RAPPORTINO E RICHIESTE
Tutte le operazioni da eseguire sul crm vanno fatte su http://crm.sigesgroup.it/index.php

### Inserimento rapportino per invio al cliente
CRM->Attivita->Rapportino Cliente
Aggiungo + Crea Intervento
Nome del Cliente.
E Continua

Completo i _Click to edit_  

Luogo: SEDE
Oggetto: PREVE DESCRIZIONE (come nelle email)
Indirizzo Email - Vuoto
Gli altri campi secondo indicazione da Pianificazione.
Al momento ho abilitato solo TU come REPARTO. Se diverso modificare successivamente da Rapportini_PuTTY
Quindi ADD
Inserisco data e per le ore
Se per SOL 1 ora
Se per Marketing Automation 3 ore
Inserire la descrizione del lavoro svolto|
QUindi Genera e Salva (Pulsante BLU) 

### Scaricare i rapportini (non sempre funziona)
Si possono scaricare i rapportini manualmente, non sempre funziona.
Subito sotto la voce _cerca per data_

### Inserimento richieste per ferie permessi
CRM->Attivita->Mie Richieste
[+]-Crea Richiesta
- Ferie
- Permessi
- Data di inizio
- Data di fine
- Ore-> 9:00
- Ore-> 18:00
- Numero Ore-> Totale ore di ferie Es. 4 giorni sono 32 ore.

Fatta la richiesta arriva una mail di conferma con il riepilogo della richiesta. Deve successivamente arrivare una mail da Corti con la conferma della richiesta.
 
 