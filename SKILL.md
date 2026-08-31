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
- Valida que el plugin de migraciones, GOBMX ACF Icon Selector y Safe SVG estén instalados y activados en tu instalación de WordPress.
- Asegúrate de tener permisos de administrador para poder crear y modificar código en tu instalación de WordPress.
- Valida que las tablas y campos que deseas crear no existan previamente en la base de datos para evitar conflictos, usa el plugin de migraciones para crear las tablas y campos necesarios, si no existen, y si existen, asegúrate de que los nombres de los campos y tablas sean consistentes con los que deseas crear.
- si las tablas y campos ya existen, asegúrate de que los nombres de los campos y tablas sean consistentes con los que deseas crear.
