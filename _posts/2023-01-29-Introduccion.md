---
title: Introducción al Hacking | Ciberseguridad
author: Droix3d
date: 29-01-2023
categories: [Por donde empezar en Ciberseguridad]
tags: [Introducción]
math: true
mermaid: true
pin: true
image:
  path: /assets/img/commons/kaliparrot/security.jpg
  alt: Tux<3 Hacker
---

## De que trata esta sección?
---
En este Post recopilaré los pasos que llegué a seguir y como estructuraba todo un temario dedicado y especialemente enfocado al campo de la Ciberseguridad, espero y sea de ayuda y motivación para todas las personas que se esten adentrando en este mundo tan peculiar, grande e interesante como puede llegar a hacer la Ciberseguridad Ofensiva :D 
espero y me puedas ayudar con un follow en mis redes sociales que dejaré en la parte inferior del Blog.
~by Omar Aka(Droix3d) *"La vida es una preparación para el futuro; y la mejor preparación para el futuro es vivir como si no hubiera ninguno."*

---

## Qué es un Hacker, un Pentester y un Red Teamer?

**Hacker**: Muy bien, comenzaremos con un concepto primordial y no  menos importante que muchas personas tendrán en mente la palabra Hacker pero no tienen el significado real, si bien es cierto el concepto de la palabra Hacker se a ido deformando con el paso de los dias. con un significado poco ético al principio, si asi se le puede llamar. Actualmente el significado a tomado un mejor cámino y no es mas que una persona que se dedica a detectar fallos de seguridad en sistemas informáticos con un próposito, Habiendo distintos y varios tipos de Hacker (hablaremos de ello mas adelante) en pocas palabras ser un Hacker es una etiqueta que llevamos todos que nos dedicamos en el campo de la Ciberseguridad 

**Pentester** : Un pentester lleva la filosofía y los pasos del *"Hacker ético"* es una práctica de la seguridad informática por la que los expertos o hackers éticos de diferentes compañías alrededor del mundo optan para explorar la estructura de seguridad informática de la empresa, a su vez reportando todas aquellas fallas y vulnerabilidades que se llegue a encontrar en una prueba o examen de penetración


**Red Teamer** : Un Red Team es un ejercicio que consiste en simular un ataque dirigido a una organización. Esto se traduce en un grupo de personas internas o externas a la empresa, comprueban la posibilidad de tener acceso a los sistemas, comprometerlos atacando puntos débiles y el impacto que esto podría tener en el negocio


## El arte del Hacking y ser un Hacker 

Si bien es cierto, cuando mencionan la palabra Hacker a la mayoría  se nos viene a la mente todas esas películas de Hollywood, hombres, jóvenes, mujeres etc.… expertos en tecnología encapuchados y enmascarados que prácticamente juegan a ser Dios con un simple portátil escondidos en un sótano XDD

Aquellas personas que con un botón y escribir algoritmos aleatoriamente en su teclado pueden llegar a comprometer un sistema altamente privilegiado jajajaja
Que asco y porquería da Hollywood y todos esos videos mal informando a las personas sobre el verdadero mundo del hacking

siempre he dicho que *"el Hacker motiva, aprende y enseña día con día"* En este campo especialmente hay muchísimas dudas sobre el camino por el cual seguir, así como existen Ingenieros en Software, Ing. En Redes, Telecomunicaciones, expertos en bases de datos y todas las profesiones habidas y por haber , el Hacker y el Hacking es igual otra profesión común como todas las demás  

## Qué se necesita para ser un experto en Ciberseguridad?

Primero que nada la paciencia es fundamental que va de la mano con la disciplina y la constancia, como muchas cosas en la vida, **"Recuerda que es una profesión no es un juego"** por lo tanto no se aprenderá todo de la noche a la mañana con verte un simple video de 10 minutos instalando Kali Linux aprendiendo herramientas de phishing para saber la contraseña de tu Ex , deja atrás todas esas estupideces que si solo vienes con esa mentalidad de invadir la privacidad de otras personas a lo script Kiddie no llegaras lejos aquí si es lo que realmente quieres, que es a lo que me lleva el siguiente punto 

## Tipos de Hackers
**White Hat** : Los hackers white hat o de sombrero blanco son uno de los tipos de hackers con experiencia en ciberseguridad. Están autorizados a hackear los sistemas de seguridad informática de gobiernos u organizaciones con el fin de comprobar fallos de ciberseguridad. Al hacerlo, identifican los puntos débiles y los corrigen para evitar futuros ataques de fuentes externas.

**Black Hat** : Los black hat o de sombrero negro también son expertos en informática, sin embargo, su intención es completamente distinta. En lugar de ser provechosos para la ciberseguridad, estos tipos de hackers utilizan sus conocimientos sobre cómo hackear para atacar otros sistemas. Así, obtienen acceso a sistemas donde no tienen una entrada autorizada, con lo que pueden robar datos o destruir el sistema.

