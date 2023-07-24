---
title: Footprinting 
author: Droix3d
date: 2023-07-24
categories: [Hacking / Red Team && Pentesting,Footprinting]
tags: [Offensive Security, RedTeam, Pentesting]
pin: true
image:
  path: ../../assets/img/commons/Footprinting/Portada1.jpg
  alt: Footprinting 
---

En el area del Pentesting a la hora de auditar y explotar  un activo la primera fase de toda metodología es la recolección de información , La enumeración es un concepto que es muy utilizado en ciberseguridad. Es sinónimo  de recopilación de información. Dentro de la enumeración hay  formas de conseguir una recolección de datos - > "Footprinting, Information Ghatering , OSINT" estos tres términos van muy bien de la mano , aunque incluyan pequeñas diferencias el objetivo de estos tres términos es la recolección de información .
La enumeración es un ciclo en el que recopilamos información repetidamente en función de los datos que tenemos o ya hemos descubierto.
En esta parte existen dos tipos de métodos a la hora de recopilar información   **Activo y Pasivo**  

## Escaneo Activo
Implica la interacción directa con los sistemas o personas objetivo para obtener la información deseada. Esto puede incluir el uso de técnicas como el escaneo de puertos, la realización de solicitudes de información a través de herramientas específicas, el envío de correos electrónicos o mensajes con intención de obtener datos relevantes, entre otros.

## Escaneo Pasivo 
No involucra una interacción directa con los sistemas o personas objetivo. En lugar de eso, se basa en la observación y el análisis de información que ya está disponible públicamente o se obtiene sin perturbar a la entidad objetivo. Esto puede incluir la búsqueda y examen de fuentes de información abiertas, como sitios web, redes sociales, registros públicos, documentos disponibles en línea, entre otros

## Footprinting

1. Es una etapa específica dentro del proceso de recopilación de información en el ámbito de la seguridad de la información.
2. Se enfoca en obtener información detallada sobre una organización específica, como su infraestructura de red, sistemas operativos, servicios expuestos, nombres de dominio, direcciones IP, empleados, entre otros.
3. El objetivo principal del Footprinting es evaluar la seguridad de la organización y detectar posibles vulnerabilidades.

## Information Gathering:

1. Es un término más amplio que engloba diferentes técnicas y métodos para recopilar información.
2. Puede incluir actividades como el Footprinting, pero también abarca otras técnicas, como búsqueda en bases de datos, consulta de registros públicos, análisis de metadatos, conversaciones con personas relevantes, entre otros.
3. El objetivo es obtener información relevante y útil sobre un tema o entidad específica, que puede incluir organizaciones, personas, eventos, etc.

## OSINT (Open Source Intelligence):

1. Es una disciplina en sí misma, que se basa en la recopilación y análisis de información de fuentes abiertas y accesibles públicamente.
2. Se centra en la búsqueda y análisis de información en fuentes como sitios web, redes sociales, foros en línea, documentos públicos, entre otros.
3. El objetivo del OSINT es obtener información relevante sobre diversos temas, como personas, organizaciones, eventos, tendencias, etc. Esta información se utiliza para análisis, toma de decisiones, investigación y otras finalidades.

## Como empezar en un recolección de información 

<mark style="background: #BBFABBA6;">La información se puede recopilar de dominios, direcciones IP, servicios accesibles y muchas otras fuentes.</mark>

Una vez que hemos identificado los objetivos en la infraestructura de nuestro activo a auditar, debemos examinar los servicios y protocolos individuales. En la mayoría de los casos, estos son servicios que permiten la comunicación entre los clientes, la infraestructura, la administración y los empleados.

Si por ejemplo fuimos contratados para investigar la seguridad informática de una empresa, comenzaremos a hacer un análisis y comprensión de ella, debemos entender como es que esta estructurada la empresa, que servicios y proveedores externos utiliza, que medidas de seguridad se pueden emplear y más 

