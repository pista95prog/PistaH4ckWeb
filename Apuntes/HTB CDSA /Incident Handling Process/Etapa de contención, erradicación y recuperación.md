Cuando se completa la investigación y hemos comprendido el tipo de incidente y el impacto en el negocio (con base en todas las pistas recopiladas y la información reunida en la línea de tiempo), es momento de entrar en la etapa de contención para evitar que el incidente cause más daños.

---

## Contención

En esta etapa, tomamos medidas para prevenir la propagación del incidente. Dividimos las acciones en [insertar prioridad] `short-term containment`y `long-term containment`[insertar prioridad]. Es importante que las acciones de contención se coordinen y ejecuten simultáneamente en todos los sistemas. De lo contrario, corremos el riesgo de notificar a los atacantes que los estamos persiguiendo, en cuyo caso podrían cambiar sus técnicas y herramientas para persistir en el entorno.

En la contención a corto plazo, las acciones tomadas tienen un impacto mínimo en los sistemas donde se producen. Algunas de estas acciones pueden incluir la ubicación de un sistema en una VLAN separada/aislada, la desconexión del cable de red del sistema o la modificación del nombre DNS del C2 del atacante a un sistema bajo nuestro control o a uno inexistente. Estas acciones contienen el daño y brindan tiempo para desarrollar una estrategia de remediación más concreta. Además, dado que mantenemos los sistemas inalterados (en la medida de lo posible), tenemos la oportunidad de tomar imágenes forenses y preservar la evidencia si esto no se hizo ya durante la investigación (esto también se conoce como la `backup`subetapa de la etapa de contención). Si una acción de contención a corto plazo requiere el apagado de un sistema, debemos asegurarnos de que se comunique a la empresa y se otorguen los permisos correspondientes.

En las acciones de contención a largo plazo, nos centramos en acciones y cambios persistentes. Estos pueden incluir el cambio de contraseñas de usuarios, la aplicación de reglas de firewall, la instalación de un sistema de detección de intrusiones en el host, la aplicación de un parche y el apagado de los sistemas. Durante estas actividades, debemos mantener informadas a la empresa y a las partes interesadas relevantes. Tenga en cuenta que el hecho de que un sistema ya esté parcheado no significa que el incidente haya terminado. Las actividades de erradicación, recuperación y postincidente aún están pendientes.

---

## Erradicación

Una vez contenido el incidente, es necesario erradicarlo para eliminar tanto la causa raíz como lo que queda del mismo, garantizando así que el atacante esté fuera de los sistemas y la red. Algunas de las actividades de esta etapa incluyen la eliminación del malware detectado, la reconstrucción de algunos sistemas y la restauración de otros desde copias de seguridad. Durante la etapa de erradicación, podemos ampliar las actividades de contención realizadas previamente aplicando parches adicionales que no eran necesarios de inmediato. Durante la etapa de erradicación, suelen realizarse actividades adicionales de fortalecimiento del sistema (no solo en el sistema afectado, sino también en toda la red en algunos casos).

---

## Recuperación

En la etapa de recuperación, restablecemos los sistemas a su funcionamiento normal. Por supuesto, la empresa debe verificar que el sistema funcione correctamente y que contenga todos los datos necesarios. Una vez verificado todo, estos sistemas se incorporan al entorno de producción. Todos los sistemas restaurados se someterán a un registro y monitoreo exhaustivos después de un incidente, ya que los sistemas comprometidos tienden a ser objetivos de nuevo si el adversario recupera el acceso al entorno en poco tiempo. Los eventos sospechosos típicos que se deben monitorear son:

- Inicios de sesión inusuales (por ejemplo, cuentas de usuario o de servicio que nunca han iniciado sesión allí antes)
- Procesos inusuales
- Cambios en el registro en ubicaciones que suelen ser modificadas por malware

La etapa de recuperación en algunos incidentes importantes puede tardar meses, ya que suele abordarse por fases. Durante las primeras fases, el enfoque se centra en aumentar la seguridad general para prevenir futuros incidentes mediante soluciones rápidas y la eliminación de las soluciones más fáciles. Las fases posteriores se centran en cambios permanentes a largo plazo para mantener la organización lo más segura posible.
