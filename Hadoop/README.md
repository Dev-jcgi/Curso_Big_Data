# 🐘 Módulo Hadoop

> Aprende a instalar, configurar y usar el ecosistema Hadoop (HDFS, YARN, MapReduce, Hive)

---

## 📋 Guías de Estudio

### 1️⃣ [Instalacion_Configuracion_Hadop_Hive.md](Instalacion_Configuracion_Hadop_Hive.md)
**Instalación completa paso a paso**

- Configurar CentOS 9 y usuario hadoop
- Instalar Java 8, Hadoop 3.4.0 y Hive 3.1.3
- Configurar archivos XML (usar carpeta `Archivos_Configuracion/`)
- Iniciar servicios: `start-all.sh`
- Verificar: http://localhost:9870 y http://localhost:8088

### 2️⃣ [Comandos_Hadoop_Explicados.md](Comandos_Hadoop_Explicados.md)
**Referencia de comandos HDFS, YARN y Hive**

- Comandos básicos HDFS: `ls`, `put`, `get`, `cat`, `rm`
- Gestión YARN: monitoreo de jobs y recursos
- Comandos Hive: crear tablas, cargar datos, consultas SQL

### 3️⃣ [Explicacion_Archivos_Configuracion_Hadoop.md](Explicacion_Archivos_Configuracion_Hadoop.md)
**Detalles de los archivos XML**

- `core-site.xml`: NameNode y propiedades core
- `hdfs-site.xml`: Replicación y directorios HDFS
- `mapred-site.xml`: MapReduce sobre YARN
- `yarn-site.xml`: ResourceManager y NodeManager
- `hive-site.xml`: Metastore y warehouse de Hive

---

## 🎯 Prácticas

### 📊 [Practica_Basica_Hive.md](Practica_Basica_Hive.md)
**Análisis básico con Hive**

**Objetivo:** Aprender a crear tablas, cargar datos y ejecutar consultas SQL en Hive

**Dataset:** Datos de ventas (productos, categorías, ventas)

**Ejercicios:**
- Crear base de datos y tablas en Hive
- Cargar datos desde archivos locales y HDFS
- Consultas de agregación (COUNT, SUM, AVG, GROUP BY)
- Joins entre tablas
- Exportar resultados

---

### 🎬 [Practica_Hive_MovieLens.md](Practica_Hive_MovieLens.md)
**Práctica avanzada con 100K registros reales**

**Objetivo:** Analizar el dataset MovieLens con técnicas avanzadas de Hive

**Dataset:** `dataset/ml-100k/`
- `u.data`: 100,000 ratings (userid, movieid, rating, timestamp)
- `u.user`: 943 usuarios (edad, género, ocupación)
- `u.item`: 1,682 películas (título, fecha, géneros)

**Ejercicios:**
1. **PASO 0:** Iniciar servicios Hadoop/Hive
2. **PASO 1:** Crear base de datos y cargar datos
3. **PASO 2:** Consultas básicas (top películas, usuarios activos)
4. **PASO 3:** Análisis demográfico (patrones por edad/género)
5. **PASO 4:** Joins complejos (combinar ratings + usuarios + películas)
6. **PASO 5:** Análisis temporal (tendencias por año/mes)
7. **PASO 6:** Consultas avanzadas (subconsultas, window functions)

**Técnicas avanzadas:**
- Tablas particionadas
- Window functions (RANK, ROW_NUMBER)
- Subconsultas correlacionadas
- Optimización de queries

---

## 📁 Archivos de Configuración

La carpeta `Archivos_Configuracion/` contiene XMLs listos para usar:

```
Archivos_Configuracion/
├── core-site.xml      # NameNode: hdfs://localhost:9000
├── hdfs-site.xml      # Replicación, directorios
├── mapred-site.xml    # Framework: YARN
├── yarn-site.xml      # ResourceManager, NodeManager
├── hive-site.xml      # Metastore Derby, warehouse
└── hive-env.sh        # JAVA_HOME, HADOOP_HOME
```

**Uso:** Copiar los archivos a `$HADOOP_HOME/etc/hadoop/` y `$HIVE_HOME/conf/`

---

## 📊 Dataset MovieLens

Ubicación: `dataset/ml-100k/`

| Archivo | Registros | Descripción |
|---------|-----------|-------------|
| u.data | 100,000 | Ratings de películas (1-5 estrellas) |
| u.user | 943 | Usuarios (edad, género, ocupación) |
| u.item | 1,682 | Películas (título, fecha, géneros) |
| u.genre | 19 | Lista de géneros cinematográficos |

---

## 🚀 Inicio Rápido

```bash
# 1. Iniciar servicios
start-all.sh

# 2. Verificar HDFS y YARN
jps

# 3. Abrir Hive
hive

# 4. Crear base de datos
CREATE DATABASE movielens;
USE movielens;

# 5. Seguir las prácticas
```

---

## 🌐 Interfaces Web

- **HDFS NameNode:** http://localhost:9870
- **YARN ResourceManager:** http://localhost:8088
- **MapReduce JobHistory:** http://localhost:19888

---

## 💡 Tips

- Siempre verificar que los servicios estén corriendo con `jps`
- Usar `hdfs dfs -ls /` para verificar HDFS
- Los logs están en `$HADOOP_HOME/logs/`
- Detener servicios: `stop-all.sh`

---

**¡Éxito con Hadoop! 🎉**
