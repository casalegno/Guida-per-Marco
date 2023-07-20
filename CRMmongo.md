db.adminCommand( { setFeatureCompatibilityVersion: "4.0" } )


ATTENZIONE: DA DICHIARAZIONE DI MASSIMO
NON IMPORTA QUALE DATABASE VIENE INDICATO NEL FILE `protected/config/hotelmarco/mongodb.php` PERCHE IL CRM SCRIVE NEL DB `documentale`




CRM HOTEL MARCO
---------------------------
	[root@clientevm yum.repos.d]# cat /var/www/html/crm-hotelmarco/protected/config/hotelmarco/mongodb.php
	<?php
	return array(
						'class' => 'EMongoClient',
						'server' => 'mongodb://192.168.235.47:27017',
						'db' => 'cavour'
			);
	?>


CRM DEFAULT
---------------------------
	[root@clientevm yum.repos.d]# cat /var/www/html/crm/protected/config/default/mongodb.php
	<?php
	return array(
						'class' => 'EMongoClient',
						'server' => 'mongodb://192.168.235.47:27017',
						'db' => 'documentale'
			);
	?>


MONGODB.CONF
----------------------------
	[root@clientevm yum.repos.d]# cat /etc/mongod.conf
	# mongod.conf

	# for documentation of all options, see:
	#   http://docs.mongodb.org/manual/reference/configuration-options/

	# where to write logging data.
	systemLog:
	  destination: file
	  logAppend: true
	  path: /var/log/mongodb/mongod.log

	# Where and how to store data.
	storage:
	  dbPath: /var/lib/mongo
	  journal:
		enabled: true
	#  engine:
	#  mmapv1:
	#  wiredTiger:

	# how the process runs
	processManagement:
	  fork: true  # fork and run in background
	  pidFilePath: /var/run/mongodb/mongod.pid  # location of pidfile
	  timeZoneInfo: /usr/share/zoneinfo

	# network interfaces
	net:
	  port: 27017
	  bindIp: 127.0.0.1,192.168.235.47  # Enter 0.0.0.0,:: to bind to all IPv4 and IPv6 addresses or, alternatively, use the net.bindIpAll setting.


	#security:

	#operationProfiling:

	#replication:

	#sharding:

	## Enterprise-Only Options

	#auditLog:

	#snmp:



SYSTEMCTL MONGOD 
----------------------------

Nel caso ci siano problemi ad esegurire il demone di mongo possiamo 