---
name: crud-maker
description: Una habilidad para generar código CRUD (Crear, Leer, Actualizar, Eliminar) para wordpress siguiendo los estandares de GOBMX
compatibility: Se requiere tener en wordpress el plugin de migraciones, GOBMX ACF Icon Selector, Safe SVG,
---

## Propósito

Esta skill tiene como propósito facilitar la creación de código CRUD (Crear, Leer, Actualizar, Eliminar) para aplicaciones en WordPress, siguiendo los estándares establecidos por GOBMX.

## Entrada

El usuario debe proporcionar la siguiente información para generar el código CRUD:
Estructura basica en lenguaje natural de los requerimientos de la aplicación, incluyendo los campos necesarios, tipos de datos y relaciones entre ellos.
ejemplo:
```text
  Creacion de crud de tareas:
  * Catalogo de estados de tareas
  - nombre: texto, 100 caracteres, no nulo
  - descripcion: texto, 250 caracteres

  * tareas
  - Titulo: texto, 250 caracteres, no nulo
  - Descripcion: texto, 450 caracteres
  - Fecha de vencimiento: fecha, no nula
  - Estado: relacion con tabla de estados
  - Usuario asignado: relacion con tabla de usuarios
```

## Antes de comenzar:
- Valida que el plugin de Migraciones Infotec, GOBMX ACF Icon Selector y Safe SVG estén instalados y activados en tu instalación de WordPress.
- Asegúrate de tener permisos de administrador para poder crear y modificar código en tu instalación de WordPress.
- Valida que las tablas y campos que deseas crear no existan previamente en la base de datos para evitar conflictos, usa el plugin de Migraciones Infotec para crear las tablas y campos necesarios, si no existen, y si existen, asegúrate de que los nombres de los campos y tablas sean consistentes con los que deseas crear.
- si las tablas y campos ya existen, asegúrate de que los nombres de los campos y tablas sean consistentes con los que deseas crear.

## Proceso obligatorio de generación

Para cada solicitud de CRUD, seguir este orden. No omitir pasos aunque sólo se solicite interfaz o backend.

### 1. Crear migraciones

* Crear migraciones para tablas, columnas, índices, restricciones, valores por defecto y llaves foráneas requeridas por el CRUD.
* Seguir exactamente estructura, archivos `up` y `down`, reversibilidad y comprobaciones descritas en [references/migraciones.md](references/migraciones.md).
* La migración debe representar reglas persistentes del dominio: `NOT NULL`, longitudes, tipos, valores por defecto, índices únicos y relaciones cuando correspondan.
* No modificar ni eliminar tablas o datos ajenos al alcance solicitado.

### 2. Generar CRUD con validaciones

* Generar creación, consulta, edición y eliminación, con autorización y protección CSRF/nonce según contexto WordPress.
* Crear una nueva sección propia dentro de `wp-admin` para cada módulo CRUD. Registrar cinco pantallas independientes para CRUD principal: listado de todos los elementos, creación de un elemento, edición de un elemento, confirmación de eliminación y carga masiva.
* No combinar alta, edición, eliminación o carga masiva dentro de listado. Cada pantalla debe tener URL o ruta propia, título claro y acción `Regresar al listado` cuando aplique.
* Pantalla de eliminación debe identificar registro, advertir consecuencia, permitir cancelar y requerir confirmación explícita protegida por permiso y nonce. Nunca eliminar mediante petición `GET`.
* Si el CRUD usa catálogos relacionados, crear también sub-secciones independientes para administrar cada catálogo. Cada catálogo debe contar con su propio CRUD completo: listar, crear, editar y eliminar, respetando permisos, validaciones, migraciones y estilos.
* Ubicar CRUD principal y CRUDs de catálogos bajo misma sección funcional del menú. Usar etiquetas, orden y jerarquía claros; no mezclar catálogo con registros principales ni reutilizar secciones ajenas.
* Mostrar en CRUD principal los catálogos como controles de relación válidos y filtrados. Un cambio en catálogo debe reflejarse sin romper integridad referencial de registros existentes.
* En pantallas de creación y edición, renderizar campos claramente conforme a [references/estilos.md](references/estilos.md): etiqueta visible en español, requerido textual, ayuda, ancho adecuado, foco, error y accesibilidad.
* Aplicar obligatoriamente todas las reglas de [references/validaciones.md](references/validaciones.md).
* Usar contrato de reglas dinámicas derivado del esquema para que frontend y backend compartan tipo, requerido, longitud, rango, catálogo, relaciones, condiciones y mensajes.
* Validar siempre en frontend y backend. Backend es autoridad final; debe rechazar datos inválidos antes de persistirlos.
* Mostrar en frontend los errores estructurados que devuelva backend, junto al campo correspondiente y en un resumen accesible cuando aplique.
* Usar consultas preparadas o APIs con parámetros enlazados. Nunca concatenar entradas de usuario en SQL.

