# Scripts/comandos para lab 3-3

## 1. Configuración y Carga de Datos

Descarga el dataset de COVID-19 en Colombia:

```bash
# Descargar desde datos abiertos de Colombia
wget https://www.datos.gov.co/api/views/gt2j-8ykr/rows.csv?accessType=DOWNLOAD -O Casos_positivos_de_COVID-19_en_Colombia.csv
```

Subir el Dataset a HDFS
```
hdfs dfs -put Casos_positivos_de_COVID-19_en_Colombia.csv /user/hadoop/datasets/covid19/
```

## 2. Análisis Exploratorio en PySpark
Cargar y Explorar el Dataset
Inicia PySpark en el nodo master de EMR y crea un DataFrame:
```python 
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("CovidAnalysis").getOrCreate()
df = spark.read.csv("hdfs:///user/hadoop/datasets/covid19/Casos_positivos_de_COVID-19_en_Colombia.csv", header=True, inferSchema=True)
df.printSchema()  # Muestra columnas y tipos de datos
```

Filtrar y Analizar Datos
Selecciona y filtra datos de interés:
```python
# Filtrar datos de casos activos
df_filtered = df.select("ID de caso", "Ciudad de ubicación", "Estado", "Edad").filter(df["Estado"] == "Activo")
df_filtered.show(10)  # Muestra los primeros 10 registros

# Agrupar por estado y calcular promedio de edad
df.groupBy("Estado").avg("Edad").show()
```

## 3. Guardar Datos en AWS S3 y Google Drive
Guarda el DataFrame en AWS S3 y Google Drive:
```python 
# Guardar en AWS S3 en formato Parquet
df_filtered.write.mode("overwrite").parquet("s3://afruao-datasets/covid-data")

# Si trabajas en Google Colab
from google.colab import drive
drive.mount('/content/drive')
df_filtered.write.csv("/content/drive/MyDrive/covid-data", header=True)
```

## 4. Script Completo: covid_analysis.py
Para ejecutar el análisis como script, crea covid_analysis.py con el siguiente contenido:
```python 
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("CovidAnalysis").getOrCreate()
df = spark.read.csv("hdfs:///user/hadoop/datasets/covid19/Casos_positivos_de_COVID-19_en_Colombia.csv", header=True, inferSchema=True)
df_filtered = df.select("ID de caso", "Ciudad de ubicación", "Estado", "Edad").filter(df["Estado"] == "Activo")

df_filtered.write.mode("overwrite").parquet("hdfs:///tmp/covid-data")
df_filtered.write.mode("overwrite").parquet("s3://hortegag-datasets/covid-data")
```

Ejecuta el script en EMR:
```
spark-submit covid_analysis.py
```

## 5. Verificar Resultados en HDFS
Consulta los resultados guardados en HDFS:
```
hdfs dfs -ls /tmp/covid-data
hdfs dfs -cat /tmp/covid-data/part-00000 | head
```