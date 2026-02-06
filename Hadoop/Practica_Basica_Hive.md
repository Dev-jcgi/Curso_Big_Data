# 🚀 Guía Definitiva: Hadoop 3.4.0 + Hive 3.3.1

> **Análisis de Ventas con Solución Completa de Errores Comunes**

**Autor:** Guía Práctica Hadoop
**Fecha:** Febrero 2026
**Versiones:** Hadoop 3.4.0 | Hive 3.3.1 | Java 8

---

## 🛠️ PASO 0: Solución de Errores al Ejecutar Hive con Hadoop

> ⚠️ **IMPORTANTE:** Esta sección resuelve todos los problemas comunes que bloquean Hive

⏱️ **Tiempo estimado:** 5-10 minutos

### 📌 Problemas que Resolveremos:

| # | Error                            | Solución                     |
| - | -------------------------------- | ----------------------------- |
| 1 | Java 17 incompatible             | Forzar Java 8                 |
| 2 | "Permission denied: user=dr.who" | Configurar usuario web        |
| 3 | "Return Code 2"                  | Configurar MapReduce con YARN |
| 4 | Permisos HDFS insuficientes      | Limpiar y configurar permisos |

---

### Paso 1: Crear hive-env.sh desde la Plantilla

```bash
# Crear archivo de configuración de Hive desde template
cp /opt/hive/conf/hive-env.sh.template /opt/hive/conf/hive-env.sh
```

---

### Paso 2: Editar Archivos de Entorno

#### 🔧 Configurar Variables de Entorno en Hive

**Archivo:** `/opt/hive/conf/hive-env.sh`

```bash
# Editar el archivo con nano o vim
nano /opt/hive/conf/hive-env.sh

# Agregar o verificar estas líneas al final:
export JAVA_HOME=/opt/jdk1.8.0_202
export HADOOP_HOME=/opt/hadoop/hadoop-3.4.0
export HIVE_HOME=/opt/hive
```

> **📝 Nota:** Guarda los cambios (Ctrl+O, Enter, Ctrl+X en nano)

---

### Paso 3: Verificar Variables de Entorno

```bash
# Verificar que las variables estén configuradas correctamente
echo $JAVA_HOME
# Esperado: /opt/jdk1.8.0_202

echo $HADOOP_HOME
# Esperado: /opt/hadoop/hadoop-3.4.0

echo $HIVE_HOME
# Esperado: /opt/hive
```

---

### Paso 4: Configurar MapReduce (mapred-site.xml)

#### 🔧 Configurar Framework MapReduce con YARN

**Archivo:** `/opt/hadoop/hadoop-3.4.0/etc/hadoop/mapred-site.xml`

```bash
# Abrir archivo de configuración
sudo nano /opt/hadoop/hadoop-3.4.0/etc/hadoop/mapred-site.xml
```

**Agregar dentro de `<configuration>`:**

```xml
<property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
</property>
<property>
    <name>mapreduce.application.classpath</name>
    <value>$HADOOP_HOME/share/hadoop/mapreduce/*:$HADOOP_HOME/share/hadoop/mapreduce/lib/*</value>
</property>
```

**💡 Explicación:** Configura MapReduce para ejecutarse sobre YARN (Resource Manager) en lugar de modo local, evitando errores "Return Code 2".

---

### Paso 5: Configurar Usuario Web (core-site.xml)

#### 🔧 Evitar Error "dr.who"

**Archivo:** `/opt/hadoop/hadoop-3.4.0/etc/hadoop/core-site.xml`

```bash
# Abrir archivo de configuración
sudo nano /opt/hadoop/hadoop-3.4.0/etc/hadoop/core-site.xml
```

**Agregar dentro de `<configuration>`:**

```xml
<property>
    <name>hadoop.http.staticuser.user</name>
    <value>hadoop</value>
    <description>Usuario para interfaz web HDFS - Evita error dr.who</description>
</property>
```

**💡 Explicación:** Por defecto, Hadoop usa el usuario ficticio "dr.who" para la interfaz web, causando errores de permisos. Esta configuración fuerza el uso del usuario real (hadoop) para todas las operaciones web.

---

### Paso 6: Limpiar Permisos de Carpetas Temporales

> **⚠️ ADVERTENCIA:** Este comando eliminará TODOS los archivos en HDFS. Solo ejecutar en ambiente de desarrollo/prueba.

```bash
# Limpiar filesystem HDFS (elimina todo)
hdfs dfs -rm -r /
```

