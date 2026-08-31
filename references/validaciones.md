# validaciones de campos CRUD

Todos los campos de formularios CRUD deben validarse en frontend y backend. Validación frontend mejora experiencia; validación backend es obligatoria y autoridad final antes de persistir datos.

## reglas generales

* Validar campos requeridos antes de enviar formulario y nuevamente en backend. No aceptar valores vacíos, `null`, ausentes o sólo espacios cuando campo sea obligatorio.
* Definir reglas desde esquema de base de datos y requerimiento: tipo, longitud máxima, nulabilidad, rango, formato, valores permitidos y unicidad cuando aplique.
* Rechazar datos inválidos en backend. Nunca corregir, truncar o convertir silenciosamente datos que cambien significado.
* Mostrar errores de backend en frontend junto al campo correspondiente; conservar valor capturado cuando sea seguro. Para error general, mostrar mensaje accesible en resumen del formulario.
* Mantener validación de frontend alineada con backend. Frontend no reemplaza backend.

## reglas dinámicas desde el esquema

Cada CRUD debe construir sus reglas a partir de metadatos declarativos del campo. No definir reglas duplicadas ni valores fijos dispersos entre vista y backend.

Cada campo debe poder declarar al menos:

* `tipo`: texto, email, entero, decimal, booleano, fecha, hora, fecha-hora, relación, lista, archivo, imagen o WYSIWYG;
* `requerido`, `nullable`, `editable`, `visible` y valor inicial;
* `min`, `max`, `longitud`, `precisión`, `escala`, patrón y valores permitidos;
* dependencias con otros campos y condición de activación;
* regla de unicidad, incluyendo campos compuestos;
* etiquetas, mensajes de error y ayuda para interfaz.

Frontend debe generar atributos, límites, ayudas y validación inmediata desde estos metadatos. Backend debe cargar mismo contrato o contrato equivalente de fuente confiable; nunca aceptar reglas, permisos ni límites enviados por navegador como autoridad.

* Si un campo cambia de tipo, longitud o requerido según otro campo, recalcular reglas, limpiar errores obsoletos y validar nuevo estado antes de guardar.
* Campo oculto o deshabilitado no debe ser validado ni persistido, salvo que backend determine que sigue siendo requerido por regla de negocio.
* Para edición, excluir registro actual al validar unicidad; para creación, no excluir ninguno.
* Validaciones entre campos deben correr en backend: fechas inicial/final, confirmación, rangos, sumas, dependencias y reglas condicionales.

Ejemplo de contrato:

```json
{
  "correo": { "tipo": "email", "requerido": true, "longitud": 150, "unico": true },
  "edad": { "tipo": "entero", "min": 0, "max": 120 },
  "fin": { "tipo": "fecha", "requerido_si": { "campo": "activo", "es": true }, "posterior_a": "inicio" }
}
```

## validación por tipo

### email

* Usar control de correo (`type="email"`) y validación de formato en frontend.
* Backend debe validar formato completo de email, longitud definida en esquema y normalización permitida por requerimiento, por ejemplo recortar espacios externos.
* Si campo es único, verificar unicidad en backend y mantener restricción única en base de datos para evitar carreras de concurrencia.

### números

* Aceptar sólo números según tipo de campo: entero, decimal, positivo, negativo, mínimo, máximo, precisión y escala definidos en esquema.
* En frontend, no permitir caracteres no numéricos. Para campos enteros, impedir también `e`, `E`, `+`, `-`, punto y coma decimal, salvo que requerimiento los autorice.
* `input type="number"` por sí solo no basta: navegadores permiten notación científica como `e`. Validar entrada con regla explícita y repetir validación en backend.
* Backend debe rechazar valores con caracteres extra, notación no permitida, `NaN`, infinito, desbordamiento y precisión mayor a columna destino.

### texto

* Definir límite de caracteres igual al máximo indicado por requerimiento y columna de base de datos.
* Frontend debe informar contador y límite, aplicar `maxlength` cuando corresponda y reportar excedentes antes de enviar.
* Backend debe contar caracteres de forma segura para Unicode y rechazar textos que excedan límite. No truncar silenciosamente.
* Aplicar formato o patrón únicamente cuando requerimiento lo defina; no usar una expresión regular genérica para todos los textos.

### booleanos

* Todo booleano debe iniciar con valor `false`, tanto en formulario como en valor por defecto de base de datos.
* Backend debe aceptar exclusivamente valores booleanos explícitos y normalizarlos antes de guardar. No interpretar texto arbitrario como verdadero.
* En actualización parcial, distinguir campo ausente de valor explícito `false` para no cambiar estado por accidente.

### WYSIWYG / texto enriquecido

* Puede aceptar HTML sólo si campo está definido como texto enriquecido.
* Sanitizar HTML en backend mediante lista permitida de etiquetas, atributos y protocolos. Eliminar scripts, manejadores de eventos como `onclick`, URLs `javascript:`, HTML peligroso y atributos no autorizados.
* Sanitizar también antes de mostrar contenido. No confiar en contenido previamente guardado.
* No usar escape de texto plano si debe conservar HTML permitido; usar sanitización con allowlist y salida segura según contexto.

### fechas, horas y fecha-hora

