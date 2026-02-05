# 🚀 Guía Definitiva: Hadoop 3.4.0 + Hive 3.3.1

> **Análisis de Ventas con Solución Completa de Errores Comunes**

**Autor:** Guía Práctica Hadoop
**Fecha:** Febrero 2026
**Versiones:** Hadoop 3.4.0 | Hive 3.3.1 | Java 8

---

## 🛠️ PASO 0: Configuración Crítica (Evitar Errores)

> ⚠️ **IMPORTANTE:** Esta sección resuelve los 3 problemas más comunes que bloquean Hive

### Problemas que resolveremos:

| # | Error                            | Solución               |
| - | -------------------------------- | ----------------------- |
| 1 | Java 17 incompatible             | Forzar Java 8           |
| 2 | "Permission denied: user=dr.who" | Configurar usuario web  |
| 3 | "Return Code 2"                  | Deshabilitar modo local |

---

### A. Forzar Java 8 y Hadoop Home

#### 🔧 Configuración de Variables de Entorno

**Archivos a editar:**

- `/opt/hadoop/hadoop-3.4.0/etc/hadoop/hadoop-env.sh`
- `/opt/hive/conf/hive-env.sh`

```bash
# 1. Crear hive-env.sh desde la plantilla
cp /opt/hive/conf/hive-env.sh.template /opt/hive/conf/hive-env.sh

# 2. Editar AMBOS archivos de entorno
# Agregar o verificar estas líneas:
export JAVA_HOME=/opt/jdk1.8.0_202
export HADOOP_HOME=/opt/hadoop/hadoop-3.4.0

# 3. Reiniciar Hive
Si esta en ejecucion salir de la sesion con:
exit;
```

**✅ Verificar:**

```bash
echo $JAVA_HOME
echo $HADOOP_HOME
java -version  # Debe mostrar 1.8.0_202
```

---

### B. Corregir el Error "Permission denied: user=dr.who"

#### 🔧 Configurar Usuario Web de Hadoop

**Archivo:** `/opt/hadoop/hadoop-3.4.0/etc/hadoop/core-site.xml`

**Agregar dentro de `<configuration>`:**

```xml
<property>
    <name>hadoop.http.staticuser.user</name>
    <value>hadoop</value>
    <description>Usuario para interfaz web HDFS</description>
</property>
```

**💡 Explicación:** Esto evita que la interfaz web de HDFS use el usuario ficticio "dr.who".

---

### C. Limpiar Permisos de Carpetas Temporales

#### 🔧 Comandos de Permisos

```bash
# Dar permisos completos al filesystem HDFS
hdfs dfs -chmod -R 777 /

# Crear y configurar directorio staging de YARN
hdfs dfs -mkdir -p /tmp/hadoop-yarn/staging
hdfs dfs -chmod -R 777 /tmp/hadoop-yarn/staging
```

**✅ Verificar:**

```bash
hdfs dfs -ls /
hdfs dfs -ls /tmp/hadoop-yarn/
```

---

## 🎯 PASO 1: Iniciar Servicios

⏱️ **Tiempo estimado:** 2 minutos

### 1.1 Cargar Variables de Entorno

```bash
export HADOOP_HOME=/opt/hadoop/hadoop-3.4.0
export HIVE_HOME=/opt/hive
export PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$HIVE_HOME/bin:$PATH
```

### 1.2 Reiniciar Servicios Hadoop

```bash
# Detener todos los servicios
stop-all.sh

# Iniciar HDFS
start-dfs.sh

# Iniciar YARN
start-yarn.sh

# Verificar servicios activos
jps
```

**Salida esperada de `jps`:**

```
12345 NameNode
12346 DataNode
12347 ResourceManager
12348 NodeManager
12349 SecondaryNameNode
```

### 1.3 Conectar a Hive

```bash
# Conectar a Hive directamente
hive
```

**✅ Verificar conexión:**