**💡 Explicación:** Elimina archivos corruptos o con permisos incorrectos. En producción, usar comandos más específicos.

---

### Paso 7: Reiniciar Servicios Hadoop

#### 🔄 Detener Todos los Servicios

```bash
# Detener HDFS, YARN y todos los servicios
stop-all.sh
```

#### 🚀 Iniciar HDFS

```bash
# Iniciar NameNode, DataNode y SecondaryNameNode
start-dfs.sh
```

#### 🚀 Iniciar YARN

```bash
# Iniciar ResourceManager y NodeManager
start-yarn.sh
```

#### ✅ Verificar Servicios Activos

```bash
# Verificar que todos los servicios estén corriendo
jps
```

**Salida esperada:**

```
12345 NameNode
12346 DataNode
12347 ResourceManager
12348 NodeManager
12349 SecondaryNameNode
12350 Jps
```

> **💡 Nota:** Los números de proceso (PIDs) variarán. Lo importante es que aparezcan los 5 servicios principales.

---

### Paso 8: Configurar Permisos HDFS (Después del Reinicio)

```bash
# Dar permisos completos al filesystem HDFS
hdfs dfs -chmod -R 777 /

# Crear directorio staging para YARN
hdfs dfs -mkdir -p /tmp/hadoop-yarn/staging
hdfs dfs -chmod -R 777 /tmp/hadoop-yarn/staging
```

**💡 Explicación:** YARN necesita permisos de escritura en `/tmp/hadoop-yarn/staging` para almacenar archivos temporales de jobs MapReduce.

**✅ Verificar estructura creada:**

```bash
hdfs dfs -ls /
hdfs dfs -ls /tmp/hadoop-yarn/
```

---

### Paso 9: Conectar a Hive

```bash
# Conectar a Hive directamente (abre la terminal interactiva)
hive
```

**✅ Verificar conexión:**

```sql
-- Dentro de Hive, ejecutar:
SHOW DATABASES;
```

**Salida esperada:**

```
OK
default
Time taken: 0.5 seconds, Fetched: 1 row(s)
```

---

### 🎉 Configuración Completa

Si todos los pasos anteriores se ejecutaron correctamente, deberías tener:

✅ Variables de entorno configuradas (Java 8, Hadoop, Hive)
✅ MapReduce configurado para ejecutarse con YARN
✅ Usuario web de Hadoop configurado correctamente
✅ Servicios Hadoop y YARN corriendo
✅ Permisos HDFS configurados
✅ Hive conectado y funcionando

Ahora puedes proceder a cargar datos y ejecutar consultas sin errores.

---

## 📊 PASO 1: Cargar Datos en HDFS

⏱️ **Tiempo estimado:** 1 minuto

### 1.1 Crear Directorio en HDFS

```bash
# Crear directorio para almacenar los datos de ventas
hdfs dfs -mkdir -p /demo/ventas
```

**✅ Verificar creación:**

```bash
hdfs dfs -ls /demo
```

**Salida esperada:**

```
drwxr-xr-x   - hadoop supergroup          0 2026-02-01 10:00 /demo/ventas
```

### 1.2 Cargar Dataset Directamente

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

### 1.3 Verificar Datos Cargados

```bash
# Ver contenido completo del archivo
hdfs dfs -cat /demo/ventas/datos.txt

# Contar líneas (debe mostrar 15)
hdfs dfs -cat /demo/ventas/datos.txt | wc -l

# Ver primeras 5 líneas
hdfs dfs -cat /demo/ventas/datos.txt | head -5
```

**✅ Verificación de integridad:**

```bash
# Confirmar que hay exactamente 15 registros
hdfs dfs -cat /demo/ventas/datos.txt | wc -l
# Salida esperada: 15
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

## 🐝 PASO 2: Ejecución de Consultas (Hive)

⏱️ **Tiempo estimado:** 5 minutos

---

### 2.1 ⚠️ CONFIGURACIÓN OBLIGATORIA (Evitar "Return Code 2")

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

### 2.2 Crear Base de Datos y Tabla

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
-- Ver estructura de la tabla
DESCRIBE ventas;
```

**Salida esperada:**

```
id                      int
producto                string
marca                   string
precio                  double
fecha                   string
region                  string
```

```sql
-- Ver primeros 5 registros
SELECT * FROM ventas LIMIT 5;
```

**Salida esperada:**

