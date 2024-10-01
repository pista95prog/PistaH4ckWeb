### Escaneo de Servicios

Estamos listos para dar el siguiente paso y comenzar a explorar una máquina. Lo primero que necesitamos es identificar el sistema operativo y cualquier servicio disponible que pueda estar ejecutándose. Un servicio es una aplicación en un equipo que realiza funciones útiles para otros usuarios o computadoras. A estas máquinas especializadas que alojan estos servicios útiles se les llama "servidores", en lugar de estaciones de trabajo, ya que permiten a los usuarios interactuar con estos servicios. Nos interesan aquellos servicios que han sido mal configurados o tienen una vulnerabilidad. En lugar de realizar las acciones esperadas, intentamos forzar al servicio a realizar una acción no intencionada que favorezca nuestros objetivos, como ejecutar un comando.

A cada computadora se le asigna una dirección IP que le permite ser identificada de manera única en la red. Los servicios en estos equipos pueden asignarse a un número de puerto para que el servicio sea accesible. Como se mencionó antes, los números de puerto van del 1 al 65,535, donde los puertos del 1 al 1,023 son reservados para servicios privilegiados. El puerto 0 está reservado en la red TCP/IP y no se usa en mensajes TCP o UDP. Si algo intenta usar el puerto 0, se asignará automáticamente al primer puerto disponible por encima del 1,024.

Para acceder a un servicio de forma remota, necesitamos conectarnos usando la dirección IP y el número de puerto correctos y utilizar un protocolo que el servicio entienda. Escanear manualmente los 65,535 puertos sería muy laborioso, por lo que existen herramientas para automatizar este proceso. Una de las más comunes es **Nmap (Network Mapper)**.

### Escaneo con Nmap

Supongamos que queremos realizar un escaneo básico contra un objetivo con IP `10.129.42.253`. Para hacer esto, tecleamos `nmap 10.129.42.253` y presionamos Enter. Esto completará rápidamente un escaneo, ya que Nmap, por defecto, solo escanea los 1,000 puertos más comunes.

**Ejemplo de escaneo básico:**


`pista95@htb[/htb]$ nmap 10.129.42.253`

Salida:


`Starting Nmap 7.80 at 2021-02-25 16:07 EST Nmap scan report for 10.129.42.253 Host is up (0.11s latency). Not shown: 995 closed ports PORT    STATE SERVICE 21/tcp  open  ftp 22/tcp  open  ssh 80/tcp  open  http 139/tcp open  netbios-ssn 445/tcp open  microsoft-ds`

#### Interpretación de los resultados

- **PORT:** Número del puerto y tipo de protocolo (en este caso TCP).
- **STATE:** Indica si el puerto está abierto, cerrado o filtrado.
- **SERVICE:** El servicio asociado al puerto. Sin embargo, Nmap solo asigna nombres genéricos de servicios, por lo que un puerto puede estar ejecutando otro servicio diferente al sugerido por el escaneo básico.

Al familiarizarnos con los puertos, notaremos que algunos están asociados a sistemas operativos específicos. Por ejemplo, el puerto 3389 indica normalmente un servicio de Escritorio Remoto en Windows, mientras que el puerto 22 (SSH) sugiere que el sistema puede ser Linux o Unix.

#### Escaneo avanzado con Nmap

Si queremos obtener más información sobre un objetivo, podemos realizar un escaneo más detallado con las opciones `-sC` (para ejecutar scripts de Nmap) y `-sV` (para detectar versiones de servicios). También podemos escanear todos los puertos con la opción `-p-`.

**Ejemplo de escaneo avanzado:**

`pista95@htb[/htb]$ nmap -sC -sV -p- 10.129.42.253`

Este escaneo detectará versiones de los servicios y otros detalles del sistema operativo.

### Banner Grabbing

El "Banner Grabbing" es una técnica útil para identificar rápidamente un servicio. Un servicio puede mostrar un banner con información sobre sí mismo una vez que se establece la conexión. Nmap puede realizar esta acción automáticamente con el siguiente comando:


`nmap -sV --script=banner <objetivo>`

También podemos hacerlo manualmente usando Netcat:

**Ejemplo de uso de Netcat:**


`pista95@htb[/htb]$ nc -nv 10.129.42.253 21`

Salida:

`(UNKNOWN) [10.129.42.253] 21 (ftp) open 220 (vsFTPd 3.0.3)`

Esto revela que el servidor está ejecutando `vsFTPd 3.0.3`.

### Uso del Protocolo FTP

FTP es un protocolo común que a menudo contiene datos interesantes. Si encontramos un servidor FTP, podemos conectarnos a él usando el comando `ftp`:

**Conexión a un servidor FTP:**


