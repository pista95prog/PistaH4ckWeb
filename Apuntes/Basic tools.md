
### Herramientas Básicas

Herramientas como SSH, Netcat, Tmux y Vim son esenciales y utilizadas a diario por la mayoría de los profesionales de la seguridad informática. Aunque estas herramientas no están diseñadas específicamente para pruebas de penetración, son fundamentales en este proceso, por lo que es importante dominarlas.

---

### Uso de SSH

**Secure Shell (SSH)** es un protocolo de red que se ejecuta por defecto en el puerto 22 y proporciona a los usuarios, como los administradores de sistemas, un método seguro para acceder de forma remota a un equipo. SSH puede configurarse con autenticación por contraseña o sin contraseña mediante autenticación por clave pública, utilizando un par de claves pública/privada de SSH. SSH puede emplearse para acceder de forma remota a sistemas en la misma red o a través de internet, facilitar conexiones a recursos en otras redes mediante el reenvío de puertos/proxying y subir o descargar archivos desde y hacia sistemas remotos.

SSH utiliza un modelo cliente-servidor, donde el usuario ejecuta una aplicación cliente SSH, como **OpenSSH**, para conectarse a un servidor SSH. Durante un ataque o evaluación en un entorno real, es común obtener credenciales en texto plano o una clave privada de SSH, que se pueden utilizar para conectar directamente a un sistema. La conexión SSH suele ser mucho más estable que una shell inversa y se puede emplear como "host de salto" para enumerar y atacar otros hosts en la red, transferir herramientas, configurar persistencia, etc.

Un ejemplo de cómo usar SSH si se obtienen credenciales sería el siguiente:

`pista95@htb[/htb]$ ssh Bob@10.10.10.10 Bob@remotehost's password: ********* Bob@remotehost#`

Además, también es posible leer claves privadas locales en un sistema comprometido o añadir nuestra clave pública para ganar acceso SSH a un usuario específico, lo que veremos más adelante.

SSH también proporciona la capacidad de mapear puertos locales de una máquina remota a nuestro localhost, lo que resulta útil en muchos escenarios, como túneles SSH para proteger conexiones.

---

### Uso de Netcat

**Netcat**, también conocido como **nc**, es una excelente herramienta de red para interactuar con puertos TCP/UDP. Se puede utilizar para múltiples propósitos durante una prueba de penetración. Su principal uso es para conectarse a shells, pero también permite interactuar con cualquier puerto abierto. Por ejemplo, podemos usar netcat para conectarnos al puerto TCP 22 y verificar qué servicio está funcionando en ese puerto:

`pista95@htb[/htb]$ netcat 10.10.10.10 22 SSH-2.0-OpenSSH_8.4p1 Debian-3`

Esta técnica se llama **Banner Grabbing** y ayuda a identificar qué servicio está corriendo en un puerto determinado. Netcat viene preinstalado en la mayoría de las distribuciones de Linux, y para máquinas con Windows, se puede descargar una versión compatible. También existe una alternativa para Windows escrita en PowerShell llamada **PowerCat**.

Netcat también puede ser utilizado para transferir archivos entre máquinas, lo que discutiremos más adelante.

Una herramienta similar a Netcat es **socat**, que tiene características adicionales como el reenvío de puertos y la conexión a dispositivos serie. Socat también puede utilizarse para mejorar una shell y obtener una TTY interactiva completa, algo clave en pruebas de penetración más avanzadas.

---

### Uso de Tmux

Los **multiplexores de terminal**, como **tmux** o **Screen**, son herramientas poderosas que amplían las capacidades de un terminal Linux, como tener múltiples ventanas dentro de una misma sesión y cambiar entre ellas. Vamos a ver algunos ejemplos de uso básico de tmux, la herramienta más común de las dos.

Si tmux no está instalado en nuestro sistema Linux, podemos instalarlo con el siguiente comando:

`pista95@htb[/htb]$ sudo apt install tmux -y`

Para iniciar tmux, simplemente escribimos `tmux` en la terminal. El comando básico para ejecutar acciones en tmux es [CTRL + B]. Para abrir una nueva ventana, presionamos [CTRL + B] y luego "C". También podemos dividir una ventana verticalmente con [CTRL + B] y [SHIFT + %], o horizontalmente con [CTRL + B] y [SHIFT + "].

Tmux es una herramienta muy útil, especialmente en sesiones prolongadas, ya que permite mantener varias tareas organizadas y, además, soporta el registro de sesiones, lo cual es crucial en compromisos técnicos.

---

### Uso de Vim

**Vim** es un editor de texto excelente para escribir código o editar archivos de texto en sistemas Linux. Una de las grandes ventajas de usar Vim es que se controla completamente con el teclado, lo que, una vez dominado, aumenta considerablemente la productividad y eficiencia al escribir o editar código. Vim suele estar instalado por defecto en la mayoría de los sistemas Linux comprometidos, por lo que aprender a usarlo es clave para editar archivos de configuración o ejecutar scripts de manera remota.

Para abrir un archivo en Vim, simplemente se agrega el nombre del archivo al comando:


`pista95@htb[/htb]$ vim /etc/hosts`

Una vez abierto un archivo, Vim comienza en **modo normal**, lo que permite navegar por el archivo. Para editarlo, presionamos la tecla **i** para entrar en el **modo de inserción**. Al terminar de editar, se presiona la tecla **esc** para regresar al modo normal. Algunos comandos útiles son:

- `x`: Corta un carácter
- `dw`: Corta una palabra
- `dd`: Corta una línea completa
- `yw`: Copia una palabra
- `yy`: Copia una línea completa
- `p`: Pega el contenido copiado

Para guardar un archivo o salir de Vim, se utiliza el comando `:` para entrar en el **modo de comandos**. Algunos comandos clave incluyen:

- `:w`: Guardar el archivo
- `:q`: Salir
- `:q!`: Salir sin guardar
- `:wq`: Guardar y salir

Vim es una herramienta extremadamente poderosa, y existen muchos atajos y comandos adicionales que pueden optimizar aún más su uso.