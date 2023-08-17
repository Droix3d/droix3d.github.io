---
title: Hacking con Google & Shodan
author: Droix3d
date: 2023-08-17
categories: [Hacking / Red Team && Pentesting,Footprinting]
tags: [Offensive Security, RedTeam, Pentesting]
pin: false
image:
  path: ../../assets/img/commons/Footprinting/shodan.png
  alt: Hacking con Google & Shodan
---

Dentro de las técnicas  de recopilación pasiva de información  nos encontramos un apartado que todo buen pentester debería de conocer, saber buscar información de fuentes publicas en google y recopilación de servidores, puertos , enumeración de servicios y DNS  de manera pública con shodan sin necesidad de intentar tener una interacción directa con el activo a auditar como podría ser un escaneo de puertos que eso ya entraría en la parte de recopilación **activa**. 
Dentro de estas búsquedas se puede encontrar 

- **Información sobre personas/organizaciones**
- **Contraseñas**
- **Documentos confidenciales**
- **Versiones de servicios vulnerables**
- **Directorios expuestos**

## Google dorking

El hacking con Google, también conocido como "Google hacking" o "Google dorking", involucra la utilización de operadores de búsqueda avanzada en Google para descubrir información que podría ser utilizada por hackers o investigadores de seguridad en sus esfuerzos. Esta técnica no implica necesariamente la explotación maliciosa de sistemas, sino más bien el uso creativo de las herramientas y características de búsqueda de Google para obtener acceso a información que normalmente no sería pública o fácilmente accesible.

Es importante destacar que el hacking con Google puede ser utilizado tanto con intenciones maliciosas como con fines legítimos, como la auditoría de seguridad, la identificación de vulnerabilidades y la mejora de la protección de la información en línea. Las personas y organizaciones deben ser conscientes de la información que comparten en línea y tomar medidas para proteger sus datos confidenciales y recursos digitales.

Para utilizar Google Dorking tendremos que usar la barra de búsqueda (como siempre) añadiéndole los dorks y palabras clave por las que filtrar. **Cuantos más dorks vayan en una búsqueda y más concretos sean, mas refinado será el resultado**