`pista95@htb[/htb]$ ftp -p 10.129.42.253`

Podemos listar los archivos disponibles en el servidor con los comandos `ls` y `cd`, y descargarlos con `get`.

### SMB (Server Message Block)

SMB es un protocolo común en sistemas Windows, y puede ser explotado para movimiento lateral en la red. Podemos utilizar Nmap para escanear un servicio SMB y detectar vulnerabilidades:

**Ejemplo de escaneo SMB:**

`nmap --script smb-os-discovery.nse -p445 <objetivo>`

Este comando interactúa con el servicio SMB para extraer información del sistema operativo.

### Escaneo con scripts de Nmap

Nmap tiene una poderosa herramienta de scripting que permite personalizar los escaneos. Un ejemplo es el escaneo de vulnerabilidades en instalaciones de Citrix:

**Ejemplo de escaneo con script de Citrix:**

`nmap --script <nombre_del_script> -p<puerto> <host>`

En este caso, el host ejecuta un sistema operativo Windows 7, y podríamos realizar más enumeraciones para confirmar si es vulnerable a **EternalBlue**. El **Metasploit Framework** incluye varios módulos relacionados con **EternalBlue** que pueden utilizarse para validar y explotar esta vulnerabilidad, como veremos en una sección próxima. Podemos realizar un escaneo contra nuestro objetivo utilizando este módulo para obtener información del servicio **SMB**. Podemos confirmar que el host ejecuta un kernel de **Linux**, la versión de **Samba** es la 4.6.2, y el nombre del host es **GS-SVCSCAN**.

### Escaneo de Servicios

`pista95@htb[/htb]$ nmap -A -p445 10.129.42.253`

**Resultados del escaneo Nmap:**

- Puerto 445/tcp abierto, servicio **Samba smbd 4.6.2**.
- Huellas digitales de sistemas operativos indican versiones de **Linux**.
- Distancia de red: 2 saltos.
- **Hostname**: GS-SVCSCAN.

El comando **nmap** también detecta que la firma del sistema operativo puede no ser precisa debido a la falta de puertos abiertos/cerrados adicionales. Además, se detecta el servicio **NetBIOS** y la firma **SMB 2.02** con la firma de mensajes activada, aunque no obligatoria.

---

### Escaneo de Shares

**SMB** permite compartir carpetas de forma remota, y estas pueden contener información sensible. Podemos utilizar la herramienta **smbclient** para enumerar y conectarnos a los recursos compartidos. En este caso, el comando **-L** lista las comparticiones disponibles, mientras que **-N** omite la solicitud de contraseña.

`pista95@htb[/htb]$ smbclient -N -L \\\\10.129.42.253`

El escaneo revela las siguientes comparticiones:

- **print$**: Controladores de impresora.
- **users**: Un recurso compartido no predeterminado.
- **IPC$**: Servicio de IPC (Samba, Ubuntu).

Intentamos conectarnos al recurso compartido **users** como usuario invitado, pero obtuvimos acceso denegado al intentar listar los archivos:

`smb: \> ls NT_STATUS_ACCESS_DENIED listing \*`

Luego, utilizamos las credenciales de **bob** con la contraseña **Welcome1** y pudimos acceder:

`pista95@htb[/htb]$ smbclient -U bob \\\\10.129.42.253\\users`

Al listar los archivos, encontramos un archivo interesante llamado **passwords.txt** en el directorio del usuario:

`smb: \bob\> ls passwords.txt                       N      156  Thu Feb 25 16:42:23 2021`

Utilizamos el comando **get** para descargar el archivo **passwords.txt**.

---

### SNMP

Las cadenas de comunidad de **SNMP** brindan información y estadísticas sobre un router o dispositivo. En las versiones 1 y 2c de **SNMP**, el control de acceso se basa en cadenas de texto plano, y si conocemos el nombre, podemos acceder a la información del dispositivo. Las versiones más antiguas no cuentan con autenticación ni cifrado.

Utilizamos **snmpwalk** para realizar consultas a un dispositivo con las cadenas de comunidad predeterminadas como **public** y **private**:


`pista95@htb[/htb]$ snmpwalk -v 2c -c public 10.129.42.253 1.3.6.1.2.1.1.5.0`

Esto reveló el nombre del host como **gs-svcscan**.

Cuando probamos con la cadena **private**, no obtuvimos respuesta:

`pista95@htb[/htb]$ snmpwalk -v 2c -c private  10.129.42.253`

Finalmente, utilizamos la herramienta **onesixtyone** para forzar un ataque de diccionario en las cadenas de comunidad y logramos identificar el dispositivo como un servidor **Linux** con un kernel **5.4.0**:

`pista95@htb[/htb]$ onesixtyone -c dict.txt 10.129.42.254`



