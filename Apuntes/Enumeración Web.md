### Enumeración Web

Al realizar el escaneo de servicios, a menudo encontramos servidores web que funcionan en los puertos 80 (HTTP) y 443 (HTTPS). Los servidores web alojan aplicaciones web (a veces más de una), que a menudo proporcionan una gran superficie de ataque y son objetivos valiosos durante una prueba de penetración. La enumeración adecuada de aplicaciones web es crucial, especialmente cuando una organización no expone muchos servicios o esos servicios están parcheados correctamente.

### GoBuster

Después de descubrir una aplicación web, es recomendable verificar si podemos descubrir archivos o directorios ocultos en el servidor web que no están destinados al acceso público. Herramientas como **ffuf** o **GoBuster** permiten realizar esta enumeración de directorios. A veces, estos archivos o directorios ocultos pueden revelar funcionalidades sensibles o páginas que exponen datos que pueden ser aprovechados para obtener acceso a la aplicación o incluso ejecutar código de forma remota en el servidor.

#### Enumeración de Directorios/Archivos

**GoBuster** es una herramienta versátil que permite realizar ataques de fuerza bruta en DNS, vhosts y directorios. También tiene funcionalidades adicionales, como la enumeración de **buckets públicos de AWS S3**. En este contexto, estamos interesados en el modo de fuerza bruta para directorios y archivos, que se especifica con el comando **dir**. A continuación, se ejecuta un escaneo simple usando la lista de palabras **common.txt**.

### Escaneo Web

bash

Copiar código

`pista95@htb[/htb]$ gobuster dir -u http://10.10.10.121/ -w /usr/share/seclists/Discovery/Web-Content/common.txt`

**Resultados del Escaneo:**
`=============================================================== Gobuster v3.0.1 by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_) =============================================================== [+] Url:            http://10.10.10.121/ [+] Threads:        10 [+] Wordlist:       /usr/share/seclists/Discovery/Web-Content/common.txt [+] Status codes:   200,204,301,302,307,401,403 [+] User Agent:     gobuster/3.0.1 [+] Timeout:        10s =============================================================== 2020/12/11 21:47:25 Starting gobuster =============================================================== /.hta (Status: 403) /.htpasswd (Status: 403) /.htaccess (Status: 403) /index.php (Status: 200) /server-status (Status: 403) /wordpress (Status: 301) =============================================================== 2020/12/11 21:47:46 Finished ===============================================================`

### Interpretación de Resultados

- **Código 200**: Indica que la solicitud de recurso fue exitosa, como en el caso de **/index.php**.
- **Código 403**: Significa que se nos prohíbe acceder al recurso, como en **/htaccess** y **/server-status**.
- **Código 301**: Indica una redirección, como en el directorio **/wordpress**, lo que significa que el recurso ha sido movido a otra ubicación.

