---
title: Introducción a redes Modelo OSI - Fundamentos
author: Droix3d 
date: 2023-02-13 
categories: [Hacking y más, Por donde empezar]
tags: [Redes, Introduccion]
pin: false
image:
  path: ../../assets/img/commons/Redes/1Redes.jpg
  alt: Redes Introducción
---

## Qué es una red?
es un conjunto de equipos conectados por medio de cables, señales, ondas o cualquier otro método de transporte de datos, comparten cierta información conectadas entre si para la transmisión de datos

## Tipos de redes en base a la distancia

**<mark style="background: #ABF7F7A6;">LAN: Local Area Network (Red de Área Local) </mark>**Es una red de comunicación cuya área de cobertura geográfica excede el ámbito de un edificio. En una red de este tipo los ETD (equipo terminal de datos) son generalmente pero no necesariamente computadoras ya sean personales del tipo rango medio o grandes computadoras trabajando en modo estación de trabajo o servidor.

<mark style="background: #ABF7F7A6;">**WAN: Wide Area Network (red de área amplia)</mark>  **Las redes WAN interconectan LAN o servidores de ficheros en otros lugares, como las WAN conectan redes sobre un área geográfica grande hace posible que las empresas puedan comunicarse a grandes distancias. 

·         La WAN esta diseñada para: 

·         Proporcionar recursos a tiempo completo 

·         Ofrecer servicios de correo electrónico, WWW (world Wide Web), transferencia de ficheros y comercio electrónico.

<mark style="background: #ABF7F7A6;">PAN: Personal Area Network (red de área personal)</mark> Permite comunicar e intercambiar información entre ordenadores, PDA, impresoras, teléfonos móviles, y otros dispositivos en un área limitada normalmente unos pocos metros   
  Las tecnologías utilizadas en PAN son conexiones por: infrarrojos, bluetooth, radio frecuencias, etc.

<mark style="background: #ABF7F7A6;">MAN: Metropolitan Area Network (red de área metropolitana)  </mark> 
  Es una red de alta velocidad (banda ancha) que da cobertura en un área geográfica extensa, proporciona capacidad de integración de múltiples servicios mediante la transmisión de datos, voz y video, sobre medios de transmisión tales como fibra óptica y par trenzado (MAN BUCLE), la tecnología de pares de cobre se posiciona como la red mas grande del mundo.

## Modelo OSI
(Modelo de interconexión de sistemas abiertos) Fue creado por la Organización Internacional para la Estandarización (ISO) en el año 1980. Es un marco de referencia para la definición de arquitecturas en la interconexión de los sistemas de comunicaciones. Este modelo se divide en 7 capas.
### Capa de aplicación:
Es responsable de intercambiar información entre los programas que corren en una computadora.
### Capa de presentación:
Formatea la información de manera que la aplicación del software pueda leerla.
### Capa de sesión:
Determina como dos dispositivos se comunican, establece y monitorea las conexiones entre computadoras
### Capa de transporte:
Corrige los errores de transmisión y asegura que la información sea entregada en forma confiable.
### Capa de red:
Identifica a las computadoras en una red y determina como dirigir la transferencia de información por la misma.
### Capa de enlace:
Agrupa los datos en secciones para prepararlos y transferirlos por la red.
### Capa física:
se define como un medio de transmisión, por ejemplo, un cable, se conecta a una computadora

![imagen 1]({{ '/assets/img/commons/Redes/2Redes.jpg' | relative_url }}){: .center-image } Modelo OSI

## IP y sus tipos
### IP 
(Internet Protocol) este conjunto de reglas sirve para comunicar a través de internet o en una red local identifica una red o dispositivo en Internet

### IP pública 
es aquella que se va a encargar de enviar y recibir mensajes a través de internet con ella puedes comunicarte con otros usuarios fuera de tu red local. Todos tenemos una a la hora de navegar por internet y es muy importante que esta IP no se comporta con terceras personas ya que pueden hacer uso de ella como  el que podrían hacerse pasar por ti, robar tu información, localizar la dirección de tu casa (aunque esta no es exacta, solo es un rango de kilómetros de tu lugar real de origen) , país de residencia o una denegación de servicio (DDoS o DoS) por eso es importante hacer uso de una VPN para cifrar la conexión o hacer uso de un proxy

### IP privada
es la que esta compartida solamente en tu area de red local, como en tu casa, empresa, negocio etc... se le conoce como red Interna, las direcciones privadas las emite un dispositivo de red, como un router, que las extrae de un conjunto de direcciones que le ha asignado un servidor DHCP.

### IP dinámica 
Una **dirección IP** es una etiqueta numérica que identifica, de manera lógica y jerárquica, a un interfaz de un dispositivo dentro de una red que utilice el protocolo IP que corresponde al nivel de red del Modelo OSI. La dirección IP puede cambiar muy a menudo por cambios en la red o porque el dispositivo encargado dentro de la red de asignar las direcciones IP decida asignar otra IP (por ejemplo, con el protocolo DHCP). A esta forma de asignación de dirección IP se denomina también _dirección_ IP dinámica (normalmente abreviado como _IP dinámica_).

### IP estática 
Los sitios de Internet que por su naturaleza necesitan estar permanentemente conectados generalmente tienen una _dirección_ IP fija (comúnmente, _IP fija_ o _IP estática_). Esta no cambia con el tiempo. Los servidores de correo, DNS, FTP públicos y servidores de páginas web necesariamente deben contar con una dirección IP fija o estática, ya que de esta forma se permite su localización en la red.

