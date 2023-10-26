---
title: Introducción a Linux
author: Droix3d
date: 2023-08-31
categories: [Por donde empezar en Ciberseguridad, Linux]
tags: [Linux, Introduccion]
pin: false
image:
  path: ../../assets/img/commons/Linux/Linux.png
  alt: TUX
---

Antes de empezar con un poco de comandos y práctica, estudiaremos un poco de historia sobre como  surge este sistema operativo, por que es la mejor opción  para tareas de hacking - pentest. 
## Historia 
La historia de Linux comienza a principios de la década de 1990 con un estudiante finlandés llamado Linus Torvalds. En 1991, mientras estudiaba ciencias de la computación en la Universidad de Helsinki, Torvalds comenzó a trabajar en un proyecto personal. Se propuso crear un sistema operativo compatible con UNIX para su computadora personal. Inicialmente, Torvalds desarrolló un núcleo de sistema operativo básico, conocido como el kernel de Linux. Este kernel actúa como intermediario entre el software y el hardware de una computadora, permitiendo que los programas se ejecuten y se comuniquen con los componentes del sistema. A medida que trabajaba en el kernel, Torvalds decidió compartir su proyecto con la comunidad en línea. A medida que más personas se unieron al proyecto, comenzaron a contribuir con mejoras, correcciones de errores y nuevas características al kernel de Linux. La colaboración en el desarrollo del kernel de Linux fue crucial para su éxito. La licencia de software libre de Linux, conocida como la Licencia Pública General de GNU (GPL), permitía a cualquier persona utilizar, modificar y distribuir el sistema operativo de forma gratuita. Hoy en día, Linux es utilizado en una amplia variedad de sistemas, desde servidores de Internet y supercomputadoras hasta dispositivos móviles y electrodomésticos inteligentes.

Con todo esto mencionado se fueron creando varias distribuciones cada una de ellas cumpliendo un rol en especifico Linux está disponible en más de 600 distribuciones (o un sistema operativo basado en el kernel de Linux y software y bibliotecas compatibles) , Los mas populares y usados son los siguiente 
## Distribuciones de Linux
- Ubuntu 
- Debian
- Fedora
- OpenSUSE
- Manjaro
- Arch
- Gentoo
- RedHat
- Mint
- Elementary 
## Kali o Parrot para ciberseguridad?
En lo personal me gusta más Kali Linux ya que ha sido un sistema mas estable para mi a comparación de Parrot o cualquier otra, pero comente en mi post de introducción al hacking la verdad es que puedes ocupar lo que gustes incluso podrías hacer pentesting desde windows sin problemas solo hay que instalar las herramientas que ocuparemos, es bueno probar de todo y lo importante es trabajar en un entorno cómodo, ahora si que para `"Para gustos, los colores"` 

