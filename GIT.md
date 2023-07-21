# GIT comandi per aggiornare la macchina

Su gitbooks trovi una guida completa ed in Italiano su come funziona Git.
https://yunwuxin1.gitbooks.io/git/content/it/
https://github.com/progit/progit/tree/61833a527c6ac0c44cc40793c034b81599c25efc/it
Su github trovi i comandi principali da eseguire con Git
https://gist.github.com/tesseslol/da62aabec74c4fed889ea39c95efc6cc

#### Procedimenti preliminari
Come prassi eseguire il backup delle cartelle da aggiornare prima di fare la git e il backup del database relativo al crm.
Se installo per la prima volta git e devo usarlo come programma di versionamento devo subito configurare i miei dati personali
```sh
git config --global user.name "nome cognome"
git config --global user.email "email@host.it"
```

## Su che macchina sto lavorando
Per sapere su quale macchina sto lavorando con Git posso venirne a conoscenza richiedendo le informazioni di configurazione.
Dove vedo anche se mi sono configurato l'account
```sh
git config --list
git config --global --list
```
il comando restituisce tutte le informazioni relative al branch ed all'indirizzo IP della macchina in uso.
[Vedi il video di configurazione](https://www.youtube.com/watch?v=7I_8k4E19nE&list=PL6PilnEO6HWbhuS25PRBz9n9ESaMmlqWL&index=4)

## Cambio di branch
Se capita di cambiare il branch mi devo posizionare all'interno della directory e devo eseguire un pull per visionare il nuovo branch.

Quindi con il comando <code>git checkout [nuovobranch]</code> passo al nuovo branch e viene aggiornata la cartella dei file.
Quindi eseguo il migrate esportando prima la variabile di ambiente e successivamente eseguendo lo _YesItIsCommand_

## La staging Area
La staging area è un contenitore di file (o un area) in cui i file ai quali abbiamo apportato delle modifiche sono stati posizionati perchè sono pronti per essere committati (quindi freezati ed inviati al repository)

## Annullare le modifiche nella directory di lavoro
È possibile usare il comando git checkout per annullare le modifiche apportate a un file nella directory di lavoro. In questo modo il file tornerà alla versione presente rispetto al Brach in sui ci si trova:
```sh
git checkout {nomefile} ##riporto alla versione originale solo questo file
git checkout . ##includo tutti i file e li ripristino
git restore {nomefile} ##riporto alla versione originale solo questo file
git restore . ##includo tutti i file e li ripristino
```
### Se non voglio annullare le modifiche?
Nel momento in cui si ha modificato dei file in locale e quindi differenziano da quelli presenti nel repository, se si prova ad eseguire una pull si ottiene un errore. 
Infatti durante l'ultima `git pull` eseguita ho regitrato questo errore:

>error: Your local changes to the following files would be overwritten by merge:
>protected/behaviors/PrenotazioniPMSBehavior.php
>protected/modules/vendite/models/WaOrdineExt.php
>Please, commit your changes or stash them before you can merge.
>Aborting

la soluzione è stata disabilitare questi due file dalla pull. Li abbiamo quindi sospesi con il comando
```sh
git checkout -- protected/behaviors/PrenotazioniPMSBehavior.php
git checkout -- protected/modules/vendite/models/WaOrdineExt.php
```
per poi eseguire nuovamente la pull.
**Questo perchè bisognava mantenere attive le modifiche fatte manualmente in locale su questi file.**

### E se il file è per caso nella staging area?
Se il file che vogliamo ripristinare si trova [nella staging area](#la-staging-area) possiamo ripristinarlo con il comando `restore` e l'opzione apposita
```sh
git restore --staged {nomefile}
```
in questo modo il file fiene tolto dalla staging area ma non ripristinato, dovro poi lanciare nuovamente il compando per ripristinarlo all'originale.
Una seconda strada per rimuovere un file dalla staging area è
```sh
git reset HEAD {nomefile} ## fa lo stesso lavoro di git restore --staged
```

## Aggiorniamo Genius7
L'aggiornamento del PMS va effettuato dalla cartella dell'ambiente e si esegue con Git sequendo i passai indicati qui sotto.
_Questi passaggi valgono sia per qualsiasi applicativo gestito con git._

1. ottengo la versione attuale dell'applicazione installata
	```sh
	git status
	```
gi
2. Controllo e nel caso ripristino i file alla versione attuale e poter eseguire la pull successivamente

3. Scarico ed aggiorno i file in due modi differenti:
	- scarica ed aggiorna i file all'ultima versione disponibile e funzionante
		```sh
		git fetch
		```

	- si occupa di scompattare e di installare i file scaricati con fetch
		```sh
		git merge
		```

	- con un unico comando eseguo sia il fetch che il merge. E plausibile che non aggiorni lo status, quindi successivamente devo eseguire un `git checkout versioneaggiornata`
		```sh
		git pull
		```
4. come ultimo passaggio eseguo un 
	```sh
	git log
	```
	cosi faccio un check del lavoro svolto.
	Per visualizzare i log con i vari tag esegue
	```sh
	git log --tags --decorate=full
	```
	Nelle prime riga del file visualizzato trovo (evidenziati in grassetto)
	la versione attuale del CRM
	l'autore e la data di aggiornamento
	> commit 795e7840627e861e8dc5666afb9483135cc394e7 (tag: **refs/tags/2.17.55**, refs/remotes/origin/crm2.17, refs/heads/cr
	> Author: **porta** <porta@PCPorta.GIS>
	> Date:   **Thu May 25** 08:52:22 2023 +0200

## Aggiorniamo il CRM
Per aggiornare il crm che ad oggi sulla master è di 2.14 all'ultima attiva bisogna farlo dalla cartella del crm _/var/www/html/crm/_  
Come primo passaggio devo eseguire un bkp della cartella quindi la eseguo ocn il comando

	cp -Rdpv crm/ crm_bkp

Prima di fare questo posso eseguire il backup del db del crm tramite Postgres.
Per fare il backup del DB con Postgress vedi file BackupBD

Ci sono due strade: **Delploy e Git**.

**La git la uso se faccio l'aggiornamento su ambiente cloud, quindi SASS o su interfaccia multiutente MULTI TENANT, come per il server Elite**

Se invece faccio l'aggiornamento sulla macchina virtuale On Premis (quindi sulla macchina del cliente) lo eseguo con il deploy (in realtà posso farlo con git, è smeplicemente piu lungo come processo ma decisamente piu sicuro).
Accedo alla cartella del CRM, quindi controllo lo stato.
Eseguo il pull della nuova versione ed una volta terminato eseguo il migrate

### Migrate: aggiorniamo il database del CRM
1. Per poter eseguire il migrate devo capire quale ambiente migrare: nella cartella `/var/www/html/crm/protected/config/`
2. trovo tutti gli ambienti esistenti: principalmente sono quelli degli sviluppatori (identificati come cartelle) quella dedicata al cliente (con il suo nuome) è quella da aggiornare.
	```sh
	export SERVER=cliente
	```
3. creo la variabile di ambiente SERVER quindi eseguo il programma di migrazione
	```sh
	./yiic migrate
	```

4. Una volta eseguito il check do conferma e porto a termine il processo.
	```sh
	git log --tags --decorate=full
	```

## Sul server di Elite
Nella macchina 192.168.250.9 dove trovo i crm di helite ho diversi crm tra questi, quelli con il numero sono quelli di backup. Le cartelle 

Per eseguire l'aggiornamento quando mi viene richiesto devo controllare lo spazio disponibile, eseguire il backup del crm attuale e cancellare il backup piu vecchio presente

``` sh	
rm -rf crm-helite_20230318
cp -Rdpv crm-helite crm-helite_20230525
```
Quindi, dalla cartella crm-helite seguendo i passaggi di Git eseguo l'aggiornamento.
Per quel che riguarda la migrate dentro questo ambiente e stato creato lo scritp cmd_global che si occupa di creare autonomamente le varibili di ambiente di ogni cliente ed eseguire la migrate.

	./cmd_global migrate

se non sono presenti delle migrate da eseguire restituisce per ogni ambiente il valore 

	---------- Ambiente statuto ----------
	Yii Migration Tool v1.0 (based on Yii v1.1.23-dev)
	No new migration found. Your system is up-to-date.



## Cosè la commit
E il salvataggio dei cambiamenti nel server di repository.

## Le aree di git
La working directory (il codice sorgente)
La stagin area (i file in attesa di essere caricati nella commit successiva)
La Git Directory (qui i dati sono salvati nel db di versionamento con le loro modifiche)

## I branch e gli aggiornamenti
Spiegazione chiara di Massimo. 
Dobbiamo vedere i branch come delle cartelle, tutte presenti nello stesso repository.
Quando scarichiamo un repository, noi scarichiamo tutti i branch esistenti, ma lavoriamo esclusivamente in uno di questi.
Nel nostro esempio quando scarichiamo il CRM scarichiamo i 3 branch principali: crm2.17, crm3.00 e HEAD. Di default lavoriamo nel branch crm2.17 e se eseguiamo degli aggiornamenti li eseguiamo all'interno di quel brach.
Se ci spostiamo in un'altro branch, dobbiamo vederlo semplicemente come un cambio di cartella di lavoro, che sarà aggiornato all'ultima volta che abbiamo richiesto una pull. All'interno di quel branch, se da allora non sono state fatte modifiche e committate nel repository, non vi sara nulla da aggiornare ed una pull non restituirà nessun risultato.