* Validar formato real, zona horaria, fechas imposibles y rangos permitidos.
* Validar relaciones: fecha final igual o posterior a fecha inicial, edad mínima, periodos no traslapados y fechas futuras o pasadas sólo cuando regla lo permita.
* Guardar formato canónico en backend; no depender del formato regional mostrado en interfaz.

### listas, select, radio y multiselección

* Aceptar exclusivamente valores presentes en catálogo permitido del backend. No confiar en opciones enviadas por navegador.
* Para multiselección, validar tipo de arreglo, máximo y mínimo de elementos, ausencia de duplicados y permisos sobre cada opción.
* Si catálogo depende de otro campo, volver a validar compatibilidad de combinación en backend.

### relaciones

* Validar que identificador relacionado exista, esté activo cuando aplique y sea accesible para usuario actual.
* Validar integridad referencial con restricción de base de datos cuando sea posible.
* No permitir asignar relaciones masivamente si campo no está declarado como editable.

### archivos e imágenes

* Validar tamaño máximo, cantidad, extensión permitida y tipo real detectado del contenido; no confiar sólo en nombre o MIME enviado por navegador.
* Renombrar archivo de forma segura, almacenar fuera de ruta ejecutable cuando aplique y bloquear archivos ejecutables o contenido activo no permitido.
* Para imágenes, validar dimensiones, peso y decodificación correcta. Eliminar metadatos sólo si requerimiento lo permite.

### contraseñas y datos sensibles

* No devolver valores actuales al frontend, ni incluirlos en errores, logs o auditorías.
* Validar longitud, complejidad e historial si regla de negocio lo exige. Guardar únicamente hash con algoritmo de contraseña vigente; nunca texto plano ni cifrado reversible.
* Confirmación de contraseña debe compararse en backend y no persistirse.

## seguridad de persistencia

* Usar consultas preparadas, APIs del ORM o capa de acceso con parámetros enlazados. Nunca concatenar entradas de usuario dentro de SQL.
* Validar y permitir explícitamente nombres dinámicos de tabla, columna, orden y dirección; dichos identificadores no se parametrizan como valores SQL.
* Aplicar autorización, nonce o protección CSRF y validación de permisos antes de crear, editar o eliminar registros.
* Usar lista explícita de campos asignables. Ignorar o rechazar atributos no declarados para evitar asignación masiva.
* Normalizar datos antes de validar cuando regla lo permita, por ejemplo espacios externos o mayúsculas en códigos; conservar dato original cuando normalización pueda alterar significado.
* Limitar longitud y complejidad de entrada antes de procesar expresiones, HTML, archivos o consultas costosas para evitar abuso de recursos.
* No revelar SQL, trazas, nombres internos de tablas ni detalles sensibles en mensajes mostrados al usuario.

## respuesta de errores

* Backend debe responder errores estructurados por campo y un mensaje general cuando aplique.
* Frontend debe marcar campo inválido, enlazar mensaje con control para lectores de pantalla y enfocar primer error al enviar.
* Errores deben ser claros y accionables, por ejemplo: `El correo no tiene formato válido` o `El título permite máximo 250 caracteres`.
* No mostrar un mensaje de éxito ni guardar parcialmente formulario cuando exista algún error de validación.

## comprobación obligatoria

Para cada CRUD, comprobar al menos:

* requerido vacío, ausente y sólo espacios;
* email válido e inválido;
* número válido, letras, `e`, límites y precisión;
* texto al límite permitido y con un carácter adicional;
* booleano inicial en `false` y actualización explícita a `false`;
* WYSIWYG con HTML permitido y carga maliciosa bloqueada;
* fechas imposibles, rangos invertidos y reglas entre campos;
* opción no permitida, relación inexistente o relación sin permiso;
* archivo con extensión falsa, tipo real inválido, exceso de tamaño o cantidad;
* regla condicional activada y desactivada, incluida limpieza de errores previos;
* unicidad simple y compuesta en creación y edición;
* campo no editable o no declarado enviado manualmente;
* intento de inyección SQL rechazado o tratado como valor mediante consultas parametrizadas;
* error generado en backend visible en frontend junto al campo correcto;
* datos válidos persistidos correctamente.

## Sanitización y Escapado Estándar

Toda entrada y salida de datos en los formularios CRUD debe regirse por las siguientes reglas estrictas del ecosistema de WordPress[cite: 12]:

* **Campos de Texto Plano:** Al procesar en el backend antes de guardar, utiliza `sanitize_text_field()`[cite: 12]. Para imprimir estos datos en el frontend, usa `esc_html()` para el contenido visible y `esc_attr()` si el dato se inserta dentro de atributos HTML[cite: 12].
* **Campos de URL y Enlaces:** Al guardar enlaces o rutas, sanitízalos siempre con `esc_url_raw()`[cite: 12]. Para imprimirlos en el frontend (como en atributos `href` o `src`), utiliza estrictamente `esc_url()`[cite: 12].
* **Campos de Texto Enriquecido (WYSIWYG):** Nunca uses `sanitize_text_field()` para estos campos, ya que eliminará todas las etiquetas HTML válidas[cite: 12]. Utiliza obligatoriamente `wp_kses_post()` tanto para procesar en el backend como para imprimir en el frontend, lo cual permite etiquetas de formato seguras pero bloquea inyecciones de scripts maliciosos[cite: 12].