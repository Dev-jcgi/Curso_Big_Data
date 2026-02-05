# 📚 Guía de Estudio: Big Data con Hadoop y Spark

> **Universidad ISU - Sistemas Inteligentes**  
> **Módulo:** Big Data y Computación Distribuida  
> **Última actualización:** Febrero 2026

<div align="center">

![Hadoop](https://img.shields.io/badge/Hadoop-3.4.0-yellow?style=for-the-badge&logo=apachehadoop)
![Hive](https://img.shields.io/badge/Hive-3.3.1-orange?style=for-the-badge&logo=apachehive)
![Spark](https://img.shields.io/badge/Spark-3.5-E25A1C?style=for-the-badge&logo=apachespark)
![Python](https://img.shields.io/badge/Python-3.8+-3776AB?style=for-the-badge&logo=python)

**📊 11 Recursos de Aprendizaje | ⏱️ ~50 horas | 🎬 2 Datasets Reales**

</div>

---

## 📑 Contenido del Curso

Este workspace contiene material completo para aprender Big Data con **Hadoop** y **Apache Spark**:

### **Módulo Hadoop** (~35h)
- 8 guías de instalación, configuración y práctica
- Dataset MovieLens 100K (100,000 registros reales)
- Tecnologías: HDFS, YARN, MapReduce, Hive

### **Módulo Spark** (~15h)
- 3 notebooks Jupyter interactivos
- Dataset e-commerce (tipo Instacart, 3.2M registros)
- Tecnologías: PySpark, Pandas, visualización

### **Objetivos de Aprendizaje**
- ✅ Configurar entornos Big Data desde cero
- ✅ Procesar datos distribuidos con Hadoop MapReduce
- ✅ Analizar datos con Hive (SQL sobre Hadoop)
- ✅ Procesar en memoria con Apache Spark
- ✅ Comparar Pandas vs Spark para diferentes volúmenes de datos

---

## � Descargar Datasets

Los archivos de datos están hospedados en **Google Drive** debido a su tamaño (>650 MB total):

**🔗 [Descargar Datasets E-commerce y MovieLens](https://drive.google.com/file/d/1AYd3PUi53Q7XynCg_7KKraUZYft3kjjo/view?usp=sharing)**

**Instrucciones:**
1. Descargar el archivo desde el enlace de arriba
2. Extraer el contenido en las carpetas correspondientes:
   - `Spark/data/` → archivos CSV de e-commerce
   - `Hadoop/ml-100k/` → archivos del dataset MovieLens
3. Ejecutar los notebooks y prácticas

**Contenido del archivo:**
- `orders.csv` (103 MB) - Pedidos de clientes
- `order_products__prior.csv` (550 MB) - Productos en pedidos
- `products.csv`, `aisles.csv`, `departments.csv` - Catálogos
- `u.data`, `u.user`, `u.item` - Dataset MovieLens 100K

---

## �📂 Estructura del Workspace

```
Articulos/
├── README.md                    # Esta guía
│
├── Hadoop/                      # Módulo Hadoop
│   ├── Guia_CentOS_Hadoop_Hive.md              # Instalación completa (27 pasos)
│   ├── Guia_Video_Instalacion_Hadoop.md        # Guía rápida para video tutorial
│   ├── Explicacion_Archivos_Configuracion_Hadoop.md  # Configuración XML
│   ├── Comandos_Hadoop_Explicados.md           # Referencia de comandos
│   ├── Practica_Hadoop_Hive_MovieLens.md       # Práctica avanzada 100K registros
│   ├── Guia_Haddop_&_HIVE.md                   # Práctica básica de ventas
│   └── ml-100k/                                # Dataset MovieLens
│       ├── u.data              # 100,000 ratings
│       ├── u.user              # 943 usuarios
│       └── u.item              # 1,682 películas
│
└── Spark/                       # Módulo Spark
    ├── Big_Data.ipynb                          # Conceptos y caso de estudio
    ├── Analisis_Pandas.ipynb                   # Análisis con Pandas
    ├── Analisis_Spark.ipynb                    # Análisis con PySpark
    └── data/                                   # Dataset e-commerce
        ├── orders.csv          # 206K pedidos
        ├── order_products__prior.csv  # 3.2M productos
        ├── products.csv        # 49K productos
        ├── aisles.csv          # 134 pasillos
        └── departments.csv     # 21 departamentos
```

---

## 📚 Contenido Detallado

### 📁 **Carpeta Hadoop/**

Material para aprender el ecosistema Hadoop (HDFS, MapReduce, Hive):

| Archivo | Descripción | Tiempo |
|---------|-------------|--------|
| [Guia_CentOS_Hadoop_Hive.md](Hadoop/Guia_CentOS_Hadoop_Hive.md) | Instalación completa paso a paso (27 pasos) | 6-8h |
| [Guia_Video_Instalacion_Hadoop.md](Hadoop/Guia_Video_Instalacion_Hadoop.md) | Guía condensada para video tutorial (20-30 min) con explicaciones detalladas de comandos bash, XMLs y archivos .env | 2-3h |
| [Explicacion_Archivos_Configuracion_Hadoop.md](Hadoop/Explicacion_Archivos_Configuracion_Hadoop.md) | Explicación de XMLs: core-site, hdfs-site, mapred-site, yarn-site | 4-5h |
| [Comandos_Hadoop_Explicados.md](Hadoop/Comandos_Hadoop_Explicados.md) | Referencia de comandos HDFS, YARN, Hive | 3-4h |
| [Practica_Hadoop_Hive_MovieLens.md](Hadoop/Practica_Hadoop_Hive_MovieLens.md) | Práctica avanzada con 100K registros reales | 5-6h |
| [Guia_Haddop_&_HIVE.md](Hadoop/Guia_Haddop_&_HIVE.md) | Práctica básica: análisis de ventas | 3-4h |

**Dataset: ml-100k/**
- `u.data`: 100,000 ratings de películas (1-5 estrellas)
- `u.user`: 943 usuarios con datos demográficos (edad, género, ocupación)
- `u.item`: 1,682 películas con géneros

**Qué aprenderás:**
- Instalar y configurar Hadoop 3.4.0 en CentOS desde cero
- Comprender archivos de configuración XML (core-site, hdfs-site, mapred-site, yarn-site)
- Configurar variables de entorno (.bashrc, hadoop-env.sh, yarn-env.sh, hive-env.sh)
- Usar comandos HDFS para gestionar archivos distribuidos
- Crear tablas Hive y ejecutar consultas SQL sobre Big Data
- Analizar 100K registros reales con MapReduce
- Monitorear jobs en YARN Web UI
- Solucionar problemas comunes de configuración
- Grabar video tutoriales de instalación (guía para video)

---

### 📁 **Carpeta Spark/**

Material para aprender Apache Spark y compararlo con Pandas:

| Notebook | Descripción | Tiempo |
|----------|-------------|--------|
| [Big_Data.ipynb](Spark/Big_Data.ipynb) | Conceptos de Big Data y caso de estudio | 2-3h |
| [Analisis_Pandas.ipynb](Spark/Analisis_Pandas.ipynb) | Análisis exploratorio con Pandas (tradicional) | 4-5h |
| [Analisis_Spark.ipynb](Spark/Analisis_Spark.ipynb) | Mismo análisis con PySpark (distribuido) | 5-6h |

**Dataset: data/** (E-commerce tipo Instacart)
- `orders.csv`: 206,000 pedidos de clientes
- `order_products__prior.csv`: 3.2 millones de productos comprados
- `products.csv`: 49,000 productos del catálogo
- `aisles.csv` y `departments.csv`: Categorías

**Qué aprenderás:**
- Cargar y procesar CSVs con Pandas y PySpark
- Realizar análisis exploratorio de datos (EDA)
- Aplicar transformaciones lazy y acciones en Spark
- Usar cache y optimizaciones (broadcast, repartition)
- Crear visualizaciones con Matplotlib
- Market Basket Analysis (productos comprados juntos)
- Comparar rendimiento: Pandas vs Spark

**Contenido de los Notebooks:**

**1. Big_Data.ipynb**
- Contexto del problema (empresa e-commerce)
- Metodología de análisis de Big Data
- Desarrollo práctico con Pandas (6 visualizaciones)
- Conceptos: volumen, variedad, velocidad

**2. Analisis_Pandas.ipynb**
- Preprocesamiento: carga, limpieza, merge
- Análisis de distribución de pedidos
- Patrones temporales (día/hora)
- Top 5 productos más vendidos
- Productos por departamento
- Market Basket Analysis con NetworkX (grafo)

**3. Analisis_Spark.ipynb**
- Configuración de SparkSession
- Conceptos clave: RDD, Transformations, Actions, DAG
- Mismo análisis que Pandas pero con PySpark
- Window Functions (técnica avanzada)
- UDFs (User Defined Functions)
- Optimizaciones: cache, broadcast, persist
- Comparación de rendimiento

---

## 🛠 Tecnologías

### Hadoop Stack
- **CentOS** 9 - Sistema operativo Linux
- **Java** 8 - Runtime para Hadoop
- **Hadoop** 3.4.0 - Framework distribuido
- **HDFS** - Almacenamiento distribuido
- **YARN** - Gestión de recursos
- **MapReduce** - Procesamiento paralelo
- **Hive** 3.1.3 - SQL sobre Hadoop

### Spark Stack
- **Python** 3.8+ - Lenguaje principal
- **Apache Spark** 3.5 - Motor en memoria
- **PySpark** - API Python para Spark
- **Pandas** - Análisis de datos
- **Matplotlib/Seaborn** - Visualización
- **NetworkX** - Grafos
- **Jupyter** - Notebooks interactivos

---

## 🎯 Ruta de Aprendizaje

### **Semanas 1-3: Módulo Hadoop**

**Semana 1: Instalación**
- Instalar CentOS en VirtualBox
- Configurar usuario hadoop y Java
- Instalar Hadoop 3.4.0
- Configurar archivos XML

**Semana 2: Comandos y Hive**
- Practicar comandos HDFS
- Instalar Hive
- Crear tablas y consultas SQL

**Semana 3: Prácticas**
- Análisis de ventas (básico)
- Dataset MovieLens 100K (avanzado)

### **Semanas 4-5: Módulo Spark**

**Semana 4: Pandas**
- Instalar Python y librerías
- Notebook Big_Data.ipynb
- Notebook Analisis_Pandas.ipynb

**Semana 5: PySpark**
- Notebook Analisis_Spark.ipynb
- Comparar Pandas vs Spark
- Optimizaciones avanzadas

---

## 📊 Comparación: Hadoop vs Spark

| Característica | Hadoop MapReduce | Apache Spark |
|----------------|------------------|--------------|
| **Velocidad** | Disco (lento) | Memoria (100x más rápido) |
| **Facilidad** | HiveQL (SQL) | Python + SQL |
| **Casos de uso** | ETL masivos, batch | Analytics, ML, streaming |
| **Cuándo usar** | TB-PB datos históricos | Análisis iterativo, interactivo |

---

## ⚠️ Solución de Problemas

### **Hadoop**

**Error: "Permission denied: user=dr.who"**
```xml
<!-- Agregar a core-site.xml -->
<property>
    <name>hadoop.http.staticuser.user</name>
    <value>hadoop</value>
</property>
```

**Error: "Return Code 2" en Hive**
```sql
SET hive.execution.engine=mr;
SET mapreduce.framework.name=yarn;
```

**HDFS en Safe Mode**
```bash
hdfs dfsadmin -safemode leave
```

### **Spark**

**OutOfMemoryError**
```python
spark = SparkSession.builder \
    .config("spark.driver.memory", "8g") \
    .getOrCreate()
```

**Lentitud en joins**
```python
from pyspark.sql.functions import broadcast
result = large_df.join(broadcast(small_df), "key")
```

---

## 📝 Comandos Útiles

### **HDFS**
```bash
hdfs dfs -ls /                    # Listar
hdfs dfs -put local.txt /hdfs/    # Subir
hdfs dfs -get /hdfs/file.txt .    # Descargar
hdfs dfs -cat /archivo.txt        # Ver contenido
```

### **Hive**
```sql
SHOW DATABASES;
CREATE DATABASE nombre;
USE nombre;
SHOW TABLES;
SELECT * FROM tabla LIMIT 10;
```

### **Spark**
```python
# Leer CSV
df = spark.read.csv("file.csv", header=True, inferSchema=True)

# Transformaciones
df.filter(col("age") > 18).select("name", "age")

# Acciones
df.count()
df.show()
```

---

## 📚 Recursos Adicionales

### Documentación Oficial
- **Hadoop:** https://hadoop.apache.org/docs/stable/
- **Hive:** https://cwiki.apache.org/confluence/display/Hive/
- **Spark:** https://spark.apache.org/docs/latest/
- **PySpark:** https://spark.apache.org/docs/latest/api/python/

### Interfaces Web (Hadoop)
- HDFS NameNode: http://localhost:9870
- YARN ResourceManager: http://localhost:8088
- MapReduce JobHistory: http://localhost:19888

---

<div align="center">

**¡Éxito en tu aprendizaje de Big Data!**

*Universidad ISU - Febrero 2026*

</div>
