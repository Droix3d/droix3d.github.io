---
title: Estructura de Linux - Asignación de Permisos, Grupos y Búsquedas a nivel de sistema
author: Droix3d
date: 2023-08-31
categories: [Por donde empezar en Ciberseguridad, Linux]
tags: [Linux, Introduccion]
pin: false
image:
  path: ../../assets/img/commons/Linux/Linux2.jpg
  alt: 
---

## Kernel 
la función es virtualizar y controlar los recursos comunes del hardware de la computadora como puede ser el CPU, memoria asignada, los datos el que se accede y otro
## SHELL
(CLI) command Line interface es la que sirve para ejecutar comandos y estos sean interpretados por el  kernel 
## Binaries
Binarios Este término se refiere a archivos que se pueden ejecutar, de forma similar a los ejecutables en Ventanas. Los binarios generalmente residen en el directorio /usr/bin o usr/sbin e incluyen utilidades como ps, cat, ls y cd 
## Case sensitivity 
Distinción de mayúsculas y minúsculas A diferencia de Windows, Linux distingue entre mayúsculas y minúsculas. Esto significa que Desktop es diferente del escritorio, que es diferente de DeskTop. Cada uno de ellos representaría un archivo o nombre de directorio diferente. Mucha gente que viene de un entorno Windows puede encontrar esto frustrante. Si recibe el mensaje de error "archivo o directorio no encontrado"
## Directory 
Esto es lo mismo que una carpeta en Windows. Un directorio proporciona una forma de organizar archivos, generalmente de manera jerárquica.
## Home 
Cada usuario tiene su propio directorio /home, y aquí es donde generalmente se almacenan los archivos. crear se guardará de forma predeterminada.
## Shell 
Este es un entorno e intérprete para ejecutar comandos en Linux. El shell más utilizado es bash, pero otro popular Los shells incluyen el shell C y el shell Z.
## Terminal 
Esta es una interfaz de línea de comandos (CLI)
# Sistema de archivos de Linux
La estructura del sistema de archivos de Linux es algo diferente a la de Windows. linux no tiene una unidad física (como la unidad C:) en la base del sistema de archivos pero usa en su lugar, un sistema de archivos lógico. En la parte superior de la estructura del sistema de archivos está /, que es a menudo referido como la raíz del sistema de archivos, como si fuera un árbol al revés

## Estructura 
La raíz (/) del sistema de archivos está en la parte superior del árbol, y los siguientes son los más subdirectorios importantes para saber: 
### /root
El directorio de inicio del Administrador (usuario root)
### /etc
Generalmente contiene los archivos de configuración de Linux, archivos que controlan cuándo y cómo los programas se ponen en marcha 
### /home
El directorio de inicio del usuario
### /mnt
Donde otros sistemas de archivos están adjuntos o montados en el sistema de archivos 
### /media
Donde los CD y los dispositivos USB generalmente se conectan o montan en el sistema de archivos 
### /bin
Donde los archivos binarios de la aplicación (el equivalente de los ejecutables en Microsoft Windows) residir Contiene binarios de comandos esenciales.
### /bin
Donde encontrará bibliotecas (programas compartidos que son similares a las DLL de Windows)
### /lib
Dónde encontrará bibliotecas (programas compartidos que son similares a las DLL de Windows)
### /dev
Contiene archivos de dispositivos para facilitar el acceso a todos los dispositivos de hardware conectados al sistema.
## Permisos y derechos 

cuando listamos todo nuestro contenido que tenemos en un directorio podemos observar diversas líneas , con letras y carácateres, esto es la representación sobre nuestros permisos que tenemos en ese preciso momento   

| letra o símbolo | Descripción                                                     |
| --------------- | ------------------------------------------------------------------- |
| d               | Directorio                                                          |
| -               | Archivos Normales |
| c               | Archivo de caracteres especiales                                    |
| l               | Archivo de enlace o vinculo                                         |
| p               | Archivo especial pipe o de tuberia                                  |
| b               | Archivo de bloques especiales (Archivos especiales de dispositivos)                                                   |
| s                |  socket                                                                   |


![](/assets/img/commons/Linux/lin1.png)

