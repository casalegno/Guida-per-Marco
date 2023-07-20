# Questo articolo è stato spostato
Inizialmente era parte dell'articolo [NuovoClienteSaaS](NuovoClienteSAAS.md), che è stato riscritto per la wiki.
#### Creare un ambiente Helite: crm documentale per un cliente nuovo

Per creare un ambiete CRM helite che si occupa della parte documentale (vedi schema creato con Mauro) sono sufficienti alcuni passaggi:

- Mi collego alla macchina dedicata: 192.168.250.9
- Accedo alla cartella di helite: _/var/www/html/crm-helite_
- Accedo alla cartella _./files_ e creo una cartella con lo stesso nome dedicato all'hotel per l'upload dei files. Assegno alla cartella l'utente/gruppo _apache_
- Modifico all'interno della cartella _./protected/config/NOMEHOTEL/_ il file _mailer.php_ inserendo nelle variabili _'upload_dir'_ e _'path_upload_dir'_ l'indirizzo corretto per il caricamento dei files.
- Nella stessa cartella
- Accedo alla cartella _./tmp_
- Copio uno dei file gia esistenti *_inputInstall.nomecliente* e lo rinomino con il nome del nuovo ambiente. Lo devo fare **come utente root**
	
		cp _inputInstall.nomecliente _inputInstall.nuovoambiente  (come base Mauro consiglia ArtHotel)
	
- accedo al file e ne aggiorno i dati secondo lo schema seguente

		<?php return array (
		/*  --- parametri per ambiente --- */
		'db' => 'nuovoambiente', // ilnome dell'ambiente anche come db
		'profilo' => 'nuovoambiente', // il nome dell'ambiente
		'codi' => '111111', //il codice cliente preso dal pannello pianificazione ** vedi immagine
		'db_sol' => 'xxx',
		'id_hotel_sol' => '1',
		'nome_hotel' => 'Hotel Test Helite',  //il nome dell'hotel
		'sito_hotel' => 'https://hoteltesthelite.com', //i dati dell'hotel
		'email_hotel' => 'info@hoteltesthelite.com',
	
		/*  NON TOCCO NULLA  
		
		--- parametri standard --- */
		'host' => '192.168.250.10',
		'user' => 'postgres',
		'password' => 'B0C4MGliy3',
		'scenario' => 'M',
		'url_crm' => 'http://helite.syshotel-crm.it',
		); ?>

- ricopio nuovamente il file appena modificato con estensione _.php_

		cp _inputInstall.nuovoambiente _inputInstall.php

- mi posiziono nella cartella _/var/www/html/crm-helite/protected_
- lancio l'installazione di tutto l'ambiente
		
		sh install
		
- se l'ambiente è richiesto dal marketing devo impostare il params.php con l'utente Amministratore SU-FERRARI21 

- Una volta terminata l'installazione posso visionare il nuovo ambiente al link
https://helite.syshotel-crm.it/q=NUOVOAMBIENTE/index.php?r=site/login

- Mando una mail ad assistenza@syshotelonline.it ed in CC a Marco con il link per il nuovo ambiente.