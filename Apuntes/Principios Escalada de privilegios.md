
Nuestro acceso inicial a un servidor remoto generalmente se realiza con el contexto de un usuario con pocos privilegios, lo que no nos da acceso completo a la máquina. Para obtener control total, necesitamos encontrar una vulnerabilidad local que nos permita escalar nuestros privilegios al usuario **root** en Linux o al usuario **administrador/SYSTEM** en Windows. Vamos a analizar algunos métodos comunes para escalar privilegios.

### **Checklists de PrivEsc**

Cuando obtenemos acceso inicial a una máquina, es crucial enumerar exhaustivamente el sistema para identificar cualquier vulnerabilidad que nos permita escalar privilegios. Existen numerosos checklists y guías disponibles en línea que ofrecen una colección de verificaciones que podemos ejecutar junto con los comandos necesarios para realizarlas.

Dos recursos excelentes son:

- **HackTricks**, que ofrece una lista detallada de escalada de privilegios para **Linux** y **Windows**.
- **PayloadsAllTheThings**, que también tiene listas para ambos sistemas operativos.

Familiarizarnos con estos comandos y técnicas es fundamental para comprender las diferentes debilidades que pueden conducir a la escalada de privilegios.

### **Scripts de Enumeración**

Muchos de estos comandos se pueden ejecutar automáticamente mediante scripts que realizan una enumeración del servidor, ejecutando comandos comunes y buscando posibles debilidades. Algunos scripts útiles incluyen:

- **LinEnum** y **linuxprivchecker** para **Linux**.
- **Seatbelt** , **JAWS**  y **Winpeas** para **Windows**.

Una herramienta muy útil para la enumeración es **PEASS (Privilege Escalation Awesome Scripts SUITE)**, que se actualiza regularmente y tiene scripts para ambos sistemas operativos.

> **Nota**: Estos scripts ejecutan muchos comandos conocidos por identificar vulnerabilidades y generan bastante "ruido", lo que puede activar el software de antivirus o sistemas de monitoreo de seguridad. Esto podría evitar que los scripts se ejecuten o incluso generar alertas sobre la posible intrusión. En algunos casos, es mejor realizar una enumeración manual para evitar levantar sospechas.

### **Ejemplo: Ejecución del Script LinPEAS**

Veamos un ejemplo de ejecución del script **LinPEAS** para Linux:


`pista95@htb[/htb]$ ./linpeas.sh`

Una vez que el script se ejecuta, comienza a recopilar información y genera un informe detallado:


`...SNIP...  Linux Privesc Checklist: https://book.hacktricks.xyz/linux-unix/linux-privilege-escalation-checklist  LEYENDA:   ROJO/AMARILLO: 99% de ser vector de escalada de privilegios   ROJO: Debes prestarle atención   LightCyan: Usuarios con consola   Azul: Usuarios sin consola y dispositivos montados   Verde: Información común (usuarios, grupos, SUID/SGID, montajes, scripts .sh, cronjobs)   LightMangenta: Tu nombre de usuario  ====================================( Información básica )===================================== OS: Linux versión 3.9.0-73-generic User & Groups: uid=33(www-data) gid=33(www-data) groups=33(www-data) ...SNIP...`

Como podemos ver, el script recolecta información y la presenta en un formato fácil de interpretar. Debemos prestar atención a las vulnerabilidades resaltadas en **rojo** o **amarillo**, ya que son posibles vectores de escalada de privilegios.


### **Exploits de Kernel**

Cuando encontramos un servidor que ejecuta un sistema operativo antiguo, una de las primeras cosas que debemos verificar son las **vulnerabilidades del kernel** que pueden existir. Si el servidor no está actualizado con los últimos parches de seguridad, es probable que sea vulnerable a exploits conocidos en versiones no parcheadas de **Linux** y **Windows**.

Por ejemplo, si vemos que un servidor está ejecutando **Linux versión 3.9.0-73-generic**, podríamos buscar vulnerabilidades conocidas para esa versión específica. Una búsqueda rápida en Google o mediante herramientas como **searchsploit** puede revelar un exploit conocido como **CVE-2016-5195** o **DirtyCow**, que es un exploit que afecta a versiones antiguas de Linux.

#### **DirtyCow (CVE-2016-5195)**

