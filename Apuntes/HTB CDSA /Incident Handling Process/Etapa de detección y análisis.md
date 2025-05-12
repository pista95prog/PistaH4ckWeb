En este punto, hemos creado procesos y procedimientos, y tenemos pautas sobre cómo actuar ante incidentes de seguridad.

Esta `detection & analysis`fase abarca todos los aspectos de la detección de incidentes, como el uso de sensores, registros y personal capacitado. También incluye el intercambio de información y conocimientos, así como el uso de inteligencia de amenazas basada en el contexto. La segmentación de la arquitectura y una comprensión clara y visibilidad de la red también son factores importantes.

Las amenazas se introducen en la organización a través de una cantidad infinita de vectores de ataque y su detección puede provenir de fuentes como:

- Un empleado que nota un comportamiento anormal
- Una alerta de una de nuestras herramientas (EDR, IDS, Firewall, SIEM, etc.)
- Actividades de caza de amenazas
- Una notificación de un tercero que nos informa que descubrieron señales de que nuestra organización estaba comprometida.

---

Es muy recomendable crear niveles de detección categorizando lógicamente nuestra red de la siguiente manera.

- Detección en el perímetro de la red (mediante firewalls, sistemas de detección/prevención de intrusiones en redes con acceso a Internet, zona desmilitarizada, etc.)
- Detección a nivel de red interna (utilizando firewalls locales, sistemas de detección/prevención de intrusiones en el host, etc.)
- Detección a nivel de endpoint (utilizando sistemas antivirus, sistemas de detección y respuesta de endpoints, etc.)
- Detección a nivel de aplicación (utilizando registros de aplicaciones, registros de servicios, etc.)

---

## Investigación inicial

Cuando se detecta un incidente de seguridad, se debe realizar una investigación inicial y establecer el contexto antes de reunir al equipo y solicitar una respuesta a incidentes a nivel de toda la organización. Piense en cómo se presenta la información si una cuenta administrativa se conecta a una dirección IP en HH:MM:SS. Sin saber qué sistema está en esa dirección IP ni a qué zona horaria se refiere, es fácil llegar a conclusiones erróneas sobre el motivo del evento. En resumen, en esta etapa debemos intentar recopilar la mayor cantidad de información posible sobre lo siguiente:

- Fecha y hora en que se reportó el incidente. Además, ¿quién lo detectó o quién lo reportó?
- ¿Cómo se detectó el incidente?
- ¿Cuál fue el incidente? ¿Phishing? ¿Indisponibilidad del sistema? Etc.
- Elaborar una lista de los sistemas afectados (si corresponde)
- Documente quién ha accedido a los sistemas afectados y qué medidas se han tomado. Anote si se trata de un incidente en curso o si la actividad sospechosa se ha detenido.
- Ubicación física, sistemas operativos, direcciones IP y nombres de host, propietario del sistema, propósito del sistema, estado actual del sistema
- (Si hay malware involucrado) Lista de direcciones IP, hora y fecha de detección, tipo de malware, sistemas impactados, exportación de archivos maliciosos con información forense sobre ellos (como hashes, copias de los archivos, etc.)

Con esa información, podemos tomar decisiones basándonos en el conocimiento adquirido. ¿Qué significa esto? Probablemente tomaríamos medidas diferentes si supiéramos que la computadora portátil del director ejecutivo está comprometida, en lugar de la de un becario.

Con la información recopilada inicialmente, podemos empezar a crear una cronología del incidente. Esta cronología nos mantendrá organizados durante todo el evento y proporcionará una visión general de lo sucedido. Los eventos de la cronología están ordenados por tiempo según su fecha de ocurrencia. Tenga en cuenta que, durante el proceso de investigación posterior, no necesariamente descubriremos evidencia en este orden temporal. Sin embargo, al ordenar la evidencia según su fecha de ocurrencia, obtendremos contexto de cada evento. La cronología también puede arrojar luz sobre si la evidencia recién descubierta forma parte del incidente actual. Por ejemplo, imaginemos que lo que creíamos que era la carga útil inicial de un ataque se descubrió posteriormente en otro dispositivo hace dos semanas. Nos encontraremos con situaciones en las que los datos que analizamos son extremadamente relevantes y con situaciones en las que los datos no están relacionados y estamos buscando en el lugar equivocado. En general, la cronología debe contener la información descrita en las siguientes columnas:

|`Date`|`Time of the event`|`hostname`|`event description`|`data source`|
|---|---|---|---|---|

Tomemos un evento y llenemos la tabla de ejemplo anterior. Se verá así:

|`Date`|`Time of the event`|`hostname`|`event description`|`data source`|
|---|---|---|---|---|
|09/09/2021|13:31 CET|SQLServer01|Se detectó la herramienta de hackers 'Mimikatz'|Software antivirus|