1. el primer carácter corresponderá dependiendo al tipo de archivo o directorio basandonos en la tabla anteriormente expicada  

2. Los siguientes nueve carcateres son los permisos y para cada usuario corresponderá tres caracteres 

3. | Usuario | Grupo | Otros 

Estos caracateres significan lo siguiente 

| letra o símbolo | Descripción                                                     |
| --------------- | ------------------------------------------------------------------- |
| -               | sin permiso                                                        |
| r               | permisos de lectura |
| w              | permisos de escritura                                    |
| x               | permisos de ejecución                                         |

Ejemplo:

Observamos mis siguiente cuatro archivos

![](/assets/img/commons/Linux/lin2.png)

 - El primer cáracter nos dice que los 4 son directorios (d)
 - Los siguientes 3 **(rwx)** corresponden a mi usuario que los creo en este caso es **droix**
 - los otros 3, vemos que no tiene w por lo tanto solo será lectuara y ejecución para **para grupos que esten dentro de droix** 
 - Los ultimos 3 vemos que de igual forma esta compuesto como el anterior solo por lectura y ejecución **Otros**

| Usuario| Grupo | Otros |
| --------- | --------- | --------- |
| rwx   | r-x    | r-x  |

## Permisos en String o cadena
Cuando hacemos una asignación de permisos en sistemas UNIX hay distintas maneras de hacerlo, la primera es en string y la segunda en octal, mostraré primeramente como cambiarlo en cadena y después en octal 

Ejemplo:
Supongamos que tienes un archivo llamado "test.txt" y quieres establecer permisos para él.
En la notación de cadena, se utilizan las letras "r" (lectura), "w" (escritura) y "x" (ejecución) para representar los permisos. Los permisos se dividen en tres secciones para el propietario (usuario), el grupo y otros.

Por ejemplo, si deseas dar permisos de lectura y escritura al propietario, permisos de lectura al grupo y permisos de ejecución a otros, usarías la siguiente notación de cadena:

```bash
chmod u=rw,g=r,o=x test.txt
```
    u=rw: Establece permisos de lectura y escritura para el propietario.
    g=r: Establece permisos de lectura para el grupo.
    o=x: Establece permisos de ejecución para otros (usuarios que no son el propietario ni miembros del grupo).

## Permisos en Octal
En la notación octal, se utilizan dígitos para representar los permisos. Cada permiso se asigna un valor en función de los siguientes valores:

    Lectura (r): 4
    Escritura (w): 2
    Ejecución (x): 1

Para establecer permisos de lectura y escritura para el propietario, permisos de lectura para el grupo y permisos de ejecución para otros, calculamos el valor octal como sigue:

Propietario: lectura (4) + escritura (2) = 6
Grupo: lectura (4) = 4
Otros: ejecución (1) = 1

Por lo tanto, el valor octal sería 641.

Para establecer estos permisos en el archivo, usaríamos el siguiente comando:

```bash
chmod 641 test.txt
```

