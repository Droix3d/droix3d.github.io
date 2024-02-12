---
title: Enumeración inicial del dominio
author: Droix3d
date: 2024-02-09
categories: [Active Directory Attack]
tags: [AD, Active Directory]
pin: true
image:
  path: ../../assets/img/commons/AD_Attack/Active.png
  alt: Active Directory
---

a la hora de enumerar un entorno de empresa es importante saber que lo mas mínimo que se pueda  obtener  información es realmente muy útil y en cierto punto puede ser muy abrumador por la cantidad de datos que se manejan . hay unos puntos claves que tenemos que tomar en cuenta a la hora de enumerar

- AD users : se intenta enumerar cuentas de usuarios válidas 
- AD Joined Computers : Los host incluyen controladores de dominio, servicios de archivos, servidores de bases de datos, servidores we y de correo etc...
- Key Services: Kerberos, Netbios, LDAP , DNS
## Enumeración pasiva de usuarios 
Internet es demasiado grande para encontrar información por lo tanto podemos hacer mucho uso de las redes sociales, que no organización tiene un perfil en facebook, una página web o un perfil de likedin? para empezar podemos hacer buen uso primero de ello . Pero realmente como podemos encontrar la suficiente recolección de información de una empresa o de una gran lista de usuarios que trabajen en una misma area y organización. En mi post de recolección de información explico el uso de **shodan y google dorks**   son piezas fundamentales que todo buen auditor de seguridad  debe de conocer, para mas información y como hacer el uso de estas herramientas el siguiente enlace en mi blog hago una explicación y práctica para recolectar información de esta manera 

