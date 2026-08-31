## cargas masivas

Cuando un módulo permita crear o actualizar múltiples registros mediante una carga masiva, implementa un flujo de importación consistente, seguro y auditable.

#### Formato obligatorio

* Toda carga masiva debe realizarse exclusivamente mediante archivos CSV.
* No implementes cargas masivas mediante JSON, XLSX, pegado libre de texto u otros formatos salvo que la solicitud lo autorice explícitamente.
* Valida antes de procesar:

  * que el archivo sea CSV;
  * que pueda abrirse correctamente;
  * que contenga encabezados;
  * que los encabezados requeridos estén presentes;
  * que no existan encabezados inválidos o ambiguos;
  * que cada fila tenga una estructura válida.
* Documenta en la interfaz las columnas requeridas, opcionales y, cuando aplique, las columnas utilizadas como clave única.
* Presenta nombres de campos, columnas, acciones, ayudas y errores en español claro y amigable. No expongas nombres técnicos de base de datos, claves internas ni identificadores de código al usuario.

#### Plantilla y registro de ejemplo obligatorios

Toda pantalla de carga masiva debe incluir dos descargas CSV generadas para la entidad y esquema vigentes:

* **Descargar plantilla:** archivo CSV con encabezados exactos y en orden válido para importación; no debe incluir registros de datos.
* **Descargar ejemplo:** archivo CSV con los mismos encabezados y un registro de ejemplo válido, ficticio y seguro para usar como referencia.

Los encabezados visibles de plantilla y ejemplo deben estar en español claro, por ejemplo `Correo electrónico`, `Fecha de vencimiento` y `Estado`. Importador debe mapearlos mediante contrato controlado a campos internos; no inferir mapeos por texto libre ni aceptar encabezados ambiguos.

Ambos archivos deben:

* reflejar columnas importables, requeridas, opcionales, longitud, formato y claves únicas actuales;
* excluir campos administrativos, de solo lectura, autogenerados, contraseñas, secretos y cualquier dato no importable;
* actualizarse cuando cambie el esquema o configuración de importación; no usar encabezados ni valores fijos desactualizados;
* usar codificación y delimitador compatibles con importador;
* ser validados mediante mismo contrato de columnas usado por proceso de carga;
* incluir, junto a descargas, indicación de cómo representar relaciones, booleanos, fechas, listas y valores permitidos cuando correspondan.

Registro de ejemplo no debe incluir datos personales, credenciales, identificadores reales ni datos de producción. Si un campo de relación requiere un catálogo existente, usar valor ficticio claramente identificado o documentar valor permitido sin inducir a una carga inválida.

#### Limpieza previa de datos

Toda pantalla de carga masiva debe incluir un switch explícito:

**Limpiar datos existentes antes de la carga**

* El switch debe estar desactivado por defecto.
* Debe explicar claramente que, al activarse, los registros existentes pertenecientes al alcance de esa importación serán eliminados antes de insertar los datos del CSV.
* La limpieza debe respetar el alcance de la entidad administrada; nunca elimines información ajena al módulo.
* La limpieza debe formar parte de la misma operación transaccional de la importación.
* Si la importación falla, cualquier eliminación realizada como parte de la limpieza también debe revertirse.
* No ejecutes la limpieza inmediatamente al activar el switch; sólo debe ocurrir cuando el usuario confirme e inicie la importación.

#### Procesamiento en chunks

Todas las cargas masivas deben procesarse lógicamente en chunks de exactamente **50 registros como máximo por bloque**.

* No cargues todo el CSV en memoria si puede procesarse incrementalmente.
* No realices commits parciales cada 50 registros.
* El tamaño del chunk controla la lectura, validación, preparación y seguimiento del progreso, pero no modifica la regla transaccional de todo-o-nada.
* El último chunk puede contener menos de 50 registros.
* Conserva el número de fila original del CSV durante todo el procesamiento para poder identificar errores posteriormente.

#### Importación transaccional

Toda carga masiva debe comportarse como una operación **all-or-nothing**.

Si cualquier registro presenta un error que impida completar la importación:

* no debe quedar ningún registro nuevo insertado;
* no debe quedar ningún registro existente actualizado;
* no debe quedar ningún registro eliminado por la opción de limpieza;
* toda modificación correspondiente a esa importación debe revertirse.

No implementes commits independientes por chunk.

Cuando la importación requiera múltiples peticiones HTTP para mostrar progreso, utiliza un flujo de preparación o staging:

1. Lee, normaliza y valida el CSV por chunks de hasta 50 registros.
2. Guarda temporalmente el estado de la importación o utiliza un mecanismo de staging perteneciente al módulo.
3. No modifiques todavía los datos definitivos.
4. Si cualquier chunk contiene errores, marca la importación como fallida, descarta el staging y conserva intactos los datos actuales.
5. Sólo después de que todos los registros hayan sido validados correctamente, ejecuta la aplicación definitiva de los cambios como una única operación transaccional.
6. Si falla la aplicación definitiva, realiza rollback completo.
7. Elimina los datos temporales de staging al finalizar, tanto en éxito como en error.