##Permisos especiales Linux
como observamos con anterioridad, Usualmente los permisos de los archivos en **[Unix](https://es.wikipedia.org/wiki/Unix)**/**[Linux](https://es.wikipedia.org/wiki/GNU/Linux)** se corresponden con un número en octal que varía entre **000** y **777**. Sin embargo, estos permisos especiales varían ese número octal entre **0000** y **7777**.

-   **Sticky bit:** 1000.
-   **SGID:** 2000.
-   **SUID:** 4000.

## Sticky Bit
Es comúnmente utilizado para /tmp 
Este permiso suele asignarse a directorios a los que todos los usuarios tienen acceso. Evita que un usuario pueda borrar o renombrar elementos pertenecientes a otro usuario, dentro de ese directorio, es decir, los elementos que hay en un directorio al que se asigna el permiso **[Sticky bit](https://es.wikipedia.org/wiki/Sticky_bit)**, sólo pueden ser renombrados o borrados por el propietario del recurso 

```bash
chmod 1775 test
O también
chmod +t /test #para activar sticky bit
chmod -t /test #para desactivar sticky bit
```

##SUID
El bit SUID  sobre un fichero significa que el que lo ejecute va a tener los mismos permisos que el que creo el archivo, Esto es util en algunas acciones aunque hay que utilizarlo con cuidado se asigna sumándole **4000** a la representación octal de los permisos de un archivo y otorgándole además, permiso de ejecución al propietario del mismo. Por ejemplo si un programa se le asigna permisos SUID con privilegios de administrador a otro usuario , el  programa correrá con permisos root 

>Estos programas constituyen un peligro potencial en un sistema.  se usa mucho a la hora de escalar privilegios en sistemas Linux para lanzar directamente una shell como aprovechandose de las fallas en configuraciones en archivos, programas o directorios  


```bash
$ sudo chmod u+s test.sh

O también:

$ sudo chmod 4755 test.sh
```

si en el binario no se le ha otorgado permisos de ejecución aparecerá un "S MAYÚSCULA" y si ya se otorgaron permisos de ejecución aparecerá un "s minúscula

## SGID
se le  asigna con **2000** a la representación octal de los permisos de un archivo, El  **SGID** es lo mismo que **SUID**, pero a nivel de grupo. Esto es, todo archivo que tenga activo el SGID, al ser ejecutado, tendrás los privilegios del grupo al que pertenece.

```bash
$ sudo chmod g+s test.sh

O también:

$ sudo chmod 2755 test.sh
```

## Linux UID and GID
Un UID es una abreviatura de la palabra User Identifier,  GID es una abreviatura de la palabra Group Identifier, se puede encontrar facilmente en /etc/passwd, que es el archivo que también almacena a todos los usuarios registrados en el sistema de UNIX facilmente se podrían identificar a la hora de ver un tipo de shell que lleguen a emplear

![](/assets/img/commons/Linux/etcpasswd.png)


Esta serie de datos se muestran en siete columnas, y estan  separados por dos puntos (:). Este archivo también contiene cuentas y grupos definidos por el sistema necesarios para la instalación, ejecución y actualización adecuadas del sistema. Las columnas estan representadas de la siguiente manera:

 1 – Nombre de aplicativo, usuario o servicio 
 2 – Contraseña: si algún  usuario ha establecido una contraseña en este campo, se indica con la letra (x).  
 3 – UID (ID de usuario)  
 4 – GID (ID de grupo)  
 5 – Gecos: contiene información general sobre algún usuario y este puede estar vacío.  
 6 – Directorio de inicio del usuario o donde esta alojado algún servicio   
 7 – Shell: la ruta de la shell y tipo de shell predeterminado para un usuario.

En el ejemplo de arriba  el primer usuario que aparece en el archivo es root. Root tiene el control general sobre todos los aspectos del sistema. Al usuario root se le asigna UID Zero (O) y GID (0). El otro que sigue son las cuentas y los grupos definidos por el sistema.

![](/assets/img/commons/Linux/lin3.png)

UID = 0 y GID = 0 son los que le dan al usuario root o sea todos los permisos  en el sistema.Por ejemplo podriamos crear un nuevo usuario asignandole un UID y GID con valor 0 y nos daremos cuenta de que ese usuario nuevo seguirá teniendo privilegios elevados a pesar de no tener el nombre de usuario root.

despues del usuario root vemos que hay  UID 1, 2, 3, 4,etc… . Es porque la mayoría de los sistemas Linux reservan los primeros 500 UID para los usuarios del sistema.
A los usuarios nuevos agregados con el comando useradd se les asigna el UID de 500. Normalmente un  usuario  creado durante el proceso de instalación,recibe el UID de 1000 y superior.

![](/assets/img/commons/Linux/lin4.png)

##  Daemons
Un [demonio](https://en.wikipedia.org/wiki/es:Demonio_(inform%C3%A1tica) "wikipedia:es:Demonio (informática)") es un programa que se ejecuta como un proceso en segundo plano (o de fondo, sin una terminal o interfaz de usuario), comúnmente esperando que ocurran eventos y ofreciendo servicios,un claro ejemplo puede ser un servidor web que espera un request para entregar en una página, o un servidor ssh esperando a que alguien intente iniciar sesión. estas son aplicaciones completas, hay demonios cuyo trabajo no es tan visible. Los demonios son para tareas como escribir mensajes en un archivo de registro como  `syslog`, `metalog`

El comando [systemctl](https://wiki.archlinux.org/title/Systemctl "Systemctl") es la interfaz de usuario utilizada para administrarlos. Lee los archivos `_nombre_.Service`

## $Variables en Bash
Las variables en Bash son elementos cruciales que almacenan información que los scripts pueden utilizar en el shell. Estas variables se dividen en dos categorías principales:

-   Variables globales
-   Variables locales

## Variables globales
Estas son variables que están disponibles para todos los procesos que se ejecutan desde el shell. Linux establece algunas variables de entorno globales en mayúsculas cuando inicias sesión en tu sistema. Un ejemplo común de una variable global es HOME, que almacena la ubicación del directorio de inicio de un usuario. Para ver el valor de una variable global, puedes utilizar el comando echo $NombreDeVariable. Por ejemplo:

```
~$ echo $HOME
/home/droix
```

Para modificar este tipo de variables debemos de identificar el tipo de shell que tenemos,  la shell por defecto que usamos es bash,pero en mi caso yo empleo una zsh entonces estaría en: `$HOME/.zshrc`

## Variables locales
A diferencia de las variables globales, las variables locales solo son visibles en el shell donde se definen. Puedes declarar una variable local en Shell escribiendo su nombre, seguido de un signo igual y el valor correspondiente, sin espacios:


```bash
~$ Name='Droix'
~$ echo $Name
~$ Droix
```
Para eliminar una variable global, puedes utilizar el comando (unset)

Para declarar variables globales, puedes escribir la variable seguida de su valor y luego usar el comando `export`. Esto garantiza que la variable persista en el sistema:

### Declarar variables  globales
```
~$ var='Global variable'
~$ echo $var
~$ export var
```
## Cambio de Propietario

**Chown (Change Owner):** El comando `chown` en Linux se utiliza para cambiar el propietario de archivos y directorios. Permite asignar un nuevo usuario o grupo propietario a un archivo o directorio específico. Esto es útil para gestionar la propiedad de archivos en un sistema y controlar quién tiene acceso a ellos.

```bash
chown <usuario> <archivo>
```

Por ejemplo, el siguiente comando cambia el propietario del archivo `script.sh` a `root`:

```bash
chown root /var/home/script.sh
```

Para cambiar recursivamente el propietario de todos los archivos y subdirectorios en una carpeta, puedes usar la opción `-R`:

```bash
chown -R root /var/home
```
Es importante tener en cuenta que cuando se utiliza `chown` de forma recursiva, los archivos y subdirectorios dentro de la carpeta seleccionada tendrán el nuevo propietario, pero el directorio principal conservará su propietario original. Si deseas recibir notificaciones sobre los cambios realizados, puedes agregar la opción `-c`.

**Assignment Groups in Linux:** En el contexto de Linux, la asignación de grupos se utiliza para cambiar el grupo al que pertenece un archivo o directorio. Puedes especificar tanto el nombre del grupo como su número de identificación de grupo (GID). La gestión de grupos es fundamental para controlar los permisos y la accesibilidad de archivos y directorios en un sistema Linux.

Algunos comandos relacionados con la gestión de usuarios y grupos en Linux son:

- `useradd`: Se emplea para añadir un nuevo usuario al sistema.
- `usermod`: Permite realizar modificaciones en un usuario existente.
- `userdel`: Se utiliza para eliminar un usuario del sistema.
- `groupadd`: Sirve para agregar un nuevo grupo.
- `groupmod`: Permite realizar modificaciones en un grupo existente.
- `groupdel`: Se emplea para eliminar un grupo.
- `/etc/passwd`: En este archivo, puedes encontrar una lista de todos los usuarios creados en el sistema, junto con sus identificadores de usuario (UID) y grupos principales (GID).
- `/etc/group`: En este archivo, se registran los GID de los grupos existentes en el sistema y los usuarios que pertenecen a cada grupo.
- `/etc/shadow`: Este archivo almacena las contraseñas de los usuarios cifradas.

**Opciones del comando `useradd`:**
- `-m`: Crea automáticamente el directorio personal del usuario.
- `-g`: Especifica el grupo principal al que se añade el usuario.
- `-G`: Define un grupo secundario al usuario.
- `-d`: Permite especificar el directorio personal del usuario, que se creará al mismo tiempo que el usuario.
- `-s`: Especifica el shell por defecto que utilizará el usuario.

Por ejemplo, para agregar un usuario llamado "pepe" con un shell Bash y crear un directorio personal en la carpeta "home", puedes ejecutar el siguiente comando:

```bash
useradd pepe -s /bin/bash -d /home/pepe
```

## Create groups in Linux
**Create Groups in Linux:** En el contexto de Linux, la creación de grupos se utiliza para establecer grupos de usuarios con objetivos y permisos específicos. Los grupos son una forma eficaz de organizar y administrar usuarios en un sistema Linux. Puedes crear grupos personalizados para asignar permisos y características específicas a múltiples usuarios.

Para crear grupos en Linux, puedes utilizar el comando `groupadd`. A continuación, se presentan ejemplos de cómo crear grupos en Linux y realizar otras operaciones relacionadas con grupos:

```bash
# Crear grupos en Linux
groupadd grupo1
groupadd grupo2
groupadd grupo3
```

Para ver la información sobre grupos existentes, puedes utilizar el comando `cat` para visualizar el contenido del archivo `/etc/group`. Esto te mostrará los identificadores de grupo (GID) de los nuevos grupos de usuarios creados:

```bash
cat /etc/group
```

También puedes verificar los usuarios que pertenecen a un grupo específico utilizando el comando `getent`. Por ejemplo, para ver los usuarios dentro del grupo "ssh," puedes ejecutar:

```bash
getent group ssh
```

### Cambiar el Grupo Primario de un Usuario en Linux
Puedes cambiar el grupo primario al que pertenece un usuario en Linux utilizando el comando `usermod`. La opción `-g` te permite especificar el nuevo grupo primario al que se añade el usuario:

```bash
sudo usermod -g grupo1 -a usuario1
```

Con este comando, asignarás el grupo "grupo1" como grupo primario para el usuario "usuario1."

### Añadir un Usuario a un Grupo Secundario en Linux
Si deseas agregar un usuario a un grupo secundario, puedes usar el comando `usermod` con la opción `-G`:

```bash
sudo usermod -G grupo2 -a usuario1
```

Este ejemplo agrega al usuario "usuario1" al grupo "grupo2" como grupo secundario.

### Eliminar Grupos con `groupdel`
Para eliminar un grupo en Linux, puedes utilizar el comando `groupdel`, seguido del nombre del grupo que deseas eliminar:

```bash
sudo groupdel nuevogrupo
```

### Quitar un Usuario de un Grupo con `deluser`
Si deseas retirar a un usuario de un grupo en Linux, puedes emplear el comando `deluser` de la siguiente manera:

```bash
deluser usuario grupo
```

Luego de ejecutar este comando, el usuario "usuario" ya no formará parte del grupo "grupo."


## Búsquedas a nivel de sistema 
en nuestro entorno de Linux podemos hacer diversas búsquedas en nuestro sistema para encontrar alguna información en concreto que estemos buscando  

```bash
# Buscar archivos por tipo (directorio, archivo, enlace simbólico, etc.) en un directorio específico:
find /ruta/del/directorio -type tipo

# Buscar archivos con un nombre específico en un directorio y subdirectorios:
find /ruta/del/directorio -name "nombre-del-archivo" -type f -print

# Buscar archivos por tamaño (menor o igual a ciertos bytes) en un directorio específico:
find /ruta/del/directorio -size -n[c]

# Buscar archivos por permisos específicos en un directorio y subdirectorios:
find /ruta/del/directorio -type f -perm permisos

# Buscar archivos modificados en los últimos n días en un directorio específico:
find /ruta/del/directorio -type f -mtime -n

# Buscar archivos accedidos en los últimos n días en un directorio específico:
find /ruta/del/directorio -type f -atime -n

# Buscar archivos creados en los últimos n días en un directorio específico:
find /ruta/del/directorio -type f -ctime -n

# Buscar archivos vacíos en un directorio específico:
find /ruta/del/directorio -type f -empty

# Buscar archivos por propietario y grupo en un directorio específico:
find /ruta/del/directorio -type f -user usuario -group grupo
```