DirtyCow es una vulnerabilidad en el manejo de la copia en escritura (copy-on-write) del kernel de Linux. A través de esta vulnerabilidad, un atacante local puede escribir en archivos de solo lectura y, potencialmente, obtener privilegios de root.

Pasos para aprovechar un exploit de kernel como DirtyCow:

1. Identificar la versión del kernel:
    `uname -r`
    
2. Buscar exploits en bases de datos públicas o usar herramientas como **searchsploit**
    
3. Descargar y ejecutar el exploit con cuidado, ya que puede causar inestabilidad en el sistema:
    - Googlear el exploit o descargarlo desde una base de datos pública como **Exploit-DB**.
    - Compilarlo y ejecutarlo en el servidor.

#### **Kernel Exploits en Windows**

El mismo principio aplica a **Windows**. Las versiones desactualizadas de Windows pueden tener vulnerabilidades críticas que permiten la escalada de privilegios. Al identificar la versión del sistema operativo, podemos buscar vulnerabilidades conocidas mediante herramientas como **searchsploit** o **Google**. Por ejemplo, vulnerabilidades como **EternalBlue** y otras similares han sido ampliamente utilizadas para explotar versiones antiguas de Windows.

#### **Consideraciones de Estabilidad**

Es importante recordar que los exploits de kernel pueden causar **inestabilidad en el sistema**. Ejecutarlos en un entorno de producción sin las precauciones necesarias puede provocar caídas del servidor o pérdida de datos. Siempre es recomendable probar estos exploits en un entorno de laboratorio antes de ejecutarlos en sistemas reales. Además, obtener la aprobación de los clientes y coordinar las acciones es crucial para evitar problemas.

---

### **Software Vulnerable**

Además de los exploits de kernel, debemos inspeccionar el software instalado en el sistema. A menudo, el software de terceros tiene vulnerabilidades que pueden aprovecharse para escalar privilegios o tomar control del sistema.

- En **Linux**, podemos usar el comando `dpkg -l` para listar todo el software instalado

- En **Windows**, podemos explorar el directorio **C:\Program Files** para ver qué aplicaciones están instaladas.

Es útil buscar versiones antiguas del software, ya que a menudo contienen vulnerabilidades no parcheadas. Una búsqueda rápida en Google o en bases de datos de vulnerabilidades puede revelar exploits públicos para dicho software.

### **User Privileges in Privilege Escalation**

Después de ganar acceso a un servidor, uno de los aspectos críticos a considerar es qué **privilegios** tiene el usuario con el que hemos accedido. Si se nos permite ejecutar comandos específicos como root (o como otro usuario), podemos ser capaces de escalar nuestros privilegios a **root/system** o acceder como un usuario diferente. A continuación se detallan algunas formas comunes de explotar ciertos privilegios de usuario:

#### **1. Sudo**

El comando **sudo** en Linux permite a un usuario ejecutar comandos como otro usuario, normalmente el usuario root. Esto se utiliza para permitir que los usuarios con menos privilegios ejecuten ciertos comandos que requieren permisos elevados sin darles acceso completo al usuario root.

- Para verificar los privilegios de **sudo** que tiene un usuario, se puede usar el siguiente comando:

    `sudo -l`
    
- **Ejemplo**:

    
    `pista95@htb[/htb]$ sudo -l  [sudo] password for user1: ...SNIP...  User user1 may run the following commands on ExampleServer:     (ALL : ALL) ALL`
    
    En este caso, la salida indica que el usuario `user1` puede ejecutar **todos** los comandos con sudo, lo que nos otorga acceso completo. Para escalar a root, simplemente ejecutamos:
    
    `pista95@htb[/htb]$ sudo su -`
    
    Luego podemos verificar el usuario actual:
    
    `[sudo] password for user1: whoami root`
#### **2. SUID**

Los archivos **SUID** (Set User ID) permiten a un usuario ejecutar un archivo con los privilegios del propietario del archivo, que a menudo es el usuario root. Esto significa que si un archivo tiene el bit SUID establecido, cualquier usuario puede ejecutarlo con los privilegios del usuario propietario.

- Para encontrar archivos SUID, podemos utilizar el siguiente comando:
    
    `find / -perm -4000 -ls 2>/dev/null`
    
