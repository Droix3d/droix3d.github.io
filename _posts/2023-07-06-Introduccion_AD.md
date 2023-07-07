---
title: Introducción a Active Directory 
author: Droix3d
date: 2023-07-06
categories: [Active Directory, Introducción a Active Directory]
tags: [AD, Active Directory]
pin: true
image:
  path: ../../assets/img/commons/Active/Active.png
  alt: Active Directory
---

Active Directory Por sus siglas (AD) es un servicio que especialmente utilizan en una red de Windows, esta estructura esta de forma jerárquica distribuida donde nos permitirá organizar de forma centralizada los recursos de una organización como:

- usuarios
- computadoras
- grupos
- dispositivos de red
- recursos compartidos de archivos
- políticas de grupo
- dispositivos 

Lo que hace especial este servicio es la forma de autenticación y autorizaciones dentro de un entorno de windows (donde sabemos que dicho sistema operativo es el mas usado para la mayoría de empleados en una organización) 

Ha sido objeto de diversos ataques cada vez mayores en los últimos años. Su diseño es de forma compatible con versiones antiguas, la falla de malas configuraciones y configuraciones por defecto se puede aprovechar para moverse lateral y verticalmente dentro de una red y obtener acceso no autorizado. AD es una base de datos de solo lectura para todos los usuarios del dominio independientemente su nivel que tengan de privilegios. 

Una cuenta de usuario básica de AD sin privilegios adicionales puede enumerar la mayoría de los objetos dentro de un AD, esto es lo que hace verdaderamente un reto  proteger adecuadamente la implementación de un AD porque prácticamente cualquier usuario independiente su nivel de privilegio puede usarse para enumerar el dominio, errores de configuraciones y fallas de los servicios. 

Se puede lograr multiples ataques con una sola cuenta de usuario de dominio estándar lo que demuestra la importancia de una planificación de seguridad cuidadosa, la segmentación de red y los privilegios mínimos.
AD una de las ventajas es que la información es fácil de encontrar y usar para administradores y usuarios, Admite millones de objetos por Dominio te permite la creación de dominios adicionales a medida que va creciendo la organización 

- LDAP es la base de AD, evolucionó a lo largo de la década de los 90, adaptando protocolos como LDAP y Kerberos con elementos propietarios de Microsoft. La primera versión beta de Active Directory fue en 1997.

- En 2003 se creo una funcionalidad extendida y mejorada agregando el (Forest) característica que permite crear "contenedores" de dominios, usuarios, computadoras y otros objetos separados 

## Estructura de Directorio Activo
Es una estructura jerárquica que permite la administración centralizada de los recursos de una organización, incluidos usuarios, computadoras, grupos, dispositivos de red y recursos compartidos de archivos, políticas de grupo, servidores y estaciones de trabajo, el servicio de directorio  [los Servicios de dominio de Active Directory (AD DS)](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) brinda formas de almacenar datos, información de nombres de usuario y contraseñas y administra los derechos necesarios para que los usuarios autorizados accedan a esta información.

<mark style="background: #16BFF3A6;">AD es esencialmente una gran base de datos a la que pueden acceder todos los usuarios dentro del dominio, independientemente de su nivel de privilegio. Se puede usar una cuenta de usuario básica de AD sin privilegios adicionales para enumerar la mayoría de los objetos contenidos en AD, incluidos, entre otros:</mark>

|                                 |                                    | 
| ---------------------------------- | ------------------------------------- |
| Información del grupo de dominio   | Unidades organizativas (OU)           |
| Política de dominio predeterminada | Niveles de dominio funcional          |
| Políticas de contraseñas           | Objetos de directiva de grupos  (GPO) |
| Equipos de dominio                 | Usuarios de dominio                   |

Active Directory está organizado en una estructura de árbol jerárquico, con un bosque en la parte superior que contiene uno o más dominios, que pueden tener subdominios anidados

### Forest
Un bosque es el límite de seguridad dentro del cual todos los objetos están bajo control administrativo. Un bosque puede contener varios dominios y un dominio puede incluir más dominios secundarios o subdominios. Una estructura donde se puede acceder a los objetos del contenido (usuarios, equipos y grupos)
contiene Domain controllers,Users, Computers

