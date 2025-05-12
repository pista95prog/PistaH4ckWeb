En esta `preparation`etapa, tenemos dos objetivos distintos. El primero es establecer la capacidad de gestión de incidentes dentro de la organización. El segundo es la capacidad de proteger y prevenir incidentes de seguridad informática mediante la implementación de medidas de protección adecuadas. Estas medidas incluyen el reforzamiento de endpoints y servidores, la organización por niveles de Active Directory, la autenticación multifactor, la gestión de acceso privilegiado, etc. Si bien la protección contra incidentes no es responsabilidad del equipo de gestión de incidentes, esta actividad es fundamental para el éxito general de dicho equipo.

---

## Requisitos previos de preparación

Durante la preparación, debemos asegurarnos de tener:

- Miembros del equipo de manejo de incidentes capacitados (los miembros del equipo de manejo de incidentes pueden ser subcontratados, pero de todos modos es necesario que tengan una capacidad básica y comprensión del manejo de incidentes internamente)
- Fuerza laboral capacitada (en la medida de lo posible, a través de actividades de concientización sobre seguridad u otros medios de capacitación)
- Políticas y documentación claras
- Herramientas (software y hardware)

---

## Políticas y documentación claras

Algunas de las políticas y documentación escritas deben contener una versión actualizada de la siguiente información:

- Información de contacto y funciones de los miembros del equipo de gestión de incidentes
- Información de contacto del departamento legal y de cumplimiento, el equipo de gestión, el soporte de TI, el departamento de comunicaciones y relaciones con los medios, las fuerzas del orden, los proveedores de servicios de Internet, la gestión de las instalaciones y el equipo de respuesta a incidentes externos.
- Política, plan y procedimientos de respuesta a incidentes
- Política y procedimientos de intercambio de información sobre incidentes
- Líneas base de sistemas y redes, a partir de una imagen dorada y un entorno de estado limpio
- Diagramas de red
- Base de datos de gestión de activos de toda la organización
- Cuentas de usuario con privilegios excesivos que el equipo puede usar bajo demanda cuando sea necesario (también en sistemas críticos para el negocio, que se gestionan con las habilidades necesarias para administrar ese sistema específico). Estas cuentas de usuario suelen habilitarse cuando se confirma un incidente durante la investigación inicial y se deshabilitan una vez finalizado. También se realiza un restablecimiento obligatorio de contraseña al deshabilitar a los usuarios.
- Capacidad para adquirir hardware, software o un recurso externo sin un proceso de adquisición completo (compra urgente de hasta cierto monto). Lo último que necesita durante un incidente es esperar semanas para la aprobación de una herramienta de $500.
- Hojas de trucos de investigación forense

Algunos casos leves pueden gestionarse con relativa rapidez y sin demasiada fricción, tanto dentro como fuera de la organización. Otros casos pueden requerir la notificación a las fuerzas del orden y la comunicación externa con clientes y proveedores externos, especialmente si surgen problemas legales a raíz del incidente. Por ejemplo, una filtración de datos que afecte a los datos de clientes debe notificarse a las fuerzas del orden dentro de un plazo determinado, de acuerdo con el RGPD. Puede haber muchos requisitos de cumplimiento normativo según la ubicación o las sucursales donde se haya producido el incidente, por lo que la mejor manera de comprenderlos es discutirlos con los equipos legales y de cumplimiento normativo para cada incidente (o de forma proactiva).

Si bien es vital contar con documentación, también es importante documentar el incidente durante la investigación. Por lo tanto, durante esta etapa, también deberá establecer una capacidad eficaz para reportar. Los incidentes pueden ser extremadamente estresantes, y es fácil olvidar esta parte a medida que se desarrollan, especialmente cuando está concentrado y trabaja a toda velocidad para resolverlo lo antes posible. Procure mantener la calma, tome notas y asegúrese de que estas contengan las marcas de tiempo, la actividad realizada, su resultado y quién la realizó. En general, debe buscar respuestas a quién, qué, cuándo, dónde, por qué y cómo.

