# Auditoría y Trazabilidad

## Registro de Movimientos (Movement Log)

* En sistemas críticos y módulos CRUD, la trazabilidad de las acciones es obligatoria. Todo registro creado, actualizado o eliminado debe desencadenar la inserción de un evento en una tabla de auditoría (por ejemplo, `movement_log`) dentro de la misma transacción de la base de datos[cite: 4].
* **Estructura del Log:** La inserción del registro de auditoría debe ejecutarse mediante la clase `$wpdb` y capturar metadatos precisos del contexto[cite: 4]:
  * `action_key`: El identificador único de la acción (por ejemplo, `participant_registration` o `participant_status_update`)[cite: 4].
  * `entity_key`: El nombre lógico de la tabla o entidad afectada (por ejemplo, `participants`)[cite: 4].
  * `entity_id`: El ID del registro que sufrió la modificación[cite: 4].
  * `ip`: La dirección IP del operador capturada mediante `$_SERVER['REMOTE_ADDR']`[cite: 4].
  * `user_agent`: Información del navegador capturada mediante `$_SERVER['HTTP_USER_AGENT']`[cite: 4].
  * `message_es`: Descripción breve y en lenguaje natural del evento[cite: 4].
  * `meta_json`: Un arreglo codificado en formato JSON (`json_encode`) con el detalle exacto del cambio, incluyendo datos críticos como el nuevo estado, un identificador o comentarios adicionales para el historial[cite: 4].