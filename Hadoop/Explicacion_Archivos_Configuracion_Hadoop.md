# 📝 Explicación de Archivos de Configuración de Hadoop y Hive

> **Guía Completa de Configuración XML**  
> **Para:** Sistemas Inteligentes - Práctica de Hadoop  
> **Fecha:** Febrero 2026

---

## 📋 Índice

1. [Introducción](#introducción)
2. [core-site.xml](#core-sitexml)
3. [hdfs-site.xml](#hdfs-sitexml)
4. [mapred-site.xml](#mapred-sitexml)
5. [yarn-site.xml](#yarn-sitexml)
6. [hive-site.xml](#hive-sitexml)
7. [Resumen de Ubicaciones](#resumen-de-ubicaciones)
8. [Jerarquía de Configuración](#jerarquía-de-configuración)

---

## 🎯 Introducción

### ¿Qué son los Archivos de Configuración?

Los archivos XML de Hadoop definen cómo se comporta el ecosistema completo:
- **Conexiones entre nodos** (IP, puertos)
- **Ubicaciones de datos** (dónde se guardan)
- **Límites de recursos** (memoria, CPU)
- **Comportamiento del sistema** (replicación, timeouts)

### Estructura General de los Archivos XML

Todos los archivos siguen el mismo formato:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
    <property>
        <name>nombre.de.la.propiedad</name>
        <value>valor_de_la_propiedad</value>
        <description>Descripción opcional</description>
    </property>
    
    <property>
        <name>otra.propiedad</name>
        <value>otro_valor</value>
    </property>
</configuration>
```

**Elementos:**
- `<configuration>`: Contenedor principal
- `<property>`: Cada configuración es una propiedad
- `<name>`: Nombre de la propiedad (clave)
- `<value>`: Valor asignado
- `<description>`: Documentación (opcional)

---

---

## 📄 core-site.xml

### 🎯 Propósito

**Configuración central de Hadoop** - Define parámetros globales que afectan a todos los componentes:
- URI del sistema de archivos por defecto (HDFS)
- Configuración de I/O (buffers, compresión)
- Directorio temporal
- Configuración de seguridad

### 📍 Ubicación

```
/opt/hadoop/hadoop-3.4.0/etc/hadoop/core-site.xml
```

---

### 🔧 Propiedades Principales

#### 1. `fs.defaultFS`

**Descripción:** URI del sistema de archivos por defecto (NameNode de HDFS)

```xml
<property>
    <name>fs.defaultFS</name>
    <value>hdfs://nodo1:9000</value>
</property>
```

**Explicación:**
- **`hdfs://`**: Protocolo del sistema de archivos distribuido
- **`nodo1`**: Hostname o IP del servidor NameNode
- **`9000`**: Puerto por defecto del NameNode

**¿Por qué es importante?**
- Todos los comandos `hdfs dfs` usan esta URI automáticamente
- Define dónde se conectan los clientes cuando acceden a HDFS
- Centraliza la configuración del cluster

**Ejemplos de valores:**
```xml
<!-- Localhost -->
<value>hdfs://localhost:9000</value>

<!-- IP específica -->
<value>hdfs://192.168.1.100:9000</value>

<!-- Sistema de archivos local (NO distribuido) -->
<value>file:///</value>
```

---

#### 2. `hadoop.tmp.dir`

**Descripción:** Directorio temporal usado por Hadoop para almacenar datos intermedios

```xml
<property>
    <name>hadoop.tmp.dir</name>
    <value>/opt/hadoop/hadoop-3.4.0/tmp</value>
</property>
```

**¿Qué se guarda aquí?**
- Datos temporales de NameNode y DataNode
- Metadatos de HDFS si no se especifica otra ubicación
- Archivos temporales de MapReduce
- Datos intermedios de operaciones

**⚠️ IMPORTANTE:**
- **NO usar `/tmp`** del sistema (se borra al reiniciar)
- Usar directorio con suficiente espacio en disco
- Asegurar permisos correctos (`chmod 755`)
- En producción, usar disco separado

**Valores recomendados:**
```xml
<!-- Desarrollo -->
<value>/opt/hadoop/hadoop-3.4.0/tmp</value>

<!-- Producción (disco dedicado) -->
<value>/data/hadoop/tmp</value>

<!-- Múltiples discos (separados por comas) -->
<value>/disk1/hadoop/tmp,/disk2/hadoop/tmp,/disk3/hadoop/tmp</value>
```

---

#### 3. `io.file.buffer.size`

**Descripción:** Tamaño del buffer de lectura/escritura en bytes

```xml
<property>
    <name>io.file.buffer.size</name>
    <value>131072</value>
</property>
```

**Explicación:**
- **131072 bytes** = **128 KB**
- Buffer más grande = menos llamadas I/O = mejor rendimiento
- Buffer más pequeño = menos memoria usada

**Conversiones:**
- 4096 = 4 KB (muy pequeño)
- 65536 = 64 KB (pequeño)
- 131072 = 128 KB (recomendado)
- 262144 = 256 KB (grande)

**¿Cuándo aumentar?**
- Archivos grandes (GBs)
- Red rápida (10 Gbps+)
- Mucha RAM disponible

**¿Cuándo disminuir?**
- RAM limitada
- Muchos usuarios concurrentes
- Archivos pequeños

---

#### 4. Propiedades Adicionales Comunes

##### `fs.trash.interval`

```xml
<property>
    <name>fs.trash.interval</name>
    <value>1440</value>
    <description>Minutos antes de vaciar papelera automáticamente</description>
</property>
```

**Valores:**
- `0`: Deshabilitado (eliminar permanentemente)
- `1440`: 24 horas
- `10080`: 7 días

---

##### `hadoop.http.staticuser.user`

```xml
<property>
    <name>hadoop.http.staticuser.user</name>
    <value>hadoop</value>
    <description>Usuario para acceso web UI</description>
</property>
```

---

##### `hadoop.proxyuser.hadoop.hosts`

```xml
<property>
    <name>hadoop.proxyuser.hadoop.hosts</name>
    <value>*</value>
    <description>Hosts desde donde el usuario puede hacer proxy</description>
</property>
```

**Valores:**
- `*`: Todos los hosts (desarrollo)
- `localhost`: Solo local
- `192.168.1.*`: Subnet específico

---

##### `hadoop.proxyuser.hadoop.groups`

```xml
<property>
    <name>hadoop.proxyuser.hadoop.groups</name>
    <value>*</value>
    <description>Grupos que pueden usar proxy</description>
</property>
```

---

### 📝 Ejemplo Completo: core-site.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
    <!-- Sistema de archivos por defecto (HDFS) -->
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://nodo1:9000</value>
        <description>URI del NameNode de HDFS</description>
    </property>
    
    <!-- Directorio temporal -->
    <property>
        <name>hadoop.tmp.dir</name>
        <value>/opt/hadoop/hadoop-3.4.0/tmp</value>
        <description>Directorio para archivos temporales</description>
    </property>
    
    <!-- Buffer de I/O -->
    <property>
        <name>io.file.buffer.size</name>
        <value>131072</value>
        <description>Tamaño del buffer: 128 KB</description>
    </property>
    
    <!-- Papelera de reciclaje -->
    <property>
        <name>fs.trash.interval</name>
        <value>1440</value>
        <description>Retención de papelera: 24 horas</description>
    </property>
    
    <!-- Usuario para Web UI -->
    <property>
        <name>hadoop.http.staticuser.user</name>
        <value>hadoop</value>
    </property>
    
    <!-- Configuración de proxy -->
    <property>
        <name>hadoop.proxyuser.hadoop.hosts</name>
        <value>*</value>
    </property>
    
    <property>
        <name>hadoop.proxyuser.hadoop.groups</name>
        <value>*</value>
    </property>
</configuration>
```

---

---

## 📄 hdfs-site.xml

### 🎯 Propósito

**Configuración específica de HDFS** - Define cómo funciona el sistema de archivos distribuido:
- Factor de replicación de datos
- Ubicación de NameNode y DataNode
- Tamaño de bloques
- Permisos y seguridad

### 📍 Ubicación

```
/opt/hadoop/hadoop-3.4.0/etc/hadoop/hdfs-site.xml
```

---

### 🔧 Propiedades Principales

#### 1. `dfs.replication`

**Descripción:** Número de réplicas de cada bloque de datos

```xml
<property>
    <name>dfs.replication</name>
    <value>1</value>
</property>
```

**Explicación:**
- **1**: Modo pseudo-distribuido (una sola máquina) ⚠️ SIN REDUNDANCIA
- **2**: Mínimo para producción pequeña
- **3**: Estándar en producción (RECOMENDADO) ✅
- **5+**: Alta disponibilidad crítica

**Ventajas de replicación:**
- ✅ **Tolerancia a fallos**: Si un DataNode falla, los datos siguen disponibles
- ✅ **Balanceo de carga**: Lecturas distribuidas entre réplicas
- ✅ **Localidad de datos**: MapReduce usa réplica más cercana

**Desventajas:**
- ❌ **Más espacio**: 3 réplicas = 3x espacio usado
- ❌ **Más red**: Escribir consume más ancho de banda

**Ejemplos por escenario:**
```xml
<!-- DESARROLLO / PRUEBAS (1 máquina) -->
<value>1</value>

<!-- PRODUCCIÓN PEQUEÑA (3-5 nodos) -->
<value>2</value>

<!-- PRODUCCIÓN ESTÁNDAR (5+ nodos) -->
<value>3</value>

<!-- DATOS CRÍTICOS (muchos nodos) -->
<value>5</value>
```

---

#### 2. `dfs.namenode.name.dir`

**Descripción:** Directorio donde el NameNode guarda los metadatos de HDFS

```xml
<property>
    <name>dfs.namenode.name.dir</name>
    <value>file:///opt/hadoop/hadoop-3.4.0/dfs/namenode</value>
</property>
```

**¿Qué se guarda aquí?**
- **FSImage**: Snapshot completo del sistema de archivos
- **EditLog**: Registro de todas las transacciones
- **Metadatos**: Nombres de archivos, permisos, ubicación de bloques

**⚠️ CRÍTICO:**
- Si se pierde este directorio, **SE PIERDE TODO EL SISTEMA DE ARCHIVOS**
- En producción, usar **múltiples ubicaciones** (RAID, NFS, discos separados)
- Hacer **backups regulares**

**Formato del valor:**
```xml
<!-- Una ubicación -->
<value>file:///opt/hadoop/hadoop-3.4.0/dfs/namenode</value>

<!-- Múltiples ubicaciones (separadas por comas) - RECOMENDADO -->
<value>file:///disk1/hadoop/namenode,file:///disk2/hadoop/namenode</value>

<!-- Con NFS para backup -->
<value>file:///local/namenode,file:///nfs/backup/namenode</value>
```

**Estructura interna:**
```
namenode/
├── current/
│   ├── fsimage_0000000000000000123  (Snapshot del filesystem)
│   ├── fsimage_0000000000000000123.md5
│   ├── edits_0000000000000000001-0000000000000000123  (Log de cambios)
│   ├── seen_txid
│   └── VERSION
└── in_use.lock
```

---

#### 3. `dfs.datanode.data.dir`

**Descripción:** Directorios donde los DataNodes guardan los bloques de datos reales

```xml
<property>
    <name>dfs.datanode.data.dir</name>
    <value>file:///opt/hadoop/hadoop-3.4.0/dfs/datanode</value>
</property>
```

**¿Qué se guarda aquí?**
- **Bloques de datos**: Los archivos divididos en chunks de 128 MB
- **Metadatos de bloques**: Checksums, versiones
- Contenido real de archivos subidos a HDFS

**Recomendaciones:**
- Usar **múltiples discos** para más I/O paralelo
- Usar discos **grandes y rápidos** (SATA, SAS, SSD)
- Evitar compartir disco con sistema operativo

**Ejemplos:**
```xml
<!-- Una ubicación (desarrollo) -->
<value>file:///opt/hadoop/hadoop-3.4.0/dfs/datanode</value>

<!-- Múltiples discos (RECOMENDADO en producción) -->
<value>file:///disk1/hadoop/datanode,file:///disk2/hadoop/datanode,file:///disk3/hadoop/datanode</value>

<!-- Con SSD para hot data -->
<value>file:///ssd/hadoop/datanode,file:///hdd1/hadoop/datanode,file:///hdd2/hadoop/datanode</value>
```

**Estructura interna:**
```
datanode/
└── current/
    ├── BP-123456-127.0.0.1-123456/
    │   └── current/
    │       └── finalized/
    │           └── subdir0/
    │               └── subdir0/
    │                   ├── blk_1073741825  (Bloque de datos)
    │                   └── blk_1073741825_1001.meta  (Checksum)
    └── VERSION
```

---

#### 4. `dfs.blocksize`

**Descripción:** Tamaño de cada bloque de datos en HDFS (en bytes)

```xml
<property>
    <name>dfs.blocksize</name>
    <value>134217728</value>
</property>
```

**Conversiones:**
- **67108864** = 64 MB
- **134217728** = 128 MB (DEFAULT) ✅
- **268435456** = 256 MB
- **536870912** = 512 MB

**¿Cómo funciona?**
- Archivo de 1 GB con bloques de 128 MB = **8 bloques**
- Archivo de 50 MB con bloques de 128 MB = **1 bloque** (no desperdicia espacio)
- Cada bloque se replica según `dfs.replication`

**Ventajas de bloques grandes (256 MB, 512 MB):**
- ✅ Menos metadatos en NameNode
- ✅ Menos overhead de red
- ✅ Mejor para archivos grandes (logs, videos, datasets masivos)

**Ventajas de bloques pequeños (64 MB, 128 MB):**
- ✅ Mejor paralelización (más bloques = más tareas Map)
- ✅ Mejor balanceo de carga
- ✅ Menos desperdicio con archivos pequeños

**Recomendaciones por caso:**
```xml
<!-- Muchos archivos pequeños (1-100 MB) -->
<value>67108864</value>  <!-- 64 MB -->

<!-- Uso general (DEFAULT) -->
<value>134217728</value>  <!-- 128 MB -->

<!-- Big Data masivo (archivos de GBs) -->
<value>268435456</value>  <!-- 256 MB -->

<!-- Procesamiento de logs enormes -->
<value>536870912</value>  <!-- 512 MB -->
```

---

#### 5. `dfs.namenode.http-address`

**Descripción:** Dirección y puerto del interfaz web del NameNode

```xml
<property>
    <name>dfs.namenode.http-address</name>
    <value>0.0.0.0:9870</value>
</property>
```

**Explicación:**
- **0.0.0.0**: Escuchar en todas las interfaces de red
- **9870**: Puerto web (antes era 50070 en Hadoop 2.x)

**Acceso:**
```
http://localhost:9870
http://192.168.1.100:9870
```

**Información disponible en Web UI:**
- Estado del cluster (live/dead DataNodes)
- Espacio usado/disponible
- Lista de archivos en HDFS
- Navegador de filesystem
- Logs del NameNode
- Información de bloques

**Valores comunes:**
```xml
<!-- Accesible desde cualquier IP -->
<value>0.0.0.0:9870</value>

<!-- Solo localhost -->
<value>localhost:9870</value>

<!-- IP específica -->
<value>192.168.1.100:9870</value>
```

---

#### 6. `dfs.datanode.http-address`

**Descripción:** Dirección y puerto del interfaz web del DataNode

```xml
<property>
    <name>dfs.datanode.http-address</name>
    <value>0.0.0.0:9864</value>
</property>
```

**Puerto por defecto:** 9864 (antes 50075 en Hadoop 2.x)

---

#### 7. `dfs.permissions.enabled`

**Descripción:** Habilitar/deshabilitar sistema de permisos HDFS

```xml
<property>
    <name>dfs.permissions.enabled</name>
    <value>false</value>
</property>
```

**Valores:**
- **true**: Permisos estrictos (como Linux: rwxr-xr-x)
- **false**: Sin restricciones (cualquiera puede hacer todo)

**Recomendación:**
- **Desarrollo/Pruebas**: `false` (más fácil)
- **Producción**: `true` (seguridad)

---

#### 8. `dfs.webhdfs.enabled`

**Descripción:** Habilitar API REST de HDFS

```xml
<property>
    <name>dfs.webhdfs.enabled</name>
    <value>true</value>
</property>
```

**¿Para qué sirve?**
- Acceder a HDFS vía HTTP/REST
- Integración con aplicaciones web
- No requiere cliente Java

**Ejemplo de uso:**
```bash
# Listar archivos vía REST
curl "http://nodo1:9870/webhdfs/v1/datos?op=LISTSTATUS&user.name=hadoop"

# Subir archivo
curl -i -X PUT "http://nodo1:9870/webhdfs/v1/datos/archivo.txt?op=CREATE&user.name=hadoop"
```

---

### 📝 Ejemplo Completo: hdfs-site.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
    <!-- Factor de replicación -->
    <property>
        <name>dfs.replication</name>
        <value>1</value>
        <description>Número de réplicas por bloque (1=pseudo-distribuido, 3=producción)</description>
    </property>
    
    <!-- Directorio de metadatos del NameNode -->
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>file:///opt/hadoop/hadoop-3.4.0/dfs/namenode</value>
        <description>Ubicación de FSImage y EditLog (CRÍTICO - hacer backup)</description>
    </property>
    
    <!-- Directorio de datos del DataNode -->
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file:///opt/hadoop/hadoop-3.4.0/dfs/datanode</value>
        <description>Ubicación de bloques de datos reales</description>
    </property>
    
    <!-- Tamaño de bloque -->
    <property>
        <name>dfs.blocksize</name>
        <value>134217728</value>
        <description>Tamaño de bloque: 128 MB</description>
    </property>
    
    <!-- Web UI del NameNode -->
    <property>
        <name>dfs.namenode.http-address</name>
        <value>0.0.0.0:9870</value>
        <description>Interfaz web del NameNode</description>
    </property>
    
    <!-- Web UI del DataNode -->
    <property>
        <name>dfs.datanode.http-address</name>
        <value>0.0.0.0:9864</value>
        <description>Interfaz web del DataNode</description>
    </property>
    
    <!-- Permisos de HDFS -->
    <property>
        <name>dfs.permissions.enabled</name>
        <value>false</value>
        <description>false=desarrollo, true=producción</description>
    </property>
    
    <!-- WebHDFS (API REST) -->
    <property>
        <name>dfs.webhdfs.enabled</name>
        <value>true</value>
        <description>Habilitar acceso REST a HDFS</description>
    </property>
</configuration>
```

---

---

## 📄 mapred-site.xml

### 🎯 Propósito

**Configuración de MapReduce** - Define cómo funcionan los trabajos de procesamiento distribuido:
- Framework de ejecución (YARN, local, classic)
- Configuración de memoria para Map y Reduce
- Directorio de staging
- Job History Server

### 📍 Ubicación

```
/opt/hadoop/hadoop-3.4.0/etc/hadoop/mapred-site.xml
```

---

### 🔧 Propiedades Principales

#### 1. `mapreduce.framework.name`

**Descripción:** Framework que ejecutará los trabajos MapReduce

```xml
<property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
</property>
```

**Valores posibles:**

| Valor | Descripción | Uso |
|-------|-------------|-----|
| **`local`** | Ejecución en un solo proceso (sin distribución) | Desarrollo y debug |
| **`classic`** | MapReduce v1 (obsoleto en Hadoop 3.x) | Legacy |
| **`yarn`** | MapReduce v2 con YARN (RECOMENDADO) ✅ | Producción |

**¿Por qué YARN?**
- ✅ Gestión de recursos centralizada
- ✅ Múltiples frameworks (no solo MapReduce)
- ✅ Mejor utilización de recursos
- ✅ Escalabilidad mejorada

---

#### 2. `mapreduce.application.classpath`

**Descripción:** Classpath de Java para aplicaciones MapReduce

```xml
<property>
    <name>mapreduce.application.classpath</name>
    <value>
        $HADOOP_HOME/share/hadoop/mapreduce/*,
        $HADOOP_HOME/share/hadoop/mapreduce/lib/*,
        $HADOOP_HOME/share/hadoop/common/*,
        $HADOOP_HOME/share/hadoop/common/lib/*,
        $HADOOP_HOME/share/hadoop/yarn/*,
        $HADOOP_HOME/share/hadoop/yarn/lib/*,
        $HADOOP_HOME/share/hadoop/hdfs/*,
        $HADOOP_HOME/share/hadoop/hdfs/lib/*
    </value>
</property>
```

**¿Para qué sirve?**
- Define dónde buscar librerías Java (.jar)
- Necesario para que MapReduce funcione correctamente
- Incluye HDFS, YARN, y dependencias

**⚠️ Si no se configura:**
- Error: `ClassNotFoundException`
- Jobs no inician

---

#### 3. `yarn.app.mapreduce.am.env`

**Descripción:** Variables de entorno para el Application Master de MapReduce

```xml
<property>
    <name>yarn.app.mapreduce.am.env</name>
    <value>HADOOP_MAPRED_HOME=$HADOOP_HOME</value>
</property>
```

**¿Qué es Application Master?**
- Proceso que coordina un job MapReduce específico
- Uno por cada job ejecutado
- Gestiona tareas Map y Reduce

---

#### 4. `mapreduce.map.env`

**Descripción:** Variables de entorno para tareas Map

```xml
<property>
    <name>mapreduce.map.env</name>
    <value>HADOOP_MAPRED_HOME=$HADOOP_HOME</value>
</property>
```

---

#### 5. `mapreduce.reduce.env`

**Descripción:** Variables de entorno para tareas Reduce

```xml
<property>
    <name>mapreduce.reduce.env</name>
    <value>HADOOP_MAPRED_HOME=$HADOOP_HOME</value>
</property>
```

---

#### 6. `mapreduce.map.memory.mb`

**Descripción:** Memoria máxima (en MB) para cada tarea Map

```xml
<property>
    <name>mapreduce.map.memory.mb</name>
    <value>1536</value>
</property>
```

**Valores típicos:**
- **1024 MB**: Mínimo funcional
- **1536 MB**: Desarrollo
- **2048 MB**: Producción pequeña
- **4096 MB**: Producción con datos grandes

**Cálculo recomendado:**
```
mapreduce.map.memory.mb = (RAM_nodo / núm_containers_simultáneos) * 0.8
```

**Ejemplo:**
- Nodo con 16 GB RAM
- 8 containers simultáneos
- `(16384 MB / 8) * 0.8 = 1638 MB` ≈ **1536 MB**

---

#### 7. `mapreduce.reduce.memory.mb`

**Descripción:** Memoria máxima (en MB) para cada tarea Reduce

```xml
<property>
    <name>mapreduce.reduce.memory.mb</name>
    <value>3072</value>
</property>
```

**Generalmente:**
```
mapreduce.reduce.memory.mb = 2 * mapreduce.map.memory.mb
```

**¿Por qué más memoria para Reduce?**
- Reduce agrupa datos de múltiples Maps
- Necesita buffers para sorting
- Operaciones de agregación consumen más RAM

---

#### 8. `mapreduce.map.java.opts`

**Descripción:** Opciones de JVM para tareas Map

```xml
<property>
    <name>mapreduce.map.java.opts</name>
    <value>-Xmx1024m</value>
</property>
```

**Explicación:**
- `-Xmx1024m`: Heap máximo de 1024 MB
- Debe ser **menor** que `mapreduce.map.memory.mb`
- Dejar ~20% para overhead de JVM

**Fórmula:**
```
-Xmx = mapreduce.map.memory.mb * 0.8
```

**Ejemplo:**
```xml
<!-- Si mapreduce.map.memory.mb = 1536 -->
<value>-Xmx1024m</value>  <!-- 1536 * 0.66 ≈ 1024 -->
```

---

#### 9. `mapreduce.reduce.java.opts`

**Descripción:** Opciones de JVM para tareas Reduce

```xml
<property>
    <name>mapreduce.reduce.java.opts</name>
    <value>-Xmx2048m</value>
</property>
```

**Fórmula:**
```
-Xmx = mapreduce.reduce.memory.mb * 0.8
```

**Ejemplo:**
```xml
<!-- Si mapreduce.reduce.memory.mb = 3072 -->
<value>-Xmx2048m</value>  <!-- 3072 * 0.66 ≈ 2048 -->
```

---

#### 10. `mapreduce.jobhistory.address`

**Descripción:** Dirección del Job History Server

```xml
<property>
    <name>mapreduce.jobhistory.address</name>
    <value>nodo1:10020</value>
</property>
```

**¿Qué es Job History Server?**
- Almacena historial de jobs completados
- Permite ver logs de jobs antiguos
- Útil para debugging y auditoría

---

#### 11. `mapreduce.jobhistory.webapp.address`

**Descripción:** Web UI del Job History Server

```xml
<property>
    <name>mapreduce.jobhistory.webapp.address</name>
    <value>0.0.0.0:19888</value>
</property>
```

**Acceso:**
```
http://localhost:19888
```

**Información disponible:**
- Jobs completados
- Jobs fallidos
- Tiempo de ejecución
- Estadísticas de recursos
- Logs completos

---

### 📝 Ejemplo Completo: mapred-site.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
    <!-- Framework de ejecución -->
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
        <description>Usar YARN como gestor de recursos</description>
    </property>
    
    <!-- Classpath de MapReduce -->
    <property>
        <name>mapreduce.application.classpath</name>
        <value>$HADOOP_HOME/share/hadoop/mapreduce/*,$HADOOP_HOME/share/hadoop/mapreduce/lib/*,$HADOOP_HOME/share/hadoop/common/*,$HADOOP_HOME/share/hadoop/common/lib/*,$HADOOP_HOME/share/hadoop/yarn/*,$HADOOP_HOME/share/hadoop/yarn/lib/*,$HADOOP_HOME/share/hadoop/hdfs/*,$HADOOP_HOME/share/hadoop/hdfs/lib/*</value>
    </property>
    
    <!-- Variables de entorno -->
    <property>
        <name>yarn.app.mapreduce.am.env</name>
        <value>HADOOP_MAPRED_HOME=$HADOOP_HOME</value>
    </property>
    
    <property>
        <name>mapreduce.map.env</name>
        <value>HADOOP_MAPRED_HOME=$HADOOP_HOME</value>
    </property>
    
    <property>
        <name>mapreduce.reduce.env</name>
        <value>HADOOP_MAPRED_HOME=$HADOOP_HOME</value>
    </property>
    
    <!-- Memoria para tareas Map -->
    <property>
        <name>mapreduce.map.memory.mb</name>
        <value>1536</value>
        <description>Memoria física para Map: 1.5 GB</description>
    </property>
    
    <!-- Memoria para tareas Reduce -->
    <property>
        <name>mapreduce.reduce.memory.mb</name>
        <value>3072</value>
        <description>Memoria física para Reduce: 3 GB</description>
    </property>
    
    <!-- Heap de JVM para Map -->
    <property>
        <name>mapreduce.map.java.opts</name>
        <value>-Xmx1024m</value>
        <description>Heap máximo para Map: 1 GB</description>
    </property>
    
    <!-- Heap de JVM para Reduce -->
    <property>
        <name>mapreduce.reduce.java.opts</name>
        <value>-Xmx2048m</value>
        <description>Heap máximo para Reduce: 2 GB</description>
    </property>
    
    <!-- Job History Server -->
    <property>
        <name>mapreduce.jobhistory.address</name>
        <value>nodo1:10020</value>
        <description>Dirección del servidor de historial</description>
    </property>
    
    <property>
        <name>mapreduce.jobhistory.webapp.address</name>
        <value>0.0.0.0:19888</value>
        <description>Web UI del historial de jobs</description>
    </property>
</configuration>
```

---

---

## 📄 yarn-site.xml

### 🎯 Propósito

**Configuración de YARN (Yet Another Resource Negotiator)** - Define cómo se gestionan recursos y se ejecutan aplicaciones:
- ResourceManager (coordinador global)
- NodeManager (ejecutor en cada nodo)
- Scheduler de recursos
- Límites de memoria y CPU

### 📍 Ubicación

```
/opt/hadoop/hadoop-3.4.0/etc/hadoop/yarn-site.xml
```

---

### 🔧 Propiedades Principales

#### 1. `yarn.nodemanager.aux-services`

**Descripción:** Servicios auxiliares que ejecuta el NodeManager

```xml
<property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
</property>
```

**¿Qué es mapreduce_shuffle?**
- Servicio que transfiere datos entre Map y Reduce
- **OBLIGATORIO** para que MapReduce funcione
- Maneja el "shuffle" (mezcla) de datos intermedios

**Flujo MapReduce:**
```
Map → [mapreduce_shuffle] → Reduce
```

**Si no está configurado:**
- Error: "Failed to run job: no aux-services configured"
- MapReduce no funcionará

---

#### 2. `yarn.nodemanager.aux-services.mapreduce_shuffle.class`

**Descripción:** Clase Java que implementa el servicio shuffle

```xml
<property>
    <name>yarn.nodemanager.aux-services.mapreduce_shuffle.class</name>
    <value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>
```

**Nota:** Esta es la clase estándar, raramente se cambia.

---

#### 3. `yarn.resourcemanager.hostname`

**Descripción:** Hostname del ResourceManager

```xml
<property>
    <name>yarn.resourcemanager.hostname</name>
    <value>nodo1</value>
</property>
```

**¿Qué es ResourceManager?**
- **Cerebro del cluster YARN**
- Asigna recursos (RAM, CPU) a aplicaciones
- Un único RM por cluster (puede tener HA con standby)

**Valores posibles:**
```xml
<value>localhost</value>
<value>nodo1</value>
<value>192.168.1.100</value>
<value>hadoop-master.domain.com</value>
```

---

#### 4. `yarn.resourcemanager.address`

**Descripción:** Dirección para clientes que envían aplicaciones

```xml
<property>
    <name>yarn.resourcemanager.address</name>
    <value>nodo1:8032</value>
</property>
```

**Puerto 8032:** Puerto RPC para comunicación cliente-RM

---

#### 5. `yarn.resourcemanager.scheduler.address`

**Descripción:** Dirección del scheduler para ApplicationMasters

```xml
<property>
    <name>yarn.resourcemanager.scheduler.address</name>
    <value>nodo1:8030</value>
</property>
```

**Puerto 8030:** Puerto para que AMs soliciten recursos

---

#### 6. `yarn.resourcemanager.resource-tracker.address`

**Descripción:** Dirección para NodeManagers que reportan al RM

```xml
<property>
    <name>yarn.resourcemanager.resource-tracker.address</name>
    <value>nodo1:8031</value>
</property>
```

**Puerto 8031:** Puerto para heartbeats de NodeManagers

---

#### 7. `yarn.resourcemanager.webapp.address`

**Descripción:** Web UI del ResourceManager

```xml
<property>
    <name>yarn.resourcemanager.webapp.address</name>
    <value>0.0.0.0:8088</value>
</property>
```

**Puerto 8088:** Puerto web del ResourceManager

**Acceso:**
```
http://localhost:8088
http://192.168.1.100:8088
```

**Información disponible:**
- Aplicaciones en ejecución
- Aplicaciones completadas/fallidas
- Uso de recursos (RAM, CPU)
- Estado de NodeManagers
- Queue de trabajos
- Métricas del cluster

---

#### 8. `yarn.nodemanager.resource.memory-mb`

**Descripción:** Memoria total (en MB) que el NodeManager puede asignar a contenedores

```xml
<property>
    <name>yarn.nodemanager.resource.memory-mb</name>
    <value>8192</value>
</property>
```

**¿Cómo calcular este valor?**

```
yarn.nodemanager.resource.memory-mb = (RAM_total - RAM_SO - RAM_otros_servicios)
```

**Ejemplo con 16 GB RAM:**
```
RAM total:           16 GB (16384 MB)
- Sistema operativo: 2 GB  (2048 MB)
- Otros servicios:   2 GB  (2048 MB)
- HDFS/NameNode:     1 GB  (1024 MB)
= YARN disponible:   11 GB (11264 MB)
```

```xml
<value>11264</value>
```

**Valores típicos:**

| RAM Total | Valor Recomendado |
|-----------|-------------------|
| 4 GB | 2048 MB |
| 8 GB | 6144 MB |
| 16 GB | 12288 MB |
| 32 GB | 26624 MB |
| 64 GB | 57344 MB |

---

#### 9. `yarn.scheduler.minimum-allocation-mb`

**Descripción:** Memoria mínima (en MB) que se puede solicitar para un contenedor

```xml
<property>
    <name>yarn.scheduler.minimum-allocation-mb</name>
    <value>1024</value>
</property>
```

**¿Para qué sirve?**
- Evita contenedores muy pequeños (ineficientes)
- Todas las solicitudes se redondean a múltiplos de este valor

**Ejemplo:**
- Si pides 1200 MB y el mínimo es 1024 MB
- YARN asignará 2048 MB (siguiente múltiplo)

---

#### 10. `yarn.scheduler.maximum-allocation-mb`

**Descripción:** Memoria máxima (en MB) que se puede solicitar para un contenedor

```xml
<property>
    <name>yarn.scheduler.maximum-allocation-mb</name>
    <value>8192</value>
</property>
```

**Regla:**
```
yarn.scheduler.maximum-allocation-mb ≤ yarn.nodemanager.resource.memory-mb
```

**Generalmente:**
```
yarn.scheduler.maximum-allocation-mb = yarn.nodemanager.resource.memory-mb * 0.8
```

---

#### 11. `yarn.nodemanager.resource.cpu-vcores`

**Descripción:** Número de CPUs virtuales disponibles para contenedores

```xml
<property>
    <name>yarn.nodemanager.resource.cpu-vcores</name>
    <value>4</value>
</property>
```

**¿Cómo calcular?**
- **Desarrollo:** Núcleos físicos - 1
- **Producción:** Núcleos físicos * 1.5 (oversubscription)

**Ejemplos:**

| CPUs Físicos | Desarrollo | Producción |
|--------------|------------|------------|
| 2 | 1 | 3 |
| 4 | 3 | 6 |
| 8 | 7 | 12 |
| 16 | 15 | 24 |

```bash
# Ver núcleos disponibles
nproc
lscpu | grep "^CPU(s):"
```

---

#### 12. `yarn.nodemanager.vmem-check-enabled`

**Descripción:** Habilitar verificación de memoria virtual

```xml
<property>
    <name>yarn.nodemanager.vmem-check-enabled</name>
    <value>false</value>
</property>
```

**Valores:**
- **false**: No verificar (RECOMENDADO para desarrollo)
- **true**: Verificar límites estrictos

**¿Por qué deshabilitar?**
- Evita errores como "Container killed on request. Exit code is 143"
- Java tiende a usar mucha memoria virtual
- En producción, considerar `true` para control estricto

---

#### 13. `yarn.nodemanager.pmem-check-enabled`

**Descripción:** Habilitar verificación de memoria física

```xml
<property>
    <name>yarn.nodemanager.pmem-check-enabled</name>
    <value>false</value>
</property>
```

**Recomendación:** `false` en desarrollo, `true` en producción

---

#### 14. `yarn.log-aggregation-enable`

**Descripción:** Agregar logs de contenedores en HDFS después de completar aplicación

```xml
<property>
    <name>yarn.log-aggregation-enable</name>
    <value>true</value>
</property>
```

**Ventajas:**
- ✅ Logs persisten después de que el contenedor termina
- ✅ Accesibles vía `yarn logs -applicationId`
- ✅ No se pierden si el nodo falla

**Desventajas:**
- ❌ Consume espacio en HDFS
- ❌ Pequeño overhead al finalizar aplicación

---

#### 15. `yarn.nodemanager.local-dirs`

**Descripción:** Directorios locales para datos intermedios de contenedores

```xml
<property>
    <name>yarn.nodemanager.local-dirs</name>
    <value>/opt/hadoop/hadoop-3.4.0/yarn/local</value>
</property>
```

**¿Qué se guarda aquí?**
- Datos temporales de contenedores
- JAR files de aplicaciones
- Archivos de trabajo intermedios

**Recomendación:**
- Usar **múltiples discos** para I/O paralelo
- Usar discos rápidos (SSD si es posible)

```xml
<!-- Múltiples discos -->
<value>/disk1/yarn/local,/disk2/yarn/local,/disk3/yarn/local</value>
```

---

#### 16. `yarn.nodemanager.log-dirs`

**Descripción:** Directorios locales para logs de contenedores

```xml
<property>
    <name>yarn.nodemanager.log-dirs</name>
    <value>/opt/hadoop/hadoop-3.4.0/yarn/logs</value>
</property>
```

**Nota:** Si `yarn.log-aggregation-enable=true`, estos logs se copian a HDFS

---

### 📝 Ejemplo Completo: yarn-site.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
    <!-- Servicios auxiliares -->
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
        <description>Servicio de shuffle para MapReduce</description>
    </property>
    
    <property>
        <name>yarn.nodemanager.aux-services.mapreduce_shuffle.class</name>
        <value>org.apache.hadoop.mapred.ShuffleHandler</value>
    </property>
    
    <!-- ResourceManager -->
    <property>
        <name>yarn.resourcemanager.hostname</name>
        <value>nodo1</value>
        <description>Hostname del ResourceManager</description>
    </property>
    
    <property>
        <name>yarn.resourcemanager.address</name>
        <value>nodo1:8032</value>
    </property>
    
    <property>
        <name>yarn.resourcemanager.scheduler.address</name>
        <value>nodo1:8030</value>
    </property>
    
    <property>
        <name>yarn.resourcemanager.resource-tracker.address</name>
        <value>nodo1:8031</value>
    </property>
    
    <!-- Web UI del ResourceManager -->
    <property>
        <name>yarn.resourcemanager.webapp.address</name>
        <value>0.0.0.0:8088</value>
        <description>Interfaz web: http://localhost:8088</description>
    </property>
    
    <!-- Recursos de memoria -->
    <property>
        <name>yarn.nodemanager.resource.memory-mb</name>
        <value>8192</value>
        <description>Memoria total disponible: 8 GB</description>
    </property>
    
    <property>
        <name>yarn.scheduler.minimum-allocation-mb</name>
        <value>1024</value>
        <description>Asignación mínima: 1 GB</description>
    </property>
    
    <property>
        <name>yarn.scheduler.maximum-allocation-mb</name>
        <value>8192</value>
        <description>Asignación máxima: 8 GB</description>
    </property>
    
    <!-- Recursos de CPU -->
    <property>
        <name>yarn.nodemanager.resource.cpu-vcores</name>
        <value>4</value>
        <description>CPUs virtuales disponibles</description>
    </property>
    
    <!-- Verificación de memoria -->
    <property>
        <name>yarn.nodemanager.vmem-check-enabled</name>
        <value>false</value>
        <description>Deshabilitar check de virtual memory</description>
    </property>
    
    <property>
        <name>yarn.nodemanager.pmem-check-enabled</name>
        <value>false</value>
        <description>Deshabilitar check de physical memory</description>
    </property>
    
    <!-- Agregación de logs -->
    <property>
        <name>yarn.log-aggregation-enable</name>
        <value>true</value>
        <description>Guardar logs en HDFS</description>
    </property>
    
    <!-- Directorios locales -->
    <property>
        <name>yarn.nodemanager.local-dirs</name>
        <value>/opt/hadoop/hadoop-3.4.0/yarn/local</value>
        <description>Datos temporales de contenedores</description>
    </property>
    
    <property>
        <name>yarn.nodemanager.log-dirs</name>
        <value>/opt/hadoop/hadoop-3.4.0/yarn/logs</value>
        <description>Logs locales de contenedores</description>
    </property>
</configuration>
```

---

---

## 📄 hive-site.xml

### 🎯 Propósito

**Configuración de Apache Hive** - Define cómo funciona el data warehouse sobre Hadoop:
- Conexión a metastore (base de datos de metadatos)
- Warehouse (ubicación de datos)
- Modo de ejecución (local vs YARN)
- Optimizaciones y paralelismo

### 📍 Ubicación

```
/opt/hive/conf/hive-site.xml
```

---

### 🔧 Propiedades Principales

#### 1. `javax.jdo.option.ConnectionURL`

**Descripción:** URL de conexión a la base de datos del metastore

```xml
<property>
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:derby:;databaseName=/opt/hive/metastore_db;create=true</value>
</property>
```

**¿Qué es el Metastore?**
- Base de datos que guarda metadatos de Hive:
  - Nombres de tablas y columnas
  - Tipos de datos
  - Ubicación de archivos en HDFS
  - Particiones
  - Estadísticas

**Opciones de base de datos:**

##### Apache Derby (embedded - para desarrollo)
```xml
<value>jdbc:derby:;databaseName=/opt/hive/metastore_db;create=true</value>
```
- ✅ No requiere instalación adicional
- ✅ Fácil setup
- ❌ Solo una conexión simultánea
- ❌ NO para producción

##### MySQL (recomendado para producción)
```xml
<value>jdbc:mysql://localhost:3306/metastore?createDatabaseIfNotExist=true&amp;useSSL=false</value>
```
- ✅ Múltiples conexiones
- ✅ Robusto y confiable
- ✅ Backups fáciles
- ⚠️ Requiere instalar MySQL

##### PostgreSQL
```xml
<value>jdbc:postgresql://localhost:5432/metastore</value>
```

---

#### 2. `javax.jdo.option.ConnectionDriverName`

**Descripción:** Driver JDBC de la base de datos

```xml
<property>
    <name>javax.jdo.option.ConnectionDriverName</name>
    <value>org.apache.derby.jdbc.EmbeddedDriver</value>
</property>
```

**Drivers por tipo de BD:**

| Base de Datos | Driver |
|---------------|--------|
| Derby | `org.apache.derby.jdbc.EmbeddedDriver` |
| MySQL | `com.mysql.cj.jdbc.Driver` o `com.mysql.jdbc.Driver` |
| PostgreSQL | `org.postgresql.Driver` |
| Oracle | `oracle.jdbc.OracleDriver` |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` |

---

#### 3. `javax.jdo.option.ConnectionUserName`

**Descripción:** Usuario de la base de datos del metastore

```xml
<property>
    <name>javax.jdo.option.ConnectionUserName</name>
    <value>hive</value>
</property>
```

**Para Derby:** No se usa (puede dejarse vacío)

**Para MySQL:**
```xml
<value>hive</value>  <!-- o root -->
```

---

#### 4. `javax.jdo.option.ConnectionPassword`

**Descripción:** Contraseña del usuario de la base de datos

```xml
<property>
    <name>javax.jdo.option.ConnectionPassword</name>
    <value>hivepassword</value>
</property>
```

**Para Derby:** No se usa (puede dejarse vacío)

**Para MySQL:**
```xml
<value>tu_password_mysql</value>
```

**⚠️ Seguridad:**
- No usar contraseñas débiles en producción
- Considerar encriptar con Hadoop Credential Provider

---

#### 5. `hive.metastore.warehouse.dir`

**Descripción:** Directorio en HDFS donde se guardan las tablas de Hive

```xml
<property>
    <name>hive.metastore.warehouse.dir</name>
    <value>/user/hive/warehouse</value>
</property>
```

**¿Qué se guarda aquí?**
- Datos de tablas **managed** (gestionadas por Hive)
- Estructura de directorios por base de datos y tabla:

```
/user/hive/warehouse/
├── mi_base_de_datos.db/
│   ├── tabla1/
│   │   ├── 000000_0
│   │   └── 000000_1
│   └── tabla2/
│       └── 000000_0
└── otra_db.db/
    └── usuarios/
        └── part-00000
```

**Nota:** Tablas **EXTERNAL** se guardan donde se especifique en `LOCATION`

---

#### 6. `hive.metastore.uris`

**Descripción:** URI del servicio Metastore (para modo remoto)

```xml
<property>
    <name>hive.metastore.uris</name>
    <value>thrift://nodo1:9083</value>
</property>
```

**Modos de Metastore:**

##### Embedded Mode (desarrollo)
```xml
<value></value>  <!-- Vacío -->
```
- Metastore en mismo proceso que Hive
- No requiere servicio separado

##### Remote Mode (producción)
```xml
<value>thrift://nodo1:9083</value>
```
- Metastore como servicio independiente
- Múltiples clientes Hive pueden conectarse
- Requiere iniciar: `hive --service metastore`

---

#### 7. `hive.server2.thrift.port`

**Descripción:** Puerto de HiveServer2 para conexiones JDBC/ODBC

```xml
<property>
    <name>hive.server2.thrift.port</name>
    <value>10000</value>
</property>
```

**¿Qué es HiveServer2?**
- Servicio que permite conexiones remotas a Hive
- Usado por:
  - Beeline (CLI alternativo)
  - Herramientas BI (Tableau, Power BI)
  - Aplicaciones JDBC

**Iniciar HiveServer2:**
```bash
hive --service hiveserver2
```

**Conectar con Beeline:**
```bash
beeline -u jdbc:hive2://localhost:10000/default -n hadoop
```

---

#### 8. `hive.server2.thrift.bind.host`

**Descripción:** IP/hostname donde escucha HiveServer2

```xml
<property>
    <name>hive.server2.thrift.bind.host</name>
    <value>0.0.0.0</value>
</property>
```

**Valores:**
- `0.0.0.0`: Todas las interfaces (acceso desde cualquier IP)
- `localhost`: Solo acceso local
- `192.168.1.100`: IP específica

---

#### 9. `hive.exec.mode.local.auto`

**Descripción:** Habilitar modo local automático para jobs pequeños

```xml
<property>
    <name>hive.exec.mode.local.auto</name>
    <value>true</value>
</property>
```

**¿Qué hace?**
- Jobs pequeños se ejecutan localmente (sin YARN)
- Evita overhead de distribuir tarea pequeña
- Más rápido para queries simples

**Criterios (se ejecuta localmente si se cumplen TODOS):**
- Input < 128 MB
- Maps < 4
- Reduces = 1

---

#### 10. `hive.exec.mode.local.auto.inputbytes.max`

**Descripción:** Máximo de bytes de entrada para modo local

```xml
<property>
    <name>hive.exec.mode.local.auto.inputbytes.max</name>
    <value>134217728</value>
</property>
```

**Valor por defecto:** 134217728 bytes = 128 MB

---

#### 11. `hive.exec.mode.local.auto.input.files.max`

**Descripción:** Máximo de archivos de entrada para modo local

```xml
<property>
    <name>hive.exec.mode.local.auto.input.files.max</name>
    <value>4</value>
</property>
```

---

#### 12. `hive.exec.dynamic.partition`

**Descripción:** Habilitar particionamiento dinámico

```xml
<property>
    <name>hive.exec.dynamic.partition</name>
    <value>true</value>
</property>
```

**¿Qué es particionamiento?**
- Dividir tabla en subdirectorios por columna(s)
- Ejemplo: particionar ventas por año y mes

```
/warehouse/ventas/
├── year=2023/
│   ├── month=01/
│   ├── month=02/
│   └── month=03/
└── year=2024/
    ├── month=01/
    └── month=02/
```

**Ventajas:**
- ✅ Queries más rápidos (skip particiones irrelevantes)
- ✅ Mejor organización

---

#### 13. `hive.exec.dynamic.partition.mode`

**Descripción:** Modo de particionamiento dinámico

```xml
<property>
    <name>hive.exec.dynamic.partition.mode</name>
    <value>nonstrict</value>
</property>
```

**Valores:**
- **`strict`**: Requiere al menos una partición estática
- **`nonstrict`**: Todas las particiones pueden ser dinámicas

---

#### 14. `hive.exec.max.dynamic.partitions`

**Descripción:** Máximo de particiones dinámicas totales

```xml
<property>
    <name>hive.exec.max.dynamic.partitions</name>
    <value>1000</value>
</property>
```

**Evita:** Crear miles de particiones accidentalmente

---

#### 15. `hive.exec.parallel`

**Descripción:** Ejecutar stages de query en paralelo cuando sea posible

```xml
<property>
    <name>hive.exec.parallel</name>
    <value>true</value>
</property>
```

**Efecto:**
- ✅ Queries más rápidos
- ✅ Mejor uso de recursos

---

#### 16. `hive.exec.parallel.thread.number`

**Descripción:** Número de threads para ejecución paralela

```xml
<property>
    <name>hive.exec.parallel.thread.number</name>
    <value>8</value>
</property>
```

**Recomendación:** Número de cores disponibles

---

#### 17. `hive.vectorized.execution.enabled`

**Descripción:** Habilitar ejecución vectorizada (procesar múltiples filas a la vez)

```xml
<property>
    <name>hive.vectorized.execution.enabled</name>
    <value>true</value>
</property>
```

**Ventaja:** Queries 3-10x más rápidos

---

#### 18. `hive.compute.query.using.stats`

**Descripción:** Usar estadísticas para responder queries simples

```xml
<property>
    <name>hive.compute.query.using.stats</name>
    <value>true</value>
</property>
```

**Ejemplo:**
```sql
SELECT COUNT(*) FROM tabla;
```
- Sin stats: Lee toda la tabla
- Con stats: Lee metastore (mucho más rápido)

---

#### 19. `hive.support.concurrency`

**Descripción:** Habilitar control de concurrencia

```xml
<property>
    <name>hive.support.concurrency</name>
    <value>true</value>
</property>
```

**Necesario para:**
- Transacciones ACID
- INSERT, UPDATE, DELETE
- Múltiples usuarios simultáneos

---

#### 20. `hive.txn.manager`

**Descripción:** Gestor de transacciones

```xml
<property>
    <name>hive.txn.manager</name>
    <value>org.apache.hadoop.hive.ql.lockmgr.DbTxnManager</value>
</property>
```

**Requerido para:** Transacciones ACID

---

### 📝 Ejemplo Completo: hive-site.xml

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
    <!-- Configuración del Metastore (Derby) -->
    <property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:derby:;databaseName=/opt/hive/metastore_db;create=true</value>
        <description>URL de conexión a Derby (embedded)</description>
    </property>
    
    <property>
        <name>javax.jdo.option.ConnectionDriverName</name>
        <value>org.apache.derby.jdbc.EmbeddedDriver</value>
        <description>Driver JDBC de Derby</description>
    </property>
    
    <property>
        <name>javax.jdo.option.ConnectionUserName</name>
        <value>APP</value>
    </property>
    
    <property>
        <name>javax.jdo.option.ConnectionPassword</name>
        <value>mine</value>
    </property>
    
    <!-- Warehouse en HDFS -->
    <property>
        <name>hive.metastore.warehouse.dir</name>
        <value>/user/hive/warehouse</value>
        <description>Ubicación de datos de tablas en HDFS</description>
    </property>
    
    <!-- Metastore remoto (comentado para modo embedded) -->
    <!--
    <property>
        <name>hive.metastore.uris</name>
        <value>thrift://nodo1:9083</value>
        <description>URI del metastore remoto</description>
    </property>
    -->
    
    <!-- HiveServer2 -->
    <property>
        <name>hive.server2.thrift.port</name>
        <value>10000</value>
        <description>Puerto de HiveServer2</description>
    </property>
    
    <property>
        <name>hive.server2.thrift.bind.host</name>
        <value>0.0.0.0</value>
        <description>Escuchar en todas las interfaces</description>
    </property>
    
    <!-- Modo local automático -->
    <property>
        <name>hive.exec.mode.local.auto</name>
        <value>true</value>
        <description>Ejecutar jobs pequeños localmente</description>
    </property>
    
    <property>
        <name>hive.exec.mode.local.auto.inputbytes.max</name>
        <value>134217728</value>
        <description>Máximo 128 MB para modo local</description>
    </property>
    
    <property>
        <name>hive.exec.mode.local.auto.input.files.max</name>
        <value>4</value>
        <description>Máximo 4 archivos para modo local</description>
    </property>
    
    <!-- Particionamiento dinámico -->
    <property>
        <name>hive.exec.dynamic.partition</name>
        <value>true</value>
        <description>Habilitar particiones dinámicas</description>
    </property>
    
    <property>
        <name>hive.exec.dynamic.partition.mode</name>
        <value>nonstrict</value>
        <description>Permitir todas las particiones dinámicas</description>
    </property>
    
    <property>
        <name>hive.exec.max.dynamic.partitions</name>
        <value>1000</value>
        <description>Máximo de particiones dinámicas</description>
    </property>
    
    <!-- Ejecución paralela -->
    <property>
        <name>hive.exec.parallel</name>
        <value>true</value>
        <description>Ejecutar stages en paralelo</description>
    </property>
    
    <property>
        <name>hive.exec.parallel.thread.number</name>
        <value>8</value>
        <description>Threads para ejecución paralela</description>
    </property>
    
    <!-- Optimizaciones -->
    <property>
        <name>hive.vectorized.execution.enabled</name>
        <value>true</value>
        <description>Ejecución vectorizada (más rápido)</description>
    </property>
    
    <property>
        <name>hive.compute.query.using.stats</name>
        <value>true</value>
        <description>Usar estadísticas para queries simples</description>
    </property>
    
    <!-- Transacciones ACID -->
    <property>
        <name>hive.support.concurrency</name>
        <value>true</value>
        <description>Habilitar control de concurrencia</description>
    </property>
    
    <property>
        <name>hive.txn.manager</name>
        <value>org.apache.hadoop.hive.ql.lockmgr.DbTxnManager</value>
        <description>Gestor de transacciones ACID</description>
    </property>
    
    <!-- Compatibilidad MapReduce -->
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
        <description>Usar YARN para ejecutar jobs</description>
    </property>
</configuration>
```

---

---

## 📂 Resumen de Ubicaciones

### Tabla de Archivos de Configuración

| Archivo | Ubicación | Propósito |
|---------|-----------|-----------|
| **core-site.xml** | `/opt/hadoop/hadoop-3.4.0/etc/hadoop/` | Configuración central de Hadoop |
| **hdfs-site.xml** | `/opt/hadoop/hadoop-3.4.0/etc/hadoop/` | Configuración de HDFS |
| **mapred-site.xml** | `/opt/hadoop/hadoop-3.4.0/etc/hadoop/` | Configuración de MapReduce |
| **yarn-site.xml** | `/opt/hadoop/hadoop-3.4.0/etc/hadoop/` | Configuración de YARN |
| **hive-site.xml** | `/opt/hive/conf/` | Configuración de Hive |

---

### Comandos para Editar

```bash
# Editar core-site.xml
nano /opt/hadoop/hadoop-3.4.0/etc/hadoop/core-site.xml

# Editar hdfs-site.xml
nano /opt/hadoop/hadoop-3.4.0/etc/hadoop/hdfs-site.xml

# Editar mapred-site.xml
nano /opt/hadoop/hadoop-3.4.0/etc/hadoop/mapred-site.xml

# Editar yarn-site.xml
nano /opt/hadoop/hadoop-3.4.0/etc/hadoop/yarn-site.xml

# Editar hive-site.xml
nano /opt/hive/conf/hive-site.xml
```

---

---

## 🔄 Jerarquía de Configuración

### Orden de Precedencia (de mayor a menor)

1. **Parámetros en línea de comandos**
   ```bash
   hadoop jar mi_app.jar -Dmapreduce.map.memory.mb=4096
   ```

2. **Variables de configuración en código**
   ```java
   Configuration conf = new Configuration();
   conf.set("mapreduce.map.memory.mb", "4096");
   ```

3. **Archivos *-site.xml** (estos archivos)
   ```xml
   <property>
       <name>mapreduce.map.memory.mb</name>
       <value>1536</value>
   </property>
   ```

4. **Archivos *-default.xml** (valores por defecto de Hadoop)
   - Dentro de los JARs de Hadoop
   - No editables

---

### Aplicar Cambios en Configuración

**⚠️ IMPORTANTE:** Después de editar cualquier archivo XML:

```bash
# Detener servicios
stop-all.sh

# Reiniciar servicios
start-all.sh

# Verificar que todo esté corriendo
jps
```

**Output esperado de `jps`:**
```
12345 NameNode
12346 DataNode
12347 SecondaryNameNode
12348 ResourceManager
12349 NodeManager
12350 Jps
```

---

---

## 📊 Tabla Resumen de Propiedades Críticas

### Por Archivo

#### core-site.xml
| Propiedad | Valor Típico | Descripción |
|-----------|--------------|-------------|
| `fs.defaultFS` | `hdfs://nodo1:9000` | URI del NameNode |
| `hadoop.tmp.dir` | `/opt/hadoop/hadoop-3.4.0/tmp` | Directorio temporal |
| `io.file.buffer.size` | `131072` (128 KB) | Buffer de I/O |

#### hdfs-site.xml
| Propiedad | Valor Típico | Descripción |
|-----------|--------------|-------------|
| `dfs.replication` | `1` (dev), `3` (prod) | Factor de replicación |
| `dfs.namenode.name.dir` | `file:///opt/.../namenode` | Metadatos de HDFS |
| `dfs.datanode.data.dir` | `file:///opt/.../datanode` | Bloques de datos |
| `dfs.blocksize` | `134217728` (128 MB) | Tamaño de bloque |

#### mapred-site.xml
| Propiedad | Valor Típico | Descripción |
|-----------|--------------|-------------|
| `mapreduce.framework.name` | `yarn` | Framework de ejecución |
| `mapreduce.map.memory.mb` | `1536` | Memoria para Map |
| `mapreduce.reduce.memory.mb` | `3072` | Memoria para Reduce |
| `mapreduce.map.java.opts` | `-Xmx1024m` | Heap de JVM para Map |

#### yarn-site.xml
| Propiedad | Valor Típico | Descripción |
|-----------|--------------|-------------|
| `yarn.nodemanager.aux-services` | `mapreduce_shuffle` | Servicio shuffle |
| `yarn.resourcemanager.hostname` | `nodo1` | Hostname del RM |
| `yarn.nodemanager.resource.memory-mb` | `8192` | Memoria total YARN |
| `yarn.scheduler.minimum-allocation-mb` | `1024` | Memoria mínima |
| `yarn.scheduler.maximum-allocation-mb` | `8192` | Memoria máxima |

#### hive-site.xml
| Propiedad | Valor Típico | Descripción |
|-----------|--------------|-------------|
| `javax.jdo.option.ConnectionURL` | `jdbc:derby:...` | Conexión a metastore |
| `hive.metastore.warehouse.dir` | `/user/hive/warehouse` | Ubicación de datos |
| `hive.exec.mode.local.auto` | `true` | Modo local automático |
| `hive.exec.dynamic.partition` | `true` | Particiones dinámicas |

---

---

## 💡 Tips Finales

### 1. Validar Configuración

```bash
# Ver configuración activa de Hadoop
hadoop conf

# Ver configuración específica
hadoop conf | grep fs.defaultFS
```

### 2. Backup de Configuración

```bash
# Hacer backup antes de cambiar
cd /opt/hadoop/hadoop-3.4.0/etc/hadoop/
cp core-site.xml core-site.xml.backup
cp hdfs-site.xml hdfs-site.xml.backup
cp mapred-site.xml mapred-site.xml.backup
cp yarn-site.xml yarn-site.xml.backup
```

### 3. Validar Sintaxis XML

```bash
# Verificar que XML esté bien formado
xmllint --noout core-site.xml

# Si no tienes xmllint, instalar
sudo yum install libxml2
```

### 4. Ver Configuración en Web UI

**HDFS NameNode:**
```
http://localhost:9870/conf
```

**YARN ResourceManager:**
```
http://localhost:8088/conf
```

### 5. Logs de Errores

```bash
# Ver logs si algo falla
tail -f /opt/hadoop/hadoop-3.4.0/logs/hadoop-hadoop-namenode-*.log
tail -f /opt/hadoop/hadoop-3.4.0/logs/hadoop-hadoop-datanode-*.log
tail -f /opt/hadoop/hadoop-3.4.0/logs/yarn-hadoop-resourcemanager-*.log
```

---

---

## 🎓 Ejemplos de Configuración por Escenario

### Escenario 1: Desarrollo Local (1 máquina, 8 GB RAM)

**core-site.xml:**
```xml
<property>
    <name>fs.defaultFS</name>
    <value>hdfs://localhost:9000</value>
</property>
```

**hdfs-site.xml:**
```xml
<property>
    <name>dfs.replication</name>
    <value>1</value>
</property>
```

**yarn-site.xml:**
```xml
<property>
    <name>yarn.nodemanager.resource.memory-mb</name>
    <value>4096</value>
</property>
```

---

### Escenario 2: Cluster Pequeño (3 nodos, 16 GB RAM cada uno)

**core-site.xml:**
```xml
<property>
    <name>fs.defaultFS</name>
    <value>hdfs://master:9000</value>
</property>
```

**hdfs-site.xml:**
```xml
<property>
    <name>dfs.replication</name>
    <value>2</value>
</property>

<property>
    <name>dfs.namenode.name.dir</name>
    <value>file:///disk1/hadoop/namenode,file:///disk2/hadoop/namenode</value>
</property>
```

**yarn-site.xml:**
```xml
<property>
    <name>yarn.nodemanager.resource.memory-mb</name>
    <value>12288</value>
</property>

<property>
    <name>yarn.scheduler.maximum-allocation-mb</name>
    <value>12288</value>
</property>
```

---

### Escenario 3: Producción (10+ nodos, 64 GB RAM cada uno)

**hdfs-site.xml:**
```xml
<property>
    <name>dfs.replication</name>
    <value>3</value>
</property>

<property>
    <name>dfs.blocksize</name>
    <value>268435456</value>  <!-- 256 MB -->
</property>

<property>
    <name>dfs.datanode.data.dir</name>
    <value>file:///disk1/hadoop/data,file:///disk2/hadoop/data,file:///disk3/hadoop/data,file:///disk4/hadoop/data</value>
</property>
```

**yarn-site.xml:**
```xml
<property>
    <name>yarn.nodemanager.resource.memory-mb</name>
    <value>57344</value>  <!-- 56 GB -->
</property>

<property>
    <name>yarn.scheduler.maximum-allocation-mb</name>
    <value>16384</value>  <!-- 16 GB -->
</property>

<property>
    <name>yarn.nodemanager.resource.cpu-vcores</name>
    <value>24</value>
</property>
```

**mapred-site.xml:**
```xml
<property>
    <name>mapreduce.map.memory.mb</name>
    <value>4096</value>
</property>

<property>
    <name>mapreduce.reduce.memory.mb</name>
    <value>8192</value>
</property>

<property>
    <name>mapreduce.map.java.opts</name>
    <value>-Xmx3072m</value>
</property>

<property>
    <name>mapreduce.reduce.java.opts</name>
    <value>-Xmx6144m</value>
</property>
```

---

<div align="center">

## 🎓 Fin de la Guía de Configuración

**Referencia Completa de Archivos XML de Hadoop y Hive**

✅ 5 archivos explicados en detalle  
✅ 80+ propiedades documentadas  
✅ Valores por defecto y recomendados  
✅ Ejemplos por escenario  
✅ Tips de troubleshooting  
✅ Fórmulas de cálculo de recursos  

---

*Guía de Configuración - Sistemas Inteligentes*  
*Febrero 2026*

</div>