Como se puede inferir, la línea de tiempo se centra principalmente en el comportamiento del atacante, por lo que las actividades que se registran muestran cuándo ocurrió el ataque, cuándo se estableció una conexión de red para acceder a un sistema, cuándo se descargaron archivos, etc. Es importante asegurarse de capturar desde dónde se detectó/descubrió la actividad y los sistemas asociados con ella.

---

## Preguntas sobre la gravedad y el alcance del incidente

Al abordar un incidente de seguridad, también debemos intentar responder las siguientes preguntas para tener una idea de la gravedad y el alcance del incidente:

- ¿Cuál es el impacto de la explotación?
- ¿Cuáles son los requisitos de explotación?
- ¿Puede verse afectado algún sistema crítico para el negocio por el incidente?
- ¿Existen medidas de remediación sugeridas?
- ¿Cuántos sistemas se han visto afectados?
- ¿El exploit se está utilizando de forma activa?
- ¿Tiene el exploit alguna capacidad similar a la de un gusano?

Los dos últimos posiblemente puedan indicar el nivel de sofisticación de un adversario.

Como puedes imaginar, los incidentes de alto impacto se manejarán con prontitud y los incidentes con una gran cantidad de sistemas afectados deberán escalarse.

---

## Confidencialidad y comunicación de incidentes

Los incidentes son temas muy confidenciales y, por lo tanto, toda la información recopilada debe conservarse solo cuando sea estrictamente necesario, a menos que la legislación aplicable o una decisión de la gerencia nos indiquen lo contrario. Existen múltiples razones para ello. El adversario puede ser, por ejemplo, un empleado de la empresa, o si se ha producido una infracción, la comunicación a las partes internas y externas debe ser gestionada por la persona designada, de acuerdo con el departamento legal.

Al iniciar una investigación, estableceremos expectativas y objetivos. Estos suelen incluir el tipo de incidente ocurrido, las fuentes de evidencia disponibles y una estimación aproximada del tiempo que el equipo necesita para la investigación. Además, según el incidente, estableceremos expectativas sobre si podremos descubrir al adversario. Por supuesto, muchos de estos factores pueden cambiar a medida que la investigación evolucione y se descubran nuevas pistas. Es importante mantener informados a todos los involucrados y a la gerencia sobre cualquier avance y expectativa.

Al iniciar una investigación, nuestro objetivo es comprender qué sucedió y cómo. Para analizar los datos relacionados con el incidente de forma adecuada y eficiente, los miembros del equipo de gestión de incidentes necesitan amplios conocimientos técnicos y experiencia en el campo. Cabe preguntarse: "¿Por qué nos importa cómo ocurrió un incidente? ¿Por qué no simplemente reconstruimos los sistemas afectados y, básicamente, nos olvidamos de que ocurrió?".

Si desconocemos cómo ocurrió un incidente o qué se vio afectado, ninguna medida correctiva que tomemos garantizará que el atacante no pueda repetir sus acciones para recuperar el acceso. Si, por otro lado, sabemos exactamente cómo entró el adversario, qué herramientas utilizó y qué sistemas se vieron afectados, podemos planificar nuestra remediación para garantizar que esta ruta de ataque no se pueda replicar.

---

## La investigación

La investigación comienza con la información recopilada inicialmente (y limitada), que contiene lo que sabemos sobre el incidente hasta el momento. Con estos datos iniciales, iniciaremos un proceso cíclico de tres pasos que se repetirá una y otra vez a medida que la investigación evolucione. Este proceso incluye:

- Creación y utilización de indicadores de compromiso (IOC)
- Identificación de nuevos clientes potenciales y sistemas impactados
- Recopilación y análisis de datos de los nuevos clientes potenciales y sistemas afectados

