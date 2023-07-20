# Configuriamo una VM

Macchina di FrontOffice.
Versione di test con USSER e PWD
Su Gorilla trovi tutte le password


## CONFIGURAZIONE BASE

Per attivare una macchina virtuale, utilizzo come base **Linux RedHat64** e la seguente configurazione

- Sistema
	- Scheda madre
		- Memoria 2048MB
		- Solo Disco fisso
	- Processore
		- 2 Core
- Archiviazione
	- Rimuoverere controllo IDE
- Rete
	- Connessa a: Scheda con bridge
	- Avanzate Tipo di scheda:  Virtio-net
	
Quindi posso avviare.

Al ogni avvio della MV viene richiesta la password di decriptazione creata appositamente per la partizione
_/u/usr_ (dati e applicativi aziendali criptati): 
```sh
SYSDAT123
```
#### La chiave di decriptazione del cliente
Al cliente viene impostata una chiave di decriptazione personalizzata
Il metodo per creare la chiave da utilizzare per il cliente come secondo accesso al lock della partizione `/u` e il seguente: PIVA+CODICE CODI