![](https://camtechblogblog.files.wordpress.com/2017/11/kbp.png)

## Por qué máquinas virtuales? 
Como expertos en auditores de seguridad es muy importante trabajar sobre entornos virtualizados...pero por que realmente?

- **Aislamiento y Seguridad:** Los entornos virtualizados crean un espacio aislado del sistema operativo principal. Esto es crucial para evitar daños accidentales o propagación de malware durante las pruebas de penetración. Si algo sale mal en el entorno virtualizado, no afectará tu sistema principal ni otros recursos.
    
- **Restauración Rápida:** Si una prueba de penetración causa un fallo en el sistema o corrompe los archivos, puedes restaurar fácilmente una copia anterior del entorno virtualizado. Esto permite volver a un estado funcional sin tener que reinstalar todo el sistema operativo.
    
- **Experimentación y Aprendizaje:** En un entorno virtualizado, puedes experimentar con diferentes herramientas, técnicas y exploits sin preocuparte por las consecuencias. Esto es especialmente útil para aprender y adquirir experiencia en pentesting sin poner en riesgo sistemas reales.
    
- **Compatibilidad:** Algunas herramientas de pentesting y exploits pueden tener requisitos específicos de sistema operativo o dependencias. Un entorno virtualizado te permite crear rápidamente un sistema compatible para ejecutar esas herramientas sin afectar tu sistema principal.

- **Fácil Clonación y Compartición:** Puedes clonar fácilmente entornos virtuales para crear múltiples configuraciones idénticas. Esto es útil para pruebas repetibles y para compartir entornos específicos con otros pentesters o colegas.
    
- **Simulación de Escenarios:** Puedes simular entornos similares a los sistemas objetivo de tus pruebas de penetración. Esto te permite evaluar cómo se comportarían las técnicas de ataque en un contexto real.

## VirtualBox / VMware 
En internet hay muchísimos videos y artículos en el cual explican al detalle como hacer una instalación de cualquier sistema operativo para pentest en un entorno virtual, los virtualizadores se actualizan asi como las distribuciones por lo tanto es muy importante descargar ultimas versiones ya sea de VMware o VirtualBox
VMware y VirtualBox son dos populares programas de virtualización que permiten crear y ejecutar máquinas virtuales en un sistema anfitrión. Aunque ambos cumplen una función similar, tienen diferencias en términos de características, enfoque y capacidades
- [VirtualBox](https://www.virtualbox.org/)
- [VMware](https://www.vmware.com/es.html)
### ISO Kali Linux & Parrot Security 
- [Kali Linux](https://www.kali.org/)
- [Parrot](https://www.parrotsec.org/)

`Dejo una guía de un video actualizado sobre como descargar Kali y Parrot para VirtualBox`

-- > [Install Kali Linux](https://itsfoss.com/install-kali-linux-virtualbox/)

---> [Install Parrot](https://www.youtube.com/watch?v=NoivU2LrUrI)

## Primeros pasos con  Linux
Como anteriormente se mencionó Linux es un código abierto por lo tanto cualquier persona puede modificar  y distribuir código con o sin fines comerciales, Es muy común que los sistemas operativos basado en Linux ejecuten Servidores, mainframes, computadoras de escritorios, consolas de videojuegos e incluso Android se basa en el kernel de Linux , `Antes de iniciar es muy importante que te comiences a familiarizar con el entorno de Linux e ir acostumbrandote a usar la pura terminal, se que puede ser diíficil al principio pero te ira gustando con el tiempo y como lo dije es FUNDAMENTAL trabajar sobre la terminal ya que lo servidores no emplean método gráfico todo es administrado desde una linea de comandos`
Antes que nada tenemos que actualizar nuestro sistema, donde se descargarán paquetes a la ultima versión y estos se añadidos a nuestro entorno `apt update` se utiliza para actualizar la lista de paquetes disponibles y `apt upgrade` se usa para actualizar los paquetes instalados en el sistema

El comando que uso para actualizar completamente todo es el siguiente:

```shell
apt update && sudo apt-get update && sudo apt full-upgrade -y && sudo apt dist-upgrade -y
```

Donde hago uso de  `AND (&&)` para concatenar nuestros parámetros si cumple la primera condición se ejecuta la siguiente  y todo sea con un comando (One liner) seguido de un `sudo` donde se tiene que ejecutar con permisos elevados "`root`" `apt full-upgrade` como `apt-get dist-upgrade` son utilizados para realizar actualizaciones completas del sistema, asegurando que todas las dependencias y paquetes estén actualizados de manera coherente 

> Nota : la mayoría de comandos donde se requiere  interactuar con nuestro sistema siempre nos pedirá permisos de root, "sudo su" y la contraseña que estableciste a la hora de instalar tu distribución de Linux

`OJO importante no utilizar upgrade en parrot ya que se irá a la mrd nuestro sistema XD en este caso solo remplaza la palabara upgrade por **parrot-upgrade**`

```shell
sudo parrot-upgrade
```

Bien ahora que ya tienes tu sistema completamente actualizado es hora de que dejes de ver tiktoks, te pongas a estudiar Linux para aprender Hacking y dejes de pensar que el Hacking es como en las películas!! Mucho éxito recuerda tener paciencia y disfrutar tu aprendizaje **En los Post siguientes sobre Linux explicaré mas a fondo cada uno de los comandos y tener buen manejo por la terminal**
## Comandos principales

| Comando    | Descripción                                                           |
| ---------- | --------------------------------------------------------------------- |
| ls         | Lista los archivos y directorios en una ubicación específica          |
| cd         | Cambia de directorio                                                  |
| pwd        | Muestra el directorio de trabajo actual                               |
| mkdir      | Crea un nuevo directorio                                              |
| rm         | Elimina archivos y directorios                                        |
| cp         | Copia archivos y directorios                                          |
| mv         | Mueve o renombra archivos y directorios                               |
| cat        | Muestra el contenido de un archivo                                    |
| less       | Permite visualizar el contenido de un archivo de forma paginada       |
| grep       | Busca patrones o cadenas de texto en archivos                         |
| find       | Busca archivos y directorios en una ubicación específica              |
| tar        | Crea o extrae archivos y directorios comprimidos en formato tar       |
| gzip       | Comprime archivos en formato gzip                                     |
| gunzip     | Descomprime archivos comprimidos en formato gzip                      |
| chmod      | Modifica los permisos de acceso a archivos y directorios              |
| chown      | Cambia el propietario de un archivo o directorio                      |
| chgrp      | Cambia el grupo de un archivo o directorio                            |
| ssh        | Inicia una sesión segura de shell remoto                              |
| scp        | Copia archivos de forma segura entre hosts remotos                    |
| su         | Cambia al usuario superusuario o a otro usuario                       |
| sudo       | Ejecuta comandos con privilegios de superusuario                      |
| top        | Muestra los procesos en ejecución y su uso de recursos                |
| ps         | Muestra información sobre los procesos en ejecución                   |
| kill       | Termina un proceso en ejecución                                       |
| ifconfig   | Muestra y configura las interfaces de red                             |
| ping       | Envía un paquete de prueba a un host remoto                           |
| traceroute | Muestra la ruta que sigue un paquete hacia un host remoto             |
| netstat    | Muestra estadísticas de red y conexiones de red                       |
| iptables   | Configura las reglas del firewall                                     |
| systemctl  | Controla y gestiona servicios del sistema                             |
| apt-get    | Gestiona paquetes en sistemas basados en Debian                       |
| yum        | Gestiona paquetes en sistemas basados en Red Hat                      |
| nano       | Abre un editor de texto en la terminal                                |
| vi/vim     | Abre el editor de texto Vim                                           |
| man        | Muestra el manual de un comando                                       |
| uname      | Muestra información sobre el sistema operativo                        |
| df         | Muestra el espacio utilizado y disponible en los sistemas de archivos |
| du         | Muestra el uso de espacio en disco de archivos y directorios          |
| history    | Muestra el historial de comandos ejecutados                           |
| clear      | Limpia la pantalla de la terminal                                     |
| exit       | Cierra la sesión actual                                               |