- **Ejemplo**:
    
    `# Buscando archivos SUID pista95@htb[/htb]$ find / -perm -4000 -ls 2>/dev/null`
    
Si encontramos un archivo SUID que tiene una vulnerabilidad conocida o que permite la ejecución de comandos, podríamos utilizarlo para escalar privilegios.

La entrada **NOPASSWD** muestra que el comando `/bin/echo` puede ejecutarse sin una contraseña. Esto sería útil si obtenemos acceso al servidor a través de una vulnerabilidad y no tenemos la contraseña del usuario. Como se indica "user", podemos ejecutar `sudo` como ese usuario y no como root. Para hacerlo, podemos especificar el usuario con `-u user`:

**Escalación de Privilegios**
`pista95@htb[/htb]$ sudo -u user /bin/echo Hello World!`
#### **3. Windows Token Privileges**

En sistemas Windows, los **privilegios de token** permiten a un usuario ejecutar acciones específicas en el sistema. Los usuarios pueden tener diferentes niveles de privilegios asignados a su token de seguridad, lo que les permite realizar ciertas acciones sin ser administradores.

- Podemos utilizar herramientas como **PowerUp** o **Windows Privilege Escalation Awesome Scripts** para enumerar los privilegios de un usuario en un sistema Windows.

#### **Ejemplo de Escalación de Privilegios en Windows**:

1. **Ver Privilegios**:
    
    - Usa `whoami /priv` para ver los privilegios disponibles para el usuario actual.

2. **Escalar Privilegios**:
    
    - Si vemos que el usuario tiene un privilegio especial, como **SeImpersonatePrivilege**, podemos buscar exploits conocidos que utilicen este privilegio para escalar a un nivel más alto.

**Tareas Programadas**  
En Linux y Windows, existen métodos para ejecutar scripts a intervalos específicos para llevar a cabo una tarea. Algunos ejemplos son tener un escaneo de antivirus ejecutándose cada hora o un script de respaldo que se ejecuta cada 30 minutos. Generalmente, hay dos formas de aprovechar las tareas programadas (Windows) o cron jobs (Linux) para escalar nuestros privilegios:

1. Añadir nuevas tareas programadas/cron jobs.
2. Engañarlas para ejecutar un software malicioso.

La forma más sencilla es verificar si se nos permite agregar nuevas tareas programadas. En Linux, una forma común de mantener tareas programadas es a través de Cron Jobs. Existen directorios específicos que podemos utilizar para añadir nuevos cron jobs si tenemos permisos de escritura sobre ellos. Estos incluyen:

- `/etc/crontab`
- `/etc/cron.d`
- `/var/spool/cron/crontabs/root`

Si podemos escribir en un directorio llamado por un cron job, podemos escribir un script bash con un comando de reverse shell, el cual debería enviarnos una reverse shell cuando se ejecute.

**Credenciales Expuestas**  
A continuación, podemos buscar archivos que podamos leer y ver si contienen alguna credencial expuesta. Esto es muy común en archivos de configuración, archivos de registro y archivos de historial de usuarios (bash_history en Linux y PSReadLine en Windows). Los scripts de enumeración que discutimos al principio suelen buscar contraseñas potenciales en archivos y nos las proporcionan, como se muestra a continuación:

  `Escalación de Privilegios ...SNIP... [+] Buscando contraseñas en archivos de configuración PHP [+] Encontrando contraseñas dentro de logs (límite 70) ...SNIP... /var/www/html/config.php: $conn = new mysqli(localhost, 'db_user', 'password123');`

Como podemos ver, la contraseña de la base de datos 'password123' está expuesta, lo que nos permitiría iniciar sesión en las bases de datos MySQL locales y buscar información interesante. También podemos verificar el **Reuso de Contraseñas**, ya que el usuario del sistema puede haber utilizado su contraseña para las bases de datos, lo que podría permitirnos usar la misma contraseña para cambiar a ese usuario, de la siguiente manera:

  `Escalación de Privilegios pista95@htb[/htb]$ su -  Contraseña: password123 whoami  root`

También podríamos utilizar las credenciales del usuario para iniciar sesión en el servidor como ese usuario.

Aquí tienes la traducción:

---

