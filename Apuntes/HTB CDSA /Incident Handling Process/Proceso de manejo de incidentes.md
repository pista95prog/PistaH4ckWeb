# Descripción general del proceso de manejo de incidentes

Ahora que estamos familiarizados con la cadena de ataque cibernético y sus etapas, podemos predecir/anticipar mejor los próximos pasos en un ataque y también sugerir medidas apropiadas contra ellos.

Al igual que la cadena de ciberataque, existen diferentes etapas al responder a un incidente, definidas como [se necesita contexto para "[se necesita contexto para " `incident handling process`]. Esto `incident handling process`define la capacidad de las organizaciones para prepararse, detectar y responder ante eventos maliciosos. Cabe destacar que este proceso es adecuado para responder a eventos de seguridad informática, pero sus etapas no se corresponden exactamente con las de la cadena de ciberataque.

Según lo define el NIST, el proceso de manejo de incidentes consta de las siguientes cuatro (4) etapas distintas: ![Diagrama de flujo del proceso de respuesta a incidentes: preparación, detección y análisis, contención, erradicación y recuperación, actividad posterior al incidente.](https://academy.hackthebox.com/storage/modules/148/handling_process.png)

Los gestores de incidentes pasan la mayor parte de su tiempo en las dos primeras etapas, `preparation`y `detection & analysis`. Aquí es donde pasamos mucho tiempo mejorándonos y buscando el siguiente evento malicioso. Cuando se detecta un evento malicioso, pasamos a la siguiente etapa y respondemos al evento `(but there should always be resources operating on the first two stages, so that there is no disruption of preparation and detection capabilities)`. Como puede ver en la imagen, el proceso no es lineal sino cíclico. El punto principal a entender en este punto es que a medida que se descubre nueva evidencia, los siguientes pasos también pueden cambiar. Es vital asegurarse de no omitir pasos en el proceso y de completar un paso antes de pasar al siguiente. Por ejemplo, si descubre diez máquinas infectadas, ciertamente no debería proceder a contener solo cinco de ellas e iniciar la erradicación mientras las cinco restantes permanecen en un estado infectado. Tal enfoque puede ser ineficaz porque, como mínimo, está notificando a un atacante que lo ha descubierto y que lo está cazando, lo que, como podría imaginar, puede tener consecuencias impredecibles.

Por lo tanto, la gestión de incidentes consta de dos actividades principales: `investigating`y `recovering`. La investigación tiene como objetivo:

- Descubra la víctima inicial del «paciente cero» y cree una cronología del incidente (en curso, aunque aún esté activa)
- Determinar qué herramientas y malware utilizó el adversario
- Documentar los sistemas comprometidos y lo que ha hecho el adversario

Tras la investigación, la actividad de recuperación implica la creación e implementación de un plan de recuperación. Una vez implementado, la empresa debe reanudar sus operaciones normales si el incidente causó alguna interrupción.

Cuando un incidente se gestiona por completo, se emite un informe que detalla la causa y el coste del mismo. Además, se realizan actividades de "lecciones aprendidas", entre otras, para comprender qué debe hacer la organización para evitar que se repitan incidentes similares.

Ahora permítanos guiarle a través de todas las etapas del `incident handling process`.