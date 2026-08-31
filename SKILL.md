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
* Crear una nueva sección propia dentro de `wp-admin` para cada módulo CRUD. Registrar menú principal, pantalla de listado, pantalla o formulario de alta/edición y acciones de eliminación del CRUD principal.
* Si el CRUD usa catálogos relacionados, crear también sub-secciones independientes para administrar cada catálogo. Cada catálogo debe contar con su propio CRUD completo: listar, crear, editar y eliminar, respetando permisos, validaciones, migraciones y estilos.
* Ubicar CRUD principal y CRUDs de catálogos bajo misma sección funcional del menú. Usar etiquetas, orden y jerarquía claros; no mezclar catálogo con registros principales ni reutilizar secciones ajenas.
* Mostrar en CRUD principal los catálogos como controles de relación válidos y filtrados. Un cambio en catálogo debe reflejarse sin romper integridad referencial de registros existentes.
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
* Implementarla exclusivamente según [references/cargas masivas.md](references/cargas%20masivas.md): CSV, validación previa, chunks máximos de 50, staging o transacción all-or-nothing, conciliación por clave única, progreso real, errores por fila y controles de seguridad.
* No implementar carga masiva si requerimiento la excluye explícitamente.

### 5. Trazabilidad y Auditoría

* Implementar obligatoriamente el registro de movimientos para cada acción que modifique datos (crear, actualizar, eliminar).
* Seguir estrictamente la estructura de logs, captura de contexto (IP, usuario, acción) y formato JSON descritos en [references/auditoria.md](references/auditoria.md).

### 6. Respetar estilos

* Diseñar y generar toda interfaz CRUD, validaciones, tablas, formularios, modales, carga masiva, avisos y estados usando obligatoriamente [references/estilos.md](references/estilos.md).
* Mantener convenciones, componentes, tokens y accesibilidad de WordPress. No introducir un lenguaje visual paralelo.

### 7. Comprobación final

* Comprobar migraciones `up` y `down`.
* Comprobar flujos crear, listar, editar y eliminar.
* Comprobar acceso a nueva sección de WordPress, CRUD principal y cada CRUD de catálogo relacionado.
* Ejecutar casos obligatorios de validación y carga masiva definidos en sus referencias.
* Verificar que errores de backend se muestren correctamente en frontend y que datos válidos se persistan conforme al esquema.
* Verificar que la comunicación utilice la API REST de WordPress y que los listados implementen `WP_List_Table`.
* Verificar que todo evento de creación, actualización o eliminación detone correctamente la inserción en la tabla de auditoría.