```
1001  Laptop   Dell      1200.0  2024-01-15  Norte
1002  Mouse    Logitech  25.0    2024-01-15  Sur
1003  Teclado  HP        45.0    2024-01-16  Este
1004  Monitor  Samsung   350.0   2024-01-16  Norte
1005  Laptop   HP        1100.0  2024-01-17  Oeste
```

```sql
-- Contar registros totales (debe ser 15)
SELECT COUNT(*) as total FROM ventas;
```

**Salida esperada:**

```
total
15
```

---

### 2.3 Consulta Principal: Agregación de Ventas

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
└──────┬──────┘  (Datos distribuidos en bloques)
       ↓
┌──────────────────┐
│  MAPPER PHASE    │  Lee datos en paralelo y emite pares <producto, precio>
│   (Map Task)     │  Ejemplo: "Laptop" → ("Laptop", 1200)
└──────┬───────────┘
       ↓
┌──────────────────┐
│ SHUFFLE & SORT   │  Agrupa todos los valores por clave (producto)
│  (Framework)     │  Ejemplo: "Laptop" → [1200, 1100, 1300, 1250]
└──────┬───────────┘
       ↓
┌──────────────────┐
│ REDUCER PHASE    │  Calcula agregaciones: COUNT, SUM, AVG
│  (Reduce Task)   │  Ejemplo: Laptop → count=4, sum=4850, avg=1212.50
└──────┬───────────┘
       ↓
┌─────────────┐
│OUTPUT (HDFS)│  4 filas con resultados agregados y ordenados
└─────────────┘
```

**💡 Concepto Big Data:**

- Esta consulta ejecuta **1 Job MapReduce** gestionado por YARN
- Los datos se procesan de forma **distribuida** y **paralela** entre múltiples nodos
- El framework maneja automáticamente la distribución, fallos y optimización

---

## 🔍 PASO 3: Monitoreo y Resultados

⏱️ **Tiempo:** Continuo durante ejecución

### 3.1 Interfaces Web de Monitoreo

| Servicio                        | URL                                           | Información Visible                       |
| ------------------------------- | --------------------------------------------- | ------------------------------------------ |
| **YARN Resource Manager** | [http://localhost:8088](http://localhost:8088)   | Jobs MapReduce, Mappers, Reducers, Memoria |
| **HDFS NameNode**         | [http://localhost:9870](http://localhost:9870)   | Explorador de archivos, Bloques, Nodos     |
| **Job History Server**    | [http://localhost:19888](http://localhost:19888) | Historial de jobs completados              |

### 3.2 Monitoreo desde Terminal

#### Ver aplicaciones YARN activas:

```bash
# Listar aplicaciones en ejecución
watch -n 2 'yarn application -list'

# Ver estado de una aplicación específica
yarn application -status application_1234567890_0001
```

#### Ver logs de aplicaciones YARN:

```bash
# Logs de una aplicación específica (reemplazar con ID real)
yarn logs -applicationId application_1234567890_0001

# Ver logs de la última aplicación ejecutada
yarn logs -applicationId $(yarn application -list -appStates FINISHED | tail -1 | awk '{print $1}')
```

### 3.3 Métricas a Observar

#### ✅ **En YARN Web UI (http://localhost:8088):**

| Métrica                 | Ubicación                       | Qué observar                              |
| ------------------------ | -------------------------------- | ------------------------------------------ |
| **Jobs MapReduce** | Applications → Running          | Estado de ejecución en tiempo real        |
| **Mappers**        | Application Details → Tasks     | Número de map tasks (típicamente 1-4)    |
| **Reducers**       | Application Details → Tasks     | Número de reduce tasks (típicamente 1-2) |
| **Memoria**        | Application Details → Resources | RAM consumida vs disponible                |
| **Progreso**       | Application Progress             | % Map → % Shuffle → % Reduce             |
| **Estado Final**   | Application State                | RUNNING → SUCCEEDED (o FAILED)            |

#### ✅ **En HDFS Web UI (http://localhost:9870):**

| Métrica               | Ubicación          | Qué observar                       |
| ---------------------- | ------------------- | ----------------------------------- |
| **Archivos**     | Utilities → Browse | Navegar a /demo/ventas/datos.txt    |
| **Bloques**      | File Details        | Número de bloques y ubicación     |
| **Replicación** | File Details        | Factor de replicación (default: 3) |
| **DataNodes**    | Datanodes Tab       | Nodos activos y capacidad           |
| **Espacio**      | Overview            | Capacidad usada/disponible          |

---

## 📤 PASO 4: Exportar Resultados

⏱️ **Tiempo estimado:** 1 minuto

### 4.1 Exportar a Directorio Local (CSV)

> **📌 Propósito:** Guardar resultados en el sistema de archivos local para análisis externo

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

**💡 Nota:** El keyword `LOCAL` hace que Hive escriba en el filesystem local, no en HDFS.

#### En Terminal:

```bash
# Ver resultados
cat /tmp/resumen_ventas/000000_0