![](https://sysbeards.com/wp-content/uploads/2021/08/image01-google.jpg)

A continuación mostraré algunos comandos principales que podemos utilizar con Google. Hay que tener en cuenta que todos ellos deben ir seguidos (sin espacios) de la consulta que quiere realizarse:

## Dorks

|Operador|Descripción|Ejemplo de Uso|
|---|---|---|
|`term`|Definiciones procedentes de páginas web|`define:cybersecurity`|
|`filetype:term`|Búsquedas por tipo de archivo|`filetype:pdf cybersecurity`|
|`site:domain`|Los resultados se restringen a los contenidos en el sitio o dominio especificado|`site:wikipedia.org cybersecurity`|
|`link:url`|Mostrar páginas que enlazan a una URL|`link:www.example.com`|
|`cache:url`|Se mostrará la versión de la página definida por url que Google tiene en su memoria|`cache:www.example.com`|
|`info:url`|presenta información sobre la página web que corresponde con la url|`info:www.example.com`|
|`related:url`|muestra páginas similares a la que especifica la url.|`related:www.example.com`|
|`allinanchor:term`|Búsqueda en anclas de enlaces|`allinanchor:"cybersecurity tools"`|
|`inanchor:term`|Búsqueda en anclas de enlaces|`inanchor:hackers`|
|`allintext:terms`|Búsqueda en el contenido de la página|`allintext:"data breach"`|
|`intext:term`|Búsqueda en el contenido de la página|`intext:password site:example.com`|
|`allinurl:terms`|Búsqueda en la URL de la página|`allinurl:"login page"`|
|`inurl:term`|Búsqueda en la URL de la página|`inurl:admin site:example.com`|
|`allintitle:terms`|Búsqueda en los títulos de la página|`allintitle:"top 10 cybersecurity"`|
|`intitle:term`|Búsqueda en los títulos de la página|`intitle:security best practices`|
|`ext:extension`|Búsqueda de archivos por extensión|`ext:txt passwords`|
|`parent directory`|Búsqueda de directorios superiores|`parent directory sensitive data`|
|`site:.gov`|Búsqueda en sitios web gubernamentales|`site:.gov cybersecurity`|
|`site:.edu`|Búsqueda en sitios web educativos|`site:.edu online courses`|
|`intitle:index.of`|Búsqueda de directorios de índices|`intitle:index.of private`|
|`intext:"Confidential"`|Búsqueda de páginas con texto específico|`intext:"Confidential" site:example.com`|

Dentro de nuestros dorks de búsquedas también podemos juntarlos con una serie de operadores , Google hace uso de los operadores booleanos para realizar búsquedas combinadas de varios términos.

| Operador  | Descripción                                              | Ejemplo de Uso                                       |
|-----------|----------------------------------------------------------|-------------------------------------------------------|
| `AND` o `+` | Resultados que contengan ambas palabras                | `cybersecurity AND tools`                           |
| `OR` o `\|` | Resultados que contengan cualquiera de las palabras   | `hacking OR penetration testing`                   |
| `NOT` o `-` | Excluye resultados que contengan la palabra           | `passwords NOT leaked`                             |
| `()`       | Orden de evaluación de los operadores booleanos       | `(cybersecurity AND tools) OR (hacking NOT ethical)` |

Tenemos una serie de dorks mas específicos para la busca de vulnerabilidades e información en **google hacking database** (GHDB) , es un proyecto comunitario que recopila y cataloga una gran cantidad de "dorks" de Google (cadenas de búsqueda avanzada) que pueden ser utilizados para encontrar información sensible o mal configurada en línea. Estos dorks se utilizan en combinación con los operadores de búsqueda avanzada de Google para descubrir datos expuestos o vulnerabilidades de seguridad en sitios web y sistemas en línea.

- **Recopilación de Dorks**: La GHDB recopila una amplia variedad de dorks que han sido desarrollados por la comunidad de seguridad y los investigadores para identificar posibles puntos débiles en la configuración de sitios web, servidores y otros recursos en línea.
    
- **Facilita la Investigación**: La base de datos proporciona una forma organizada y centralizada de acceder a dorks específicos para realizar búsquedas avanzadas en Google. Esto puede ser útil para investigadores de seguridad, profesionales de ciberseguridad y hackers éticos que buscan identificar posibles problemas de seguridad.
    
- **Conciencia de Seguridad**: La GHDB también ayuda a concienciar sobre la importancia de asegurar adecuadamente los sistemas y recursos en línea. Al exponer cómo ciertas configuraciones mal realizadas o información sensible pueden ser encontradas con búsquedas simples, la GHDB promueve la mejora de las prácticas de seguridad en línea.

Con esto podriamos hacer busqueda de todo tipo como anteriormente mencioné, contraseñas publicas, archivos de configuración por extensiones, servicios expuestos a alguna vulnerabilidad filtrando por alguna versión vulnerable e incluso busquedas por nombres de personas, esto es un tema un poco delicado ya que el 90% de la población hace uso de redes sociales esto significa que por mas que ocultes tu información en tus redes siempre será expuesta publicamente ya que asi es como operán **"cuando el producto es gratis el producto eres tú"** 

## Google Hacking Database & Uso de Dorks

![](/assets/img/commons/Footprinting/google.png)

![](/assets/img/commons/Footprinting/google2.png)

![](/assets/img/commons/Footprinting/google3.png)

en este dork se especifica y se pone de ejemplo un dork en busca de inyecciones SQL

![](/assets/img/commons/Footprinting/google4.png)

## Dorks SQLinjection

```text
allinurl:*.php?txtCodiInfo=
inurl:read.php?=
inurl:”ViewerFrame?Mode=”
inurl:index.php?id=
inurl:trainers.php?id=
inurl:buy.php?category=
inurl:article.php?ID=
inurl:play_old.php?id=
inurl:declaration_more.php?decl_id=
inurl:pageid=
inurl:games.php?id=
inurl:page.php?file=
inurl:newsDetail.php?id=
inurl:gallery.php?id=
inurl:article.php?id=
inurl:show.php?id=
inurl:staff_id=
inurl:newsitem.php?num=
inurl:readnews.php?id=
inurl:top10.php?cat=
inurl:historialeer.php?num=
inurl:reagir.php?num=
inurl:Stray-Questions-View.php?num=
inurl:forum_bds.php?num=
inurl:game.php?id=
inurl:view_product.php?id=
inurl:newsone.php?id=
inurl:sw_comment.php?id=
inurl:news.php?id=
inurl:avd_start.php?avd=
inurl:event.php?id=
inurl:product-item.php?id=
inurl:sql.php?id=
inurl:news_view.php?id=
inurl:select_biblio.php?id=
inurl:humor.php?id=
inurl:aboutbook.php?id=
inurl:ogl_inet.php?ogl_id=
inurl:fiche_spectacle.php?id=
inurl:communique_detail.php?id=
inurl:sem.php3?id=
inurl:kategorie.php4?id=
inurl:news.php?id=
inurl:index.php?id=
inurl:faq2.php?id=
inurl:show_an.php?id=
inurl:preview.php?id=
inurl:loadpsb.php?id=
inurl:opinions.php?id=
inurl:spr.php?id=
inurl:pages.php?id=
inurl:announce.php?id=
inurl:clanek.php4?id=
inurl:participant.php?id=
inurl:download.php?id=
inurl:main.php?id=
inurl:review.php?id=
inurl:chappies.php?id=
inurl:prod_detail.php?id=
inurl:viewphoto.php?id=
inurl:article.php?id=
inurl:person.php?id=
inurl:productinfo.php?id=
inurl:showimg.php?id=
inurl:view.php?id=
inurl:website.php?id=
inurl:hosting_info.php?id=
inurl:gallery.php?id=
inurl:rub.php?idr=
inurl:view_faq.php?id=
inurl:artikelinfo.php?id=
inurl:detail.php?ID=
inurl:index.php?=
inurl:profile_view.php?id=
inurl:category.php?id=
inurl:publications.php?id=
inurl:fellows.php?id=
inurl:downloads_info.php?id=
inurl:prod_info.php?id=
inurl:shop.php?do=part&id=
inurl:productinfo.php?id=
inurl:collectionitem.php?id=
inurl:band_info.php?id=
inurl:product.php?id=
inurl:releases.php?id=
inurl:ray.php?id=
inurl:produit.php?id=
inurl:pop.php?id=
inurl:shopping.php?id=
inurl:productdetail.php?id=
inurl:post.php?id=
inurl:viewshowdetail.php?id=
inurl:clubpage.php?id=
inurl:memberInfo.php?id=
inurl:section.php?id=
inurl:theme.php?id=
inurl:page.php?id=
inurl:shredder-categories.php?id=
inurl:tradeCategory.php?id=
inurl:product_ranges_view.php?ID=
inurl:shop_category.php?id=
inurl:transcript.php?id=
inurl:channel_id=
inurl:item_id=
inurl:newsid=
inurl:trainers.php?id=
inurl:news-full.php?id=
inurl:news_display.php?getid=
inurl:index2.php?option=
inurl:readnews.php?id=
inurl:top10.php?cat=
inurl:newsone.php?id=
inurl:event.php?id=
inurl:product-item.php?id=
inurl:sql.php?id=
inurl:aboutbook.php?id=
inurl:preview.php?id=
inurl:loadpsb.php?id=
inurl:pages.php?id=
inurl:material.php?id=
inurl:clanek.php4?id=
inurl:announce.php?id=
inurl:chappies.php?id=
inurl:read.php?id=
inurl:viewapp.php?id=
inurl:viewphoto.php?id=
inurl:rub.php?idr=
inurl:galeri_info.php?l=
inurl:review.php?id=
inurl:iniziativa.php?in=
inurl:curriculum.php?id=
inurl:labels.php?id=
inurl:story.php?id=
inurl:look.php?ID=
inurl:newsone.php?id=
inurl:aboutbook.php?id=
inurl:material.php?id=
inurl:opinions.php?id=
inurl:announce.php?id=
inurl:rub.php?idr=
inurl:galeri_info.php?l=
```
## Shodan
Shodan es un motor de búsqueda especializado en dispositivos conectados a Internet. A diferencia de los motores de búsqueda convencionales que indexan contenido en sitios web, Shodan se centra en indexar información sobre dispositivos en línea, como servidores, cámaras IP, enrutadores, impresoras y otros dispositivos que están conectados a la red.
Shodan no busca contenido en sitios web, sino que recopila datos sobre los servicios y puertos que están abiertos en los dispositivos. Esto permite a los usuarios buscar dispositivos en función de criterios específicos, como tipode dispositivo, ubicación geográfica, sistema operativo, versión de software y más. Esta información es útil tanto para investigadores de seguridad como para administradores de sistemas, ya que puede revelar información sobre la exposición de dispositivos a Internet y posibles vulnerabilidades.

- **Realizar Búsquedas**: Una vez que tengas tu clave de API, puedes utilizar la interfaz de búsqueda de Shodan para buscar dispositivos específicos. Puedes realizar búsquedas simples utilizando palabras clave, o puedes utilizar operadores avanzados para refinar tus búsquedas.
    
- **Filtrar Resultados**: Shodan te permitirá filtrar los resultados según diversos criterios, como ubicación geográfica, tipo de dispositivo, sistema operativo y puertos abiertos.
    
- **Analizar Resultados**: Después de realizar una búsqueda, obtendrás una lista de resultados que muestran información sobre los dispositivos encontrados. Esto puede incluir direcciones IP, ubicación, puertos abiertos, servicios expuestos y más.
    
- **Detalles de Dispositivos**: Al hacer clic en un resultado, podrás ver detalles adicionales sobre el dispositivo, como información sobre el software que se está ejecutando, la versión del sistema operativo y otros datos relevantes.
    
- **Mapa de Dispositivos**: Shodan también muestra un mapa con la ubicación geográfica aproximada de los dispositivos encontrados.

![](/assets/img/commons/Footprinting/shodan2.png)

## Shodan Cheat Sheet


| Acción                            | Comando o Consejo                                             |
|----------------------------------|---------------------------------------------------------------|
| Búsqueda por palabra clave        | `keyword`                                                     |
| Búsqueda por IP                  | `ip:xxx.xxx.xxx.xxx`                                          |
| Búsqueda por ciudad               | `city:"city name"`                                            |
| Búsqueda por país                | `country:"country code"`                                      |
| Filtrar por puerto                | `port:port_number`                                            |
| Filtrar por sistema operativo      | `os:"operating system"`                                       |
| Filtrar por software              | `product:"software name"`                                     |
| Búsqueda de servicios específicos | `service:"service name"`                                      |
| Búsqueda de dispositivos vulnerables | `vuln:"vulnerability"`                                      |
| Búsqueda en título               | `title:"title text"`                                          |
| Filtrar por organización           | `org:"organization name"`                                     |
| Búsqueda combinada               | `keyword port:port_number os:"operating system"`             |
| Uso de comodines                 | `hostname:*.example.com`                                      |
| Ver información detallada de dispositivo | Hacer clic en un resultado para ver detalles.             |
| Uso responsable                  | Utiliza Shodan de manera ética y no invasiva.                |

Se puede observar mas comando para shodan en este apartado 

[Shodan Cheat Sheet](https://cheatography.com/sir-slammington/cheat-sheets/shodan/)

![](/assets/img/commons/Footprinting/shodan3.png)

En este apartado podemos observar que hago un filtro por el país (Country: CH) busqueda de puertos especificos abiertos (ports: 80,21,22,161,5060) 

![](/assets/img/commons/Footprinting/shodan4.png)

aqui hago lo mismo sobre la el país de corea y la ciudad de Seoul.

Podemos ahora hacer un filtrado en busca de vulnerabilidades (CVE)

![](/assets/img/commons/Footprinting/shodan5.png)

o bien buscar por servicios especificos, esto se usa mucho a la hora de auditar un activo ya de esta manera se obtiene información sobre los servicios que tiene expuesto sin necesidad de enviar una serie de paquete TCP/UDP de manera directo ya que esto generaría ruido y si emplean herramientas de protección como IDSP/IPS podrías ser bloqueado 

![](/assets/img/commons/Footprinting/shodan6.png)

## Principal función  de Shodan 

Como expertos en Ciberseguridad el uso de esta herramienta es muy poderosa por algo se llama (el Google para Hackers) se utiliza Shodan como una herramienta valiosa para obtener información sobre la superficie de ataque en línea, identificar vulnerabilidades de seguridad y evaluar la exposición de sistemas y dispositivos conectados a Internet. Aquí hay más información sobre cómo los expertos en hacking usan Shodan y su importancia:

**1. Identificación de Dispositivos Externos:** Shodan permite  identificar una amplia gama de dispositivos y sistemas conectados a Internet, como servidores, routers, cámaras IP, impresoras, sistemas SCADA, y más. Esto brinda una visión más amplia de la infraestructura tecnológica expuesta en línea.

**2. Búsqueda de Vulnerabilidades Conocidas:** Los expertos pueden utilizar Shodan para buscar sistemas que sean vulnerables a exploits conocidos, como CVE (Common Vulnerabilities and Exposures). Esto ayuda a detectar sistemas que aún no han sido parcheados o asegurados adecuadamente.

**3. Identificación de Configuraciones Inseguras:** Shodan permite identificar dispositivos mal configurados o con contraseñas débiles. ayuda a informar a los propietarios y administradores de sistemas sobre posibles problemas de seguridad.

**4. Exploración de Redes Industriales:** Shodan puede usarse para identificar sistemas SCADA y otros dispositivos industriales conectados a Internet. Esto es importante para evaluar la seguridad de las infraestructuras críticas y prevenir posibles ciberataques.

**5. Análisis de Exposición:** se puede evaluar cómo sus propias organizaciones están expuestas en línea y tomar medidas para reducir la superficie de ataque. Esto incluye identificar servicios innecesarios y puertos abiertos que podrían ser utilizados por atacantes.

**6. Investigación de Brechas de Seguridad:** Shodan puede ser utilizado para buscar servidores o dispositivos que hayan sido comprometidos y estén mostrando señales de actividad maliciosa. Esto ayuda  a investigar y responder a incidentes de seguridad.

**7. Hacking Ético y Pruebas de Penetración:** se utiliza Shodan en pruebas de penetración para evaluar la seguridad de sistemas y redes en busca de posibles vulnerabilidades. Esto ayuda a las organizaciones a fortalecer sus medidas de seguridad. 

**"Este contenido está diseñado únicamente con fines éticos y de aprendizaje. No me hago responsable del uso que los lectores puedan dar a la información proporcionada. Se recomienda encarecidamente actuar de manera ética y respetuosa, y obtener siempre la autorización adecuada antes de realizar cualquier actividad que involucre sistemas o datos de terceros."**