En un nivel simple puede esta estructurado de la siguiente manera: 
```shell-session
TECNOC.LOCAL/
├── ADMIN.TECNOC.LOCAL
│   ├── GPOs
│   └── OU
│       └── EMPLOYEES
│           ├── COMPUTERS
│           │   └── FILE01
│           ├── GROUPS
│           │   └── System Department 
│           └── USERS
│               └── james.jones
├── CORP.TECNOC.LOCAL
└── DEV.TECNOC.LOCAL
```
`TECNOC.LOCAL`es el dominio raíz y contiene los subdominios (ya sean dominios secundarios o raíz de árbol) `ADMIN.TECNOC.LOCAL`, `CORP.TECNOC.LOCAL`, y `DEV.TECNOC.LOCAL`
También se puede observar multiples dominios secundarios debajo de cada dominio raíz 
Active Directory proporciona autenticación y autorización  dentro de un entorno de dominio de Windows.

# Active Directory Terminology

Un objeto es cualquier recurso presente dentro de un entorno de Active Directory como unidades organizativas, impresoras, usuarios, controladores de dominio, etc.

## Atributos Active Directory 
Cada objeto tiene un conjunto de atributos que se utilizan para definir las características de un objeto dado. Por ejemplo un objeto de una computadora contiene atributos con el nombre host y el nombre DNS, Todos estos atributos tendrán un nombre LDAP asociado que se puede realizar consultas LDAP  

## Esquema
Es cualquier modelo empresarial, donde se define los tipos de objetos que pueden existir en la base de datos AD , cada objeto tiene su propia información que se almacenan en atributos

## Dominio
es un grupo de objetos de computadoras, usuarios, unidades organizativas, grupos etc. Se podría pensar como que cada dominio es una ciudad diferente dentro de un estado o país y pueden operar de forma completamente independientes entre si 

## Forest 
colección de dominios de AD contiene a todos los objetos incluyendo, dominios, usuarios, grupos , equipos y objetos de directiva de grupo, un bosque puede tener uno o varios dominios 

## Árbol 
comienza en un único dominio raíz. Un bosque es una colección de árboles AD. Dos árboles en el mismo bosque no pueden compartir un nombre (espacio de nombres). Digamos que tenemos dos árboles en un bosque AD: `tecnoc.local`y `tecnoc.local`. Un dominio hijo del primero sería `corp.tecnoc.local`mientras que un dominio hijo del segundo podría ser `corp.tecnoc.local`.

## Envase
Los objetos contenedores contienen otros objetos y tienen un lugar definido en la jerarquía del sub árbol de directorios 

## Hoja 
Los objetos hoja no contienen otros objetos y se encuentran al final de la jerarquía del sub árbol 

## Identificador único global (GUID)
un GUID es un valor único de 128 bits que se asigna cuando se crea un usuario o grupo de dominio. Este valor de GUID es único en toda la empresa, similar a una dirección MAC  a cada objeto creado por Active Directory se le asigna GUID . El GUID se almacena en el `ObjectGUID`atributo, podemos consultar por su `objectGUID`use PowerShell o búsquelo especificando su nombre completo, GUID, SID o nombre de cuenta SAM.

## Principios de Seguridad
es cualquier  cosa que el sistema operativo puede autenticar, incluyendo usuarios, cuentas de computadora o inclusos procesos que se ejecutan en el contexto de una cuenta de usuario o de computadora, en AD los principios de seguridad son objetos de dominio que pueden administrar el acceso a otros recursos dentro del dominio.
Se puede tener cuentes de usuarios locales y grupos de seguridad utilizados para controlar el acceso a recursos solo en esa computadora específica. Estos no son administrados por AD sino por el **Administrador de cuentas de seguridad (SAM)**

## Identificador de seguridad (SID)
se usa como un identificador único para una entidad de seguridad o un grupo de seguridad. Cada cuenta, grupo o proceso tiene su propio SID único que, en un entorno de AD, Cuando un usuario inicia sesión, el sistema crea un token de acceso para él que contiene el SID del usuario, los derechos que se le otorgaron y los SID de los grupos de los que el usuario es miembro, se utiliza para identificar de forma única tanto usuarios como otros objetos de seguridad en un dominio de Active Directory, En resumen, el SID se utiliza para identificar de forma única usuarios, grupos y equipos en un dominio de Active Directory, y se utiliza para controlar el acceso a recursos y objetos de seguridad dentro del dominio.

## Nombre distinguidos (DN) y (RDN)
Son dos términos relacionados con la estructura jerárquica de los objetos de AD.
### DN (Distinguished Name)
Describe la ruta completa de un objeto AD, El DN es el nombre completo y único de un objeto en el árbol de AD. El DN está formado por el nombre RDN del objeto y el nombre de todos los contenedores que lo contienen, desde el objeto hasta la raíz del árbol el formato sería el siguiente