### Direcciones privadas
pueden ser utilizadas por los hosts que usan traducción de dirección de red (NAT) para conectarse a una red pública o por los hosts que no se conectan a Internet. En una misma red no pueden existir dos direcciones iguales, pero sí se pueden repetir en dos redes privadas que no tengan conexión entre sí o que se conecten mediante el protocolo NAT.

### IPv4
Las direcciones IPv.4 se expresan por un número binario de 32 bits, permitiendo un espacio de direcciones de hasta 4.294.967.296 (232) direcciones posibles. Las _direcciones IP_ se pueden expresar como números de notación decimal: se dividen los 32 bits de la dirección en cuatro octetos. El valor decimal de cada octeto está comprendido en el rango de 0 a 255 [el número binario de 8 bits más alto es 11111111 y esos bits, de derecha a izquierda, tienen valores decimales de 1, 2, 4, 8, 16, 32, 64 y 128, lo que suma 255].
En la expresión de direcciones IPv4 en decimal se separa cada octeto por un carácter único ".". Cada uno de estos octetos puede estar comprendido entre 0 y 255, salvo algunas excepciones. Los ceros iniciales, si los hubiera, se pueden obviar.

### IPv6
La función de la dirección IPv6 es exactamente la misma que la de su predecesor IPv4, pero dentro del protocolo IPv6. Está compuesta por 128 bits y se expresa en una notación hexadecimal de 32 dígitos. IPv6 permite actualmente que cada persona en la Tierra tenga asignados varios millones de IPs, ya que puede implementarse con 2128 (3.4×1038 hosts direccionables). La ventaja con respecto a la dirección IPv4 es obvia en cuanto a su capacidad de direccionamiento.
Su representación suele ser hexadecimal y para la separación de cada par de octetos se emplea el símbolo ":". Un bloque abarca desde 0000 hasta FFFF.

## DHCP
Asignación automática No se requiere asignar manualmente direcciones IP. El servidor DHCP asigna al cliente DHCP, en el primer contacto, una dirección IP permanente que no podrá reutilizar ningún otro cliente DHCP.

## Qué es una NAT y sus tipos?
### NAT
En redes, NAT significa **Network Adress Translation** o **Traducción de direcciones de red** en español. Se trata de un sistema que se utiliza en las redes **bajo el protocolo IP** y que nos **permite el intercambio de paquetes entre dos redes que tienen asignadas mutuamente direcciones IP incompatibles**.
En resumen, el NAT ayuda a **controlar que las direcciones IPv4 se agoten**, administrando direcciones IPv4 locales o privadas de todos los equipos conectados a la red. Entonces, si hay tres ordenadores, dos móviles, una impresora y cualquier otro equipo, cada uno tiene una dirección IP privada, lo que haría un total de siete direcciones. NAT los trata individualmente dentro de la red, pero para Internet, es solo una dirección IP, como si fuera un dispositivo único.

### NAT Estática
es cuando una dirección ip privada siempre se traduce en la misma dirección ip pública

### NAT dinámica
el router tiene un grupo de direcciones ip, a cada una de las direcciones ip privadas se le otorga una ip publica del grupo de las ip del router

## Conexión en máquinas Virtuales
### Adaptador puente
El **adaptador puente** hace que la máquina virtual se conecte a la misma red que el anfitrión, de tal forma que la MV se comportará como si fuera un PC más conectado a la red real. Nos permite conectar entre MV, desde el anfitrión, y a Internet bidireccionalmente. Por contra, nos puede ocasionar problemas puesto que estará conectado a la red real (especialmente en caso de montar servidores).

### Anfitrión 
En modo **solo anfitrión** podremos conectarnos desde el anfitrión a nuestras máquinas virtuales y viceversa, así como conectar entre máquinas virtuales. En todo caso, no tendremos por defecto conexión a internet (ni de salida ni de entrada)

## ISP
Proveedor de servicios de Internet. **Una empresa que le proporciona acceso a Internet, normalmente a través de una conexión de acceso telefónico, de DSL o de banda ancha**.

## ICMP 
protocolo de control de mensajes de internet, ICMP **maneja los mensajes de error y control para IP**. Este protocolo permite a las pasarelas y los sistemas principales enviar informes de problemas a la máquina que envía un paquete.

## Envío de mensajes 
- Existen 2 grupos: 
1. Control de Transmisión 
2. Internet

Segmentación -> Tomar un mensaje y dividirlo en pequeñas partes
Multiplexación -> es la combinación de dos o mas canales de información en un solo medio de transmisión

**PDU Protocol Data Unit** es una unidad que nos permite identificar la información a medida que es transmitida a través de las capas de red.

## Seguridad empleada en modelo osi
Normalmente  todos los puntos a tratar de acuerdo al modelo osi son importante pero sobre hay que tener muy bien cubierta las primeras tres capas del modelo osi para tener buena gestión 

1 - Física - evitar conexiones físicas como cables no autorizados, memorias USB , routers, switches etc... 
Tipo de ataque mas común - > SNIFFING 

2 - Enlace de Datos - Normalmente se componen de falsificaciones de datos como puede ser la técnica para enmascarar la dirección MAC de un dispositivo de red que está codificada en una tarjeta de red (MAC spoofing)

3 - Red - es donde se da la captura y escaneo de paquetes en una red el ataque mas presentado en esta capa es el típico (MAN In The Middle) Hombre en el medio como su nombre lo dice puede actuar un intermediario para la leer, insertar y modificar a voluntad paquetes que pasen en una red
