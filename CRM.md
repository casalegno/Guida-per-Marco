# CRM

## Ambiente e versione
Nella parte in basso a dx del crm appare l'ambiente in uso e la vesione in uso.
Se l'ambiente è *cliente* significa che la cartella che sta utilizzando è all'interno del crm sotto `protected/config/cliente`


## Helite
Sulla macchina Webcheckin sono presenti gli ambienti dedicati al documentale, che prevalentemente si appoggiano a mongodb
Il CRM dedicato ad Helite ha diversi ambienti al suo interno. Creare nuovi ambienti per i clienti non è un problema ed è [sufficiente segure la guida dedicata](./Wiki/01.Creazione%20ambiente%20CRM%20(multitenant%20e%20dedicato).md)

### Gli ambienti
Per accedere ad uno di questi ambienti è sufficiente puntare alla pagina
https://helite.syshotel-crm.it/q={ambiente}/index.php, sostituendo il valore di *{ambiente}* con il nome del cliente.
Per questi ambienti non è necessario creare o modificare nessun virtual host.
All'interno di questo ambiente gira un trigger che contrlla e registra tutti gli ambienti creati, quindi è importante non creare ambienti che non servono.

### I file 
- `_dirs.php` contiene due array con i nomi degli ambienti che non devono essere presi in considerazione in fase di migrate o in fase di trigger.
- `_triggers.php` è il file che gestisce i parametri del trigger




## Framework YII

### Postgres 15
Per potersi collegare ad un framwork con Postgres15 è necessario aggiornare il framework. Sulle macchine Sysdat è sufficiente una pull.




## Gli errori

 tail -f /var/log/httpd/helite_access_log | grep athena
  tail -f /var/log/httpd/helite_error_log | grep athena