**Claves SSH**  
Finalmente, hablemos sobre las claves SSH. Si tenemos acceso de lectura al directorio .ssh de un usuario específico, podemos leer sus claves privadas de SSH que se encuentran en /home/user/.ssh/id_rsa o /root/.ssh/id_rsa, y usarlas para iniciar sesión en el servidor. Si podemos leer el directorio /root/.ssh/ y podemos leer el archivo id_rsa, podemos copiarlo a nuestra máquina y usar la opción -i para iniciar sesión con él:

  `Escalación de Privilegios pista95@htb[/htb]$ vim id_rsa pista95@htb[/htb]$ chmod 600 id_rsa pista95@htb[/htb]$ ssh root@10.10.10.10 -i id_rsa  root@10.10.10.10#`

Ten en cuenta que usamos el comando 'chmod 600 id_rsa' en la clave después de haberla creado en nuestra máquina para cambiar los permisos del archivo a uno más restrictivo. Si las claves SSH tienen permisos laxos, es decir, si pueden ser leídas por otras personas, el servidor SSH evitará que funcionen.

Si nos encontramos con acceso de escritura al directorio .ssh de un usuario, podemos colocar nuestra clave pública en el directorio SSH del usuario en /home/user/.ssh/authorized_keys. Esta técnica se utiliza generalmente para obtener acceso SSH después de haber obtenido un shell como ese usuario. La configuración actual de SSH no aceptará claves escritas por otros usuarios, por lo que solo funcionará si ya hemos tomado el control de ese usuario. Primero debemos crear una nueva clave con ssh-keygen y la opción -f para especificar el archivo de salida:

  `Escalación de Privilegios pista95@htb[/htb]$ ssh-keygen -f key  Generating public/private rsa key pair. Enter passphrase (empty for no passphrase): ***** Enter same passphrase again: *****  Your identification has been saved in key Your public key has been saved in key.pub The key fingerprint is: SHA256:...SNIP... user@parrot The key's randomart image is: +---[RSA 3072]----+ |   ..o.++.+      | ...SNIP... |     . ..oo+.    | +----[SHA256]-----+`

Esto nos dará dos archivos: key (que usaremos con ssh -i) y key.pub, que copiaremos a la máquina remota. Copiemos key.pub, luego en la máquina remota, la agregaremos en /root/.ssh/authorized_keys:

  `Escalación de Privilegios user@remotehost$ echo "ssh-rsa AAAAB...SNIP...M= user@parrot" >> /root/.ssh/authorized_keys`

Ahora, el servidor remoto debería permitirnos iniciar sesión como ese usuario utilizando nuestra clave privada:

  `Escalación de Privilegios pista95@htb[/htb]$ ssh root@10.10.10.10 -i key  root@remotehost#` 

Como podemos ver, ahora podemos iniciar sesión como el usuario root. Los módulos de Escalación de Privilegios en Linux y Windows entran en más detalles sobre cómo usar cada uno de estos métodos para la Escalación de Privilegios, así como muchos otros.


Laboratorio: 
user1@ng-450064-gettingstartedprivesc-c7kg2-67bc8db6c6-kdhzh:/home/user2$ sudo user
user1@ng-450064-gettingstartedprivesc-c7kg2-67bc8db6c6-kdhzh:/home/user2$ sudo -l
Matching Defaults entries for user1 on
    ng-450064-gettingstartedprivesc-c7kg2-67bc8db6c6-kdhzh:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User user1 may run the following commands on
        ng-450064-gettingstartedprivesc-c7kg2-67bc8db6c6-kdhzh:
    (user2 : user2) NOPASSWD: /bin/bash
user1@ng-450064-gettingstartedprivesc-c7kg2-67bc8db6c6-kdhzh:/home/user2$ sudo -u user2 /bin/bash
user2@ng-450064-gettingstartedprivesc-c7kg2-67bc8db6c6-kdhzh:~$ whoami
user2
user2@ng-450064-gettingstartedprivesc-c7kg2-67bc8db6c6-kdhzh:~$ 

cat /root/.ssh/id_rsa y vemos la clave privada del usuario root
Nos la copiamos y creamos un archivo llamado id_rsa y entramos con ssh especificando la clave privada:
Para poder ejecutarlo bien, tenemos que hacer un sudo chmod 600 a la clave id_rsa.
ssh root@94.237.56.229 -p 44342 -i id_rsa 
