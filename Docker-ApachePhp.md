# Apache e PHP
Inizio i testi di configurazione di Apache e PHP con un container.
Poichè da indicazioni il CRM per funzionare necessita di diversi moduli PHP conviene creare una immagine dedicata da utilizzare per lanciare i container.

La soluzione per creare una immagine adhoc è quella di utilizzare un file di configurazione.
Utilizzando podman sappiamo che il file puo chiamarsi sia *Containerfile* sia *Dockerfile*.
La [guida ufficiale di podman](https://docs.podman.io/en/latest/markdown/podman-build.1.html) è esaustiva ma online si trovano tante guide utili
<!-- TOC -->

- [I moduli di PHP](#i-moduli-di-php)
- [Creiamo il Dockerfile](#creiamo-il-dockerfile)
- [Creiamo immagine](#creiamo-immagine)

<!-- /TOC -->

## I moduli di PHP
Il CRM per funzionare ha bisogno di alcuni modulidi PHP. Quelli installati di default nell'immagine php:7.4-apache sono i seguenti
```sh
root@ac83c09fe5c6:/etc# php -m
[PHP Modules]
Core
ctype
curl
date
dom
fileinfo
filter
ftp
hash
iconv
json
libxml
mbstring
mysqlnd
openssl
pcre
PDO
pdo_sqlite
Phar
posix
readline
Reflection
session
SimpleXML
sodium
SPL
sqlite3
standard
tokenizer
xml
xmlreader
xmlwriter
zlib

[Zend Modules]
```
Per ottenere la configurazione di PHPinfo() è sufficiente lanciare il comando all'interno del container salvando tutto su un file locale.
```sh
podman exec -it hotel php -i > php.info
```
Da questo file otteniamo le informazioni per il file di configurazione di php.ini
> Configuration File (php.ini) Path => /usr/local/etc/php
> Loaded Configuration File => (none)
> Scan this dir for additional .ini files => /usr/local/etc/php/conf.d
> Additional .ini files parsed => /usr/local/etc/php/conf.d/docker-php-ext-sodium.ini

Sappiamo qu

## Creiamo il Dockerfile

Questa è la prima stesura del dockerfile
```yml
FROM php:8-apache
#RUN mkdir -p /etc/apache2/ssl
#
#copio il calore di phpini sviluppaa localmente in quello che viene letto di     default
RUN mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"
#la copia del certificato https
#COPY ./ssl/*.pem /etc/apache2/ssl/
#copio la configurazione di apache
COPY ./apache/000-default.conf /etc/apache2/sites-available/000-default.conf
#eseguo l'installazione dei vari moduli necessari di php7.4
RUN apt update && \
# espongo le porte 80 http e 443 https
EXPOSE 80
#EXPOSE 443
```

## Creiamo immagine
Per creare l'immagine viene usata l'opzione *build* con diversi parametri
```sh
podman image build -t php74A .
```

# Creiamo il container
Come primo passaggio creo il container per vedere se funziona di base e se riesco ad accedervi.
```sh
podman create-p 8888:80 --name hotel -v /var/www/html/hotel:/var/www/html php:7.4-apache
```
