# Scripts/comandos para lab 3-1

### Conexión por SSH a instancia.

```
ssh -i <your-key-pair.pem> hadoop@<master-public-dns-name>
```

### Verificar directorios en HDFS.

```
hdfs dfs -ls /
hdfs dfs -ls /user
hdfs dfs -ls /user/hadoop
hdfs dfs -ls /user/hadoop/datasets
```

### Instalación de Git.

```
sudo yum install git
```

### Clonación del repositorio.

```
git clone https://github.com/st0263eafit/st0263-242.git
```

### Crear directorios en HDFS

```
hdfs dfs -mkdir /user/hadoop/datasets
hdfs dfs -mkdir /user/hadoop/datasets/gutenberg
```

### Cargar archivos a HDFS

```
hdfs dfs -put /home/ec2-home/st0263-242/bigdata/datasets/gutenberg-small/*.txt /user/hadoop/datasets/gutenberg/
hdfs dfs -copyFromLocal /home/ec2-home/st0263-242/bigdata/datasets/gutenberg-small/*.txt /user/hadoop/datasets/gutenberg/
```

### Verificar archivos cargados

```
hdfs dfs -ls /user/hadoop/datasets
hdfs dfs -ls /user/hadoop/datasets/gutenberg-small
```

### Obtener archivos de HDFS a la máquina local
```
hdfs dfs -get /user/hadoop/datasets/gutenberg-small/*.txt ~hadoop/mis_datasets/
hdfs dfs -copyToLocal /user/hadoop/datasets/gutenberg-small/*.txt ~hadoop/mis_datasets/
```

### Listar archivos en la carpeta local
```
ls -l mis_datasets
```

### Creación de carpetas en HDFS y copia de archivos
```
hdfs dfs -mkdir /user/hadoop/datasets
hdfs dfs -mkdir /user/hadoop/datasets/gutenberg-small
hdfs dfs -put /local-path-to-cloned-repo/* /user/hadoop/datasets/gutenberg-small
```

### Copiar archivos a Hive
```
hdfs dfs -put /root/st0263-242/bigdata/datasets/gutenberg-small/*.txt /user/hadoop/datasets/gutenberg-small/
```

### Copiar archivos a S3.

```
hadoop fs -put /root/st0263-242/bigdata/datasets/ s3://afruao-datasets/
```

### Copiar archivos hacia el AWS S3 vía SSH.

```
hadoop fs -put /root/st0263-242/bigdata/datasets/ s3://afruao-datasets/
```