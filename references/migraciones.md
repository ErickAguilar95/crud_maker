# migraciones

## proposito
Las migraciones tienen la finalidad de versionar todos los cambios a la base de datos, mediante steps que el plugin guarda al ejecutar migraciones

## uso 
- la estructura de los archivos son
  ```text
  <timestamp>_<name>.<action/up/down>.sql
  2026_03_01_000000_create_products_table.up.sql
  2026_03_01_000000_create_products_table.down.sql
  ```
- se crea un archivo .sql donde se agregan los queries a ejecutar
- up: guarda los quieries a ejecutar para progresar
- down: guarda los queries para revertir los cambios aplicados en up

## validaciones
- tanto up como down deben de poder ejecutarse correctamente

## dbDelta y Versionado Seguro

* **Creación y Actualización de Tablas:** Toda migración `up` que modifique esquemas debe ejecutarse utilizando estrictamente la función nativa `dbDelta()` incluyendo previamente su archivo requerido (`wp-admin/includes/upgrade.php`)[cite: 6]. Esta función crea la tabla si no existe o actualiza su estructura de forma segura si ya existe, evitando por completo el uso de sentencias destructivas como `DROP TABLE` en entornos de producción.
* **Control de Versiones de Base de Datos:** Es obligatorio implementar un sistema de versionado guardando un número de versión en la tabla `wp_options`[cite: 6]. El orquestador debe validar la versión; `dbDelta()` solo debe ejecutarse si la versión del código es superior a la guardada en la base de datos[cite: 6]. Si la validación es falsa, el script se omite, manteniendo el rendimiento del sitio al máximo[cite: 6].