![](https://droix3d.github.io/posts/GoogleHacking_Shodan/)

- [GoogleHacking & Shodan](https://droix3d.github.io/posts/GoogleHacking_Shodan/) 

![](https://www.cybersecurityup.it/images/pillole/GoogleHacking.jpeg)

Herramientas como [linkedin2username](https://github.com/initstring/linkedin2username) sirven mucho a la hora de buscar usuarios en una organización en concreto a través de LinkedIn, LinkedIn es una plataforma muy común en donde grandes organizaciones y personas que trabajan en ellas les gusta dar a conocer el puesto , el area y los años que llevan dentro no solo ocurre en esta plataforma hay muchas mas en donde se puede aprovechar un buen OSINT en las redes sociales para sacar una gran lista de usuarios   

![](https://github.com/initstring/linkedin2username/blob/master/drawing.jpeg?raw=true)

## Identificar usuarios
habrá ocasiones que en un entorno real se pueda dar el caso de que nos puedan proporcionar un usuario válido dentro del AD para enumerar **(hay que recordar que aunque tengamos bajos privilegios podemos enumerar la mayoría de cosas del AD)** por lo tanto tenemos que encontrar la manera de establecer un punto de apoyo en el dominio obteniendo credenciales sin cifrar o un hash de contraseñas NTLM para un usuario, una shell en un host unido al dominio. ***Obtener un usuario válido con credenciales es fundamental en las primeras etapas de una prueba de penetración interna.***

podemos guiarnos de herramientas como **Kerbrute**, aprovecha el hecho de que los errores de autenticación previa de Kerberos a menudo no activan registros ni alertas. Esto la hace sigilosa, por lo tanto podemos usarla con un buen diccionario de usuarios para saber cual es válido y cual no 

```shell-session
    __             __               __     
   / /_____  _____/ /_  _______  __/ /____ 
  / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
 / ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                        

Version: dev (9cfb81e) - 02/17/22 - Ronnie Flathers @ropnop

This tool is designed to assist in quickly bruteforcing valid Active Directory accounts through Kerberos Pre-Authentication.
It is designed to be used on an internal Windows domain with access to one of the Domain Controllers.
Warning: failed Kerberos Pre-Auth counts as a failed login and WILL lock out accounts

Usage:
  kerbrute [command]
  
  <SNIP>
```

![](/assets/img/commons/AD_Attack/Pasted image 20231006102549.png)
## Enumeración Activa en un dominio 
sabemos bien que a la hora de tener una recolección de información activa normalmente se hace uso de herramientas para escanear puerto como **nmap** siendo la mas común, si tenemos ya una lista de hosts activas en la red podemos hacer una buena enumeración con dicha herramienta, 
Buscamos determinar qué servicios ejecuta cada host, identificar hosts críticos como `Domain Controllers`y `web servers`e identificar hosts potencialmente vulnerables para investigar más adelante.
después de hacer el análisis amplio lo recomendable es centrarnos en los protocolos estandar que suelen acompañar a los Servicios de AD como:

- DNS
- SMB
- LDAP
- KERBEROS
- RPC
- WINRM 
- RDP

```bash
nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 10.10.10.161 
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.94 ( https://nmap.org ) at 2023-10-06 10:23 CST
Initiating SYN Stealth Scan at 10:23
Scanning 10.10.10.161 [65535 ports]

Completed SYN Stealth Scan at 10:23, 31.00s elapsed (65535 total ports)
Nmap scan report for 10.10.10.161
Host is up, received user-set (0.15s latency).
Scanned at 2023-10-06 10:23:17 CST for 31s
Not shown: 65261 closed tcp ports (reset), 250 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE          REASON
53/tcp    open  domain           syn-ack ttl 127
88/tcp    open  kerberos-sec     syn-ack ttl 127
135/tcp   open  msrpc            syn-ack ttl 127
139/tcp   open  netbios-ssn      syn-ack ttl 127
389/tcp   open  ldap             syn-ack ttl 127
445/tcp   open  microsoft-ds     syn-ack ttl 127
464/tcp   open  kpasswd5         syn-ack ttl 127
593/tcp   open  http-rpc-epmap   syn-ack ttl 127
636/tcp   open  ldapssl          syn-ack ttl 127
3268/tcp  open  globalcatLDAP    syn-ack ttl 127
3269/tcp  open  globalcatLDAPssl syn-ack ttl 127
5985/tcp  open  wsman            syn-ack ttl 127
9389/tcp  open  adws             syn-ack ttl 127
47001/tcp open  winrm            syn-ack ttl 127
49664/tcp open  unknown          syn-ack ttl 127
49665/tcp open  unknown          syn-ack ttl 127
49666/tcp open  unknown          syn-ack ttl 127
49667/tcp open  unknown          syn-ack ttl 127
49671/tcp open  unknown          syn-ack ttl 127
49676/tcp open  unknown          syn-ack ttl 127
49677/tcp open  unknown          syn-ack ttl 127
49684/tcp open  unknown          syn-ack ttl 127
49706/tcp open  unknown          syn-ack ttl 127
49942/tcp open  unknown          syn-ack ttl 127

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 31.15 seconds
           Raw packets sent: 148541 (6.536MB) | Rcvd: 90851 (3.634MB)
```

observamos que los mas comunes que mencione anteriormente están habilitados pero esto porque se debe realmente? En un entorno de empresa es muy común tener varias areas y el intercambio de información es fundamental , ya sea desde una autenticación a un servicio usando Kerberos con su sistema de autenticación de tickets , hacer uso de un host remotamente o también compartir archivos por red com usando el protocolo SMB  

Es importante tomar nota de cada uno de ellos ya que por ejemplo un sistema no podría estar actualizado esto También significa que existe la posibilidad de que exploits más antiguos como EternalBlue, MS08-067 y otros funcionen y nos proporcionen un shell a nivel de SISTEMA.

```shell
nmap -sCV -p53,88,135,139,389,445,464,593,636,3268,3269,5985,9389,47001,49664,49665,49666,49667,49671,49676,49677,49684,49703,49956 10.10.10.161
Starting Nmap 7.94 ( https://nmap.org ) at 2023-10-06 10:29 CST
Nmap scan report for htb.local (10.10.10.161)
Host is up (0.38s latency).

PORT      STATE  SERVICE      VERSION
53/tcp    open   domain       Simple DNS Plus
88/tcp    open   kerberos-sec Microsoft Windows Kerberos (server time: 2023-10-06 16:36:00Z)
135/tcp   open   msrpc        Microsoft Windows RPC
139/tcp   open   netbios-ssn  Microsoft Windows netbios-ssn
389/tcp   open   ldap         Microsoft Windows Active Directory LDAP (Domain: htb.local, Site: Default-First-Site-Name)
445/tcp   open   0            Windows Server 2016 Standard 14393 microsoft-ds (workgroup: HTB)
464/tcp   open   kpasswd5?
593/tcp   open   ncacn_http   Microsoft Windows RPC over HTTP 1.0
636/tcp   open   tcpwrapped
3268/tcp  open   ldap         Microsoft Windows Active Directory LDAP (Domain: htb.local, Site: Default-First-Site-Name)
3269/tcp  open   tcpwrapped
5985/tcp  open   http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open   mc-nmf       .NET Message Framing
47001/tcp open   http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open   msrpc        Microsoft Windows RPC
49665/tcp open   msrpc        Microsoft Windows RPC
49666/tcp open   msrpc        Microsoft Windows RPC
49667/tcp open   msrpc        Microsoft Windows RPC
49671/tcp open   msrpc        Microsoft Windows RPC
49676/tcp open   msrpc        Microsoft Windows RPC
49677/tcp open   ncacn_http   Microsoft Windows RPC over HTTP 1.0
49684/tcp open   msrpc        Microsoft Windows RPC
49703/tcp closed unknown
49956/tcp closed unknown
Service Info: Host: FOREST; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: FOREST
|   NetBIOS computer name: FOREST\x00
|   Domain name: htb.local
|   Forest name: htb.local
|   FQDN: FOREST.htb.local
|_  System time: 2023-10-06T09:36:54-07:00
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2023-10-06T16:36:51
|_  start_date: 2023-10-06T16:01:16
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: required
|_clock-skew: mean: 2h26m50s, deviation: 4h02m32s, median: 6m48s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 71.74 seconds
```

Por eso es recomendable hacer un buen escaneo en cada uno de estos servicios apuntando a **versiones, puerto, servicios y las autenticaciones que se llevan en ella** en este ejemplo podemos observar el nombre del dominio con su respectivo FQDN 

FQDN (Fully Qualified Domain Name) se refiere a un nombre de dominio completamente calificado y único que identifica un recurso en la red de manera única. Un FQDN consta de dos partes principales:

1. **Nombre de Host:** Esta es la parte izquierda del FQDN y generalmente se refiere al nombre de una computadora, servidor u otro dispositivo en la red. Por ejemplo, "servidor" o "equipo1".
    
2. **Nombre de Dominio:** Esta es la parte derecha del FQDN y representa el dominio al que pertenece el recurso. En el contexto de Active Directory, el nombre de dominio es el nombre del dominio de AD al que está unida la computadora o el servidor. Por ejemplo, "miempresa.local"

```
Domain name: htb.local
Forest name: htb.local
FQDN: FOREST.htb.local
```
 
## Identificación de hosts 
al la hora de auditar un entorno real normalmente nos darán como target un servidor con dicha dirección IP pero normalmente dicho segmento de red hay conectados mas hosts que están conectados a un Domain controller , hay muchas maneras de enumerar todo un segmento de red para identificar mas hosts que están conectadas a ella 

Para este ejemplo haŕe una enumeración sobre mi pequeño laboratorio de Active Directory que hice, Mi máquina atacante Kali Linux corresponde a la IP - 192.168.94.4 (Todo lo tengo configurado por NAT creando una red virtual solo para mis máquinas windows , el windows server y mi máquina de Kali), las máquinas windows las tendré configuradas como Workstation,quedando de la siguiente forma. 

Kali Linux - 192.168.94.4

Windows server - 192.168.94.5

Máquina Windows1 (WS01)- 192.168.94.6

Máquina Widows2 (WS02)- 192.168.94.7


### Wireshark & TCPdump

podemos hacer un sniffing de tráfico rápido por la red para saber las conexiones entre los hosts y ahí sacar su respectiva dirección IP  , cuando se envían  paquetes broadcast sabemos que se envían a todo un grupo de hosts , se pueden notar solicitudes ARP y respuestas MDNS  y otros paquetes de capa dos (dado que es una red conmutada). En este pequeño ejercicio entraré al explorador de archivos e ingresaré a un recurso que no existe o no se puede resolver mediante los métodos habituales de resolución de nombres (DNS, por ejemplo) en un entorno de Windows o Active Directory, el sistema puede recurrir a protocolos alternativos para intentar resolver el nombre a una dirección IP. Uno de estos protocolos es el Protocolo de Resolución de Nombres de Enlace Local LLMNR (Link-Local Multicast Name Resolution), 
Podemos observar el tráfico que se genera cuando ingresamos a esos recursos 

![](/assets/img/commons/AD_Attack/Pasted image 20240212124313.png)

Tanto como Workstation-1 y Workstation-2 están tratandose de comunicar a un recurso inexistente  del controlador de dominio.
De igual forma con tcpdump podemos escuchar la traza de paquetes que pasan por la red y descubir los hosts que se encuentran

![](/assets/img/commons/AD_Attack/Pasted image 20240212125138.png)

![](/assets/img/commons/AD_Attack/Pasted image 20240212125527.png)

Hay distintas maneras de poder hacer un análisis de hosts en una red, Wireshark por excelencia y el mas usado , pero existen otras herramientas como Nmap , haciendo un barrido de hosts a través de ARP con **arp-scan** o envenenar el tráfico con **Responder** y ver los request entre los hosts y el dominio   

### Fping & Nmap
con fping podemos hacer un barrido por todo el segmento de igual forma  `a` para mostrar los objetivos que están activos, `s` para imprimir detalles, `g` para generar una lista de objetivos de la red indicando el CIDR, y `q` para no mostrar los resultados por host.

![](/assets/img/commons/AD_Attack/Pasted image 20240212125934.png)

Nmap por excelencia es la mas usada, en este caso de igual forma haré otro barrido de puros hosts con nmap

![](/assets/img/commons/AD_Attack/Pasted image 20240212131201.png)

En próximos artículos sobre active directory hablaré y enseñaré como explotar y comprometer al 100% un DC