`CN=UserName,OU=Users,DC=Domain,DC=com.`

## Ejemplo DN
Supongamos que tienes un objeto de usuario llamado "Juan Pérez" en la unidad organizativa "Empleados" en el dominio "miempresa.com". El DN completo del objeto sería: "CN=Juan Pérez,OU=Empleados,DC=miempresa,DC=com".

### RDN (Relative Distinguished Name)
El nombre distinguido relativo identifica de forma única el objeto dentro de su contenedor inmediato. El RDN se compone de uno o más atributos, pero el atributo CN es uno de los más comunes y se utiliza con frecuencia en el RDN.

## Ejemplo RDN
Tomando como ejemplo el `DN "CN=Juan Pérez,OU=Empleados,DC=miempresa,DC=com", el RDN sería "CN=Juan Pérez".` Esto significa que el objeto de usuario se identifica de forma única dentro de la unidad organizativa "Empleados" por su valor de atributo CN, que en este caso es "Juan Pérez".`

## sAMAccountName
es un atributo de objeto en Active Directory sAMAccountName es un nombre de cuenta de usuario o de computadora que se utiliza para autenticar y autorizar a los usuarios y computadoras en un dominio de Active Directory. Es un nombre de cuenta NetBIOS que tiene una longitud máxima de 20 caracteres y debe ser único dentro de un dominio. (es el nombre de inicio de sesión del usuario)

## User Principal Name (UPN)
aquí otra forma de identificar  a los usuarios de AD Este atributo consta de un prefijo (el nombre de la cuenta de usuario) y un sufijo (el nombre de dominio) en el formato de `juanperez@miempresa.com`, El UPN es un identificador de inicio de sesión que se utiliza en entornos de Active Directory para proporcionar una forma más amigable de inicio de sesión para los usuarios

## Nombre principal de servicio (SPN)
un nombre principal de servicio (service principal Name)  identifica de forma única una instancia de servicio Por ejemplo, si tenemos un servidor web llamado "webserver1" que se ejecuta en un dominio de Active Directory llamado "miempresa.local", el SPN para el servicio web en este servidor podría ser algo como `"http/webserver1.miempresa.local"` o `"http/webserver1"` , La autenticación Kerberos los utiliza para asociar una instancia de un servicio con una cuenta de inicio de sesión, lo que permite que una aplicación cliente solicite el servicio para autenticar una cuenta sin necesidad de conocer el nombre de la cuenta.

## Global Catalog 
Un catálogo global es un controlador de dominio que almacena copias de TODOS los objetos en un bosque de Active Directory, El GC permite que tanto los usuarios como las aplicaciones encuentren información sobre cualquier objeto en CUALQUIER dominio en el bosque

## Nombre de dominio completo (FQDN)
se identifica por ser el nombre completo de una computadora o de un hosts Se escribe con el nombre de host y el nombre de dominio en el formato [nombre de host].[nombre de dominio].[tld].  se usa para especificar la ubicación de un objeto en la jerarquía de árbol de DNS por ejemplo  `DC01.MIEMPRESA.COM`.

## Tombstone
Una lápida es un objeto contenedor en AD que almacena objetos eliminados dentro de AD el objeto permanecerá durante un periodo de tiempo determinado, y se le conoce como Tombstone LifeTime Según la versión del sistema operativo de DC, este valor para eliminar por completo el objeto será de 60 o 180 días. Si se elimina un objeto en un dominio que no tiene una Papelera de reciclaje de AD, se convertirá en un objeto de desecho

## SYSVOL
Uno de los recursos que están muy presentes a la hora de compartir archivos a nivel de red es la carpeta SYSVOL donde almacena copias de archivos públicos en el dominio, como  configuración de políticas de grupo, secuencias de comandos de inicio/cierre de sesión y, a menudo, contiene otros tipos de secuencias de comandos que se ejecutan para realizar diversas tareas en el entorno de AD, es muy importante tomar en cuenta este directorio ya que si estamos en un pentest de Directorio Activo y podemos enumerar su contenido dentro de esta carpeta normalmente hay un archivo llamado groups.xml con un contraseña definida en variable como "cpassword" se puede usar gpp-decrypt para descifrarla en texto claro 

![](https://adsecurity.org/wp-content/uploads/2015/12/GPP-LocalAdmin-PasswordChange-XML-File-02.png)