### 3. Uso de API REST y WP_List_Table

* **Comunicación Frontend/Backend:** Para la creación, lectura, actualización y eliminación de registros desde interfaces personalizadas, genera la comunicación consumiendo la API REST nativa de WordPress (usando rutas `/wp-json/`) mediante `apiFetch`[cite: 5]. Esto permite separar responsabilidades: el backend maneja la lógica pura de datos y el frontend gestiona la vista y los errores sin recargar la página.
* **Listados Nativos Administrativos:** Cuando se requiera mostrar listas de registros en el área de administración (`wp-admin`), utiliza obligatoriamente la clase nativa `WP_List_Table` de WordPress[cite: 6]. Esto garantiza que las tablas hereden automáticamente la paginación, las columnas ordenables, las acciones en bloque y el aspecto visual exacto del núcleo de WordPress.

### 4. Generar carga masiva

* Incluir carga masiva correspondiente a entidad CRUD cuando módulo permita crear o actualizar múltiples registros.
* Implementarla exclusivamente según [references/cargas masivas.md](references/cargas%20masivas.md): CSV, plantilla con registro de ejemplo, exportación compatible para reimportar sin edición manual, validación previa, chunks máximos de 50, staging o transacción all-or-nothing, conciliación por clave única, progreso real en modal sin navegación ni pantalla blanca, errores por fila y controles de seguridad.
* No implementar carga masiva si requerimiento la excluye explícitamente.

### 5. Trazabilidad y Auditoría

* Implementar obligatoriamente el registro de movimientos para cada acción que modifique datos (crear, actualizar, eliminar).
* Seguir estrictamente la estructura de logs, captura de contexto (IP, usuario, acción) y formato JSON descritos en [references/auditoria.md](references/auditoria.md).

### 6. Respetar estilos

* Diseñar y generar toda interfaz CRUD, validaciones, tablas, formularios, modales, carga masiva, avisos y estados usando obligatoriamente [references/estilos.md](references/estilos.md).
* Mantener convenciones, componentes, tokens y accesibilidad de WordPress. No introducir un lenguaje visual paralelo.

### 7. Comprobación final

Antes de dar por terminado desarrollo, ejecutar y reportar validación funcional en entorno de pruebas o datos desechables. Nunca usar datos de producción para estas pruebas.

* Ejecutar migraciones `up` y comprobar tablas, columnas, índices, restricciones, valores por defecto y llaves foráneas. Ejecutar `down` en entorno desechable y confirmar reversión correcta; volver a ejecutar `up` cuando sea necesario para continuar pruebas.
* Crear datos dummy válidos para CRUD principal y catálogos relacionados. Probar páginas de listado, creación, edición, eliminación confirmada y carga masiva; comprobar relaciones, permisos, auditoría y que interfaz muestre cambios persistidos.
* Ejecutar casos obligatorios de [references/validaciones.md](references/validaciones.md): requeridos, tipos, límites, reglas dinámicas, errores de backend visibles en frontend y persistencia de datos válidos.
* Probar exportación CSV con datos dummy y, cuando haya filtros, comprobar que exportación respete mismo alcance filtrado y permisos.
* Importar CSV recién exportado sin editarlo. Confirmar compatibilidad de encabezados y formatos, actualización por clave única sin duplicados, datos preservados y resultado visible en interfaz.
* Probar plantilla CSV con su único registro ficticio, archivo inválido y carga con error. Confirmar errores por fila, rollback completo y que no queden cambios parciales.
* Probar interfaz de progreso: iniciar carga, confirmar apertura de modal, URL sin redirección, avance real por chunks, resultado final y error visible sin pantalla blanca.
* Probar permisos de carga masiva con usuario autorizado y no autorizado: descargar plantilla, exportar, subir e importar. Confirmar que backend niegue endpoint directo a usuario sin capacidad y que no se genere archivo, staging ni cambios de datos.
* Comprobar acceso a nueva sección de WordPress, CRUD principal y cada CRUD de catálogo relacionado.
* Verificar que comunicación use API REST de WordPress, listados implementen `WP_List_Table` y cada creación, actualización o eliminación genere auditoría.
* Informar resultados ejecutados, entorno usado y cualquier prueba no realizada; no declarar desarrollo terminado si falla una prueba obligatoria.