---

## Herramientas (software y hardware)

De ahora en adelante, también debemos asegurarnos de contar con las herramientas adecuadas para realizar el trabajo. Estas incluyen, entre otras:

- Una computadora portátil adicional o una estación de trabajo forense para cada miembro del equipo de gestión de incidentes, con el fin de preservar imágenes de disco y archivos de registro, realizar análisis de datos e investigar sin restricciones (sabemos que aquí se probará malware, por lo que herramientas como los antivirus deben estar deshabilitadas). Estos dispositivos deben manejarse adecuadamente y no de forma que representen riesgos para la organización.
- Herramientas de adquisición y análisis de imágenes forenses digitales
- Herramientas de captura y análisis de memoria
- Captura y análisis de respuestas en vivo
- Herramientas de análisis de registros
- Herramientas de captura y análisis de red
- Cables y conmutadores de red
- Bloqueadores de escritura
- Discos duros para imágenes forenses
- Cables de alimentación
- Destornilladores, pinzas y otras herramientas pertinentes para reparar o desmontar dispositivos de hardware si es necesario.
- Creador de indicadores de compromiso (IOC) y capacidad de buscar IOC en toda la organización
- Formularios de cadena de custodia
- software de cifrado
- Sistema de seguimiento de tickets
- Instalación segura para almacenamiento e investigación
- Sistema de manejo de incidentes independiente de la infraestructura de su organización

Muchas de las herramientas mencionadas anteriormente formarán parte de lo que se conoce como `jump bag`"siempre listo con las herramientas necesarias para recoger y partir de inmediato". Sin esta bolsa preparada, reunir todas las herramientas necesarias sobre la marcha puede llevar días o semanas antes de estar listo para responder.

Finalmente, queremos recalcar la importancia de que su sistema de documentación sea completamente independiente de la infraestructura de su organización y esté debidamente protegido. Parta de la base de que todo su dominio está comprometido y que todos los sistemas pueden quedar indisponibles. De igual manera, las comunicaciones sobre un incidente deben realizarse a través de canales ajenos a los sistemas de la organización; asuma que los adversarios tienen control total y pueden leer canales de comunicación como el correo electrónico.

Otra parte de esta `preparation`etapa es la protección contra incidentes. Si bien la protección no es necesariamente responsabilidad del equipo de gestión de incidentes, este debe conocer todas las actividades relacionadas con la protección para comprender mejor el tipo y la complejidad de un incidente y saber dónde buscar artefactos o evidencia que puedan contribuir a la investigación.

Veamos ahora algunas de las medidas de protección más recomendadas, que tienen un alto impacto de mitigación contra la mayoría de las amenazas.

---

## DMARC