No mantengas una transacción de base de datos abierta entre distintas peticiones HTTP.

Si el almacenamiento existente no permite transacciones reales, implementa una estrategia equivalente de staging y reemplazo atómico o rollback explícito. No presentes una importación como transaccional si pueden quedar cambios parciales.

#### Creación y actualización por clave única

Cuando la solicitud indique uno o más campos únicos para la carga masiva, esos campos deben utilizarse como clave de conciliación para determinar si cada fila crea o actualiza un registro.

* Acepta una clave única simple o compuesta por varios campos.
* Normaliza y valida los valores antes de realizar comparaciones.
* Todos los campos que formen parte de la clave deben tener valor válido.
* Si la combinación de campos únicos de una fila coincide con un registro existente, actualiza ese registro.
* Si no existe coincidencia, crea un registro nuevo.
* Una coincidencia debe identificar como máximo un registro existente.
* Si una clave del CSV coincide con más de un registro existente, considera la fila como error y cancela la importación completa.
* Si el mismo valor o combinación de clave única aparece duplicado dentro del propio CSV, considera la importación inválida y no realices cambios.
* No utilices campos diferentes como criterio implícito de actualización cuando el usuario haya especificado una clave única.
* Si el usuario no especifica una clave única, no inventes una silenciosamente: utiliza únicamente el identificador natural ya definido por el dominio si existe y está documentado; de lo contrario, trata las filas como nuevos registros o documenta la limitación según corresponda al módulo solicitado.

#### Progreso y resultado de la importación

Toda carga masiva debe mostrar una interfaz de progreso que permita conocer el estado real de la operación.

Debe incluir como mínimo:

* barra de progreso;
* total de registros detectados;
* registros procesados;
* registros que serán creados;
* registros que serán actualizados;
* errores detectados.

La barra debe reflejar el avance real de los chunks procesados y no una animación basada únicamente en tiempo.

Durante la fase de validación pueden mostrarse contadores preliminares de registros a crear y actualizar. Sólo después del commit final deben presentarse como registros efectivamente creados o actualizados.

Al finalizar correctamente, muestra un resumen como mínimo con:

* total procesado;
* creados;
* actualizados;
* errores: `0`.

Si existe cualquier error, la importación debe finalizar como fallida, realizar rollback o descartar el staging y mostrar:

* total de errores;
* número de fila del CSV;
* clave única del registro, cuando esté disponible;
* campo relacionado, cuando pueda determinarse;
* detalle legible del error;
* valor recibido cuando sea seguro mostrarlo.

Los errores deben permanecer visibles al final de la carga; no dependas únicamente de avisos temporales o mensajes que desaparezcan.

Nunca muestres como creados o actualizados registros que posteriormente hayan sido revertidos.

#### Seguridad de la carga

* Verifica capacidad y nonce antes de aceptar el archivo y nuevamente antes de aplicar los cambios definitivos.
* No confíes en el nombre, MIME type o extensión enviados por el navegador como única validación.
* Sanitiza y valida cada valor según el campo de destino antes de prepararlo para persistencia.
* No permitas que valores del CSV modifiquen propiedades administrativas que no estén expresamente incluidas entre las columnas importables.
* Limita el alcance de las operaciones de creación, actualización y limpieza a la entidad administrada por el módulo.
* Escapa toda información proveniente del CSV antes de mostrarla nuevamente en la interfaz, incluidos los mensajes de error.
* Los archivos temporales y datos de staging deben eliminarse después de completar o cancelar la importación.

### Comprobación obligatoria de cargas masivas

Cuando un módulo incluya importación CSV, además de las comprobaciones generales verifica al menos:

* que únicamente acepte CSV;
* que permita descargar plantilla CSV y ejemplo CSV;
* que encabezados, instrucciones y errores se presenten en español claro, sin nombres técnicos internos;
* que plantilla tenga encabezados importables correctos y no incluya registros;
* que ejemplo incluya exactamente un registro válido, ficticio y compatible con validación;
* que plantilla y ejemplo cambien junto con columnas o reglas del esquema;
* que procese los registros en chunks de máximo 50;
* que muestre progreso real;
* que muestre contadores de creados, actualizados y errores;
* que detalle los errores por fila al finalizar;
* que una clave única existente produzca una actualización y no un duplicado;
* que una clave única inexistente produzca una creación;
* que claves duplicadas dentro del CSV sean rechazadas;
* que coincidencias ambiguas contra datos existentes sean rechazadas;
* que el switch de limpieza esté desactivado por defecto;
* que la limpieza sólo afecte a la entidad administrada;
* que un error en cualquier fila deje la base de datos exactamente en el estado anterior a la importación;
* que un error posterior a una limpieza también restaure los registros eliminados;
* que no existan commits parciales entre chunks;
* que el resumen final corresponda únicamente a operaciones realmente confirmadas;
* que versión del plugin y changelog hayan sido actualizados cuando corresponda.
