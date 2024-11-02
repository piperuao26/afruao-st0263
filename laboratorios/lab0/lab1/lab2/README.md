# Scripts/comandos para lab 3-2

### Usuarios
ingresar como usuario de hadoop
```
username: hadoop
password: ********
```

### Archivos de trabajo
Ubicación temporal en HDFS para los archivos hdi-data.csv y export-data.csv:
```
/user/hadoop/datasets/onu/hdi
```

### Gestión (DDL) y Consultas (DQL)
Crear la tabla HDI en HDFS usando Beeline
```
# tabla manejada por hive: /user/hive/warehouse
$ beeline
use usernamedb;
CREATE TABLE HDI (id INT, country STRING, hdi FLOAT, lifeex INT, mysch INT, eysch INT, gni INT) 
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
STORED AS TEXTFILE;
```

### Cargar datos a la tabla en HDFS
Opción 1: Copiar datos directamente hacia HDFS
```
$ hdfs dfs -put hdfs:///user/hadoop/datasets/onu/hdi-data.csv hdfs:///user/hive/warehouse/usernamedb.db/hdi
```

Opción 2: Cargar datos desde Hive
Primero, otorgar permisos completos al directorio:
```
$ hdfs dfs -chmod -R 777 /user/hadoop/datasets/onu/
$ beeline
0: jdbc:hive2://sandbox-hdp.hortonworks.com:2> LOAD DATA INPATH '/user/hadoop/datasets/onu/hdi-data.csv' INTO TABLE HDI;
```

### Crear una tabla externa en HDFS
```
use usernamedb;
CREATE EXTERNAL TABLE HDI (id INT, country STRING, hdi FLOAT, lifeex INT, mysch INT, eysch INT, gni INT) 
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
STORED AS TEXTFILE 
LOCATION '/user/hadoop/datasets/onu/hdi/';
```

### Crear la tabla HDI en EMR/S3/Hue/Hive
```
# tabla externa en S3: 
use usernamedb;
CREATE EXTERNAL TABLE HDI (id INT, country STRING, hdi FLOAT, lifeex INT, mysch INT, eysch INT, gni INT) 
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
STORED AS TEXTFILE 
LOCATION 's3://afruao-datasets/onu/hdi/';
```

### Consultas y Cálculos sobre la tabla HDI
Mostrar tablas y describir la tabla HDI
```
use usernamedb;
show tables;
describe hdi;
```
### Consultar todos los datos de HDI
```
select * from hdi;
```

### Consultar países con GNI mayor a 2000
```
select country, gni from hdi where gni > 2000;
```

### Ejecutar un JOIN en Hive
Crear la tabla EXPO en Hive
Obtener los datos base export-data.csv y ubicarlos en 'datasets'.
```
use usernamedb;
CREATE EXTERNAL TABLE EXPO (country STRING, expct FLOAT) 
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
STORED AS TEXTFILE 
LOCATION 's3://afruao-datasets/onu/export/';
```

### Realizar el JOIN entre las tablas HDI y EXPO
```
SELECT h.country, gni, expct FROM HDI h JOIN EXPO e ON (h.country = e.country) WHERE gni > 2000;
```

### WORDCOUNT EN HIVE
Crear la tabla docs en Hive para WordCount
Alternativa 1: Usar HDFS
```
use usernamedb;
CREATE EXTERNAL TABLE docs (line STRING) 
STORED AS TEXTFILE 
LOCATION 'hdfs://localhost/user/hadoop/datasets/gutenberg-small/';
```

Alternativa 2: Usar S3
```
CREATE EXTERNAL TABLE docs (line STRING) 
STORED AS TEXTFILE 
LOCATION 's3://hortegag-datasets/gutenberg-small/';
```

### WordCount ordenado por palabra
```
SELECT word, count(1) AS count FROM (SELECT explode(split(line,' ')) AS word FROM docs) w 
GROUP BY word 
ORDER BY word DESC LIMIT 10;
```

### WordCount ordenado por frecuencia de menor a mayor
```
SELECT word, count(1) AS count FROM (SELECT explode(split(line,' ')) AS word FROM docs) w 
GROUP BY word 
ORDER BY count DESC LIMIT 10;
```

# Reto: Almacenar los resultados de WordCount en una tabla

Para almacenar los resultados de un WordCount en una tabla en Hive, puedes seguir estos pasos. Esto te permitirá guardar el conteo de frecuencia de palabras en una tabla para futuras consultas.

## 1. Crear la Tabla para Almacenar Resultados

Primero, crea una nueva tabla en Hive para almacenar los resultados del WordCount. Esta tabla contendrá dos columnas: `word` y `count`.

```sql
CREATE TABLE wordcount_results (
    word STRING,
    count INT
)
STORED AS TEXTFILE;
```

## 2. Insertar Resultados del WordCount en la Tabla
Usa una consulta INSERT INTO para ejecutar el WordCount y almacenar los resultados en la tabla wordcount_results.
```sql
INSERT INTO TABLE wordcount_results
SELECT word, count(1) AS count
FROM (SELECT explode(split(line, ' ')) AS word FROM docs) w
GROUP BY word;
```

## 3. Consultar la Tabla de Resultados de WordCount
Para verificar que los datos se han almacenado correctamente en la tabla wordcount_results, realiza una consulta simple:
```sql 
SELECT * FROM wordcount_results
ORDER BY count DESC
LIMIT 10;
```
Este paso muestra las palabras más frecuentes en la tabla wordcount_results.

## Resumen del Script Completo
```sql 
-- Crear la tabla para almacenar los resultados
CREATE TABLE wordcount_results (
    word STRING,
    count INT
)
STORED AS TEXTFILE;

-- Ejecutar el WordCount e insertar los resultados en la tabla
INSERT INTO TABLE wordcount_results
SELECT word, count(1) AS count
FROM (SELECT explode(split(line, ' ')) AS word FROM docs) w
GROUP BY word;

-- Consultar los resultados de WordCount
SELECT * FROM wordcount_results
ORDER BY count DESC
LIMIT 10;
```