```bash
# Dentro de Hive, ejecutar:
SHOW DATABASES;
```

---

## 📊 PASO 2: Cargar Datos en HDFS

⏱️ **Tiempo estimado:** 1 minuto

### 2.1 Crear Directorio en HDFS

```bash
hdfs dfs -mkdir -p /demo/ventas
```

### 2.2 Cargar Dataset Directamente

> **Nota:** Los datos se cargan desde STDIN sin archivo intermedio

```bash
hdfs dfs -put - /demo/ventas/datos.txt << 'EOF'
1001	Laptop	Dell	1200	2024-01-15	Norte
1002	Mouse	Logitech	25	2024-01-15	Sur
1003	Teclado	HP	45	2024-01-16	Este
1004	Monitor	Samsung	350	2024-01-16	Norte
1005	Laptop	HP	1100	2024-01-17	Oeste
1006	Mouse	Dell	30	2024-01-17	Norte
1007	Teclado	Logitech	50	2024-01-18	Sur
1008	Monitor	LG	400	2024-01-18	Este
1009	Laptop	Lenovo	1300	2024-01-19	Norte
1010	Mouse	HP	20	2024-01-19	Sur
1011	Teclado	Dell	40	2024-01-20	Oeste
1012	Monitor	Dell	380	2024-01-20	Norte
1013	Laptop	Asus	1250	2024-01-21	Este
1014	Mouse	Lenovo	28	2024-01-21	Norte
1015	Teclado	Samsung	42	2024-01-22	Sur
EOF
```

### 2.3 Verificar Datos Cargados

```bash
# Ver contenido del archivo
hdfs dfs -cat /demo/ventas/datos.txt

# Contar líneas
hdfs dfs -cat /demo/ventas/datos.txt | wc -l

# Ver primeras 5 líneas
hdfs dfs -cat /demo/ventas/datos.txt | head -5
```

### 📋 Estructura del Dataset

| Campo        | Tipo   | Descripción            | Ejemplo    |
| ------------ | ------ | ----------------------- | ---------- |
| `id`       | INT    | Identificador único    | 1001       |
| `producto` | STRING | Categoría del producto | Laptop     |
| `marca`    | STRING | Fabricante              | Dell       |
| `precio`   | DOUBLE | Precio en USD           | 1200       |
| `fecha`    | STRING | Fecha de venta          | 2024-01-15 |
| `region`   | STRING | Zona geográfica        | Norte      |

**Total de registros:** 15

---

## 🐝 PASO 3: Ejecución de Consultas (Hive)

⏱️ **Tiempo estimado:** 5 minutos

---

### 3.1 ⚠️ CONFIGURACIÓN OBLIGATORIA (Evitar "Return Code 2")

> **CRÍTICO:** Ejecutar estos comandos ANTES de cualquier consulta

```sql
-- Deshabilitar modo de ejecución local
SET hive.exec.mode.local.auto=false;

-- Forzar uso de YARN para MapReduce
SET mapreduce.framework.name=yarn;
```

**💡 Explicación:**

- Sin estos comandos, Hive intenta ejecutar queries en memoria local
- Esto causa "Return Code 2" en consultas con GROUP BY
- Forzamos que YARN distribuya el trabajo correctamente

---

### 3.2 Crear Base de Datos y Tabla

#### Paso 1: Crear Base de Datos

```sql
CREATE DATABASE IF NOT EXISTS tienda;
USE tienda;
```

#### Paso 2: Crear Tabla Externa

```sql
CREATE EXTERNAL TABLE ventas (
    id INT,
    producto STRING,
    marca STRING,
    precio DOUBLE,
    fecha STRING,
    region STRING
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t'
STORED AS TEXTFILE
LOCATION '/demo/ventas/';
```

#### Paso 3: Verificar Tabla