[DMARC](https://dmarcly.com/blog/how-to-implement-dmarc-dkim-spf-to-stop-email-spoofing-phishing-the-definitive-guide#what-is-dmarc) es una protección de correo electrónico contra el phishing basada en los ya existentes [SPF](https://dmarcly.com/blog/how-to-implement-dmarc-dkim-spf-to-stop-email-spoofing-phishing-the-definitive-guide#what-is-spf) y [DKIM](https://dmarcly.com/blog/how-to-implement-dmarc-dkim-spf-to-stop-email-spoofing-phishing-the-definitive-guide#what-is-dkim) . La idea de DMARC es rechazar los correos electrónicos que simulan provenir de su organización. Por lo tanto, si un adversario suplanta un correo electrónico haciéndose pasar por un empleado solicitando el pago de una factura, el sistema lo rechazará antes de que llegue al destinatario. DMARC es fácil y económico de implementar; sin embargo, es fundamental realizar pruebas exhaustivas; de lo contrario (y esto ocurre a menudo), se corre el riesgo de bloquear correos electrónicos legítimos sin posibilidad de recuperarlos.

Con las reglas de filtrado de correo electrónico, puede llevar DMARC al siguiente nivel y aplicar protección adicional contra correos electrónicos que no superan la prueba DMARC procedentes de dominios ajenos. Esto es posible porque algunos sistemas de correo electrónico realizan una comprobación de DMARC e incluyen un encabezado que indica si DMARC ha sido aprobado o no. Si bien esto puede ser increíblemente eficaz para detectar correos electrónicos de phishing de cualquier dominio, requiere pruebas exhaustivas antes de su implementación en un entorno de producción. Los correos electrónicos enviados en nombre de algún servicio de envío de correo electrónico suelen tener un alto índice de falsos positivos, ya que tienden a no superar la prueba DMARC debido a la incompatibilidad de dominios.

---

## Endurecimiento de endpoints (y EDR)

Los dispositivos endpoint (estaciones de trabajo, portátiles, etc.) son los puntos de entrada de la mayoría de los ataques que enfrentamos a diario. Si consideramos que la mayoría de las amenazas se originan en internet y se dirigen a usuarios que navegan por sitios web, abren archivos adjuntos o ejecutan archivos maliciosos, un porcentaje de esta actividad se realizará desde sus endpoints corporativos.

Ya existen algunos estándares de reforzamiento de endpoints ampliamente reconocidos, siendo las líneas base de CIS y Microsoft las más populares, y estos deberían ser la base de las líneas base de reforzamiento de su organización. Algunas acciones muy importantes (que realmente funcionan) que conviene tener en cuenta y abordar son:

- Deshabilitar LLMNR/NetBIOS
- Implementar LAPS y eliminar privilegios administrativos de los usuarios regulares
- Deshabilitar o configurar PowerShell en modo "ConstrainedLanguage"
- Habilitar reglas de reducción de superficie de ataque (ASR) si usa Microsoft Defender
- Implemente la lista blanca. Sabemos que es prácticamente imposible de implementar. Considere al menos bloquear la ejecución desde carpetas con permisos de escritura (Descargas, Escritorio, AppData, etc.). Estas son las ubicaciones donde se encontrarán inicialmente los exploits y las cargas maliciosas. Recuerde también bloquear scripts como .hta, .vbs, .cmd, .bat, .js y similares. Preste atención a los archivos [LOLBin](https://lolbas-project.github.io/) al implementar la lista blanca. No los pase por alto; se utilizan como acceso inicial para eludir la lista blanca.
- Utilice firewalls basados ​​en host. Como mínimo, bloquee la comunicación entre estaciones de trabajo y el tráfico saliente a LOLBins.
- Implemente un producto EDR. Actualmente, [AMSI](https://learn.microsoft.com/en-us/windows/win32/amsi/how-amsi-helps) proporciona una gran visibilidad de los scripts ofuscados para que los productos antimalware inspeccionen el contenido antes de su ejecución. Se recomienda encarecidamente elegir únicamente productos que se integren con AMSI.

Cuando se trata de endurecimiento, `Don't let perfect be the enemy of good`.

---

## Protección de red

La segmentación de red es una técnica eficaz para evitar que una brecha se extienda a toda la organización. Los sistemas críticos para el negocio deben estar aislados y las conexiones solo deben permitirse cuando la empresa lo requiera. Los recursos internos no deben acceder directamente a Internet (a menos que estén ubicados en una DMZ).

Además, al hablar de protección de red, se deben considerar los sistemas IDS/IPS. Su eficacia se evidencia cuando se realiza la interceptación SSL/TLS, lo que permite identificar el tráfico malicioso basándose en el contenido de la red y no en la reputación de las direcciones IP, que es un método tradicional y muy ineficiente para detectar tráfico malicioso.

Además, asegúrese de que solo los dispositivos aprobados por la organización puedan conectarse a la red. Soluciones como 802.1x pueden utilizarse para reducir el riesgo de BYOD (traiga su propio dispositivo) o de que dispositivos maliciosos se conecten a la red corporativa. Si su empresa solo opera en la nube y utiliza, por ejemplo, Azure/Azure AD, puede lograr una protección similar con políticas de acceso condicional que permitan el acceso a los recursos de la organización solo si se conecta desde un dispositivo administrado por la empresa.

---

## Gestión de identidades con privilegios / MFA / Contraseñas

Actualmente, el robo de credenciales de usuarios privilegiados es la vía de escalada más común en entornos de Active Directory. Además, un error común es que los usuarios administradores tengan una contraseña débil (pero a menudo compleja) o una contraseña compartida con su cuenta de usuario habitual (que puede obtenerse mediante múltiples vectores de ataque, como el keylogging). Como referencia, una contraseña débil pero compleja es "¡Contraseña1!". Incluye mayúsculas, minúsculas, números y caracteres especiales; sin embargo, es fácilmente predecible y se puede encontrar en muchas listas de contraseñas que los atacantes emplean en sus ataques. Se recomienda enseñar a los empleados a usar frases de contraseña, ya que son más difíciles de adivinar y de forzar. Un ejemplo de una frase de contraseña fácil de recordar, pero larga y compleja, es "Me gusta mi café caliente". Si se conoce un segundo idioma, se pueden combinar palabras de varios idiomas para mayor protección.

La autenticación multifactor (MFA) es otra solución de protección de identidad que debe implementarse al menos para cualquier tipo de acceso administrativo a **TODAS** las aplicaciones y dispositivos.

---

## Escaneo de vulnerabilidades

Realice análisis continuos de vulnerabilidades en su entorno y corrija al menos las vulnerabilidades "altas" y "críticas" que descubra. Si bien el análisis puede automatizarse, las correcciones suelen requerir intervención manual. Si por alguna razón no puede aplicar parches, ¡segmente segmente los sistemas vulnerables!

---

## Capacitación de concientización del usuario

Capacitar a los usuarios para que reconozcan comportamientos sospechosos y los reporten cuando los descubran es un gran logro para nosotros. Si bien es improbable alcanzar el 100% de éxito en esta tarea, se sabe que estas capacitaciones reducen significativamente el número de ataques exitosos. Las pruebas periódicas "sorpresa" también deberían formar parte de esta capacitación, incluyendo, por ejemplo, correos electrónicos mensuales de phishing, memorias USB perdidas en el edificio de oficinas, etc.

---

## Evaluación de seguridad de Active Directory

La mejor manera de detectar configuraciones de seguridad incorrectas o vulnerabilidades críticas expuestas es analizarlas desde la perspectiva de un atacante. Realizar sus propias revisiones (o contratar a un tercero si la organización no cuenta con las habilidades necesarias) garantizará que, cuando un dispositivo endpoint se vea comprometido, el atacante no tenga la posibilidad de escalar la seguridad a privilegios elevados en la red. Cuantas más herramientas y actividad adicional genere un atacante, mayor será la probabilidad de detectarlo, así que intente eliminar las victorias fáciles y las oportunidades más fáciles en la medida de lo posible.

Active Directory tiene algunas rutas de escalamiento/errores conocidos y únicos. Además, se descubren nuevos errores con bastante frecuencia. Las evaluaciones de seguridad de Active Directory son cruciales para la seguridad del entorno en su conjunto. No asuma que los administradores de su sistema conocen todos los errores descubiertos o publicados, porque en realidad probablemente no lo estén.

---

## Ejercicios del equipo morado

Necesitamos capacitar a los gestores de incidentes y mantenerlos comprometidos. Sin duda, el mejor lugar para hacerlo es dentro del entorno de la organización. Los ejercicios del equipo morado son, en esencia, evaluaciones de seguridad realizadas por un equipo rojo que, de forma continua o puntual, informan al equipo azul sobre sus acciones, hallazgos, cualquier deficiencia de visibilidad o seguridad, etc. Estos ejercicios ayudan a identificar vulnerabilidades en una organización, a la vez que ponen a prueba la capacidad defensiva del equipo azul en términos de registro, monitorización, detección y capacidad de respuesta. Si una amenaza pasa desapercibida, existe la oportunidad de mejorar. En el caso de las amenazas detectadas, el equipo azul puede probar los manuales de estrategias y los procedimientos de gestión de incidentes para garantizar su robustez y el logro del resultado esperado.