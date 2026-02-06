# 🎬 Guía Práctica: Hadoop 3.4.0 + Hive 3.3.1 con Dataset MovieLens

> **Análisis de Ratings de Películas con 100,000 Registros Reales**

**Autor:** Guía de Estudio Big Data
**Fecha:** Febrero 2026
**Versiones:** Hadoop 3.4.0 | Hive 3.3.1 | Java 8
**Dataset:** MovieLens 100K (943 usuarios, 1682 películas, 100,000 ratings)

---

## 📋 CONTENIDO

1. [Iniciar Servicios Hadoop y YARN](#-paso-0-iniciar-servicios-hadoop-y-yarn)
2. [Cargar Dataset desde Archivo .data](#-paso-1-cargar-dataset-desde-archivo-data-a-hdfs)
3. [Crear Tablas y Consultas en Hive](#-paso-2-crear-base-de-datos-y-tablas-en-hive)
4. [Consultas Analíticas con MapReduce](#-paso-3-consultas-analíticas-con-mapreduce-yarn)
5. [Monitoreo de Jobs YARN](#-paso-4-monitoreo-de-jobs-yarn-y-mapreduce)
6. [Exportar Resultados](#-paso-5-exportar-resultados)
7. [Consultas Avanzadas Bonus](#-paso-6-consultas-avanzadas-bonus)

---

## 🚀 PASO 0: Iniciar Servicios Hadoop y YARN

⏱️ **Tiempo estimado:** 1 minuto

### 0.1 Iniciar Todos los Servicios

```bash
# Iniciar HDFS y YARN simultáneamente
start-all.sh

# Esperar 15 segundos a que todos los servicios inicien
sleep 15
```

> **💡 Comandos útiles:**
> - **Iniciar todo:** `start-all.sh`
> - **Detener todo:** `stop-all.sh`

---

### 0.2 Verificar que Todos los Servicios Están Activos

```bash
# Ver TODOS los servicios Java activos
jps
```

**✅ Salida esperada:**

```
12345 NameNode
12346 DataNode
12347 SecondaryNameNode
12348 ResourceManager
12349 NodeManager
12350 Jps
```

**💡 Explicación de los Servicios:**

| Servicio                    | Tipo      | Responsabilidad                                            |
| --------------------------- | --------- | ---------------------------------------------------------- |
| **NameNode**          | HDFS      | Administra metadatos (nombres, ubicación de bloques)      |
| **DataNode**          | HDFS      | Almacena bloques de datos reales                           |
| **SecondaryNameNode** | HDFS      | Realiza checkpoints del NameNode                           |
| **ResourceManager**   | YARN      | Gestiona recursos del cluster (CPU, RAM) y distribuye jobs |
| **NodeManager**       | YARN      | Ejecuta contenedores con tasks MapReduce                   |

> **✅ CHECKPOINT:** Si ves los 5 servicios, puedes continuar al siguiente paso.

---

### 0.3 Verificar Interfaces Web

**Abrir en el navegador:**

| Servicio                       | URL                    | Propósito                               |
| ------------------------------ | ---------------------- | ---------------------------------------- |
| **HDFS NameNode**        | http://localhost:9870  | Explorar archivos HDFS, ver bloques      |
| **YARN ResourceManager** | http://localhost:8088  | Monitorear jobs MapReduce en tiempo real |
| **Job History Server**   | http://localhost:19888 | Historial de jobs completados (opcional) |

**💡 Tip de Estudio:**
Deja estas pestañas abiertas durante toda la práctica para observar cómo Hadoop procesa los datos en tiempo real.

---

## 📁 PASO 1: Cargar Dataset desde Archivo .data a HDFS

⏱️ **Tiempo estimado:** 3-5 minutos (depende del tamaño del archivo)

---

### 1.1 Entender el Dataset MovieLens

**Dataset:** `u.data` - 100,000 ratings de películas
**Usuarios:** 943
**Películas:** 1682
**Formato:** Tab-separated values (TSV)

**Estructura de cada línea:**

```
user_id <TAB> movie_id <TAB> rating <TAB> timestamp
```

**Ejemplo real:**

```
196	242	3	881250949
186	302	3	891717742
22	377	1	878887116
```

**Interpretación:**

- Usuario 196 calificó la película 242 con 3 estrellas el 881250949 (Unix timestamp)
- Usuario 186 calificó la película 302 con 3 estrellas
- Usuario 22 calificó la película 377 con 1 estrella

---

### 1.2 Verificar que el Archivo Existe Localmente

```bash
# Navegar al directorio del dataset (ajusta la ruta según tu caso)
cd /ruta/a/tu/dataset/ml-100k/

# Verificar que el archivo existe
ls -lh u.data

# Ver primeras 10 líneas del archivo
head -10 u.data

# Contar total de líneas (debe ser 100,000)
wc -l u.data
```

**✅ Salida esperada:**

```
-rw-r--r-- 1 usuario grupo 1.9M Feb 01 10:00 u.data
100000 u.data
```

---

### 1.3 Crear Directorio en HDFS para el Dataset

```bash
# Crear estructura de directorios en HDFS
hdfs dfs -mkdir -p /datasets/movielens

# Verificar creación
hdfs dfs -ls /datasets/
```

**✅ Salida esperada:**

```
drwxrwxrwx   - hadoop supergroup          0 2026-02-01 10:15 /datasets/movielens
```

---

### 1.4 Cargar Archivo .data desde Sistema Local a HDFS

> **🎯 CONCEPTO CLAVE:** Este es el momento donde los datos pasan del filesystem local al sistema distribuido HDFS

```bash
# Método 1: Usando ruta absoluta local
hdfs dfs -put /ruta/completa/al/archivo/ml-100k/u.data /datasets/movielens/

# Método 2: Desde el directorio actual (si ya estás en ml-100k/)
cd /ruta/a/tu/dataset/ml-100k/
hdfs dfs -put u.data /datasets/movielens/

# Método 3: Con renombramiento (recomendado para claridad)
hdfs dfs -put u.data /datasets/movielens/ratings.data
```

**💡 En esta práctica usaremos el Método 3:**

```bash
cd /home/hadoop/ml-100k/

hdfs dfs -put u.data /datasets/movielens/ratings.data
```

---

### 1.5 Verificar que los Datos se Cargaron Correctamente

```bash
# Ver el archivo en HDFS
hdfs dfs -ls /datasets/movielens/

# Ver primeras 20 líneas del archivo EN HDFS
hdfs dfs -cat /datasets/movielens/ratings.data | head -20

# Contar líneas en HDFS (debe ser 100,000)
hdfs dfs -cat /datasets/movielens/ratings.data | wc -l

# Ver información detallada del archivo (tamaño, replicación, bloques)
hdfs dfs -stat "%b bytes, %r replicas, %o bloques" /datasets/movielens/ratings.data
```

**✅ Salida esperada:**

```bash
# hdfs dfs -ls /datasets/movielens/
-rw-r--r--   3 hadoop supergroup    1979173 2026-02-01 10:20 /datasets/movielens/ratings.data

# hdfs dfs -cat ... | wc -l
100000
```

**💡 Explicación del Output:**

- `-rw-r--r--`: Permisos del archivo
- `3`: Factor de replicación (el archivo existe en 3 DataNodes)
- `hadoop`: Usuario propietario
- `1979173`: Tamaño en bytes (~1.9 MB)
- `2026-02-01 10:20`: Fecha de carga

---

### 1.6 Explorar Distribución de Bloques en HDFS

> **🎯 CONCEPTO BIG DATA:** HDFS divide archivos grandes en bloques (default: 128 MB). Nuestro archivo es pequeño (1.9 MB) así que será un solo bloque.

```bash
# Ver información detallada de bloques
hdfs fsck /datasets/movielens/ratings.data -files -blocks -locations
```

**✅ Salida esperada (ejemplo simplificado):**

```
/datasets/movielens/ratings.data 1979173 bytes, 1 block(s):
 0. BP-1234567890-127.0.0.1-1234567890123:blk_1073741825_1001 len=1979173 repl=3
    [DataNode1:50010, DataNode2:50010, DataNode3:50010]
Status: HEALTHY
```

**💡 Interpretación:**

- El archivo tiene **1 bloque** (porque es < 128 MB)
- Está **replicado 3 veces** (copia en 3 DataNodes diferentes)
- Si un DataNode falla, los otros 2 tienen la copia completa

---

### 1.7 Validación Final con Interfaz Web

**Abrir en navegador:** http://localhost:9870

**Navegación:**

1. Clic en **"Utilities"** → **"Browse the file system"**
2. Navegar a `/datasets/movielens/`
3. Clic en `ratings.data`
4. Ver información de bloques, replicación y DataNodes

**Captura conceptual de lo que verás:**

```
File Information:
Path: /datasets/movielens/ratings.data
Size: 1.89 MB (1,979,173 bytes)
Block Size: 128 MB
Replication: 3
Owner: hadoop
Permissions: rw-r--r--

Blocks:
Block ID: blk_1073741825
Block Pool ID: BP-1234567890-127.0.0.1-1234567890123
Generation Stamp: 1001
Bytes: 1,979,173
Replicas (3):
  - DataNode: localhost:50010
  - DataNode: localhost:50010
  - DataNode: localhost:50010
```

> **✅ CHECKPOINT:** Si ves el archivo en la interfaz web con replicación = 3, los datos están correctamente distribuidos en HDFS.

---

## 🐝 PASO 2: Crear Base de Datos y Tablas en Hive

⏱️ **Tiempo estimado:** 3-4 minutos

---

### 2.1 Conectar a Hive

```bash
# Conectar a Hive (abre terminal interactiva)
hive
```

**✅ Salida esperada:**

```
SLF4J: Class path contains multiple SLF4J bindings.
...
Hive Session ID = 12345678-abcd-1234-5678-123456789abc

hive>
```

> **💡 Nota:** Los warnings de SLF4J son normales y pueden ignorarse. El prompt `hive>` indica que estás conectado.

---

### 2.2 ⚠️ CONFIGURACIÓN OBLIGATORIA (Evitar Errores)

> **CRÍTICO:** Ejecuta estos comandos ANTES de cualquier consulta con GROUP BY o agregaciones

```sql
-- Deshabilitar modo local de Hive (fuerza uso de YARN)
SET hive.exec.mode.local.auto=false;

-- Forzar framework MapReduce en YARN
SET mapreduce.framework.name=yarn;

-- Ver configuración actual (opcional, para verificar)
SET hive.exec.mode.local.auto;
SET mapreduce.framework.name;
```

**✅ Salida esperada:**

```
hive.exec.mode.local.auto=false
mapreduce.framework.name=yarn
```

**💡 Explicación:**

- **Problema:** Por defecto, Hive intenta ejecutar consultas en modo local (en memoria) para datasets pequeños
- **Consecuencia:** Esto causa "Return Code 2" en consultas con GROUP BY
- **Solución:** Forzar que Hive SIEMPRE use YARN para distribuir el trabajo con MapReduce

---

### 2.3 Crear Base de Datos

```sql
-- Crear base de datos para el proyecto MovieLens
CREATE DATABASE IF NOT EXISTS movielens
COMMENT 'Database para análisis de ratings de películas MovieLens 100K'
LOCATION '/user/hive/warehouse/movielens.db';

-- Ver bases de datos disponibles
SHOW DATABASES;

-- Usar la base de datos recién creada
USE movielens;

-- Verificar que estamos en la base correcta
SELECT current_database();
```

**✅ Salida esperada:**

```
OK
Time taken: 0.5 seconds

OK
default
movielens
Time taken: 0.3 seconds

OK
Time taken: 0.2 seconds

OK
movielens
Time taken: 0.1 seconds
```

---

### 2.4 Crear Tabla Externa para los Ratings

> **🎯 CONCEPTO:** Una tabla EXTERNA en Hive permite consultar datos en HDFS SIN moverlos. Si eliminamos la tabla, los datos en HDFS permanecen intactos.

```sql
-- Crear tabla externa apuntando a los datos en HDFS
CREATE EXTERNAL TABLE IF NOT EXISTS ratings (
    user_id INT COMMENT 'ID del usuario (1-943)',
    movie_id INT COMMENT 'ID de la película (1-1682)',
    rating INT COMMENT 'Calificación (1-5 estrellas)',
    timestamp_unix BIGINT COMMENT 'Timestamp Unix (segundos desde 1970-01-01)'
)
COMMENT 'Tabla de 100,000 ratings de películas del dataset MovieLens'
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t'
STORED AS TEXTFILE
LOCATION '/datasets/movielens/'
TBLPROPERTIES (
    'skip.header.line.count'='0',
    'source'='MovieLens 100K dataset',
    'version'='1.0'
);
```

**💡 Explicación Línea por Línea:**

| Cláusula                      | Propósito                                         |
| ------------------------------ | -------------------------------------------------- |
| `CREATE EXTERNAL TABLE`      | Crea tabla que referencia datos existentes en HDFS |
| `IF NOT EXISTS`              | No falla si la tabla ya existe                     |
| `user_id INT`                | Define columna con tipo de dato entero             |
| `COMMENT`                    | Documentación (útil para equipos)                |
| `ROW FORMAT DELIMITED`       | Los campos están separados por un delimitador     |
| `FIELDS TERMINATED BY '\t'`  | Usa tabulador (TAB) como separador                 |
| `STORED AS TEXTFILE`         | Archivo de texto plano (no binario)                |
| `LOCATION`                   | Ruta en HDFS donde están los datos                |
| `skip.header.line.count='0'` | No tiene fila de encabezado (empieza con datos)    |

---

### 2.5 Verificar que la Tabla se Creó Correctamente

```sql
-- Ver todas las tablas en la base de datos actual
SHOW TABLES;

-- Ver estructura de la tabla (schema)
DESCRIBE ratings;

-- Ver información detallada de la tabla (metadata completo)
DESCRIBE FORMATTED ratings;
```

**✅ Salida esperada:**

```sql
-- SHOW TABLES;
OK
ratings
Time taken: 0.2 seconds

-- DESCRIBE ratings;
OK
user_id             	int                 	ID del usuario (1-943)
movie_id            	int                 	ID de la película (1-1682)
rating              	int                 	Calificación (1-5 estrellas)
timestamp_unix      	bigint              	Timestamp Unix
Time taken: 0.3 seconds

-- DESCRIBE FORMATTED ratings; (salida resumida)
# col_name            	data_type           	comment
user_id             	int                 	ID del usuario (1-943)
movie_id            	int                 	ID de la película (1-1682)
rating              	int                 	Calificación (1-5 estrellas)
timestamp_unix      	bigint              	Timestamp Unix

# Detailed Table Information
Database:           	movielens
Owner:              	hadoop
CreateTime:         	Fri Feb 01 10:30:00 UTC 2026
LastAccessTime:     	UNKNOWN
Retention:          	0
Location:           	hdfs://localhost:9000/datasets/movielens/
Table Type:         	EXTERNAL_TABLE
Table Parameters:
	EXTERNAL            	TRUE
	source              	MovieLens 100K dataset
	version             	1.0
```

---

### 2.6 Prueba Inicial: Ver los Datos en Hive

```sql
-- Ver primeras 10 filas
SELECT * FROM ratings LIMIT 10;

-- Contar total de registros (debe ser 100,000)
SELECT COUNT(*) AS total_ratings FROM ratings;

-- Ver rating mínimo y máximo (deben ser 1 y 5)
SELECT MIN(rating) AS min_rating, MAX(rating) AS max_rating FROM ratings;
```

**✅ Salida esperada:**

```sql
-- SELECT * FROM ratings LIMIT 10;
OK
196	242	3	881250949
186	302	3	891717742
22	377	1	878887116
244	51	2	880606923
166	346	1	886397596
298	474	4	884182806
115	265	2	881171488
253	465	5	891628467
305	451	3	886324817
6	86	3	883603013
Time taken: 1.2 seconds, Fetched: 10 row(s)

-- SELECT COUNT(*) AS total_ratings FROM ratings;
OK
100000
Time taken: 25.3 seconds, Fetched: 1 row(s)

-- SELECT MIN(rating) AS min_rating, MAX(rating) AS max_rating FROM ratings;
OK
1	5
Time taken: 22.1 seconds, Fetched: 1 row(s)
```

> **💡 Observación:** El COUNT(*) toma ~25 segundos porque ejecuta un JOB MapReduce completo en YARN. Veremos esto en detalle en el siguiente paso.

---

## 🔄 PASO 3: Consultas Analíticas con MapReduce (YARN)

⏱️ **Tiempo estimado:** 10-15 minutos (incluye observación de jobs)

---

### 🎯 Objetivo de Aprendizaje

En este paso ejecutaremos consultas que disparan **Jobs MapReduce distribuidos** gestionados por YARN. Observaremos cómo Hadoop procesa 100,000 registros en paralelo.

---

### 3.1 Consulta 1: Distribución de Ratings (1-5 estrellas)

> **📊 Pregunta de Negocio:** ¿Cuántos ratings hay de cada calificación (1, 2, 3, 4, 5 estrellas)?

#### SQL:

```sql
SELECT 
    rating,
    COUNT(*) AS cantidad,
    ROUND(COUNT(*) * 100.0 / 100000, 2) AS porcentaje
FROM ratings
GROUP BY rating
ORDER BY rating;
```

#### 🔍 Conceptos Big Data en Esta Consulta:

| Concepto                  | Explicación                                                   |
| ------------------------- | -------------------------------------------------------------- |
| **GROUP BY rating** | Dispara fase**Shuffle & Sort** de MapReduce para agrupar |
| **COUNT(\*)**       | Operación de agregación ejecutada por**Reducers**      |
| **ORDER BY**        | Ordenamiento final en fase Reduce                              |

#### 📈 Flujo MapReduce Explicado:

```
┌─────────────────────────────────────────────────────────┐
│ INPUT (HDFS): /datasets/movielens/ratings.data         │
│ 100,000 registros distribuidos en bloques              │
└───────────────────────┬─────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────────┐
│ MAP PHASE (Parallel)                                     │
│ - Lee registros en paralelo (múltiples mappers)        │
│ - Extrae la columna 'rating' de cada fila              │
│ - Emite pares clave-valor: (rating, 1)                 │
│ Ejemplo:                                                 │
│   Input:  196\t242\t3\t881250949                        │
│   Output: (3, 1)                                        │
│   Input:  186\t302\t3\t891717742                        │
│   Output: (3, 1)                                        │
│   Input:  22\t377\t1\t878887116                         │
│   Output: (1, 1)                                        │
└───────────────────────┬─────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────────┐
│ SHUFFLE & SORT (Framework Hadoop)                       │
│ - Agrupa TODOS los pares por clave (rating)            │
│ - Transfiere datos entre nodos (network shuffle)       │
│ Resultado:                                               │
│   Rating 1: [1, 1, 1, 1, ..., 1] → 6,110 valores       │
│   Rating 2: [1, 1, 1, 1, ..., 1] → 11,370 valores      │
│   Rating 3: [1, 1, 1, 1, ..., 1] → 27,145 valores      │
│   Rating 4: [1, 1, 1, 1, ..., 1] → 34,174 valores      │
│   Rating 5: [1, 1, 1, 1, ..., 1] → 21,201 valores      │
└───────────────────────┬─────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────────┐
│ REDUCE PHASE (Parallel)                                 │
│ - Cada reducer recibe una clave y todos sus valores    │
│ - Suma los valores (COUNT)                              │
│ - Calcula porcentaje                                     │
│ - Ordena por rating (ORDER BY)                          │
│ Resultado final:                                         │
│   (1, 6110, 6.11%)                                      │
│   (2, 11370, 11.37%)                                    │
│   (3, 27145, 27.15%)                                    │
│   (4, 34174, 34.17%)                                    │
│   (5, 21201, 21.20%)                                    │
└───────────────────────┬─────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────────┐
│ OUTPUT (Hive Result Set)                                │
│ 5 filas con agregaciones y porcentajes                 │
└─────────────────────────────────────────────────────────┘
```

#### ✅ Resultado Esperado:

| rating | cantidad | porcentaje |
| ------ | -------- | ---------- |
| 1      | 6,110    | 6.11       |
| 2      | 11,370   | 11.37      |
| 3      | 27,145   | 27.15      |
| 4      | 34,174   | 34.17      |
| 5      | 21,201   | 21.20      |

**📊 Interpretación:**

- La mayoría de usuarios (34.17%) dan **4 estrellas**
- Solo el 6.11% dan **1 estrella** (peor calificación)
- Dataset balanceado con tendencia positiva (más 4-5 que 1-2)

---

### 3.2 Consulta 2: Top 10 Películas Más Calificadas

> **📊 Pregunta de Negocio:** ¿Qué películas recibieron más ratings (no necesariamente las mejor calificadas)?

#### SQL:

```sql
SELECT 
    movie_id,
    COUNT(*) AS num_ratings,
    ROUND(AVG(rating), 2) AS rating_promedio,
    MIN(rating) AS rating_min,
    MAX(rating) AS rating_max
FROM ratings
GROUP BY movie_id
ORDER BY num_ratings DESC
LIMIT 10;
```

#### 🔍 Job MapReduce Generado:

**Monitorea en:** http://localhost:8088

Verás:

- **Nombre del Job:** `Stage-1` (Map) + `Stage-2` (Reduce)
- **Mappers:** Típicamente 2-4 (depende del tamaño de bloques)
- **Reducers:** Típicamente 1-2 (para agregación final)
- **Progreso:** Map 100% → Shuffle 100% → Reduce 100%

#### ✅ Resultado Esperado:

| movie_id | num_ratings | rating_promedio | rating_min | rating_max |
| -------- | ----------- | --------------- | ---------- | ---------- |
| 50       | 583         | 4.36            | 1          | 5          |
| 258      | 509         | 4.16            | 1          | 5          |
| 100      | 508         | 3.59            | 1          | 5          |
| 181      | 507         | 4.07            | 1          | 5          |
| 294      | 485         | 4.20            | 1          | 5          |
| 286      | 481         | 4.22            | 1          | 5          |
| 288      | 478         | 3.86            | 1          | 5          |
| 1        | 452         | 3.88            | 1          | 5          |
| 300      | 431         | 4.07            | 1          | 5          |
| 121      | 429         | 3.43            | 1          | 5          |

**📊 Interpretación:**

- La película ID 50 tiene **583 ratings** (la más popular)
- Tiene un promedio de **4.36 estrellas** (alta calidad percibida)
- Todas las top 10 tienen al menos 400 ratings (estadísticamente significativo)

---

### 3.3 Consulta 3: Top 10 Películas Mejor Calificadas (con mínimo de ratings)

> **📊 Pregunta de Negocio:** ¿Cuáles son las mejores películas con al menos 100 ratings? (Para evitar sesgos de películas con pocos ratings)

#### SQL:

```sql
SELECT 
    movie_id,
    COUNT(*) AS num_ratings,
    ROUND(AVG(rating), 2) AS rating_promedio,
    ROUND(STDDEV(rating), 2) AS desviacion_std
FROM ratings
GROUP BY movie_id
HAVING COUNT(*) >= 100
ORDER BY rating_promedio DESC, num_ratings DESC
LIMIT 10;
```

#### 🔍 Conceptos Nuevos:

| Cláusula                                           | Propósito                                             |
| --------------------------------------------------- | ------------------------------------------------------ |
| `HAVING COUNT(*) >= 100`                          | Filtra DESPUÉS de agrupar (diferente a WHERE)         |
| `STDDEV(rating)`                                  | Calcula desviación estándar (dispersión de ratings) |
| `ORDER BY rating_promedio DESC, num_ratings DESC` | Ordena por promedio primero, luego por cantidad        |

**💡 Diferencia WHERE vs HAVING:**

- `WHERE`: Filtra **filas individuales** ANTES de agrupar
- `HAVING`: Filtra **grupos completos** DESPUÉS de agrupar

Ejemplo:

```sql
-- WHERE filtra filas antes de contar
SELECT movie_id, COUNT(*) 
FROM ratings 
WHERE rating >= 4  -- Solo cuenta ratings de 4 y 5
GROUP BY movie_id;

-- HAVING filtra grupos después de contar
SELECT movie_id, COUNT(*) 
FROM ratings 
GROUP BY movie_id
HAVING COUNT(*) >= 100;  -- Solo muestra películas con ≥100 ratings
```

#### ✅ Resultado Esperado:

| movie_id | num_ratings | rating_promedio | desviacion_std |
| -------- | ----------- | --------------- | -------------- |
| 408      | 112         | 4.49            | 0.78           |
| 318      | 298         | 4.47            | 0.76           |
| 483      | 243         | 4.46            | 0.74           |
| 169      | 296         | 4.47            | 0.75           |
| 64       | 283         | 4.45            | 0.77           |
| 114      | 243         | 4.43            | 0.78           |
| 603      | 209         | 4.42            | 0.79           |
| 12       | 267         | 4.39            | 0.82           |
| 50       | 583         | 4.36            | 0.89           |
| 178      | 162         | 4.36            | 0.81           |

**📊 Interpretación:**

- Película ID 408: **4.49 promedio** con 112 ratings (muy alta calidad)
- Desviación estándar baja (0.78) indica **consenso** entre usuarios
- Película ID 50 sigue en top 10 incluso con 583 ratings

---

### 3.4 Consulta 4: Análisis de Actividad de Usuarios

> **📊 Pregunta de Negocio:** ¿Cómo se distribuye la actividad de usuarios? ¿Hay usuarios super activos?

#### SQL:

```sql
SELECT 
    user_id,
    COUNT(*) AS num_ratings,
    ROUND(AVG(rating), 2) AS rating_promedio,
    MIN(rating) AS rating_min,
    MAX(rating) AS rating_max,
    COUNT(DISTINCT movie_id) AS peliculas_distintas
FROM ratings
GROUP BY user_id
ORDER BY num_ratings DESC
LIMIT 10;
```

#### 🔍 Concepto Nuevo: COUNT(DISTINCT)

```sql
-- COUNT(*) cuenta todas las filas (incluyendo duplicados)
SELECT user_id, COUNT(*) FROM ratings GROUP BY user_id;

-- COUNT(DISTINCT movie_id) cuenta películas ÚNICAS por usuario
SELECT user_id, COUNT(DISTINCT movie_id) FROM ratings GROUP BY user_id;
```

**Ejemplo:**

```
User 1 ratings:
  movie 50 → 4 stars
  movie 50 → 5 stars  (calificó la misma película 2 veces - caso raro)
  movie 100 → 3 stars

COUNT(*) = 3 ratings
COUNT(DISTINCT movie_id) = 2 películas distintas
```

#### ✅ Resultado Esperado:

| user_id | num_ratings | rating_promedio | rating_min | rating_max | peliculas_distintas |
| ------- | ----------- | --------------- | ---------- | ---------- | ------------------- |
| 405     | 737         | 3.33            | 1          | 5          | 737                 |
| 655     | 685         | 4.40            | 1          | 5          | 685                 |
| 13      | 636         | 3.74            | 1          | 5          | 636                 |
| 450     | 540         | 3.76            | 1          | 5          | 540                 |
| 276     | 518         | 3.51            | 1          | 5          | 518                 |
| 416     | 493         | 3.66            | 1          | 5          | 493                 |
| 537     | 490         | 3.89            | 1          | 5          | 490                 |
| 303     | 484         | 3.92            | 1          | 5          | 484                 |
| 234     | 480         | 4.10            | 1          | 5          | 480                 |
| 393     | 448         | 3.76            | 1          | 5          | 448                 |

**📊 Interpretación:**

- Usuario 405: **737 ratings** (usuario más activo)
- Usuario 655: **685 ratings** pero con promedio 4.40 (más generoso)
- Usuario 13: **636 ratings** con promedio 3.74 (más crítico)
- Todos tienen `num_ratings == peliculas_distintas` (no hay ratings duplicados)

---

### 3.5 Consulta 5: Análisis Temporal (Ratings por Mes)

> **📊 Pregunta de Negocio:** ¿Cómo varía la actividad de usuarios a lo largo del tiempo?

#### 🔧 Preparación: Convertir Timestamp Unix a Fecha Legible

```sql
-- Primero exploramos los timestamps
SELECT 
    user_id,
    movie_id,
    rating,
    timestamp_unix,
    from_unixtime(timestamp_unix) AS fecha_completa,
    from_unixtime(timestamp_unix, 'yyyy-MM') AS anio_mes
FROM ratings
LIMIT 10;
```

**✅ Salida:**

| user_id | movie_id | rating | timestamp_unix | fecha_completa      | anio_mes |
| ------- | -------- | ------ | -------------- | ------------------- | -------- |
| 196     | 242      | 3      | 881250949      | 1997-12-04 15:55:49 | 1997-12  |
| 186     | 302      | 3      | 891717742      | 1998-04-04 19:22:22 | 1998-04  |

#### SQL Principal:

```sql
SELECT 
    from_unixtime(timestamp_unix, 'yyyy-MM') AS mes,
    COUNT(*) AS num_ratings,
    ROUND(AVG(rating), 2) AS rating_promedio,
    COUNT(DISTINCT user_id) AS usuarios_activos,
    COUNT(DISTINCT movie_id) AS peliculas_calificadas
FROM ratings
GROUP BY from_unixtime(timestamp_unix, 'yyyy-MM')
ORDER BY mes;
```

#### ✅ Resultado Esperado (primeras 10 filas):

| mes     | num_ratings | rating_promedio | usuarios_activos | peliculas_calificadas |
| ------- | ----------- | --------------- | ---------------- | --------------------- |
| 1997-09 | 2,669       | 3.52            | 405              | 873                   |
| 1997-10 | 5,954       | 3.53            | 612              | 1,202                 |
| 1997-11 | 8,286       | 3.54            | 671              | 1,309                 |
| 1997-12 | 9,452       | 3.53            | 686              | 1,341                 |
| 1998-01 | 11,372      | 3.54            | 705              | 1,376                 |
| 1998-02 | 10,561      | 3.54            | 691              | 1,345                 |
| 1998-03 | 12,643      | 3.52            | 719              | 1,401                 |
| 1998-04 | 18,063      | 3.53            | 753              | 1,445                 |

**📊 Interpretación:**

- Dataset recolectado entre **Septiembre 1997 - Abril 1998**
- Pico de actividad en **Abril 1998** (18,063 ratings)
- Rating promedio muy estable (~3.53) a través del tiempo
- Crecimiento de usuarios activos: 405 → 753

---

## 📊 PASO 4: Monitoreo de Jobs YARN y MapReduce

⏱️ **Tiempo:** Durante la ejecución de consultas

---

### 🎯 Objetivo

Aprender a monitorear jobs MapReduce en tiempo real para entender cómo Hadoop distribuye y procesa los datos.

---

### 4.1 Monitoreo desde Interfaz Web YARN

**URL:** http://localhost:8088

#### 📍 Navegación:

1. **Applications → All Applications**

   - Ver lista de todos los jobs (RUNNING, FINISHED, FAILED)
2. **Clic en un Application ID** (ej: `application_1234567890_0001`)

   - Ver detalles del job en ejecución
3. **Dentro del job:**

   - **Application Master:** Coordinador del job
   - **Progress:** Porcentaje de Map → Shuffle → Reduce
   - **Tasks:** Mappers y Reducers individuales
   - **Logs:** Salida estándar y errores

#### 📊 Métricas Clave a Observar:

| Métrica                  | Ubicación        | Qué Observar                      |
| ------------------------- | ----------------- | ---------------------------------- |
| **Estado**          | Application State | RUNNING → SUCCEEDED (o FAILED)    |
| **Progreso Map**    | Progress          | 0% → 100%                         |
| **Progreso Reduce** | Progress          | 0% → 100%                         |
| **# Mappers**       | Tasks             | Típicamente 2-4 para 1.9 MB       |
| **# Reducers**      | Tasks             | Típicamente 1-2 para agregaciones |
| **Memoria**         | Resources         | RAM usada vs disponible            |
| **Duración**       | Elapsed Time      | Tiempo total de ejecución         |

---

### 4.2 Monitoreo desde Terminal

#### Ver aplicaciones activas:

```bash
# Listar aplicaciones en ejecución
yarn application -list

# Actualizar cada 2 segundos (watch mode)
watch -n 2 'yarn application -list'
```

**✅ Salida ejemplo:**

```
Total number of applications (application-types: [], states: [RUNNING] and tags: []):1
Application-Id	    Application-Name	Application-Type  User	  State	    Final-State	Progress
application_1234567890_0001  INSERT OVERWRITE DIRECTORY  MAPREDUCE  hadoop  RUNNING  UNDEFINED  50%
```

#### Ver estado detallado de un job:

```bash
# Reemplazar con el Application ID real
yarn application -status application_1234567890_0001
```

**✅ Salida ejemplo:**

```
Application Report :
	Application-Id : application_1234567890_0001
	Application-Name : INSERT OVERWRITE DIRECTORY
	Application-Type : MAPREDUCE
	User : hadoop
	Queue : default
	Start-Time : 1704192000000
	Finish-Time : 0
	Progress : 75%
	State : RUNNING
	Final-State : UNDEFINED
	Tracking-URL : http://localhost:8088/proxy/application_1234567890_0001/
```

#### Ver logs de un job (después de que termine):

```bash
# Ver logs completos
yarn logs -applicationId application_1234567890_0001

# Ver solo los últimos 100 KB
yarn logs -applicationId application_1234567890_0001 -size -102400
```

---

### 4.3 Monitoreo desde Interfaz Web HDFS

**URL:** http://localhost:9870

#### 📍 Navegación:

1. **Overview**

   - Capacidad usada/disponible del cluster
   - DataNodes activos
2. **Datanodes**

   - Lista de nodos con su estado
   - Capacidad individual
   - Bloques almacenados
3. **Utilities → Browse the file system**

   - Explorar archivos en HDFS
   - Ver replicación y bloques
4. **Navegar a `/datasets/movielens/ratings.data`**

   - Ver información del archivo
   - Ver distribución de bloques
   - Ver réplicas en diferentes DataNodes

---

### 4.4 Interpretar el Flujo de un Job MapReduce

#### Ejemplo: Ejecutar y Observar

```sql
-- En Hive, ejecuta esta consulta
SELECT rating, COUNT(*) AS cantidad 
FROM ratings 
GROUP BY rating 
ORDER BY rating;
```

#### 📊 Timeline de Ejecución:

```
T=0s   ┌─────────────────────────────────────────────────┐
       │ Hive compila SQL a plan de ejecución MapReduce │
       └────────────────────┬────────────────────────────┘
                            ↓
T=2s   ┌─────────────────────────────────────────────────┐
       │ YARN ResourceManager asigna contenedores        │
       │ - ApplicationMaster: 1 contenedor               │
       │ - Mappers: 2-4 contenedores                     │
       │ - Reducers: 1-2 contenedores                    │
       └────────────────────┬────────────────────────────┘
                            ↓
T=3s   ┌─────────────────────────────────────────────────┐
       │ MAP PHASE: Lee bloques de HDFS en paralelo     │
       │ - Mapper 1: Procesa primeros 50,000 registros  │
       │ - Mapper 2: Procesa últimos 50,000 registros   │
       │ - Emite pares (rating, 1) para cada fila       │
       │ Progreso: Map 0% → 50% → 100%                  │
       └────────────────────┬────────────────────────────┘
                            ↓
T=15s  ┌─────────────────────────────────────────────────┐
       │ SHUFFLE & SORT PHASE: Transferencia de datos   │
       │ - Agrupa pares por clave (rating)              │
       │ - Transfiere datos entre nodos                 │
       │ - Ordena las claves                             │
       │ Progreso: Shuffle 0% → 100%                     │
       └────────────────────┬────────────────────────────┘
                            ↓
T=20s  ┌─────────────────────────────────────────────────┐
       │ REDUCE PHASE: Agregación                        │
       │ - Reducer 1: Suma valores para ratings 1-3     │
       │ - Reducer 2: Suma valores para ratings 4-5     │
       │ - Ordena resultado final (ORDER BY)            │
       │ Progreso: Reduce 0% → 100%                      │
       └────────────────────┬────────────────────────────┘
                            ↓
T=25s  ┌─────────────────────────────────────────────────┐
       │ OUTPUT: Escribe resultado final                 │
       │ - Hive recibe 5 filas del job                  │
       │ - Muestra resultado en terminal                 │
       │ Estado: SUCCEEDED                               │
       └─────────────────────────────────────────────────┘
```

**💡 Observa en http://localhost:8088 durante cada fase:**

- **T=3-15s:** Verás "Map Progress" aumentando
- **T=15-20s:** Verás "Shuffle Progress" aumentando
- **T=20-25s:** Verás "Reduce Progress" aumentando

---

### 4.5 Diagnóstico de Errores Comunes

#### ❌ Error 1: "Return Code 2 from org.apache.hadoop.hive.ql.exec.mr.MapRedTask"

**Causa:** Hive intenta ejecutar en modo local en lugar de YARN

**Solución:**

```sql
SET hive.exec.mode.local.auto=false;
SET mapreduce.framework.name=yarn;
```

**Verificar en logs:**

```bash
# Ver logs del último job fallido
yarn logs -applicationId $(yarn application -list -appStates FAILED | tail -1 | awk '{print $1}')
```

---

#### ❌ Error 2: "Container killed by YARN for exceeding memory limits"

**Causa:** El job MapReduce requiere más memoria de la disponible

**Diagnóstico en YARN UI:**

1. Ir a http://localhost:8088
2. Clic en el job FAILED
3. Ver "Diagnostics" → "Container exited with a non-zero exit code 137"

**Solución:**

```sql
-- Aumentar memoria para Map y Reduce tasks
SET mapreduce.map.memory.mb=2048;
SET mapreduce.reduce.memory.mb=2048;
SET mapreduce.map.java.opts=-Xmx1638m;
SET mapreduce.reduce.java.opts=-Xmx1638m;
```

---

#### ❌ Error 3: Job permanece en "ACCEPTED" sin ejecutarse

**Causa:** No hay recursos disponibles en YARN

**Diagnóstico:**

```bash
# Ver recursos del cluster
yarn node -list -all

# Ver configuración de memoria
yarn resourcemanager -format
```

**Verificar en YARN UI:**

http://localhost:8088 → Cluster Metrics → "Memory Available"

---

## 💾 PASO 5: Exportar Resultados

⏱️ **Tiempo estimado:** 2-3 minutos

---

### 5.1 Exportar a Sistema de Archivos Local (CSV)

> **📌 Uso:** Para análisis externo con Excel, Python, R, etc.

#### SQL en Hive:

```sql
-- Exportar distribución de ratings a archivo local
INSERT OVERWRITE LOCAL DIRECTORY '/tmp/movielens_export/distribucion_ratings'
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
SELECT 
    rating,
    COUNT(*) AS cantidad,
    ROUND(COUNT(*) * 100.0 / 100000, 2) AS porcentaje
FROM ratings
GROUP BY rating
ORDER BY rating;
```

**💡 Explicación:**

- `INSERT OVERWRITE LOCAL DIRECTORY`: Escribe en filesystem local (no HDFS)
- `/tmp/movielens_export/`: Ruta local donde se guardarán los archivos
- `ROW FORMAT DELIMITED FIELDS TERMINATED BY ','`: Formato CSV
- Hive crea múltiples archivos si hay múltiples reducers (000000_0, 000001_0, etc.)

#### Verificar en Terminal:

```bash
# Ver archivos generados
ls -lh /tmp/movielens_export/distribucion_ratings/

# Ver contenido
cat /tmp/movielens_export/distribucion_ratings/000000_0

# Formatear como tabla
column -t -s',' /tmp/movielens_export/distribucion_ratings/000000_0
```

**✅ Salida esperada:**

```bash
$ cat /tmp/movielens_export/distribucion_ratings/000000_0
1,6110,6.11
2,11370,11.37
3,27145,27.15
4,34174,34.17
5,21201,21.2
```

---

### 5.2 Exportar Top Películas a CSV con Encabezado

```sql
-- Crear archivo con encabezado (header)
INSERT OVERWRITE LOCAL DIRECTORY '/tmp/movielens_export/top_peliculas'
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
SELECT * FROM (
    -- Fila de encabezado
    SELECT 'movie_id' as c1, 'num_ratings' as c2, 'rating_promedio' as c3, 0 as sort_col
  
    UNION ALL
  
    -- Datos calculados
    SELECT 
        CAST(movie_id AS STRING) as c1, 
        CAST(COUNT(*) AS STRING) as c2, 
        CAST(ROUND(AVG(rating), 2) AS STRING) as c3,
        1 as sort_col
    FROM ratings
    GROUP BY movie_id
) t
ORDER BY t.sort_col ASC, CAST(t.c2 AS INT) DESC
LIMIT 21; -- 20 películas + 1 encabezado
```

**💡 Truco:** Usamos `UNION ALL` para agregar una fila de encabezado

#### Descargar desde servidor (si trabajas remoto):

```bash
# Comprimir exportaciones
tar -czf ~/movielens_export.tar.gz -C /tmp/ movielens_export/

# Una vez terminado, verifica que el archivo existe y tiene un tamaño razonable:
ls -lh ~/movielens_export.tar.gz

# Intentar moverlo a tu home (aquí veremos si falla de nuevo)
mv /tmp/movielens_export.tar.gz ~/

# Copiar a tu máquina local (desde tu PC)
scp usuario@servidor:/tmp/movielens_export.tar.gz ~/Descargas/

# Descomprimir localmente
tar -xzf ~/Descargas/movielens_export.tar.gz
```

---

### 5.3 Exportar a HDFS (para compartir con otros procesos)

> **📌 Uso:** Guardar resultados para otros jobs Hadoop, Spark, o usuarios del cluster

#### SQL en Hive:

```sql
-- Exportar a HDFS (sin LOCAL)
INSERT OVERWRITE DIRECTORY '/results/movielens/top_peliculas'
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
SELECT 
    movie_id,
    COUNT(*) AS num_ratings,
    ROUND(AVG(rating), 2) AS rating_promedio
FROM ratings
GROUP BY movie_id
ORDER BY num_ratings DESC
LIMIT 100;
```

#### Verificar en HDFS:

```bash
# Ver archivos en HDFS
hdfs dfs -ls /results/movielens/top_peliculas/

# Ver contenido
hdfs dfs -cat /results/movielens/top_peliculas/000000_0 | head -10

# Descargar a local si necesitas
hdfs dfs -get /results/movielens/top_peliculas/000000_0 ~/top_peliculas.csv
```

#### Ver en Interfaz Web:

http://localhost:9870 → Utilities → Browse → `/results/movielens/top_peliculas/`

---

### 5.4 Crear Tabla Materializada para Consultas Rápidas

> **🎯 CONCEPTO:** Materializar resultados de consultas costosas para reutilizarlos sin re-calcular

```sql
-- Crear tabla con estadísticas pre-calculadas de películas
CREATE TABLE movie_stats AS
SELECT 
    movie_id,
    COUNT(*) AS num_ratings,
    ROUND(AVG(rating), 2) AS rating_promedio,
    ROUND(STDDEV(rating), 2) AS rating_stddev,
    MIN(rating) AS rating_min,
    MAX(rating) AS rating_max,
    PERCENTILE_APPROX(rating, 0.5) AS rating_mediana
FROM ratings
GROUP BY movie_id;

-- Ahora podemos consultar rápidamente SIN re-calcular
SELECT * FROM movie_stats 
WHERE num_ratings >= 100 
ORDER BY rating_promedio DESC 
LIMIT 10;
```

**💡 Ventaja:** La segunda consulta NO ejecuta MapReduce, es instantánea

#### Verificar ubicación en HDFS:

```sql
DESCRIBE FORMATTED movie_stats;
```

```bash
# Ver datos en HDFS
hdfs dfs -ls /user/hive/warehouse/movielens.db/movie_stats/
hdfs dfs -cat /user/hive/warehouse/movielens.db/movie_stats/000000_0 | head -5
```

---

### 5.5 Exportar Múltiples Reportes (Script Completo)

#### Crear script `export_reports.hql`:

```sql
-- Archivo: export_reports.hql
-- Configuración inicial
USE movielens;
SET hive.exec.mode.local.auto=false;
SET mapreduce.framework.name=yarn;

-- Reporte 1: Distribución de ratings
INSERT OVERWRITE LOCAL DIRECTORY '/tmp/reports/distribucion_ratings'
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
SELECT rating, COUNT(*) AS cantidad
FROM ratings GROUP BY rating ORDER BY rating;

-- Reporte 2: Top 50 películas
INSERT OVERWRITE LOCAL DIRECTORY '/tmp/reports/top_peliculas'
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
SELECT movie_id, COUNT(*) AS num_ratings, ROUND(AVG(rating), 2) AS avg_rating
FROM ratings GROUP BY movie_id 
ORDER BY num_ratings DESC LIMIT 50;

-- Reporte 3: Actividad por mes
INSERT OVERWRITE LOCAL DIRECTORY '/tmp/reports/actividad_mensual'
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
SELECT 
    from_unixtime(timestamp_unix, 'yyyy-MM') AS mes,
    COUNT(*) AS num_ratings,
    COUNT(DISTINCT user_id) AS usuarios_activos
FROM ratings 
GROUP BY from_unixtime(timestamp_unix, 'yyyy-MM')
ORDER BY mes;
```

#### Ejecutar script desde terminal:

```bash
# Ejecutar todos los reportes en batch
hive -f export_reports.hql

# Ver resultados
ls -lh /tmp/reports/*/
```

---

## 🎓 PASO 6: Consultas Avanzadas (Bonus)

⏱️ **Tiempo estimado:** 15-20 minutos

---

### 6.1 Análisis de Consenso: Películas Polarizantes vs Consensuadas

> **📊 Pregunta:** ¿Qué películas generan más desacuerdo entre usuarios?

#### SQL:

```sql
SELECT 
    movie_id,
    COUNT(*) AS num_ratings,
    ROUND(AVG(rating), 2) AS rating_promedio,
    ROUND(STDDEV(rating), 2) AS desviacion,
    CASE 
        WHEN STDDEV(rating) >= 1.5 THEN 'Polarizante'
        WHEN STDDEV(rating) >= 1.0 THEN 'Mixta'
        ELSE 'Consensuada'
    END AS tipo_opinion
FROM ratings
GROUP BY movie_id
HAVING COUNT(*) >= 50  -- Solo películas con suficientes ratings
ORDER BY desviacion DESC
LIMIT 20;
```

**🔍 Conceptos:**

- **CASE WHEN**: Lógica condicional (como IF-ELSE)
- **Alta desviación (> 1.5)**: Usuarios muy divididos (algunos 1★, otros 5★)
- **Baja desviación (< 1.0)**: Usuarios de acuerdo en la calidad

**📊 Interpretación:**

- Películas polarizantes: Gustos muy divididos (ej: películas experimentales)
- Películas consensuadas: Acuerdo general (ej: clásicos universales)

---

### 6.2 Usuarios Generosos vs Críticos

> **📊 Pregunta:** ¿Qué usuarios dan las mejores vs peores calificaciones en promedio?

#### Top 10 Usuarios Más Generosos:

```sql
SELECT 
    user_id,
    COUNT(*) AS num_ratings,
    ROUND(AVG(rating), 2) AS rating_promedio,
    SUM(CASE WHEN rating = 5 THEN 1 ELSE 0 END) AS cinco_estrellas,
    SUM(CASE WHEN rating = 1 THEN 1 ELSE 0 END) AS una_estrella
FROM ratings
GROUP BY user_id
HAVING COUNT(*) >= 100  -- Solo usuarios activos
ORDER BY rating_promedio DESC
LIMIT 10;
```

#### Top 10 Usuarios Más Críticos:

```sql
SELECT 
    user_id,
    COUNT(*) AS num_ratings,
    ROUND(AVG(rating), 2) AS rating_promedio,
    SUM(CASE WHEN rating = 5 THEN 1 ELSE 0 END) AS cinco_estrellas,
    SUM(CASE WHEN rating = 1 THEN 1 ELSE 0 END) AS una_estrella
FROM ratings
GROUP BY user_id
HAVING COUNT(*) >= 100
ORDER BY rating_promedio ASC
LIMIT 10;
```

**🔍 Concepto SUM + CASE:**

```sql
SUM(CASE WHEN rating = 5 THEN 1 ELSE 0 END)
```

Traduce a: "Cuenta cuántos ratings de 5 estrellas tiene este usuario"

---

### 6.3 Análisis de Picos de Actividad (Día de la Semana)

> **📊 Pregunta:** ¿Qué día de la semana los usuarios califican más películas?

#### SQL:

```sql
SELECT 
    CASE pmod(CAST(from_unixtime(timestamp_unix, 'u') AS INT), 7)
        WHEN 0 THEN 'Domingo'
        WHEN 1 THEN 'Lunes'
        WHEN 2 THEN 'Martes'
        WHEN 3 THEN 'Miércoles'
        WHEN 4 THEN 'Jueves'
        WHEN 5 THEN 'Viernes'
        WHEN 6 THEN 'Sábado'
    END AS dia_semana,
    COUNT(*) AS num_ratings,
    ROUND(COUNT(*) * 100.0 / 100000, 2) AS porcentaje
FROM ratings
GROUP BY pmod(CAST(from_unixtime(timestamp_unix, 'u') AS INT), 7)
ORDER BY 
    CASE pmod(CAST(from_unixtime(timestamp_unix, 'u') AS INT), 7)
        WHEN 0 THEN 7 ELSE pmod(CAST(from_unixtime(timestamp_unix, 'u') AS INT), 7)
    END;
```

**🔍 Conceptos:**

- `from_unixtime(timestamp, 'u')`: Extrae día de la semana (1-7)
- `pmod(x, 7)`: Módulo positivo (maneja negativos correctamente)
- Ordenamos para que empiece en Lunes y termine en Domingo

---

### 6.4 Películas con Mayor Variabilidad en el Tiempo

> **📊 Pregunta:** ¿Cambian los ratings de una película con el tiempo?

#### SQL:

```sql
-- Crear vista temporal con fechas
CREATE TEMPORARY TABLE ratings_with_date AS
SELECT 
    *,
    from_unixtime(timestamp_unix, 'yyyy-MM') AS month,
    from_unixtime(timestamp_unix, 'yyyy-MM-dd') AS date_only
FROM ratings;

-- Analizar cambio de ratings en el tiempo
SELECT 
    movie_id,
    MIN(date_only) AS primera_calificacion,
    MAX(date_only) AS ultima_calificacion,
    ROUND(AVG(CASE WHEN month <= '1997-12' THEN rating END), 2) AS rating_primeros_meses,
    ROUND(AVG(CASE WHEN month >= '1998-03' THEN rating END), 2) AS rating_ultimos_meses,
    ROUND(
        AVG(CASE WHEN month >= '1998-03' THEN rating END) - 
        AVG(CASE WHEN month <= '1997-12' THEN rating END), 
        2
    ) AS cambio_rating
FROM ratings_with_date
GROUP BY movie_id
HAVING COUNT(*) >= 50
ORDER BY ABS(cambio_rating) DESC
LIMIT 20;
```

**💡 Interpretación:**

- `cambio_rating > 0`: La película mejoró su percepción con el tiempo
- `cambio_rating < 0`: La película perdió popularidad con el tiempo

---

### 6.5 Matriz de Correlación: Ratings por Usuario Activo

> **📊 Pregunta:** ¿Los usuarios activos califican diferente que los pasivos?

#### SQL:

```sql
WITH user_activity AS (
    SELECT 
        user_id,
        COUNT(*) AS num_ratings,
        CASE 
            WHEN COUNT(*) >= 200 THEN 'Muy Activo'
            WHEN COUNT(*) >= 100 THEN 'Activo'
            WHEN COUNT(*) >= 50 THEN 'Moderado'
            ELSE 'Pasivo'
        END AS nivel_actividad
    FROM ratings
    GROUP BY user_id
)
SELECT 
    ua.nivel_actividad,
    COUNT(DISTINCT r.user_id) AS num_usuarios,
    COUNT(*) AS total_ratings,
    ROUND(AVG(r.rating), 2) AS rating_promedio,
    ROUND(STDDEV(r.rating), 2) AS rating_stddev
FROM ratings r
JOIN user_activity ua ON r.user_id = ua.user_id
GROUP BY ua.nivel_actividad
ORDER BY 
    CASE ua.nivel_actividad
        WHEN 'Muy Activo' THEN 1
        WHEN 'Activo' THEN 2
        WHEN 'Moderado' THEN 3
        ELSE 4
    END;
```

**🔍 Concepto WITH (CTE - Common Table Expression):**

```sql
WITH user_activity AS (
    -- Subconsulta temporal nombrada
    SELECT user_id, COUNT(*) AS num_ratings
    FROM ratings GROUP BY user_id
)
-- Ahora usamos user_activity como si fuera una tabla
SELECT * FROM user_activity;
```

**Ventajas de CTE:**

- Código más legible
- Evita subconsultas anidadas complejas
- Se puede referenciar múltiples veces

---

### 6.6 Top Películas por Segmento de Rating

> **📊 Pregunta:** ¿Cuáles son las películas más populares entre cada grupo de calificación?

#### SQL:

```sql
SELECT 
    rating AS segmento,
    movie_id,
    COUNT(*) AS num_ratings_en_segmento,
    RANK() OVER (PARTITION BY rating ORDER BY COUNT(*) DESC) AS ranking
FROM ratings
GROUP BY rating, movie_id
HAVING RANK() OVER (PARTITION BY rating ORDER BY COUNT(*) DESC) <= 5
ORDER BY rating DESC, ranking;
```

**🔍 Concepto WINDOW FUNCTIONS:**

- `RANK() OVER (...)`: Asigna un ranking dentro de cada partición
- `PARTITION BY rating`: Crea grupos por cada valor de rating (1-5)
- `ORDER BY COUNT(*) DESC`: Ordena por cantidad descendente

**Resultado conceptual:**

```
segmento=5 (5 estrellas):
  ranking 1: movie 318 (150 calificaciones de 5★)
  ranking 2: movie 50 (140 calificaciones de 5★)
  ...
segmento=4 (4 estrellas):
  ranking 1: movie 100 (200 calificaciones de 4★)
  ranking 2: movie 258 (180 calificaciones de 4★)
  ...
```

---

### 6.7 Análisis de "Long Tail": Distribución de Popularidad

> **📊 Pregunta:** ¿Cuántas películas concentran el 80% de los ratings? (Principio de Pareto)

#### SQL:

```sql
WITH movie_popularity AS (
    SELECT 
        movie_id,
        COUNT(*) AS num_ratings
    FROM ratings
    GROUP BY movie_id
    ORDER BY num_ratings DESC
),
cumulative_ratings AS (
    SELECT 
        movie_id,
        num_ratings,
        SUM(num_ratings) OVER (ORDER BY num_ratings DESC ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS cumsum,
        100000 AS total
    FROM movie_popularity
)
SELECT 
    COUNT(*) AS num_peliculas,
    SUM(num_ratings) AS total_ratings,
    ROUND(SUM(num_ratings) * 100.0 / 100000, 2) AS porcentaje_acumulado
FROM cumulative_ratings
WHERE cumsum <= 80000  -- 80% del total
UNION ALL
SELECT 
    COUNT(*) AS num_peliculas,
    SUM(num_ratings) AS total_ratings,
    ROUND(SUM(num_ratings) * 100.0 / 100000, 2) AS porcentaje_acumulado
FROM cumulative_ratings;
```

**💡 Interpretación Esperada:**

- ~20% de las películas (~336 películas) concentran 80% de los ratings
- ~80% de las películas (~1346 películas) reciben solo 20% de los ratings
- Esto confirma el "efecto Long Tail" en consumo de contenido

---

## 🧹 LIMPIEZA Y CIERRE

⏱️ **Tiempo estimado:** 2-3 minutos

---

### 🛑 Paso 1: Salir de Hive

```sql
-- Dentro de Hive, ejecutar:
EXIT;

-- O simplemente:
quit;
```

---

### 🛑 Paso 2: Eliminar Datos en Hive (Opcional)

> ⚠️ Solo ejecutar si quieres eliminar todo lo creado en la práctica

```bash
# Conectar de nuevo a Hive
hive

# Ejecutar comandos de limpieza
```

```sql
-- Usar la base de datos
USE movielens;

-- Eliminar tablas
DROP TABLE IF EXISTS ratings;
DROP TABLE IF EXISTS movie_stats;

-- Eliminar base de datos
DROP DATABASE IF EXISTS movielens;

-- Verificar limpieza
SHOW DATABASES;
SHOW TABLES;

-- Salir
EXIT;
```

---

### 🛑 Paso 3: Eliminar Datos en HDFS

```bash
# Eliminar dataset original
hdfs dfs -rm -r /datasets/movielens/

# Eliminar resultados exportados
hdfs dfs -rm -r /results/movielens/

# Eliminar archivos temporales
hdfs dfs -rm -r /tmp/hive/

# Verificar limpieza
hdfs dfs -ls /datasets/
hdfs dfs -ls /results/
```

---

### 🛑 Paso 4: Eliminar Exportaciones Locales

```bash
# Eliminar exportaciones en filesystem local
rm -rf /tmp/movielens_export/
rm -rf /tmp/reports/

# Verificar
ls /tmp/ | grep -E 'movielens|reports'
# No debe mostrar nada
```

---

### 🛑 Paso 5: Detener Servicios Hadoop y YARN

```bash
# Detener YARN
stop-yarn.sh

# Detener HDFS
stop-dfs.sh

# O detener todo de una vez
stop-all.sh

# Verificar que todos los servicios se detuvieron
jps
# Solo debe aparecer: Jps
```

---

### 🛑 Paso 6: Verificación Final

```bash
# Verificar que no hay procesos Java de Hadoop
jps

# Verificar que las interfaces web no responden
curl -I http://localhost:9870 2>&1 | grep "Failed to connect"
curl -I http://localhost:8088 2>&1 | grep "Failed to connect"
```

**✅ Salida esperada:**

```
1234 Jps

curl: (7) Failed to connect to localhost port 9870: Connection refused
curl: (7) Failed to connect to localhost port 8088: Connection refused
```

---

## 📚 RESUMEN DE APRENDIZAJE

---

### ✅ Conceptos de Big Data Cubiertos

| Concepto                                         | Explicación                                                          | Dónde se Aplicó                                        |
| ------------------------------------------------ | --------------------------------------------------------------------- | -------------------------------------------------------- |
| **HDFS (Hadoop Distributed File System)**  | Sistema de archivos distribuido que replica datos en múltiples nodos | Paso 2: Carga de u.data a HDFS con replicación 3x       |
| **MapReduce**                              | Paradigma de programación paralela (Map → Shuffle → Reduce)        | Paso 4: Todas las consultas con GROUP BY                 |
| **YARN (Yet Another Resource Negotiator)** | Gestor de recursos y scheduler de jobs                                | Paso 5: Monitoreo de jobs en http://localhost:8088       |
| **Hive**                                   | Data warehouse SQL sobre Hadoop (HiveQL)                              | Paso 3-4: Consultas SQL traducidas a MapReduce           |
| **Bloques HDFS**                           | División de archivos en bloques de 128 MB (default)                  | Paso 2.6: Análisis de bloques con `hdfs fsck`         |
| **Replicación**                           | Copias de bloques en múltiples DataNodes (fault tolerance)           | Paso 2.3: Factor de replicación = 3                     |
| **Particionamiento**                       | División de datos para procesamiento paralelo                        | Paso 4: Mappers procesan particiones en paralelo         |
| **Agregaciones distribuidas**              | COUNT, SUM, AVG ejecutadas en paralelo                                | Paso 4: Todas las consultas con funciones de agregación |
| **Tablas externas**                        | Tablas Hive que referencian datos externos sin moverlos               | Paso 3.4: CREATE EXTERNAL TABLE                          |
| **Materialización**                       | Pre-cálculo de resultados para queries rápidas                      | Paso 6.4: CREATE TABLE AS SELECT                         |

---

### 📊 Estadísticas del Dataset Procesado

| Métrica                         | Valor                        |
| -------------------------------- | ---------------------------- |
| **Total de ratings**       | 100,000                      |
| **Usuarios únicos**       | 943                          |
| **Películas únicas**     | 1,682                        |
| **Período**               | Septiembre 1997 - Abril 1998 |
| **Tamaño del archivo**    | 1.9 MB (u.data)              |
| **Formato**                | TSV (Tab-Separated Values)   |
| **Rating promedio**        | ~3.53 estrellas              |
| **Rating más común**     | 4 estrellas (34.17%)         |
| **Usuario más activo**    | User 405 (737 ratings)       |
| **Película más popular** | Movie 50 (583 ratings)       |

---

### 🎯 Resultados de Consultas Principales

#### 1. Distribución de Ratings:

```
★★★★★ (5): 21,201 ratings (21.20%)
★★★★☆ (4): 34,174 ratings (34.17%) ← Más común
★★★☆☆ (3): 27,145 ratings (27.15%)
★★☆☆☆ (2): 11,370 ratings (11.37%)
★☆☆☆☆ (1):  6,110 ratings (6.11%)
```

#### 2. Top 5 Películas Más Calificadas:

| Rank | Movie ID | # Ratings | Avg Rating |
| ---- | -------- | --------- | ---------- |
| 1    | 50       | 583       | 4.36       |
| 2    | 258      | 509       | 4.16       |
| 3    | 100      | 508       | 3.59       |
| 4    | 181      | 507       | 4.07       |
| 5    | 294      | 485       | 4.20       |

---

### 🔧 Comandos Esenciales para Referencia Rápida

#### Iniciar Hadoop:

```bash
start-dfs.sh && start-yarn.sh
jps  # Verificar servicios
```

#### Cargar datos a HDFS:

```bash
hdfs dfs -put archivo.data /ruta/en/hdfs/
hdfs dfs -ls /ruta/en/hdfs/
```

#### Conectar a Hive:

```bash
hive
```

#### Configuración obligatoria en Hive:

```sql
SET hive.exec.mode.local.auto=false;
SET mapreduce.framework.name=yarn;
```

#### Monitorear jobs:

```
YARN UI: http://localhost:8088
HDFS UI: http://localhost:9870
```

#### Detener Hadoop:

```bash
stop-all.sh
```

---

### 🎓 Habilidades Desarrolladas

✅ **Operaciones HDFS:**

- Cargar archivos desde sistema local a HDFS
- Explorar estructura distribuida de archivos
- Entender replicación y bloques

✅ **Gestión Hive:**

- Crear bases de datos y tablas externas
- Ejecutar consultas SQL analíticas
- Configurar ejecución con YARN

✅ **MapReduce:**

- Entender flujo Map → Shuffle → Reduce
- Interpretar progreso de jobs
- Analizar distribución de trabajo

✅ **Monitoreo:**

- Usar interfaces web (YARN, HDFS)
- Leer logs de aplicaciones
- Diagnosticar errores comunes

✅ **Exportación:**

- Guardar resultados en CSV local
- Exportar a HDFS para compartir
- Crear tablas materializadas

✅ **Análisis Avanzado:**

- Window functions (RANK, OVER)
- CTEs (WITH clauses)
- Agregaciones complejas

---

## 🚀 PRÓXIMOS PASOS

### Nivel Intermedio:

1. **Particionamiento de Tablas:**

   ```sql
   CREATE TABLE ratings_partitioned (
       user_id INT, movie_id INT, rating INT
   )
   PARTITIONED BY (year STRING, month STRING)
   STORED AS ORC;
   ```
2. **Bucketing para Optimización:**

   ```sql
   CREATE TABLE ratings_bucketed (
       user_id INT, movie_id INT, rating INT
   )
   CLUSTERED BY (movie_id) INTO 10 BUCKETS
   STORED AS PARQUET;
   ```
3. **Joins con Otras Tablas:**

   - Cargar `u.item` (información de películas)
   - Cargar `u.user` (información demográfica)
   - Realizar análisis combinados

### Nivel Avanzado:

1. **Integración con Spark:**

   ```bash
   spark-sql --master yarn
   ```
2. **Formatos Columnares:**

   - ORC (Optimized Row Columnar)
   - Parquet (formato Apache)
   - Comparar rendimiento
3. **Tuning de MapReduce:**

   ```sql
   SET mapreduce.job.reduces=10;
   SET mapreduce.map.memory.mb=4096;
   ```
4. **UDFs (User Defined Functions):**

   - Crear funciones personalizadas en Java
   - Registrar en Hive

---

## 🔗 RECURSOS ADICIONALES

| Recurso                        | URL                                                                                         |
| ------------------------------ | ------------------------------------------------------------------------------------------- |
| **Hadoop 3.4.0 Docs**    | https://hadoop.apache.org/docs/r3.4.0/                                                      |
| **Hive Language Manual** | https://cwiki.apache.org/confluence/display/Hive/LanguageManual                             |
| **MovieLens Dataset**    | https://grouplens.org/datasets/movielens/                                                   |
| **YARN REST API**        | https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/ResourceManagerRest.html |
| **HDFS Commands**        | https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html     |

---

<div align="center">

## 🎉 ¡Práctica Completada con Éxito!

**Has procesado 100,000 registros reales con Hadoop + Hive**

✅ HDFS: Sistema de archivos distribuido configurado
✅ YARN: Jobs MapReduce ejecutados correctamente
✅ Hive: Consultas SQL traducidas a MapReduce
✅ Monitoreo: Interfaces web y logs dominados
✅ Exportación: Resultados guardados en CSV y HDFS
✅ Big Data: Conceptos fundamentales aplicados

---

### 📖 Para Estudiar

1. Revisa los logs de YARN de tus consultas más complejas
2. Experimenta cambiando el número de reducers
3. Compara tiempos de ejecución con diferentes configuraciones
4. Carga los otros archivos del dataset (u.item, u.user) y practica JOINS

---

*Guía de Estudio Big Data - Febrero 2026*
*Dataset: MovieLens 100K | Hadoop 3.4.0 | Hive 3.3.1*

</div>