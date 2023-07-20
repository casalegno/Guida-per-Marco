# Mysql su Debian/Ubuntu

La configurazione del sistema basato su Debian Ubuntu è differente rispetto a quello di Oracle.

E plausibile che il servizio mysql.service non sia presente, non esiste `mysqld`.
Quindi dobbiamo fermare il singolo servizio mysql
```sh
sudo service mysql stop
```
modificare il file di configurazione presente nella cartella *mysql*
```sh
vi /etc/mysql/conf.d/mysql.cnf
### potrebbe anche essere in un'altra posizione
vi /usr/locale/etc/my.cnf #macos
```
quindi modifichiamo la password dell'utente
```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'SYSDAT';
FLUSH PRIVILEGES;
exit;
```
Nel caso la password non venga accettata per le policy, controlliamo i valori e nel caso modifichiamo o i valori o la password;
```sql
SHOW VARIABLES LIKE 'validate_password.%';
SET GLOBAL validate_password.policy = 0;
SET GLOBAL validate_password.length = 6;
```