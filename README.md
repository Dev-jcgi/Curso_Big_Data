# 📚 Guía de Estudio: Big Data con Hadoop y Spark

> **Universidad ISU - Sistemas Inteligentes**
> **Módulo:** Big Data y Computación Distribuida
> **Última actualización:** Febrero 2026

<div align="center">

![Hadoop](https://img.shields.io/badge/Hadoop-3.4.0-yellow?style=for-the-badge&logo=apachehadoop)
![Hive](https://img.shields.io/badge/Hive-3.3.1-orange?style=for-the-badge&logo=apachehive)
![Spark](https://img.shields.io/badge/Spark-3.5-E25A1C?style=for-the-badge&logo=apachespark)
![Python](https://img.shields.io/badge/Python-3.8+-3776AB?style=for-the-badge&logo=python)

**📊 11 Recursos de Aprendizaje | ⏱️ ~8 horas | 🎬 2 Datasets Reales**

</div>

---

## 📑 Contenido del Curso

**Módulo Hadoop** (~5h): HDFS, YARN, MapReduce, Hive | Dataset MovieLens 100K

**Módulo Spark** (~3h): PySpark, Pandas | Dataset E-commerce 3.2M registros

---

## � Descargar Datasets

Los archivos de datos están hospedados en **Google Drive** debido a su tamaño (>650 MB total):

**🔗 [Descargar Datasets E-commerce y MovieLens](https://drive.google.com/file/d/1AYd3PUi53Q7XynCg_7KKraUZYft3kjjo/view?usp=drive_link)**

**Instrucciones:**

1. Descargar el archivo desde el enlace de arriba
2. Extraer el contenido en las carpetas correspondientes:
   - `Spark/data/` → archivos CSV de e-commerce
   - `Hadoop/dataset/ml-100k/` → archivos del dataset MovieLens
3. Ejecutar los notebooks y prácticas

**Contenido del archivo:**

- `orders.csv` (103 MB) - Pedidos de clientes
- `order_products__prior.csv` (550 MB) - Productos en pedidos
- `products.csv`, `aisles.csv`, `departments.csv` - Catálogos
- `u.data`, `u.user`, `u.item` - Dataset MovieLens 100K

> 💡 **Nota:** Los archivos XML de configuración ya están incluidos en `Hadoop/Archivos_Configuracion/` y listos para usar.

---

## �📂 Estructura del Workspace

```
Articulos/
├── README.md                    # Esta guía
│
├── Hadoop/                      # Módulo Hadoop
│   ├── Instalacion_Configuracion_Hadop_Hive.md # Instalación completa paso a paso
│   ├── Explicacion_Archivos_Configuracion_Hadoop.md  # Configuración XML detallada
│   ├── Comandos_Hadoop_Explicados.md           # Referencia de comandos HDFS/YARN
│   ├── Practica_Hive_MovieLens.md              # Práctica avanzada 100K registros
│   ├── Practica_Basica_Hive.md                 # Práctica básica de análisis
│   │
│   ├── Archivos_Configuracion/                 # ⭐ Archivos XML listos para usar
│   │   ├── core-site.xml       # Configuración core de Hadoop
│   │   ├── hdfs-site.xml       # Configuración HDFS
│   │   ├── mapred-site.xml     # Configuración MapReduce
│   │   ├── yarn-site.xml       # Configuración YARN
│   │   ├── hive-site.xml       # Configuración Hive
│   │   └── hive-env.sh         # Variables de entorno Hive
│   │
│   └── dataset/                                # Datasets para prácticas
│       ├── ml-100k/            # Dataset MovieLens completo
│       │   ├── u.data          # 100,000 ratings
│       │   ├── u.user          # 943 usuarios
│       │   └── u.item          # 1,682 películas
│       └── u.data              # Archivo de ratings (copia rápida)
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

| Archivo                                                                                          | Descripción                                                                            |
| ------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------- |
| [Instalacion_Configuracion_Hadop_Hive.md](Hadoop/Instalacion_Configuracion_Hadop_Hive.md)           | Instalación y configuración completa paso a paso                                      |
| [Practica_Basica_Hive.md](Hadoop/Practica_Basica_Hive.md)                                           | Práctica básica de análisis con Hive                                                 |
| [Practica_Hive_MovieLens.md](Hadoop/Practica_Hive_MovieLens.md)                                     | Práctica avanzada con dataset MovieLens (100K registros reales)                        |
| [Comandos_Hadoop_Explicados.md](Hadoop/Comandos_Hadoop_Explicados.md)                               | Referencia completa de comandos HDFS, YARN, Hive                                        |
| [Explicacion_Archivos_Configuracion_Hadoop.md](Hadoop/Explicacion_Archivos_Configuracion_Hadoop.md) | Explicación detallada de XMLs: core-site, hdfs-site, mapred-site, yarn-site, hive-site |

**📁 Archivos de Configuración Listos para Usar:**

> 💡 **Nuevo:** Ahora incluye archivos XML y de configuración preconfigurados en `Hadoop/Archivos_Configuracion/`

| Archivo                                                       | Descripción                                            |
| ------------------------------------------------------------- | ------------------------------------------------------- |
| [core-site.xml](Hadoop/Archivos_Configuracion/core-site.xml)     | Configuración del NameNode y propiedades core          |
| [hdfs-site.xml](Hadoop/Archivos_Configuracion/hdfs-site.xml)     | Configuración del sistema de archivos distribuido      |
| [mapred-site.xml](Hadoop/Archivos_Configuracion/mapred-site.xml) | Configuración de MapReduce sobre YARN                  |
| [yarn-site.xml](Hadoop/Archivos_Configuracion/yarn-site.xml)     | Configuración del ResourceManager y NodeManager        |
| [hive-site.xml](Hadoop/Archivos_Configuracion/hive-site.xml)     | Configuración del metastore y warehouse de Hive        |
| [hive-env.sh](Hadoop/Archivos_Configuracion/hive-env.sh)         | Variables de entorno para Hive (JAVA_HOME, HADOOP_HOME) |

**📊 Dataset: dataset/**

- `ml-100k/u.data`: 100,000 ratings de películas (1-5 estrellas)
- `ml-100k/u.user`: 943 usuarios con datos demográficos (edad, género, ocupación)
- `ml-100k/u.item`: 1,682 películas con títulos y géneros
- `u.data`: Copia directa del archivo de ratings para acceso rápido

**Qué aprenderás:** Instalar Hadoop/Hive, usar XMLs preconfigurados, comandos HDFS, consultas Hive sobre 100K registros, monitorear YARN Web UI

---

### 📁 **Carpeta Spark/**

Material para aprender Apache Spark y compararlo con Pandas:

| Notebook                                          | Descripción                                    |
| ------------------------------------------------- | ----------------------------------------------- |
| [Big_Data.ipynb](Spark/Big_Data.ipynb)               | Conceptos de Big Data y caso de estudio         |
| [Analisis_Pandas.ipynb](Spark/Analisis_Pandas.ipynb) | Análisis exploratorio con Pandas (tradicional) |
| [Analisis_Spark.ipynb](Spark/Analisis_Spark.ipynb)   | Mismo análisis con PySpark (distribuido)       |

**Dataset: data/** (E-commerce tipo Instacart)

- `orders.csv`: 206,000 pedidos de clientes
- `order_products__prior.csv`: 3.2 millones de productos comprados
- `products.csv`: 49,000 productos del catálogo
- `aisles.csv` y `departments.csv`: Categorías

**Qué aprenderás:** EDA con Pandas/PySpark, transformaciones Spark, optimizaciones (cache, broadcast), Market Basket Analysis, comparar rendimiento

---

## 🎬 Video Tutorial

**🔗 [Instalación Hadoop + Hive en YouTube](https://youtu.be/IMESmbcKoX4)** (~30 min)

## �🎯 Ruta de Aprendizaje

### **Módulo Hadoop**

**1: Instalación**

- Instalar CentOS en VirtualBox
- Configurar usuario hadoop y Java
- Instalar Hadoop 3.4.0
- Configurar archivos XML

**2: Comandos y Hive**

- Practicar comandos HDFS
- Instalar Hive
- Crear tablas y consultas SQL

**3: Prácticas**

- Análisis de ventas (básico)
- Dataset MovieLens 100K (avanzado)

### **Módulo Spark**

**4: Pandas**

- Instalar Python y librerías
- Notebook Big_Data.ipynb
- Notebook Analisis_Pandas.ipynb

**5: PySpark**

- Notebook Analisis_Spark.ipynb
- Comparar Pandas vs Spark
- Optimizaciones avanzadas

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

### Tutoriales Complementarios

- [Hive Tutorial (ES)](https://aitor-medrano.github.io/iabd2223/hadoop/06hive.html)
- [YARN Architecture](https://labex.io/es/tutorials/linux-yarn-architecture-and-development-272324)
- [Spark en Windows](https://medium.com/@FitoMAD/guía-de-instalación-de-apache-spark-en-windows-ffac5ad132cb)
- [Hadoop-Hive Integration](https://www.aptech.com/resources/tutorials/hadoop-hive-integration-tutorial/)
- [Hadoop en CentOS](https://www.tutorialspoint.com/how-to-install-and-configure-apache-hadoop-on-a-single-node-in-centos-8)
- [Spark Quick Guide](https://www-tutorialspoint-com.translate.goog/apache_spark/apache_spark_quick_guide.htm)

### Videos Educativos

- [Big Data Series](https://www.youtube.com/watch?v=USvIAsZk8jE)
- [Hadoop &amp; Hive](https://www.youtube.com/watch?v=iWc0lG9dKFs)
- [Big Data Architecture](https://www.youtube.com/watch?v=oTZPxyE6QoY)

### Otros Recursos

- [Hadoop vs Spark](https://phoenixnap.com/kb/hadoop-vs-spark)
- [Data Science Resources](https://manushgupta.github.io/DS/)
- [PySpark RDD Creation](https://www.analyticsvidhya.com/blog/2022/08/create-rdd-in-apache-spark-using-pyspark/)

---

<div align="center">

**¡Éxito en tu aprendizaje de Big Data!**

*Universidad ISU - Febrero 2026*

</div>
