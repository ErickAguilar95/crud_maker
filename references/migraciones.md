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