Es importante familiarizarnos con los diferentes **códigos de estado HTTP**, ya que nos indican el resultado de las solicitudes. Los códigos HTTP se pueden encontrar en [este enlace](https://developer.mozilla.org/es/docs/Web/HTTP/Status).

### Explotación de WordPress

El escaneo identificó una instalación de **WordPress** en **/wordpress**. WordPress es el CMS (sistema de gestión de contenido) más utilizado y, debido a su popularidad, tiene una gran superficie de ataque. Al visitar **[http://10.10.10.121/wordpress](http://10.10.10.121/wordpress)** en un navegador, descubrimos que **WordPress aún está en modo de configuración**, lo que podría permitirnos obtener **ejecución remota de código (RCE)** en el servidor si logramos completarla.

### Enumeración de Subdominios DNS

Es posible que existan recursos importantes alojados en subdominios de un dominio, como paneles de administración o aplicaciones con funcionalidades adicionales que podrían ser explotadas. Para descubrir estos subdominios, podemos usar **GoBuster** con el modo DNS. A continuación, se muestra cómo realizar este proceso:

### Instalación de SecLists

Primero, es recomendable clonar el repositorio de **SecLists** de GitHub, que contiene muchas listas útiles para fuzzing y explotación.

#### Clonar SecLists:


`pista95@htb[/htb]$ git clone https://github.com/danielmiessler/SecLists`

#### Instalar SecLists (si aún no está instalado):

`pista95@htb[/htb]$ sudo apt install seclists -y`

### Configuración del Servidor DNS

Para realizar la enumeración, es útil añadir un servidor DNS, como **1.1.1.1** (servidor DNS de Cloudflare), al archivo **/etc/resolv.conf**:

`sudo nano /etc/resolv.conf`

`nameserver 1.1.1.1`

### Enumeración de Subdominios con GoBuster

Una vez que tengamos configurado el servidor DNS, podemos proceder a realizar un escaneo para enumerar los subdominios de un dominio, como **inlanefreight.com** (una compañía ficticia de logística).

#### Comando para escanear subdominios:

`pista95@htb[/htb]$ gobuster dns -d inlanefreight.com -w /usr/share/SecLists/Discovery/DNS/namelist.txt`

### Resultados del Escaneo:

`=============================================================== Gobuster v3.0.1 by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_) =============================================================== [+] Domain:     inlanefreight.com [+] Threads:    10 [+] Timeout:    1s [+] Wordlist:   /usr/share/SecLists/Discovery/DNS/namelist.txt =============================================================== 2020/12/17 23:08:55 Starting gobuster =============================================================== Found: blog.inlanefreight.com Found: customer.inlanefreight.com Found: my.inlanefreight.com Found: ns1.inlanefreight.com Found: ns2.inlanefreight.com Found: ns3.inlanefreight.com =============================================================== 2020/12/17 23:10:34 Finished ===============================================================`

### Interpretación de los Resultados

El escaneo revela varios subdominios interesantes que se pueden investigar más a fondo, como:

- **blog.inlanefreight.com**: Posiblemente un sitio de blog, que podría contener vulnerabilidades o información útil.
- **customer.inlanefreight.com**: Un subdominio potencialmente dirigido a clientes, que podría tener acceso a datos sensibles.
- **my.inlanefreight.com**: Podría ser un portal personal o de inicio de sesión.
- **ns1.inlanefreight.com**, **ns2.inlanefreight.com**, **ns3.inlanefreight.com**: Subdominios relacionados con los servidores de nombres (DNS).

### Próximos Pasos

Se recomienda investigar más sobre cada subdominio descubierto para identificar posibles vulnerabilidades. La identificación de subdominios es solo el primer paso en la enumeración web; el siguiente sería analizar los servicios que estos subdominios pueden estar ejecutando.

### Consejos para la Enumeración Web

Aquí hay algunos consejos adicionales para la enumeración web, que son útiles tanto en desafíos como **Hack The Box (HTB)** como en escenarios del mundo real. Estos métodos te permitirán obtener una visión clara de las tecnologías y configuraciones empleadas en los servidores web.

---

#### **Captura de Banners / Encabezados de Servidores Web**

Los **encabezados HTTP** del servidor web brindan una imagen clara de lo que se aloja en él. Estos encabezados pueden revelar información crucial como el servidor o framework utilizado, métodos de autenticación y posibles configuraciones de seguridad mal implementadas. Puedes usar la herramienta **cURL** para obtener los encabezados directamente desde la línea de comandos.

##### Ejemplo de captura de encabezados con cURL:

`pista95@htb[/htb]$ curl -IL https://www.inlanefreight.com`

**Salida esperada:**


`HTTP/1.1 200 OK Date: Fri, 18 Dec 2020 22:24:05 GMT Server: Apache/2.4.29 (Ubuntu) Link: <https://www.inlanefreight.com/index.php/wp-json/>; rel="https://api.w.org/" Link: <https://www.inlanefreight.com/>; rel=shortlink Content-Type: text/html; charset=UTF-8`

Esta salida muestra que el servidor usa **Apache 2.4.29** en **Ubuntu** y expone enlaces que sugieren que el sitio usa **WordPress**.

#### **Herramienta EyeWitness**

Otra herramienta útil es **EyeWitness**, que permite tomar capturas de pantalla de aplicaciones web, identificar los frameworks o servidores utilizados, y verificar credenciales por defecto.

---

### **WhatWeb**

**WhatWeb** es una herramienta de línea de comandos que se utiliza para identificar las versiones de servidores web, frameworks y aplicaciones web, lo que permite detectar posibles tecnologías y vulnerabilidades asociadas. Es muy útil para enumerar rápidamente un objetivo y descubrir detalles específicos de la infraestructura web.

#### Ejemplo de uso básico de WhatWeb:

`pista95@htb[/htb]$ whatweb 10.10.10.121`

**Salida esperada:**

`http://10.10.10.121 [200 OK] Apache[2.4.41], Country[RESERVED][ZZ], Email[license@php.net], HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.10.10.121], Title[PHP 7.4.3 - phpinfo()]`

Esta salida muestra que el servidor está ejecutando **Apache 2.4.41** en **Ubuntu Linux** y tiene un archivo **phpinfo()**, lo que podría proporcionar más detalles sobre la configuración de PHP.

#### Escaneo de red con WhatWeb:

Puedes automatizar la enumeración de aplicaciones web en una red completa con **WhatWeb** usando la opción `--no-errors` para omitir errores y escanear múltiples direcciones IP.

`pista95@htb[/htb]$ whatweb --no-errors 10.10.10.0/24`

**Salida esperada:**

`http://10.10.10.11 [200 OK] Country[RESERVED][ZZ], HTTPServer[nginx/1.14.1], IP[10.10.10.11], PoweredBy[Red,nginx], Title[Test Page for the Nginx HTTP Server on Red Hat Enterprise Linux] http://10.10.10.100 [200 OK] Apache[2.4.41], Country[RESERVED][ZZ], HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.10.10.100], Title[File Sharing Service] http://10.10.10.121 [200 OK] Apache[2.4.41], Country[RESERVED][ZZ], Email[license@php.net], HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.10.10.121], Title[PHP 7.4.3 - phpinfo()] http://10.10.10.247 [200 OK] Bootstrap, Country[RESERVED][ZZ], Email[contact@cross-fit.htb], Frame, HTML5, HTTPServer[OpenBSD httpd], IP[10.10.10.247], JQuery[3.3.1], PHP[7.4.12], Script, Title[Fine Wines], X-Powered-By[PHP/7.4.12], X-UA-Compatible[ie=edge]`

Este escaneo proporciona un resumen de los servidores web encontrados en una red, identificando tecnologías clave como **Apache**, **Nginx**, versiones de **PHP**, frameworks como **Bootstrap**, y detalles adicionales como direcciones de correo o encabezados personalizados.

---

### **Consejos Adicionales**

1. **Identifica CMS vulnerables**: Si identificas sistemas de gestión de contenido (CMS) como **WordPress** o **Joomla**, busca vulnerabilidades conocidas o versiones antiguas que puedan ser explotables.
2. **Analiza la seguridad de los encabezados HTTP**: Encabezados como `X-Frame-Options`, `Content-Security-Policy`, o `X-XSS-Protection` proporcionan información clave sobre las configuraciones de seguridad del servidor web.
3. **Automatiza el análisis con scripts**: Utiliza scripts personalizados para analizar múltiples IP o dominios a la vez, combinando herramientas como **WhatWeb**, **cURL**, y **Nmap** para obtener resultados más completos en menos tiempo.
### Certificados SSL/TLS

Los **certificados SSL/TLS** son una fuente potencialmente valiosa de información cuando se utiliza HTTPS en un servidor. Estos certificados son utilizados para establecer conexiones seguras entre el servidor y los clientes, pero también pueden revelar detalles importantes sobre la organización o el dominio al que pertenecen.

Al navegar a `https://10.10.10.121/` y ver el certificado en un navegador, podemos obtener detalles relevantes como:

- **Nombre de la organización** (quién emitió el certificado).
- **Ubicación** (ciudad, país).
- **Nombre común** (nombre del dominio).
- **Información de contacto**, como una dirección de correo electrónico.

Esta información puede ser útil para identificar posibles objetivos para ataques de **phishing** o para realizar investigaciones adicionales sobre la organización. En pruebas de penetración, si el **phishing** está dentro del alcance, los datos obtenidos del certificado pueden facilitar la creación de correos electrónicos falsificados, haciéndolos parecer legítimos.

Además, la **verificación de los certificados SSL** también puede ayudar a identificar problemas de seguridad en la configuración de HTTPS, como el uso de algoritmos criptográficos débiles, la presencia de certificados autofirmados o caducados, lo que podría significar una menor seguridad.

### Robots.txt

El archivo **robots.txt** es común en muchos sitios web. Este archivo está diseñado para informar a los motores de búsqueda como Googlebot qué recursos deben o no deben ser indexados. Sin embargo, a veces contiene información valiosa sobre la estructura interna del sitio, como directorios privados o páginas administrativas que no deben ser accesibles públicamente.

Por ejemplo, si encontramos un archivo `robots.txt` con entradas prohibidas como:

`Disallow: /private/ Disallow: /admin/`

Esto nos da pistas sobre la ubicación de páginas sensibles, que podríamos intentar acceder manualmente o usar herramientas automatizadas para investigarlas.

En el ejemplo proporcionado, al navegar a `http://10.10.10.121/private`, se revela una **página de inicio de sesión de administrador de HTB**, lo que indica la existencia de un área protegida en el servidor.

### Código Fuente

Revisar el **código fuente** de las páginas web también es una táctica importante en la **enumeración web**. Al inspeccionar el código fuente de una página (usando [CTRL + U] en la mayoría de los navegadores), a veces podemos encontrar comentarios o información oculta que no está destinada a ser pública.

En este caso, un ejemplo sería encontrar un comentario de un desarrollador en el código fuente que contiene credenciales de prueba, como:

`<!-- Credenciales de prueba para testear la aplicación: usuario: test, password: test123 -->`

Estas credenciales pueden usarse para acceder al sitio web, especialmente si los desarrolladores han olvidado eliminarlas después de las pruebas.

---

### Ampliación de la Información:

1. **Análisis de certificados SSL/TLS**: Más allá de los datos de contacto, los certificados pueden revelar la cadena de confianza, mostrando qué autoridad de certificación (CA) emitió el certificado y si se utiliza un certificado **Wildcard**, que cubre múltiples subdominios de un dominio. Esto puede ayudar a identificar qué dominios adicionales pueden estar presentes en la infraestructura de la empresa.
    
2. **Robots.txt**: Aunque los motores de búsqueda respetan este archivo, un atacante no está obligado a hacerlo. Por lo tanto, **si un recurso es lo suficientemente sensible para ser excluido del índice**, debería protegerse de otras maneras, como la autenticación, y no solo depender de su omisión en el robots.txt.
    
3. **Código fuente**: Los comentarios y configuraciones mal gestionadas en el código fuente pueden proporcionar pistas valiosas sobre la estructura interna de la aplicación. Además de credenciales, podrían encontrarse comentarios que revelan **API keys**, direcciones internas de la red, o incluso detalles sobre las tecnologías utilizadas, como versiones exactas de marcos de trabajo o bibliotecas. Esto facilita la búsqueda de vulnerabilidades específicas.
    
4. **Certificados autofirmados**: Un certificado autofirmado puede indicar que el servidor no está configurado correctamente, lo que puede aumentar la probabilidad de **ataques de intermediario (MITM)**. Además, si el certificado ha caducado, podría significar que la infraestructura no está bien mantenida.