Los principios de enumeración se basan en algunas preguntas que facilitarán todas nuestras investigaciones, es muy importante llevar una metodología de por medio , no será igual recolectar información cuando audites una web a cuando se esté auditando un Directorio activo 
Estas preguntas pueden estructurarse como 
¿Que podemos ver? ¿Que ganamos con ello? ¿Como podemos usarlo? ¿ Que podemos interpretar y deducir con lo que vimos?

![](https://www.lentz-detektei.de/wp-content/uploads/2023/06/osint-recherchen-2-e1686562519554.jpg)

## Metodología de enumeración 
- **Recopilación de información (Reconocimiento inicial):** Reúne toda la información pública y abierta disponible sobre el objetivo, como dominios, subdominios, direcciones IP, registros WHOIS, etc. Utiliza herramientas como `nslookup`, `dig`, `theHarvester`, y consulta bases de datos públicas para obtener información relevante.
- **Identificación de servicios y puertos abiertos:** Utiliza herramientas como Nmap para escanear los puertos y servicios activos en las direcciones IP encontradas. Esto te permitirá saber qué servicios están disponibles y potencialmente vulnerable
- **Enumeración de información del sistema:** Obtén información detallada sobre los servicios, versiones de software, sistemas operativos, usuarios y grupos. Herramientas como banner grabbing y Enum4linux pueden ser útiles para esta tarea.
- **Análisis de vulnerabilidades:** Utiliza herramientas como Nessus o OpenVAS para realizar un análisis de vulnerabilidades. Estas herramientas te ayudarán a identificar posibles vulnerabilidades en los servicios y aplicaciones encontradas.
- **Enumeración de usuarios:** Intenta identificar nombres de usuarios válidos para los sistemas objetivo. Utiliza técnicas como enumeración de usuarios en servicios (por ejemplo, mediante el uso de herramientas como Enum4linux para sistemas Windows) y búsqueda de información pública sobre empleados.
- **Enumeración de recursos y directorios:** Utiliza herramientas como Dirb o Gobuster para buscar recursos y directorios ocultos en los servicios web. Esto puede ayudarte a descubrir puntos de entrada adicionales y posibles vulnerabilidades.
- **Enumeración de información de red:** Si es posible, utiliza herramientas como SNMPwalk para recopilar información sobre la topología de la red y dispositivos conectados.
- **Enumeración de servicios web:** Realiza un análisis exhaustivo de los servicios web encontrados, buscando posibles vulnerabilidades y puntos débiles, como inyecciones SQL, XSS (cross-site scripting) o problemas de autenticación.

No hay una metodología escrita en un documento que diga que  es la correcta pero si en toda la fase inicial es muy importante y primordial tener en cuenta una buena enumeración esto nos dará información para una correcta explotación de vulnerabilidades que es lo que queremos conseguir

Para la recaudación de información hay distintas formas muy  comunes que se llegan a emplear y uso de algunas herramientas,  para la recopilación información  tratamos de comprender la funcionalidad del activo al cual estamos auditando y qué tecnologías y estructuras son necesarias para que los servicios se ofrezcan con éxito y eficiencia. Una vez que tenemos claro nuestro objetivo sobre la  **Recolección de información** vamos a pasar con algunas herramientas con su descripción acerca de la recopilación activa y pasiva 

## Recopilación Activa 
La recopilación activa implica interactuar directamente con el objetivo para obtener información. Es una fase más intrusiva que la pasiva y generalmente implica enviar solicitudes y recibir respuestas del objetivo. Es una fase más intrusiva que la pasiva y generalmente implica enviar solicitudes y recibir respuestas del objetivo.
- **Nmap:** Una poderosa herramienta de escaneo de redes que permite descubrir hosts activos, puertos abiertos y servicios en una red.
- **Nikto:** Se utiliza para realizar un escaneo de vulnerabilidades en servidores web, en busca de problemas comunes como configuraciones inseguras o versiones vulnerables de software.
- **Dirb/Gobuster:** Estas herramientas ayudan a enumerar recursos y directorios en servidores web al realizar peticiones HTTP en busca de rutas ocultas o no enlazadas.
- **theHarvester:** Una herramienta que permite recopilar información de correos electrónicos, nombres de dominio y subdominios a partir de fuentes públicas como motores de búsqueda, redes sociales y bases de datos.
- **DNSenum:** Utilizado para enumerar información de DNS y obtener subdominios y registros DNS adicionales mediante consultas de transferencia de zona y otros métodos.
- **WPScan:** Se enfoca específicamente en la enumeración de vulnerabilidades en sitios web construidos con WordPress, identificando problemas relacionados con temas, plugins y versiones de WordPress.
- **enum4linux:** Herramienta específica para enumerar información en sistemas Windows a través del protocolo SMB, como usuarios, grupos y políticas.

![](https://neoteker.or.id/wp-content/uploads/2020/07/nmap.png)

## Recopilación Pasiva
Se basa en fuentes de información públicas y no requiere enviar solicitudes directas al objetivo.
- **Google Dorks:** Utilizando operadores de búsqueda avanzada en Google, puedes encontrar información sensible y exposiciones accidentales, como contraseñas o archivos confidenciales que han sido indexados por los motores de búsqueda.
- **Shodan/Censys:** Estas herramientas son motores de búsqueda para dispositivos conectados a Internet. Pueden ayudarte a encontrar dispositivos vulnerables, como cámaras IP o servidores mal configurados.
- **Maltego:** Una herramienta de inteligencia de código abierto que recopila y cruza datos de diversas fuentes para crear visualizaciones gráficas y conexiones de información entre objetivos.
- **OSINT Framework:** Es una recopilación de herramientas y recursos de código abierto para inteligencia de fuentes abiertas (Open Source Intelligence), que incluye fuentes para búsqueda de correos electrónicos, nombres de usuarios, información de dominios, etc.

![](https://hackersonlineclub.com/wp-content/uploads/2016/11/Google-Hacking-Database.png)

**En otro de mis post dedicaré un apartado específicamente el uso de las herramientas de recolección pasiva y activa de información**

Como anteriormente mencioné nuestra recolección de información será dependiendo al activo del cual vayamos a atacar por ejemplo es muy común en los sitios web que dejen registros como, números de teléfono, cuentas de correo, nombre del personal , consultar certificado SSL/TLS   que esto puede incluir más que solo un subdominio, y esto significa que el certificado se usa para varios dominios, y estos probablemente aún estén activos. 

## Herramientas de fuentes publicas para la recolección de información 

para recolección de fuentes publicas en un sitio web están  los siguientes sitios web

1. **WHOIS Lookup:** Sitios web como whois.net o whois.icann.org permiten buscar información sobre el registro de dominios, lo que incluye detalles sobre el propietario del dominio, el registrador, fechas de registro, etc.
    
2. **DNS Dumpster:** Esta herramienta te permite explorar los registros DNS de un dominio y encontrar subdominios y otros registros de DNS relacionados.
    
3. **Shodan:** Shodan es un motor de búsqueda que permite buscar dispositivos conectados a Internet, como servidores, routers, cámaras IP, entre otros. Puede proporcionar información sobre servicios expuestos y vulnerabilidades.
    
4. **Netcraft:** Netcraft proporciona información sobre el servidor web y tecnologías utilizadas por un sitio web, así como datos de ubicación y tiempo de actividad histórico.
    
5. **Wayback Machine (archive.org):** Wayback Machine es un recurso que te permite ver capturas de pantalla históricas de sitios web. Puedes obtener información sobre la evolución del sitio a lo largo del tiempo.
    
6. **Robtex:** Robtex es una herramienta que te permite explorar información de dominios y direcciones IP, incluyendo registros DNS, redes relacionadas y subdominios.
    
7. **BuiltWith:** BuiltWith es un servicio que revela las tecnologías utilizadas en un sitio web, como sistemas de gestión de contenido (CMS), frameworks, plugins y mucho más.
    
8. **Hunter.io:** Herramienta en línea que se utiliza para buscar direcciones de correo electrónico asociadas a un dominio específico.
    
9. **[PhoneBook.cz](https://phonebook.cz/):** La agenda telefónica enumera todos los dominios, direcciones de correo electrónico o URL para el dominio de entrada dado. Se permiten comodines como *.gov.uk.
    
10. **[Email-checker.net](https://email-checker.net/check):** Email Checker es una pequeña herramienta gratuita que te ayuda a averiguar si una dirección de correo electrónico es válida o no.
    
11. **[CentralOps.net](https://centralops.net/co/):** Investigar dominios y direcciones IP. Obtenga información del registrante, registros de DNS y más, todo en un solo informe.

## Metadatos
los metadatos pueden referirse a información oculta o datos incrustados en archivos o documentos que pueden revelar detalles sobre su creación, modificación o autoría. Estos metadatos pueden contener información sensible o confidencial que podría ser utilizada en investigaciones de seguridad o auditorías de sistemas.

Algunos ejemplos de metadatos en archivos digitales son:

1. **Metadatos en documentos de Microsoft Office:** Los archivos de Microsoft Word, Excel y PowerPoint pueden contener información sobre el autor, la empresa, la última fecha de modificación y más.
    
2. **Metadatos en imágenes:** Las imágenes pueden tener información sobre el dispositivo utilizado para tomarlas, coordenadas GPS del lugar donde se tomó la foto, entre otros detalles.
    
3. **Metadatos en archivos PDF:** Los archivos PDF pueden incluir información sobre el autor, el título, la fecha de creación y la última fecha de modificación.
    
4. **Metadatos en archivos de audio y video:** Los archivos multimedia pueden tener información sobre el creador, la fecha de grabación, el software utilizado y más.
 
 Es importante comprender la importancia de proteger y eliminar metadatos innecesarios antes de compartir o distribuir archivos, especialmente aquellos que podrían contener información personal o confidencial. En la fase de pruebas de penetración o auditoría de seguridad, se puede utilizar la información de los metadatos para evaluar la seguridad y privacidad del sistema.
 
![](https://www.smartdatacollective.com/wp-content/uploads/2019/04/what-cyber-criminals-can-do-with-your-metadata.jpg)

## Herramientas para análisis de metadatos 

1. **ExifTool:** ExifTool es una herramienta de línea de comandos desarrollada por Phil Harvey que permite leer, escribir y editar metadatos en una amplia variedad de tipos de archivos, incluyendo imágenes, videos, documentos y más.
    
2. **FOCA (Fingerprinting Organizations with Collected Archives):** FOCA es una herramienta desarrollada por ElevenPaths que se utiliza para analizar metadatos y realizar tareas de footprinting sobre documentos de Microsoft Office (Word, Excel, PowerPoint) y archivos PDF. FOCA es útil para obtener información sobre la estructura interna de un documento y revelar detalles sobre la organización que lo creó.
    
3. **Metagoofil:** Metagoofil es una herramienta de código abierto que se enfoca en buscar y extraer metadatos de documentos en la web. Puede buscar en múltiples sitios web y extraer metadatos de archivos como PDF, Word, Excel, PowerPoint y más.
    
4. **Mat2 (Metadata Anonymisation Toolkit):** Mat2 es una herramienta de código abierto que se utiliza para anonimizar y eliminar metadatos de archivos antes de compartirlos o distribuirlos. Es útil para proteger la privacidad y la seguridad al compartir archivos públicamente.
    
5. **Hachoir-metadata:** Hachoir-metadata es una biblioteca y herramienta de línea de comandos en Python que permite extraer y editar metadatos de varios tipos de archivos multimedia y documentos.
    
6. **QuickHash:** Aunque no es una herramienta específica para metadatos, QuickHash es una herramienta de código abierto que se utiliza para calcular hash de archivos, lo que puede ser útil en la verificación de la integridad de los archivos y metadatos.

![](https://ayudaleyprotecciondatos.es/wp-content/uploads/2020/09/FOCA-06.png)
 
 
 