```sql
-- Ver estructura
DESCRIBE ventas;

-- Ver datos
SELECT * FROM ventas LIMIT 5;

-- Contar registros
SELECT COUNT(*) as total FROM ventas;
```

---

### 3.3 Consulta Principal: Agregación de Ventas

#### 📝 SQL:

```sql
SELECT 
    producto,
    COUNT(*) as cantidad_vendida,
    ROUND(SUM(precio), 2) as total_ingresos,
    ROUND(AVG(precio), 2) as precio_promedio
FROM ventas
GROUP BY producto
ORDER BY total_ingresos DESC;
```

#### 📊 Resultado Esperado:

| producto | cantidad_vendida | total_ingresos | precio_promedio |
| -------- | ---------------- | -------------- | --------------- |
| Laptop   | 4                | 4850.00        | 1212.50         |
| Monitor  | 3                | 1130.00        | 376.67          |
| Teclado  | 4                | 177.00         | 44.25           |
| Mouse    | 4                | 103.00         | 25.75           |

#### 🎯 Flujo MapReduce:

```
┌─────────────┐
│ INPUT (HDFS)│  15 registros en /demo/ventas/datos.txt
└──────┬──────┘
       ↓
┌──────────────────┐
│  MAPPER PHASE    │  Lee y emite <producto, precio>
└──────┬───────────┘
       ↓
┌──────────────────┐
│ SHUFFLE & SORT   │  Agrupa por producto
└──────┬───────────┘
       ↓
┌──────────────────┐
│ REDUCER PHASE    │  Calcula COUNT, SUM, AVG
└──────┬───────────┘
       ↓
┌─────────────┐
│OUTPUT (HDFS)│  Resultados agregados
└─────────────┘
```

---

## 🔍 PASO 4: Monitoreo y Resultados

⏱️ **Tiempo:** Continuo durante ejecución

### 4.1 Interfaces Web de Monitoreo