# O con formato:
column -t -s',' /tmp/resumen_ventas/000000_0
```

### 4.2 Exportar a HDFS (para compartir)

> **📌 Propósito:** Guardar resultados en HDFS para compartir con otros usuarios o procesos

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

**💡 Nota:** Sin `LOCAL`, Hive escribe directamente en HDFS. Los resultados serán accesibles desde cualquier nodo del cluster.

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

> **⚠️ Advertencia:** Solo ejecutar después de completar toda la práctica

---

### Detener Servicios

```bash
# 1. Salir de Hive (si estás dentro de la sesión interactiva)
quit;

# 2. Detener servicios individualmente
stop-yarn.sh     # Detiene ResourceManager y NodeManager
stop-dfs.sh      # Detiene NameNode, DataNode, SecondaryNameNode

# O detener todo a la vez (alternativa)
stop-all.sh
```

**✅ Verificar detención:**

```bash
jps
# Solo debe aparecer: Jps
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

> **🎯 Objetivo:** Estas consultas demuestran capacidades avanzadas de Hive y patrones de análisis comunes en Big Data

> **📝 Requisitos:** Ejecutar después del PASO 3 (tabla `ventas` debe existir)

---

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

**📊 Propósito:** Análisis multi-dimensional (producto + marca)

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

**🎯 Concepto:** El `GROUP BY` con múltiples columnas crea subcategorías para análisis más detallado.

---

### ✅ Consulta 4: Análisis temporal (por fecha)

**📊 Propósito:** Identificar tendencias diarias de ventas

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

**📊 Propósito:** Filtrado y ranking de registros individuales

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

**🎯 Concepto:** Esta consulta NO usa MapReduce (no hay GROUP BY), se ejecuta más rápido como simple filtrado.

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

**📊 Propósito:** Materializar resultados para consultas futuras más rápidas

```sql
-- Crear tabla con resultados agregados (CTAS: Create Table As Select)
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

-- Esta tabla ahora está en HDFS y se puede consultar rápidamente sin re-calcular
DESCRIBE FORMATTED resumen_ventas;
```

**🎯 Concepto:** Las tablas materializadas son una técnica de optimización común en Big Data. Ejecutas el cálculo costoso una vez y reutilizas los resultados.

---

### ✅ Consulta 8: Filtrado con HAVING

**📊 Propósito:** Filtrar DESPUÉS de la agregación (diferente a WHERE)

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

**🎯 Concepto:**

- `WHERE` filtra **antes** de agrupar (filas individuales)
- `HAVING` filtra **después** de agrupar (grupos completos)
- En este caso, eliminamos combinaciones producto-marca con menos de 2 ventas

---

## 📚 RESUMEN DE SOLUCIONES

> **🎯 Referencia Rápida:** Usa esta sección como troubleshooting guide cuando encuentres errores

---

### ✅ Errores Resueltos

| # | Error Original                   | Causa Raíz                                              | Solución Aplicada                                                  |
| - | -------------------------------- | -------------------------------------------------------- | ------------------------------------------------------------------- |
| 1 | Java 17 causa fallos             | Incompatibilidad de bytecode con bibliotecas Hadoop/Hive | Forzar Java 8 en `hadoop-env.sh` y `hive-env.sh`                |
| 2 | "Permission denied: user=dr.who" | Usuario ficticio default en web UI                       | Agregar `hadoop.http.staticuser.user=hadoop` en `core-site.xml` |
| 3 | "Return Code 2" en queries       | Hive intenta ejecución local en lugar de YARN           | `SET hive.exec.mode.local.auto=false` antes de consultas          |
| 4 | Permisos en staging              | Directorio temporal sin permisos de escritura            | `hdfs dfs -chmod -R 777 /tmp/hadoop-yarn/staging`                 |

### 🎯 Comandos Clave

> **📝 Cheat Sheet:** Comandos esenciales para ejecución rápida

---

#### Inicio Rápido:

```bash
# 1. Iniciar servicios Hadoop (HDFS + YARN)
start-dfs.sh && start-yarn.sh

# 2. Verificar servicios (deben aparecer 5-6 procesos)
jps

# 3. Configurar permisos HDFS (solo la primera vez)
hdfs dfs -chmod -R 777 /
hdfs dfs -mkdir -p /tmp/hadoop-yarn/staging
hdfs dfs -chmod -R 777 /tmp/hadoop-yarn/staging
```

#### Conectar a Hive:

```bash
# Conectar directamente a Hive
hive
```

#### Configuración Obligatoria en Hive:

> **⚠️ EJECUTAR SIEMPRE antes de cualquier consulta con GROUP BY**

```sql
SET hive.exec.mode.local.auto=false;
SET mapreduce.framework.name=yarn;
```

#### Monitoreo:

```bash
# Ver aplicaciones YARN en ejecución (actualiza cada 2 segundos)
watch -n 2 'yarn application -list'

# Acceder a interfaces web
# YARN Resource Manager (jobs MapReduce):
http://localhost:8088

# HDFS NameNode (explorador de archivos):
http://localhost:9870

# Job History Server (historial):
http://localhost:19888
```

### 📊 Resultado Final

**Lo que has logrado en esta práctica:**

✅ Dataset de 15 ventas cargado en HDFS distribuido
✅ Tabla Hive externa creada y vinculada a datos HDFS
✅ Consulta principal con GROUP BY ejecutada vía MapReduce en YARN
✅ 7 consultas adicionales con análisis avanzados (agregaciones, estadísticas, filtros)
✅ Resultados exportados a CSV local y HDFS
✅ Todos los errores comunes diagnosticados y resueltos

**Conceptos de Big Data demostrados:**
📊 Procesamiento distribuido con MapReduce
📊 Almacenamiento en HDFS con replicación
📊 Gestión de recursos con YARN
📊 SQL sobre datos distribuidos (Hive)
📊 Monitoreo de jobs en tiempo real

---

## 🔗 RECURSOS ADICIONALES

### 📚 Documentación Oficial:

| Recurso                            | Descripción                                | URL                                                                                         |
| ---------------------------------- | ------------------------------------------- | ------------------------------------------------------------------------------------------- |
| **Hadoop 3.4.0 Docs**        | Guía completa de configuración y comandos | https://hadoop.apache.org/docs/r3.4.0/                                                      |
| **Hive Language Manual**     | Referencia de HiveQL (DDL, DML, funciones)  | https://cwiki.apache.org/confluence/display/Hive/LanguageManual                             |
| **YARN ResourceManager API** | API REST para monitoreo programmático      | https://hadoop.apache.org/docs/r3.4.0/hadoop-yarn/hadoop-yarn-site/ResourceManagerRest.html |
| **HDFS Commands Guide**      | Comandos completos de HDFS shell            | https://hadoop.apache.org/docs/r3.4.0/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html     |

### 💻 Herramientas de Monitoreo:

| Interfaz                        | Puerto | Propósito                                                    | URL                    |
| ------------------------------- | ------ | ------------------------------------------------------------- | ---------------------- |
| **YARN Resource Manager** | 8088   | Monitorear jobs MapReduce, estado de cluster, uso de recursos | http://localhost:8088  |
| **HDFS NameNode**         | 9870   | Explorar archivos HDFS, ver bloques, estado de DataNodes      | http://localhost:9870  |
| **Job History Server**    | 19888  | Ver historial completo de jobs finalizados con logs           | http://localhost:19888 |

> **💡 Tip:** Accede a estas URLs mientras ejecutas consultas para ver el procesamiento en tiempo real

---

<div align="center">

## 🎉 ¡Práctica Completada!

**Hadoop 3.4.0 + Hive 3.3.1 funcionando correctamente**

✅ MapReduce distribuido con YARN
✅ SQL sobre Big Data con Hive
✅ Todos los errores comunes resueltos
✅ Monitoreo y debugging configurado

---

### 📈 Próximos Pasos

**Nivel Intermedio:**

- Particionamiento de tablas Hive (`PARTITIONED BY`)
- Bucketing para optimización (`CLUSTERED BY`)
- Joins entre múltiples tablas
- UDFs (User Defined Functions)

**Nivel Avanzado:**

- Integración con Spark SQL
- Compresiones (Snappy, ORC, Parquet)
- Hive en cluster multi-nodo
- Tuning de MapReduce (memoria, paralelismo)

---

*Guía creada en Febrero 2026*
*Versión: 1.0 - Completa y probada*

</div>