**Gray Hat** : Los hackers grey hat se encuentran entre los black hat y los white hat. Este tipo de hackers informáticos trabajan con buenas y con malas intenciones. La intención que hay detrás de la piratería decide el tipo de pirata informático.Experimentando con sistemas para encontrar lagunas, romper defensas y, en general, encontrar una experiencia divertida en el hacking.

**Hacktivistas** : Este tipo de hackers informáticos tienen la intención de hackear sitios web gubernamentales. Un hacktivista puede ser un individuo o un grupo de hackers sin nombre cuya intención es obtener acceso a sitios web y redes gubernamentales.

**Script Kiddie - Blue Hat** : No tiene ningún deseo de conocimiento, ninguna motivación para la autoeducación y ningún respeto por las habilidades: utiliza programas informáticos estándar para el hackeo, el phishing, el phreaking y otras formas de caos electrónico sin saber siquiera cómo funcionan o qué hacen.(Buscan Hackear Facebooks)

### Qué se necesita saber para dedicarte a la Ciberseguridad?
>Esta es la parte que todos estaban esperando. Principalmente y en lo **personal** acomode los temas de acuerdo a su nivel de importancia para este campo y conforme a mi experiencia  

1. **Manejo y creación de Máquinas virtuales** : A su vez saber realizar instalaciones de cualquier sistema operativo de Windows y distribuciones básicas de Linux en VM (Virtual Machine) y booteable   

2. **Sistemas operativos** : Conocer un poco de teoría y fundamentos sobre Windows y Linux - conocer la terminal y la shell de ambos ya que será la forma en como se trabajara

3. **Linux** : Aprender Linux en esta area es **fundamental** Conocer las distribuciones que hay , diferencias y acostumbrarse totalmente de trabajar con la pura terminal , olvídate totalmente del modo gráfico, la mayoría de herramientas que emplea Kali y parrot son con puros comandos, ocuparas pocas  que emplean método grafico . también  saber cómo está estructurado el Kernel y saber sobre toda la raiz del sistema operativo ya que llega a ser un poco diferente a windows 

4. **Redes** : Saber como se comunican los protocolos, puertos, servicios en un modelo OSI es algo que no debe faltar en toda fase de reconocimiento, conceptos de LAN,WAN,VPN,PROXY,NAT,DHCP,IP,TCP,UDP y modelo TCP-IP es algo por donde comenzaría a darle con el estudio

5. **Programación** : Algunas personas pondrían mas arriba este lugar pero les diré mi punto de vista. La programacion en este campo es primordial, ya que analizaras código, interpretaras y crearas herramientas. peeeero no a un  nivel full stack developer, me refiero a que aprenderás y harás lo necesario para vulnerar un sistema, conocer las típicas fallas de sanitización de código y donde se explotan las vulnerabilides y con esto no me refiero a que debas de saber todos los lenguajes de programación habidas y por haber, con aprender Python y scripting en bash al 100% créeme que nada te faltara 😊 , mas adelante cuando vayas un poco mas avanzado recomiendo aprender C y un poco en ensamblador.
**en pocas palabras  es más análisis , interpretación y explotación en código no vas a desarrollar un sitio web para que no te metas de lleno con un curso de html , css y java script xd** SOLO SERA LO NECESARIO 

6. **Criptografía** conocer técnicas de cifrado básicas o codificado, tipos de hash, tipos de cifrado y sus diferencias, formas en las que se emplean y como es que se llega a proteger la **Integridad,Disponibilidad y la Confidencialidad**

7. **Uso de Herramientas básicas para la recopilación de información Information Gathering and Footprinting** Escaneo pasivo y activo de recolección de información 

8. **Extra -> Realizar tu primera máquina en tu laboratorio** Descargar y ejecutar una máquina que simula una prueba de penetración estilo CTF para principiantes **Vulnhub** es muy buena opción  

## Kali o Parrot?

> Aquí es donde abriré la discusión mas grande de todos que se dedican a la ciberseguridad 

![](/assets/img/commons/kaliparrot/kalivsparrot.png)

La distribución a elegir será de acuerdo a tus gustos, te recomiendo probar los dos en VM y el que mas te agrade lo puedes usar como tu dia a dia, lo que si es que no ocuparas al 100% las herramientas que te proporcionan eso ten por seguro , puedes tirar incluso de un ubuntu e instalar las herramientas que se necesitan para el hacking pero para no revolverte con tantas instalaciones y dependecnias si eres nuevo te recomiendo elegir cualquiera de los dos ya que son distribuciones basadas en *debian*

>es como elegir tu pokemon xdd