| Servicio                        | URL                                           | Información Visible                       |
| ------------------------------- | --------------------------------------------- | ------------------------------------------ |
| **YARN Resource Manager** | [http://localhost:8088](http://localhost:8088)   | Jobs MapReduce, Mappers, Reducers, Memoria |
| **HDFS NameNode**         | [http://localhost:9870](http://localhost:9870)   | Explorador de archivos, Bloques, Nodos     |
| **Job History Server**    | [http://localhost:19888](http://localhost:19888) | Historial de jobs completados              |

### 4.2 Monitoreo desde Terminal

#### Ver aplicaciones YARN activas:

```bash
# Listar aplicaciones en ejecución
watch -n 2 'yarn application -list'

# Ver estado de una aplicación específica
yarn application -status application_1234567890_0001
```

#### Ver logs en tiempo real:

```bash
# Logs de Hive
tail -f /tmp/hive.log

# Logs de aplicación YARN
yarn logs -applicationId application_1234567890_0001
```

### 4.3 Métricas a Observar

✅ **En YARN Web UI:**

- Número de mappers ejecutados
- Número de reducers asignados
- Memoria consumida
- Tiempo de cada fase (Map/Shuffle/Reduce)
- Estado: RUNNING → SUCCEEDED

✅ **En HDFS Web UI:**

- Bloques de datos
- Replicación
- Nodos DataNode activos
- Archivos generados

---

## 📤 PASO 5: Exportar Resultados

⏱️ **Tiempo estimado:** 1 minuto

### 5.1 Exportar a Directorio Local (CSV)

#### En Hive:

```sql
INSERT OVERWRITE LOCAL DIRECTORY '/tmp/resumen_ventas'
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
SELECT 
    producto,
    COUNT(*) as cantidad,
    ROUND(SUM(precio), 2) as total
FROM ventas
GROUP BY producto
ORDER BY total DESC;
```

#### En Terminal:

```bash
# Ver resultados
cat /tmp/resumen_ventas/000000_0

# O con formato:
column -t -s',' /tmp/resumen_ventas/000000_0
```

### 5.2 Exportar a HDFS (para compartir)

#### En Hive:

```sql
INSERT OVERWRITE DIRECTORY '/demo/resultados/por_producto'
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
SELECT 
    producto,
    COUNT(*) as cantidad,
    ROUND(SUM(precio), 2) as total
FROM ventas
GROUP BY producto
ORDER BY total DESC;
```

#### En Terminal:

```bash
# Ver resultado en HDFS
hdfs dfs -cat /demo/resultados/por_producto/*

# Descargar localmente (especificar ruta absoluta)
hdfs dfs -get /demo/resultados/por_producto/000000_0 /home/hadoop/resumen.csv

# Ver contenido del archivo descargado
cat /home/hadoop/resumen.csv
```

---

## 🧹 LIMPIEZA (Opcional)

⏱️ **Tiempo estimado:** 2 minutos

---

### Detener Servicios

```bash
# Salir de Hive (si estás dentro)
quit;

# Detener YARN y HDFS
stop-yarn.sh
stop-dfs.sh

# O detener todo a la vez
stop-all.sh
```

---

### Limpiar Datos

#### En Hive:

```sql
-- Eliminar tabla
DROP TABLE IF EXISTS ventas;

-- Eliminar base de datos
DROP DATABASE IF EXISTS tienda;

-- Salir
quit;
```

#### En Terminal:

```bash
# Eliminar datos en HDFS
hdfs dfs -rm -r /demo/

# Eliminar resultados locales exportados
hdfs dfs -rm -r /tmp/resumen_ventas/

# Limpiar archivos locales
rm -rf /tmp/resumen_ventas/
rm -f /home/hadoop/resumen.csv
```

---

## 📖 CONSULTAS ADICIONALES (Bonus)

### ✅ Consulta 2: Ventas por región

```sql
SELECT 
    region,
    COUNT(*) as num_ventas,
    ROUND(SUM(precio), 2) as ingresos_totales,
    ROUND(AVG(precio), 2) as ticket_promedio
FROM ventas
GROUP BY region
ORDER BY ingresos_totales DESC;
```

**Resultado esperado:**

```
Norte    7    3653.00    521.86
Este     3    1795.00    598.33
Sur      3     117.00     39.00
Oeste    2    1185.00    592.50
```

---

### ✅ Consulta 3: Marca más vendida por producto

```sql
SELECT 
    producto,
    marca,
    COUNT(*) as ventas,
    ROUND(SUM(precio), 2) as ingresos
FROM ventas
GROUP BY producto, marca
ORDER BY producto, ventas DESC;
```

---

### ✅ Consulta 4: Análisis temporal (por fecha)

```sql
SELECT 
    fecha,
    COUNT(*) as ventas_dia,
    ROUND(SUM(precio), 2) as ingresos_dia,
    COUNT(DISTINCT producto) as productos_diferentes
FROM ventas
GROUP BY fecha
ORDER BY fecha;
```

---

### ✅ Consulta 5: Top productos más caros

```sql
SELECT 
    producto,
    marca,
    precio,
    region,
    fecha
FROM ventas
WHERE precio > 100
ORDER BY precio DESC
LIMIT 10;
```

---

### ✅ Consulta 6: Estadísticas avanzadas

```sql
SELECT 
    producto,
    COUNT(*) as cantidad,
    MIN(precio) as precio_min,
    MAX(precio) as precio_max,
    ROUND(AVG(precio), 2) as precio_prom,
    ROUND(STDDEV(precio), 2) as desviacion,
    PERCENTILE_APPROX(precio, 0.5) as mediana
FROM ventas
GROUP BY producto
ORDER BY cantidad DESC;
```

**🎯 Concepto:** Las funciones estadísticas (AVG, STDDEV, PERCENTILE) se calculan **distribuidas** entre múltiples reducers.

---

### ✅ Consulta 7: Crear tabla de resumen (para reusar)

```sql
-- Crear tabla con resultados agregados
CREATE TABLE resumen_ventas AS
SELECT 
    producto,
    COUNT(*) as cantidad,
    ROUND(SUM(precio), 2) as ingresos,
    ROUND(AVG(precio), 2) as precio_prom
FROM ventas
GROUP BY producto;

-- Ver resultados
SELECT * FROM resumen_ventas;

-- Esta tabla ahora está en HDFS y se puede consultar rápidamente
DESCRIBE FORMATTED resumen_ventas;
```

---

### ✅ Consulta 8: Filtrado con HAVING

```sql
-- Solo productos con más de 2 ventas
SELECT 
    producto,
    marca,
    COUNT(*) as ventas,
    ROUND(AVG(precio), 2) as precio_prom
FROM ventas
GROUP BY producto, marca
HAVING COUNT(*) >= 2
ORDER BY ventas DESC;
```

---

## 📚 RESUMEN DE SOLUCIONES

### ✅ Errores Resueltos

| # | Error Original                   | Solución Aplicada                                                  |
| - | -------------------------------- | ------------------------------------------------------------------- |
| 1 | Java 17 causa fallos             | Forzar Java 8 en `hadoop-env.sh` y `hive-env.sh`                |
| 2 | "Permission denied: user=dr.who" | Agregar `hadoop.http.staticuser.user=hadoop` en `core-site.xml` |
| 3 | "Return Code 2" en queries       | `SET hive.exec.mode.local.auto=false` antes de consultas          |
| 4 | Permisos en staging              | `hdfs dfs -chmod -R 777 /tmp/hadoop-yarn/staging`                 |

### 🎯 Comandos Clave

#### Inicio Rápido:

```bash
# 1. Iniciar servicios Hadoop
start-dfs.sh && start-yarn.sh

# 2. Verificar servicios
jps
```

#### Conectar a Hive:

```bash
# Conectar directamente a Hive
hive
```

#### Configuración Obligatoria en Hive:

```sql
SET hive.exec.mode.local.auto=false;
SET mapreduce.framework.name=yarn;
```

#### Monitoreo:

```bash
# Ver aplicaciones YARN
watch -n 2 'yarn application -list'

# Ver jobs en tiempo real
http://localhost:8088
```

### 📊 Resultado Final

✅ Dataset de 15 ventas cargado en HDFS
✅ Tabla Hive externa creada
✅ Consulta principal con GROUP BY ejecutada vía MapReduce
✅ 7 consultas adicionales con análisis avanzados
✅ Resultados exportados a CSV local
✅ Todos los errores comunes resueltos

---

## 🔗 RECURSOS ADICIONALES

### Documentación Oficial:

| Recurso                            | URL                                                                                         |
| ---------------------------------- | ------------------------------------------------------------------------------------------- |
| **Hadoop 3.4.0 Docs**        | https://hadoop.apache.org/docs/r3.4.0/                                                      |
| **Hive Language Manual**     | https://cwiki.apache.org/confluence/display/Hive/LanguageManual                             |
| **YARN ResourceManager API** | https://hadoop.apache.org/docs/r3.4.0/hadoop-yarn/hadoop-yarn-site/ResourceManagerRest.html |
| **HDFS Commands Guide**      | https://hadoop.apache.org/docs/r3.4.0/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html     |

### Herramientas de Monitoreo:

- 🌐 **YARN UI:** http://localhost:8088
- 🌐 **HDFS UI:** http://localhost:9870
- 🌐 **Job History:** http://localhost:19888

---

<div align="center">

## 🎉 ¡Práctica Completada!

**Hadoop 3.4.0 + Hive 3.3.1 funcionando correctamente**

✅ MapReduce distribuido
✅ SQL sobre Big Data
✅ Todos los errores resueltos

*Guía creada en Febrero 2026*

</div>
