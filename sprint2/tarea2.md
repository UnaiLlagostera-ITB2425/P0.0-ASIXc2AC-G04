# Documentación Sprint 2: Servidor de Base de Datos
## 1. Estructura y creación de la base de datos
Se ha creado la base de datos crud_db y la tabla principal equipaments_educacio para almacenar los datos importados desde el CSV de equipamientos educativos.
```sql
sudo systemctl restart mysql
sudo systemctl status mysql
```
![Estructura de la base de datos]​

## 2. Gestión y reinicio del servicio MySQL
Se reinició y comprobó el estado del servicio MySQL tras los cambios de configuración para asegurar el correcto funcionamiento del servidor antes y después de las importaciones.
```sql
SHOW DATABASES;
USE crud_db;
SHOW TABLES;

![Reinicio y estado de MySQL]​
```
## 3. Conversión del archivo CSV a UTF-8
El archivo original, recibido en formato UTF-16LE, fue convertido correctamente a UTF-8 usando iconv para evitar problemas de codificación durante la importación en MySQL.
```sql
CREATE TABLE equipaments_educacio (
  register_id VARCHAR(50),
  name VARCHAR(255),
  institution_id VARCHAR(50),
  institution_name VARCHAR(255),
  created DATE,
  modified DATE,
  addresses_roadtype_id VARCHAR(50),
  addresses_roadtype_name VARCHAR(255),
  addresses_road_id VARCHAR(50),
  addresses_road_name VARCHAR(255),
  addresses_start_street_number VARCHAR(50),
  addresses_end_street_number VARCHAR(50),
  addresses_neighborhood_id VARCHAR(50),
  addresses_neighborhood_name VARCHAR(255),
  addresses_district_id VARCHAR(50),
  addresses_district_name VARCHAR(255),
  addresses_zip_code VARCHAR(20),
  addresses_town VARCHAR(255),
  addresses_main_address VARCHAR(255),
  addresses_type VARCHAR(100),
  values_id VARCHAR(50),
  values_attribute_id VARCHAR(50),
  values_category VARCHAR(255),
  values_attribute_name VARCHAR(255),
  values_value VARCHAR(255),
  values_outstanding VARCHAR(255),
  values_description TEXT,
  secondary_filters_id VARCHAR(50),
  secondary_filters_name VARCHAR(255),
  secondary_filters_fullpath VARCHAR(255),
  secondary_filters_tree VARCHAR(255),
  secondary_filters_asia_id VARCHAR(50),
  geo_epgs_25831_x VARCHAR(50),
  geo_epgs_25831_y VARCHAR(50),
  geo_epgs_4326_lat VARCHAR(50),
  geo_epgs_4326_lon VARCHAR(50),
  estimated_dates VARCHAR(255),
  start_date DATE,
  end_date DATE,
  timetable TEXT
);
```
![Conversión a UTF-8]​

## 4. Backup de la base de datos
Se creó un respaldo completo de la base de datos con mysqldump, garantizando la seguridad de los datos insertados ante cualquier eventualidad.
```sql
iconv -f UTF-16LE -t UTF-8 opendatabcn_llista-equipaments_educacio-csv.csv -o opendatabcn_utf8.csv
ls
```
![Backup de la base de datos]​

## 5. Estado activo del servicio MySQL
Comprobación del servicio con systemctl status mysql para asegurar que el servidor estaba activo y funcionando durante todas las operaciones.
```sql
LOAD DATA LOCAL INFILE '/home/bchecker/opendatabcn_utf8.csv'
INTO TABLE equipaments_educacio
FIELDS TERMINATED BY ',' ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 LINES;
```
![Comprobación de MySQL activo]​

## 6. Importación del archivo CSV
Los datos del CSV ya convertido a UTF-8 se importaron correctamente utilizando LOAD DATA LOCAL INFILE, verificando la ausencia de errores graves y la carga total de registros esperados.
```sql
SELECT COUNT(*) FROM equipaments_educacio;
SELECT name FROM equipaments_educacio LIMIT 10;
```
![Importación de datos]​

## 7. Conteo de registros importados
Se verificó el número de registros importados en la tabla equipaments_educacio, confirmando la carga completa de los datos.
```sql
mysqldump -u root -p crud_db > backup.sql
ls | grep backup
```
![Conteo de registros]​

## 8. Visualización de datos y ejemplos
Se realizó una consulta de ejemplo para comprobar que los datos principales (como el campo name) se muestran correctamente y con tildes/contenido esperado.
```sql
![Ejemplo de datos]​

## 9. Definición y comprobación de la estructura de la tabla
Se documenta la estructura final de la tabla tal como está en el proyecto, mostrando los campos y tipos de datos de cada uno.
```sql
![Creación de la tabla]​