![Diagrama de flujo que muestra el proceso de investigación: los datos de la investigación inicial conducen a los IOC, los sistemas comprometidos y la recopilación y el análisis.](https://academy.hackthebox.com/storage/modules/148/investigation_new.png)

Ahora vamos a explicar con más detalle el proceso descrito arriba.

---

### Datos de la investigación inicial

Para llegar a una conclusión, una investigación debe basarse en pistas válidas descubiertas no solo durante esta fase inicial, sino a lo largo de todo el proceso de investigación. El equipo de gestión de incidentes debe identificar nuevas pistas constantemente y no centrarse únicamente en un hallazgo específico, como una herramienta maliciosa conocida. Reducir la investigación a una actividad específica suele resultar en hallazgos limitados, conclusiones prematuras y una comprensión incompleta del impacto general.

---

### Creación y uso de IOC

Un indicador de vulnerabilidad es una señal de que se ha producido un incidente. Los IOC se documentan de forma estructurada, lo que representa los artefactos de la vulnerabilidad. Ejemplos de IOC pueden ser direcciones IP, valores hash de archivos y nombres de archivo. De hecho, dada la importancia de los IOC para una investigación, se han desarrollado lenguajes especiales como OpenIOC para documentarlos y compartirlos de forma estándar. Otro estándar ampliamente utilizado para IOC es Yara. Existen diversas herramientas gratuitas, como el Editor de IOC de Mandiant, para crear o editar IOC. Con estos lenguajes, podemos describir y utilizar los artefactos que descubrimos durante la investigación de un incidente. Incluso podemos obtener IOC de terceros si conocemos al adversario o el ataque.

Para aprovechar los IOC, tendremos que implementar una herramienta de obtención y búsqueda de IOC (nativa o de terceros, posiblemente a gran escala). Un enfoque común es utilizar WMI o PowerShell para operaciones relacionadas con IOC en entornos Windows. ¡Precaución! Durante una investigación, debemos ser extremadamente cuidadosos para evitar que las credenciales de nuestros usuarios con privilegios elevados se almacenen en caché al conectarse a sistemas (potencialmente) comprometidos (o a cualquier sistema, en realidad). Más específicamente, debemos asegurarnos de utilizar únicamente protocolos y herramientas de conexión que no almacenen en caché las credenciales tras un inicio de sesión exitoso (como WinRM). Los inicios de sesión de Windows con tipo 3 (inicio de sesión de red) no suelen almacenar en caché las credenciales en los sistemas remotos. El mejor ejemplo de "conocimiento de herramientas" que me viene a la mente es "PsExec". Cuando se utiliza "PsExec" con credenciales explícitas, estas se almacenan en caché en la máquina remota. Cuando se usa "PsExec" sin credenciales en la sesión del usuario conectado, estas no se almacenan en caché en la máquina remota. Este es un excelente ejemplo de cómo la misma herramienta deja diferentes rastros, así que tenga cuidado.

---

### Identificación de nuevos clientes potenciales y sistemas afectados

Tras buscar IOC, es de esperar encontrar coincidencias que revelen otros sistemas con los mismos indicios de vulnerabilidad. Estas coincidencias podrían no estar directamente relacionadas con el incidente que investigamos. Por ejemplo, nuestra IOC podría ser demasiado genérica. Necesitamos identificar y eliminar falsos positivos. También podríamos encontrarnos con una gran cantidad de coincidencias. En este caso, debemos priorizar aquellas en las que nos centraremos, idealmente aquellas que puedan proporcionarnos nuevas pistas tras un posible análisis forense.

---

### Recopilación y análisis de datos de los nuevos clientes potenciales y los sistemas afectados

Una vez identificados los sistemas que incluyeron nuestros IOC, recopilaremos y preservaremos el estado de dichos sistemas para su posterior análisis con el fin de descubrir nuevas pistas o responder a preguntas de investigación sobre el incidente. Dependiendo del sistema, existen múltiples enfoques sobre cómo y qué datos recopilar. En ocasiones, deseamos realizar una "respuesta en vivo" en un sistema mientras está en funcionamiento, mientras que en otros casos, podemos querer apagarlo y luego realizar un análisis en él. La respuesta en vivo es el enfoque más común, en el que recopilamos un conjunto predefinido de datos que suele contener abundantes artefactos que pueden explicar lo sucedido en un sistema. Apagar un sistema no es una decisión fácil cuando se trata de preservar información valiosa, ya que, en muchos casos, gran parte de los artefactos solo residen en la memoria RAM del equipo, que se pierde si este se apaga. Independientemente del enfoque de recopilación que elijamos, es fundamental garantizar una interacción mínima con el sistema para evitar alterar cualquier evidencia o artefacto.

Una vez recopilados los datos, es momento de analizarlos. Este suele ser el proceso que más tiempo requiere durante un incidente. El análisis de malware y el análisis forense de discos son los tipos de análisis más comunes. Cualquier pista recién descubierta y validada se añade a la cronología, que se actualiza constantemente. Cabe destacar también que el análisis forense de memoria es una función cada vez más popular y sumamente relevante al abordar ataques avanzados.

Tenga en cuenta que durante el proceso de recopilación de datos, debe realizar un seguimiento de la cadena de custodia para garantizar que los datos examinados sean admisibles ante los tribunales si se toman medidas legales contra un adversario.

Durante una investigación, descubrimos un archivo malicioso con un valor hash MD5 de 'b40f6b2c167239519fcfb2028ab2524a'. ¿Cómo se suele denominar este valor hash en las investigaciones? Formato de respuesta: Abreviatura
IOC

