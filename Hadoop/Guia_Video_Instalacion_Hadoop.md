# 🎥 Guía para Video: Instalación de Hadoop + Hive en CentOS

> **Para:** Video tutorial de instalación  
> **Duración estimada:** 20-30 minutos  
> **Nivel:** Básico

---

## 📋 Requisitos Previos

- **CentOS 10** instalado en VirtualBox
- **4 GB RAM** mínimo
- **20 GB disco** disponible
- **Conexión a Internet** activa

---

## 🎯 PARTE 1: Preparación del Sistema (5 min)

### 1. Actualizar Sistema y Verificar Red

```bash
# Actualizar paquetes
sudo yum update
# yum: gestor de paquetes de CentOS/RHEL
# update: descarga e instala actualizaciones de seguridad y sistema

# Verificar conectividad
ping -c 4 google.com
# ping: envía paquetes ICMP para probar conexión
# -c 4: envía solo 4 paquetes y termina

# Si no hay conexión, activar interfaz
sudo nmcli device connect enp0s3
# nmcli: NetworkManager Command Line Interface
# device connect: activa la interfaz de red especificada
# enp0s3: nombre típico de la interfaz ethernet en VirtualBox
```

### 2. Instalar Herramientas Base

```bash
sudo yum install -y net-tools wget telnet
# install: comando para instalar paquetes
# -y: responde "yes" automáticamente a todas las preguntas
# net-tools: incluye ifconfig, netstat, route (diagnóstico de red)
# wget: descarga archivos desde internet vía HTTP/FTP
# telnet: prueba conexiones TCP a puertos específicos
```

### 3. Crear Usuario Hadoop

```bash
# Crear usuario
sudo useradd hadoop
# useradd: crea un nuevo usuario en el sistema
# crea /home/hadoop automáticamente

sudo passwd hadoop
# passwd: establece la contraseña del usuario
# pedirá ingresar la contraseña dos veces

# Dar permisos sudo
sudo usermod -aG wheel hadoop
# usermod: modifica un usuario existente
# -aG: append to Group (agregar a grupo sin quitar otros)
# wheel: grupo con privilegios de administrador (sudo)

# Crear carpeta de trabajo
sudo mkdir /opt/hadoop
# mkdir: crea directorio
# /opt: ubicación estándar para software opcional/adicional

sudo chown -R hadoop:hadoop /opt/hadoop
# chown: cambia el propietario (owner) de archivos/directorios
# -R: recursivo (aplica a todo el contenido)
# hadoop:hadoop: formato usuario:grupo

# Cambiar a usuario hadoop
su - hadoop
# su: switch user (cambiar de usuario)
# - : carga el entorno completo del usuario (variables, directorio home)
```

---

## ☕ PARTE 2: Instalación de Java 8 (5 min)

### 4. Descargar y Descomprimir Java

```bash
# Ir a /opt y descargar Java 8
cd /opt
# cd: change directory (cambiar directorio)
# /opt: directorio estándar para instalaciones manuales

# (Mostrar descarga de jdk-8u202-linux-x64.tar.gz desde Oracle)

# Descomprimir
sudo tar -zxvf jdk-8u202-linux-x64.tar.gz
# tar: herramienta de archivado
# -z: descomprimir con gzip
# -x: extraer archivos
# -v: verbose (mostrar progreso)
# -f: file (especifica el archivo)
# Resultado: crea carpeta jdk1.8.0_202/

sudo chown -R hadoop:hadoop /opt/jdk1.8.0_202
# Asigna propiedad al usuario hadoop recursivamente
```

### 5. Configurar Java

```bash
# Registrar Java en el sistema
sudo alternatives --install /usr/bin/java java /opt/jdk1.8.0_202/bin/java 2
# alternatives: sistema de gestión de versiones alternativas
# --install: registra un nuevo comando
# /usr/bin/java: enlace simbólico que se creará
# java: nombre del grupo de alternativas
# /opt/jdk1.8.0_202/bin/java: ruta real del ejecutable
# 2: prioridad (mayor número = mayor prioridad)

sudo alternatives --install /usr/bin/javac javac /opt/jdk1.8.0_202/bin/javac 2
# javac: compilador de Java (necesario para Hadoop)

# Verificar
java -version
# Debe mostrar: java version "1.8.0_202"
```

### 6. Configurar Variables de Entorno

```bash
# Editar .bashrc
nano ~/.bashrc
# nano: editor de texto simple en terminal
# ~/.bashrc: archivo de configuración del shell bash
# ~: atajo para /home/hadoop
# Se ejecuta cada vez que abres una terminal

# Agregar al final:
export JAVA_HOME=/opt/jdk1.8.0_202
# JAVA_HOME: variable que indica dónde está instalado Java
# Hadoop y otras herramientas la usan para encontrar Java

export JRE_HOME=/opt/jdk1.8.0_202/jre
# JRE_HOME: Java Runtime Environment (entorno de ejecución)

export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
# PATH: lista de directorios donde el sistema busca comandos
# $PATH: valor actual del PATH
# Se agrega :$JAVA_HOME/bin para ejecutar java desde cualquier lugar
# El : es el separador entre rutas

# Recargar
source ~/.bashrc
# source: ejecuta el archivo en la sesión actual
# Carga las nuevas variables sin reiniciar la terminal

echo $JAVA_HOME
# echo: imprime el valor de la variable
# Verifica que se configuró correctamente
```

---

## 🐘 PARTE 3: Instalación de Hadoop 3.4.0 (8 min)

### 7. Descargar y Descomprimir Hadoop

```bash
cd /opt/hadoop
# Ir al directorio de trabajo de Hadoop

wget https://dlcdn.apache.org/hadoop/common/hadoop-3.4.0/hadoop-3.4.0.tar.gz
# wget: descarga archivos desde URLs
# Descarga ~600 MB (puede tardar varios minutos)

tar -zxvf hadoop-3.4.0.tar.gz
# Descomprime y crea carpeta hadoop-3.4.0/
# Contiene bin/, sbin/, etc/hadoop/, share/, etc.
```

### 8. Configurar Variables Hadoop

```bash
nano ~/.bashrc

# Agregar:
export HADOOP_HOME=/opt/hadoop/hadoop-3.4.0
# HADOOP_HOME: ubicación de la instalación de Hadoop
# Los scripts de Hadoop usan esta variable

export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
# bin/: comandos de usuario (hdfs, hadoop, yarn, mapred)
# sbin/: scripts de administración (start-dfs.sh, start-yarn.sh)
# Ahora puedes ejecutar estos comandos desde cualquier directorio

# Recargar
source ~/.bashrc
# Aplica los cambios inmediatamente

hadoop version
# Verifica que Hadoop esté accesible
# Debe mostrar: Hadoop 3.4.0
```

### 9. Configurar Hostname

```bash
sudo nmcli general hostname nodo1
# Establece el nombre de la máquina como "nodo1"
# Hadoop usa este nombre para identificar los nodos del cluster

# Editar /etc/hosts
sudo nano /etc/hosts
# /etc/hosts: mapea nombres de host a direcciones IP
# Se consulta antes de hacer DNS lookup

# Agregar:
127.0.0.1   localhost nodo1
# 127.0.0.1: loopback (la propia máquina)
# Asocia "nodo1" con localhost

10.0.2.15   nodo1  # Usar tu IP real
# Obtén tu IP con: ifconfig o ip addr
# Permite que otros servicios se conecten a "nodo1" por IP
```

### 10. Configurar core-site.xml

```bash
vi /opt/hadoop/hadoop-3.4.0/etc/hadoop/core-site.xml
```

```xml
<configuration>
    <!-- Configuración central de Hadoop -->
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://nodo1:9000</value>
        <description>
            URI del sistema de archivos por defecto.
            - hdfs://: protocolo del sistema de archivos distribuido
            - nodo1: hostname del NameNode
            - 9000: puerto donde el NameNode escucha conexiones RPC
            Todos los comandos hdfs dfs usarán esta dirección.
        </description>
    </property>
</configuration>
```

### 11. Configurar hdfs-site.xml

```bash
vi /opt/hadoop/hadoop-3.4.0/etc/hadoop/hdfs-site.xml
```

```xml
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
        <description>
            Factor de replicación: número de copias de cada bloque.
            - 1: solo una copia (modo pseudo-distribuido, sin redundancia)
            - 3: estándar en producción para redundancia y disponibilidad
            Determina cuántas veces se replica cada bloque en diferentes DataNodes.
        </description>
    </property>
    
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>/datos/namenode</value>
        <description>
            Directorio donde el NameNode almacena los METADATOS del sistema de archivos.
            NO guarda archivos reales, solo el índice/catálogo.
            Contiene: fsimage (snapshot del sistema), edits (log de transacciones).
            CRÍTICO: hacer backup frecuente de este directorio.
        </description>
    </property>
    
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>/datos/datanode</value>
        <description>
            Directorio donde el DataNode almacena los BLOQUES de datos físicos.
            Aquí se guardan los archivos divididos en bloques (tamaño por defecto: 128 MB).
            Puede especificar múltiples directorios separados por coma para usar varios discos.
        </description>
    </property>
</configuration>
```

### 12. Crear Directorios de Datos

```bash
sudo mkdir -p /datos/namenode /datos/datanode
# mkdir -p: crea directorios padres si no existen
# /datos/namenode: almacenará metadatos de HDFS
# /datos/datanode: almacenará bloques de datos reales
# Estos directorios fueron especificados en hdfs-site.xml

sudo chown -R hadoop:hadoop /datos
# Cambia propietario a usuario hadoop
# Necesario para que Hadoop pueda leer/escribir
```

### 13. Formatear NameNode e Iniciar HDFS

```bash
# Formatear (solo primera vez)
hdfs namenode -format
# Inicializa el sistema de archivos HDFS
# Crea la estructura inicial en /datos/namenode
# ADVERTENCIA: solo ejecutar UNA VEZ (borra datos existentes)

# Abrir puertos firewall
sudo firewall-cmd --zone=public --add-port=9000/tcp --permanent
# 9000: puerto del NameNode (comunicación HDFS)

sudo firewall-cmd --zone=public --add-port=9870/tcp --permanent
# 9870: interfaz web del NameNode

sudo firewall-cmd --reload
# Aplica los cambios del firewall

# Iniciar HDFS
start-dfs.sh
# Script que inicia:
# - NameNode: gestiona metadatos y el árbol de archivos
# - DataNode: almacena y sirve bloques de datos
# - SecondaryNameNode: realiza checkpoints de metadatos

# Verificar servicios
jps
# Java Process Status: muestra procesos Java corriendo
# Debes ver: NameNode, DataNode, SecondaryNameNode
```

**Mostrar en navegador:** `http://localhost:9870`

### 14. Configurar YARN (MapReduce)

**Editar mapred-site.xml:**
```bash
vi /opt/hadoop/hadoop-3.4.0/etc/hadoop/mapred-site.xml
```

```xml
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
        <description>
            Framework de ejecución para jobs MapReduce.
            - yarn: usa YARN para gestionar recursos (recomendado)
            - local: ejecución en un solo proceso sin YARN (solo para pruebas)
            YARN permite compartir recursos con otras aplicaciones del cluster.
        </description>
    </property>
    
    <property>
        <name>mapreduce.application.classpath</name>
        <value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib/*</value>
        <description>
            Classpath de Java para aplicaciones MapReduce.
            Incluye todos los JAR files necesarios para ejecutar jobs.
            La variable $HADOOP_MAPRED_HOME se resuelve automáticamente al directorio de MapReduce.
        </description>
    </property>
</configuration>
```

**Editar yarn-site.xml:**
```bash
vi /opt/hadoop/hadoop-3.4.0/etc/hadoop/yarn-site.xml
```

```xml
<configuration>
    <property>
        <name>yarn.resourcemanager.hostname</name>
        <value>nodo1</value>
        <description>
            Hostname del ResourceManager.
            El ResourceManager gestiona y asigna recursos (CPU, RAM) del cluster,
            acepta aplicaciones y monitorea el estado de los NodeManagers.
        </description>
    </property>
    
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
        <description>
            Servicios auxiliares que ejecuta el NodeManager.
            mapreduce_shuffle: permite transferir datos intermedios entre Map y Reduce.
            SIN ESTA CONFIGURACIÓN, MapReduce NO FUNCIONARÁ.
        </description>
    </property>
    
    <property>
        <name>yarn.nodemanager.vmem-check-enabled</name>
        <value>false</value>
        <description>
            Desactiva la verificación de memoria virtual.
            En entornos de desarrollo/prueba evita que fallen jobs por límites de vmem.
            En producción configurar correctamente yarn.nodemanager.vmem-pmem-ratio en lugar de desactivar.
        </description>
    </property>
</configuration>
```

### 15. Iniciar YARN

```bash
# Abrir puerto
sudo firewall-cmd --zone=public --add-port=8088/tcp --permanent
# 8088: interfaz web de YARN ResourceManager
# Muestra jobs ejecutándose, completados, recursos usados

sudo firewall-cmd --reload

# Iniciar YARN
start-yarn.sh
# Inicia:
# - ResourceManager: acepta jobs y asigna recursos
# - NodeManager: ejecuta contenedores en el nodo
# - Cada contenedor ejecuta una tarea (Map o Reduce)

# Verificar
jps
# Ahora debes ver también: ResourceManager y NodeManager
```

**Mostrar en navegador:** `http://localhost:8088`

### 16. Demostración HDFS

```bash
# Crear archivo de prueba
echo "Hola Hadoop" > prueba.txt
# echo: imprime texto
# >: redirige la salida al archivo prueba.txt
# Crea archivo local en sistema de archivos Linux

# Crear carpeta en HDFS
hdfs dfs -mkdir /demo
# hdfs dfs: comando para interactuar con HDFS
# -mkdir: crea directorio en HDFS (no en Linux)
# /demo: ruta absoluta en HDFS

# Subir archivo
hdfs dfs -put prueba.txt /demo/
# -put: copia archivo del sistema local a HDFS
# El archivo se divide en bloques de 128MB (si fuera grande)
# Cada bloque se replica según dfs.replication (1 en nuestro caso)

# Listar
hdfs dfs -ls /demo
# -ls: lista contenido del directorio HDFS
# Muestra: permisos, replicación, tamaño, fecha, nombre

# Ver contenido
hdfs dfs -cat /demo/prueba.txt
# -cat: muestra contenido del archivo
# Lee todos los bloques y los une automáticamente
```

---

## 🐝 PARTE 4: Instalación de Hive 3.1.3 (10 min)

### 17. Descargar e Instalar Hive

```bash
cd /opt

sudo wget https://archive.apache.org/dist/hive/hive-3.1.3/apache-hive-3.1.3-bin.tar.gz
# Descarga Apache Hive (data warehouse sobre Hadoop)
# ~350 MB aproximadamente

sudo tar -zxvf apache-hive-3.1.3-bin.tar.gz
# Descomprime el archivo

sudo mv apache-hive-3.1.3-bin /opt/hive
# mv: move (renombrar/mover)
# Simplifica el nombre para facilitar acceso

sudo chown -R hadoop:hadoop /opt/hive
# Asigna propiedad al usuario hadoop
```

### 18. Configurar Variables Hive

```bash
nano ~/.bashrc

# Agregar:
export HIVE_HOME=/opt/hive
# HIVE_HOME: ubicación de Hive
# Scripts de Hive buscan configuración aquí

export PATH=$PATH:$HIVE_HOME/bin
# Agrega comandos: hive, beeline, hiveserver2, schematool
# Ahora ejecutables desde cualquier directorio

# Recargar
source ~/.bashrc
# Aplica cambios sin cerrar terminal
```

### 19. Resolver Conflicto Guava

```bash
# Eliminar Guava vieja
rm /opt/hive/lib/guava-19.0.jar
# Guava: librería de utilidades de Google
# Hive 3.1.3 trae Guava 19.0 que es incompatible con Hadoop 3.4.0
# Causa: java.lang.NoSuchMethodError al iniciar Hive

# Copiar versión compatible
cp /opt/hadoop/hadoop-3.4.0/share/hadoop/common/lib/guava-*.jar /opt/hive/lib/
# cp: copy
# *: wildcard (coincide con cualquier nombre)
# Copia la versión de Guava que usa Hadoop (~27.0 o superior)
# Resuelve el conflicto de versiones
```

### 20. Configurar Directorios HDFS

```bash
hdfs dfs -mkdir -p /tmp
# -p: crea directorios padres si no existen
# /tmp: Hive usa este directorio para archivos temporales

hdfs dfs -mkdir -p /user/hive/warehouse
# /user/hive/warehouse: ubicación por defecto de tablas Hive
# Cada tabla tendrá su subdirectorio aquí
# Ejemplo: /user/hive/warehouse/demo/

hdfs dfs -chmod g+w /tmp
# chmod: cambia permisos
# g+w: group + write (grupo puede escribir)
# Necesario para que Hive pueda crear archivos temporales

hdfs dfs -chmod g+w /user/hive/warehouse
# Permite a Hive crear y gestionar tablas
```

### 21. Configurar hive-site.xml

```bash
nano /opt/hive/conf/hive-site.xml
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:derby:;databaseName=metastore_db;create=true</value>
        <description>
            URL de conexión JDBC para la base de datos del Metastore.
            El Metastore almacena esquemas de tablas, columnas, particiones y estadísticas.
            Derby: base de datos embebida (simple, solo para desarrollo, un usuario a la vez).
            Producción: usar MySQL, PostgreSQL u Oracle para acceso concurrente.
            create=true: crea la base de datos automáticamente si no existe.
        </description>
    </property>
    
    <property>
        <name>hive.metastore.warehouse.dir</name>
        <value>/user/hive/warehouse</value>
        <description>
            Ubicación en HDFS donde se almacenan los datos de las tablas de Hive.
            Cada CREATE TABLE crea un subdirectorio aquí.
            Cada INSERT INTO guarda archivos de datos en estos subdirectorios.
            Asegurar permisos adecuados: hdfs dfs -chmod 1777 /user/hive/warehouse
        </description>
    </property>
</configuration>
```

### 22. Inicializar Metastore

```bash
schematool -initSchema -dbType derby
# schematool: herramienta de gestión del metastore
# -initSchema: inicializa el esquema de la base de datos
# Crea tablas: TBLS, COLUMNS_V2, PARTITIONS, etc.
# -dbType derby: tipo de base de datos (Derby embebida)
# Ejecutar SOLO UNA VEZ (falla si ya está inicializado)
# Crea directorio metastore_db/ en el directorio actual
```

### 23. Iniciar Hive y Demostración

```bash
# Iniciar Hive
hive
```

**En Hive CLI:**
```sql
-- Ver bases de datos
SHOW DATABASES;
-- Lista todas las bases de datos disponibles
-- Por defecto existe: "default"

-- Crear tabla
CREATE TABLE demo (
    id INT,
    nombre STRING
);
-- Crea tabla en HDFS: /user/hive/warehouse/demo/
-- INT: tipo entero (4 bytes)
-- STRING: texto de longitud variable
-- Hive soporta: TINYINT, BIGINT, FLOAT, DOUBLE, DATE, ARRAY, MAP, etc.

-- Insertar datos
INSERT INTO demo VALUES (1, 'Hadoop');
-- Cada INSERT ejecuta un JOB MapReduce (lento)
-- En producción se usa LOAD DATA para insertar masivamente

INSERT INTO demo VALUES (2, 'Spark');
-- Crea archivo en HDFS dentro de /user/hive/warehouse/demo/
-- Formato: archivo de texto con valores separados por delimitador

-- Consultar
SELECT * FROM demo;
-- SQL estándar
-- Hive traduce a MapReduce (versiones antiguas) o Tez (más rápido)
-- Lee los archivos de /user/hive/warehouse/demo/ en HDFS

-- Salir
quit;
-- Cierra la sesión de Hive CLI
```

---

## 📄 EXPLICACIÓN DETALLADA: Archivos de Configuración

### 🔧 Archivos XML de Hadoop

Los archivos XML de Hadoop siguen la estructura estándar de configuración de Apache:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <property>
        <name>nombre.de.propiedad</name>
        <value>valor</value>
        <description>Descripción opcional</description>
    </property>
</configuration>
```

---

### 📘 core-site.xml (Configuración Central)

**Ubicación:** `$HADOOP_HOME/etc/hadoop/core-site.xml`

**Propósito:** Configuración global que aplica a todos los componentes de Hadoop.

#### Propiedades Esenciales:

```xml
<configuration>
    <!-- 1. Sistema de archivos por defecto -->
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://nodo1:9000</value>
        <description>
            URI del sistema de archivos por defecto.
            - hdfs://: Protocolo HDFS (Hadoop Distributed File System)
            - nodo1: Hostname del NameNode
            - 9000: Puerto RPC (Remote Procedure Call)
            
            Alternativas:
            - file:///: Sistema de archivos local (modo standalone)
            - hdfs://nameservice1: Cuando usas HDFS HA (High Availability)
        </description>
    </property>

    <!-- 2. Directorio temporal -->
    <property>
        <name>hadoop.tmp.dir</name>
        <value>/tmp/hadoop-${user.name}</value>
        <description>
            Directorio base para archivos temporales.
            Por defecto: /tmp/hadoop-${user.name}
            ${user.name}: se reemplaza con el usuario actual
            
            Importante: Cambiar en producción a un directorio persistente
            Ejemplo: /datos/hadoop/tmp
        </description>
    </property>

    <!-- 3. Configuración de I/O -->
    <property>
        <name>io.file.buffer.size</name>
        <value>131072</value>
        <description>
            Tamaño del buffer para operaciones de lectura/escritura.
            Valor: 131072 bytes = 128 KB
            Por defecto: 4096 bytes (4 KB)
            
            Mayor buffer = mejor rendimiento para archivos grandes
            Aumentar a 128KB o 256KB para mejorar throughput
        </description>
    </property>

    <!-- 4. Factor de compresión -->
    <property>
        <name>io.compression.codecs</name>
        <value>org.apache.hadoop.io.compress.GzipCodec,
               org.apache.hadoop.io.compress.DefaultCodec,
               org.apache.hadoop.io.compress.BZip2Codec,
               org.apache.hadoop.io.compress.SnappyCodec,
               org.apache.hadoop.io.compress.Lz4Codec</value>
        <description>
            Códecs de compresión disponibles.
            - GzipCodec: buena compresión, lento
            - SnappyCodec: compresión rápida, menos eficiente
            - Lz4Codec: muy rápido, compresión moderada
            - BZip2Codec: mejor compresión, muy lento, splittable
        </description>
    </property>

    <!-- 5. Usuario proxy (para HiveServer2, Oozie, etc.) -->
    <property>
        <name>hadoop.proxyuser.hadoop.hosts</name>
        <value>*</value>
        <description>
            Hosts desde donde el usuario hadoop puede hacer proxy.
            *: todos los hosts (solo desarrollo)
            Producción: listar IPs específicas separadas por coma
        </description>
    </property>

    <property>
        <name>hadoop.proxyuser.hadoop.groups</name>
        <value>*</value>
        <description>
            Grupos que pueden ser suplantados por el usuario hadoop.
            *: todos los grupos (solo desarrollo)
        </description>
    </property>
</configuration>
```

---

### 📗 hdfs-site.xml (Configuración de HDFS)

**Ubicación:** `$HADOOP_HOME/etc/hadoop/hdfs-site.xml`

**Propósito:** Configuración específica del sistema de archivos distribuido.

```xml
<configuration>
    <!-- 1. Factor de replicación -->
    <property>
        <name>dfs.replication</name>
        <value>1</value>
        <description>
            Número de réplicas de cada bloque.
            
            Valores típicos:
            - 1: Desarrollo/pseudo-distribuido (sin redundancia)
            - 3: Producción (estándar Apache)
            - 5+: Datos críticos
            
            Cómo funciona:
            - Archivo de 1 GB con bloques de 128 MB = 8 bloques
            - Con replicación 3: 8 × 3 = 24 bloques totales
            - Se distribuyen en diferentes DataNodes
        </description>
    </property>

    <!-- 2. Tamaño de bloque -->
    <property>
        <name>dfs.blocksize</name>
        <value>134217728</value>
        <description>
            Tamaño de cada bloque en bytes.
            Valor: 134217728 bytes = 128 MB (por defecto)
            
            Versiones anteriores: 64 MB
            Hadoop 3.x: 128 MB
            Hadoop con archivos muy grandes: 256 MB o 512 MB
            
            Ejemplo:
            - Archivo de 500 MB con bloques de 128 MB:
              4 bloques completos (512 MB) + 1 bloque de 116 MB
            
            Bloques grandes:
            ✅ Menos metadatos en NameNode
            ✅ Menos overhead de red
            ❌ Menos paralelismo en MapReduce
        </description>
    </property>

    <!-- 3. Directorio del NameNode -->
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>file:///datos/namenode</value>
        <description>
            Directorio(s) donde el NameNode guarda metadatos.
            
            Contiene:
            - fsimage: snapshot del sistema de archivos
            - edits: log de transacciones
            - VERSION: versión del formato
            
            Múltiples directorios (RAID):
            file:///datos1/namenode,file:///datos2/namenode
            Escribe en todos simultáneamente (espejo)
            
            ⚠️ CRÍTICO: Si se pierde, pierdes todo HDFS
            Hacer backup frecuente del fsimage
        </description>
    </property>

    <!-- 4. Directorio del DataNode -->
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file:///datos/datanode</value>
        <description>
            Directorio(s) donde el DataNode guarda bloques.
            
            Múltiples discos:
            file:///disk1/datanode,file:///disk2/datanode,file:///disk3/datanode
            Round-robin entre discos (no RAID, más throughput)
            
            Cada bloque se guarda como archivo:
            - blk_1073741825: datos del bloque
            - blk_1073741825_1001.meta: checksum del bloque
        </description>
    </property>

    <!-- 5. NameNode HTTP Address -->
    <property>
        <name>dfs.namenode.http-address</name>
        <value>0.0.0.0:9870</value>
        <description>
            Dirección de la interfaz web del NameNode.
            0.0.0.0: escucha en todas las interfaces
            9870: puerto (en Hadoop 2.x era 50070)
            
            UI muestra:
            - Estado del cluster (live/dead nodes)
            - Capacidad usada/disponible
            - Browse directory (explorador de archivos)
            - Logs y métricas
        </description>
    </property>

    <!-- 6. DataNode HTTP Address -->
    <property>
        <name>dfs.datanode.http.address</name>
        <value>0.0.0.0:9864</value>
        <description>
            Puerto HTTP del DataNode.
            Por defecto: 9864 (era 50075 en Hadoop 2.x)
        </description>
    </property>

    <!-- 7. Secondary NameNode -->
    <property>
        <name>dfs.namenode.secondary.http-address</name>
        <value>nodo1:9868</value>
        <description>
            Dirección del Secondary NameNode.
            
            Función:
            - NO es un backup del NameNode
            - Hace checkpoints periódicos
            - Combina fsimage + edits → nuevo fsimage
            - Reduce tiempo de arranque del NameNode
            
            Puerto: 9868 (era 50090 en Hadoop 2.x)
        </description>
    </property>

    <!-- 8. Permisos -->
    <property>
        <name>dfs.permissions.enabled</name>
        <value>true</value>
        <description>
            Habilita permisos estilo Unix en HDFS.
            true: usa permisos (producción)
            false: todos tienen acceso total (solo desarrollo)
        </description>
    </property>

    <!-- 9. Modo seguro -->
    <property>
        <name>dfs.namenode.safemode.threshold-pct</name>
        <value>0.999f</value>
        <description>
            Porcentaje de bloques que deben reportarse antes de salir de safe mode.
            0.999 = 99.9% de bloques
            
            Safe Mode:
            - Al arrancar, NameNode entra en modo de solo lectura
            - Espera a que DataNodes reporten sus bloques
            - Sale cuando se alcanza el threshold
            
            En desarrollo puedes bajar a 0.95 (95%)
        </description>
    </property>

    <!-- 10. Balanceo de carga -->
    <property>
        <name>dfs.datanode.balance.bandwidthPerSec</name>
        <value>10485760</value>
        <description>
            Ancho de banda para balanceo (bytes/seg).
            Valor: 10485760 = 10 MB/s
            
            Usado por: hdfs balancer
            Redistribuye bloques entre DataNodes
            para equilibrar uso de disco
        </description>
    </property>
</configuration>
```

---

### 📙 mapred-site.xml (Configuración de MapReduce)

**Ubicación:** `$HADOOP_HOME/etc/hadoop/mapred-site.xml`

**Propósito:** Configuración del framework MapReduce.

```xml
<configuration>
    <!-- 1. Framework de ejecución -->
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
        <description>
            Framework para ejecutar jobs MapReduce.
            
            Opciones:
            - yarn: usa YARN (ResourceManager/NodeManager)
            - local: ejecución en un solo proceso (debugging)
            - classic: MapReduce v1 (obsoleto)
            
            YARN permite:
            - Compartir recursos con otras aplicaciones
            - Mejor utilización del cluster
            - Soporte para frameworks no-MapReduce (Spark, Tez)
        </description>
    </property>

    <!-- 2. Classpath de MapReduce -->
    <property>
        <name>mapreduce.application.classpath</name>
        <value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib/*</value>
        <description>
            Classpath de Java para aplicaciones MapReduce.
            Incluye JARs necesarios para ejecutar jobs.
            
            $HADOOP_MAPRED_HOME: se resuelve automáticamente
            *: incluye todos los .jar en el directorio
        </description>
    </property>

    <!-- 3. Memoria para Map tasks -->
    <property>
        <name>mapreduce.map.memory.mb</name>
        <value>1024</value>
        <description>
            Memoria RAM para cada Map task (MB).
            Por defecto: 1024 MB (1 GB)
            
            Ajustar según:
            - Tamaño de datos procesados
            - Complejidad del mapper
            - Memoria disponible en NodeManagers
            
            Ejemplo: procesar archivos XML grandes → 2048 MB
        </description>
    </property>

    <!-- 4. Memoria para Reduce tasks -->
    <property>
        <name>mapreduce.reduce.memory.mb</name>
        <value>1024</value>
        <description>
            Memoria RAM para cada Reduce task (MB).
            
            Reducers suelen necesitar más memoria:
            - Deben almacenar datos intermedios
            - Ordenación y agregación
            
            Valores típicos: 2048-4096 MB
        </description>
    </property>

    <!-- 5. Heap de JVM para Map -->
    <property>
        <name>mapreduce.map.java.opts</name>
        <value>-Xmx819m</value>
        <description>
            Opciones de JVM para Map tasks.
            -Xmx819m: máximo heap de 819 MB
            
            Regla general: 80% de mapreduce.map.memory.mb
            Ejemplo: 1024 MB × 0.8 = 819 MB
            
            Otras opciones útiles:
            -Xmx819m -XX:+UseG1GC (usa garbage collector G1)
        </description>
    </property>

    <!-- 6. Heap de JVM para Reduce -->
    <property>
        <name>mapreduce.reduce.java.opts</name>
        <value>-Xmx819m</value>
        <description>
            Opciones de JVM para Reduce tasks.
            Similar a map, pero ajustar según necesidad.
        </description>
    </property>

    <!-- 7. Paralelismo de Map -->
    <property>
        <name>mapreduce.job.maps</name>
        <value>2</value>
        <description>
            Número sugerido de Map tasks.
            
            Hadoop calcula automáticamente basado en:
            - Tamaño de entrada
            - Tamaño de split (dfs.blocksize)
            
            No es un límite estricto, solo una sugerencia.
        </description>
    </property>

    <!-- 8. Paralelismo de Reduce -->
    <property>
        <name>mapreduce.job.reduces</name>
        <value>1</value>
        <description>
            Número de Reduce tasks.
            
            Valores:
            - 0: solo Map (sin agregación)
            - 1: un solo reduce (salida ordenada globalmente)
            - N: múltiples reduces (salida particionada)
            
            Regla general:
            0.95 × (nodos × mapreduce.tasktracker.reduce.tasks.maximum)
        </description>
    </property>

    <!-- 9. Compresión de salida Map -->
    <property>
        <name>mapreduce.map.output.compress</name>
        <value>true</value>
        <description>
            Comprimir salida intermedia de Map.
            
            Ventajas:
            ✅ Reduce I/O de disco
            ✅ Reduce tráfico de red (shuffle)
            ✅ Jobs más rápidos
            
            Desventaja:
            ❌ Consume CPU para comprimir/descomprimir
        </description>
    </property>

    <property>
        <name>mapreduce.map.output.compress.codec</name>
        <value>org.apache.hadoop.io.compress.SnappyCodec</value>
        <description>
            Códec para comprimir salida Map.
            
            Opciones:
            - SnappyCodec: rápido, recomendado
            - Lz4Codec: muy rápido
            - GzipCodec: mejor compresión, más lento
        </description>
    </property>

    <!-- 10. Historial de Jobs -->
    <property>
        <name>mapreduce.jobhistory.address</name>
        <value>nodo1:10020</value>
        <description>
            Dirección del Job History Server.
            Almacena información de jobs completados.
        </description>
    </property>

    <property>
        <name>mapreduce.jobhistory.webapp.address</name>
        <value>nodo1:19888</value>
        <description>
            Interfaz web del Job History Server.
            Acceder: http://nodo1:19888
            
            Muestra:
            - Jobs ejecutados
            - Duración de cada fase
            - Contadores
            - Logs de errores
        </description>
    </property>
</configuration>
```

---

### 📕 yarn-site.xml (Configuración de YARN)

**Ubicación:** `$HADOOP_HOME/etc/hadoop/yarn-site.xml`

**Propósito:** Configuración del gestor de recursos YARN.

```xml
<configuration>
    <!-- 1. Hostname del ResourceManager -->
    <property>
        <name>yarn.resourcemanager.hostname</name>
        <value>nodo1</value>
        <description>
            Host donde corre el ResourceManager.
            
            ResourceManager:
            - Acepta aplicaciones (jobs)
            - Asigna recursos (contenedores)
            - Monitorea NodeManagers
            - Reinicia ApplicationMasters fallidos
        </description>
    </property>

    <!-- 2. Servicios auxiliares -->
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
        <description>
            Servicios adicionales del NodeManager.
            
            mapreduce_shuffle:
            - Permite transferir datos entre Map y Reduce
            - Fase de shuffle: ordena y agrupa claves
            - SIN ESTO, MapReduce NO FUNCIONA
        </description>
    </property>

    <property>
        <name>yarn.nodemanager.aux-services.mapreduce_shuffle.class</name>
        <value>org.apache.hadoop.mapred.ShuffleHandler</value>
        <description>
            Clase Java que implementa el shuffle.
            ShuffleHandler: servidor HTTP que sirve datos intermedios.
        </description>
    </property>

    <!-- 3. Memoria del NodeManager -->
    <property>
        <name>yarn.nodemanager.resource.memory-mb</name>
        <value>8192</value>
        <description>
            Memoria total disponible para contenedores (MB).
            
            Cálculo:
            RAM total - SO - Hadoop daemons - cache
            
            Ejemplo:
            - Servidor con 16 GB RAM
            - SO: 2 GB
            - Daemons: 2 GB
            - Cache/buffer: 2 GB
            - Disponible para YARN: 10 GB = 10240 MB
            
            Por defecto: 8192 MB
            Producción: ajustar según hardware
        </description>
    </property>

    <!-- 4. CPU del NodeManager -->
    <property>
        <name>yarn.nodemanager.resource.cpu-vcores</name>
        <value>8</value>
        <description>
            Número de cores virtuales disponibles.
            
            Generalmente: número de cores físicos
            Con hyperthreading: cores × 2
            
            Ejemplo:
            - CPU con 4 cores físicos, 8 lógicos
            - Configurar: 8 vcores
        </description>
    </property>

    <!-- 5. Memoria mínima por contenedor -->
    <property>
        <name>yarn.scheduler.minimum-allocation-mb</name>
        <value>1024</value>
        <description>
            Memoria mínima que se puede solicitar (MB).
            Por defecto: 1024 MB
            
            Si una app solicita menos, se redondea a este valor.
            Ejemplo: app pide 512 MB → recibe 1024 MB
        </description>
    </property>

    <!-- 6. Memoria máxima por contenedor -->
    <property>
        <name>yarn.scheduler.maximum-allocation-mb</name>
        <value>8192</value>
        <description>
            Memoria máxima que se puede solicitar (MB).
            
            Debe ser ≤ yarn.nodemanager.resource.memory-mb
            
            Si una app solicita más, se rechaza.
        </description>
    </property>

    <!-- 7. CPU mínima por contenedor -->
    <property>
        <name>yarn.scheduler.minimum-allocation-vcores</name>
        <value>1</value>
        <description>
            CPU mínima (vcores).
            Por defecto: 1 vcore
        </description>
    </property>

    <!-- 8. CPU máxima por contenedor -->
    <property>
        <name>yarn.scheduler.maximum-allocation-vcores</name>
        <value>4</value>
        <description>
            CPU máxima (vcores).
            Por defecto: 4 vcores
        </description>
    </property>

    <!-- 9. Verificación de memoria virtual -->
    <property>
        <name>yarn.nodemanager.vmem-check-enabled</name>
        <value>false</value>
        <description>
            Habilita check de memoria virtual.
            
            false: solo verifica memoria física (RAM)
            true: verifica RAM + swap
            
            Problema común:
            - Linux puede asignar vmem >> RAM física
            - Causa: "Container killed on request. Exit code is 143"
            
            Desarrollo: false
            Producción: true + ajustar yarn.nodemanager.vmem-pmem-ratio
        </description>
    </property>

    <property>
        <name>yarn.nodemanager.vmem-pmem-ratio</name>
        <value>2.1</value>
        <description>
            Ratio memoria virtual / física.
            2.1: puede usar hasta 2.1× RAM física
            
            Ejemplo:
            - Contenedor con 1 GB RAM
            - vmem permitida: 2.1 GB
        </description>
    </property>

    <!-- 10. Scheduler -->
    <property>
        <name>yarn.resourcemanager.scheduler.class</name>
        <value>org.apache.hadoop.yarn.server.resourcemanager.scheduler.capacity.CapacityScheduler</value>
        <description>
            Algoritmo de scheduling.
            
            Opciones:
            - CapacityScheduler: múltiples colas con garantías (por defecto)
            - FairScheduler: reparto equitativo entre usuarios
            - FifoScheduler: primero en llegar, primero en ejecutar (simple)
        </description>
    </property>

    <!-- 11. Interfaz web -->
    <property>
        <name>yarn.resourcemanager.webapp.address</name>
        <value>0.0.0.0:8088</value>
        <description>
            Dirección de la UI web de YARN.
            
            Muestra:
            - Applications (corriendo/completadas)
            - Cluster Metrics (memoria/CPU usado)
            - Nodes (NodeManagers live/dead)
            - Scheduler (colas y recursos)
        </description>
    </property>

    <!-- 12. Logs de aplicaciones -->
    <property>
        <name>yarn.log-aggregation-enable</name>
        <value>true</value>
        <description>
            Agregar logs de contenedores en HDFS.
            
            true:
            - Logs se copian a HDFS cuando app termina
            - Útil para debugging después de ejecución
            - Ruta: /tmp/logs/usuario/logs/application_id
            
            false:
            - Logs se quedan en NodeManagers locales
            - Se pierden si se reinicia NodeManager
        </description>
    </property>

    <property>
        <name>yarn.nodemanager.remote-app-log-dir</name>
        <value>/tmp/logs</value>
        <description>
            Directorio en HDFS para logs agregados.
        </description>
    </property>
</configuration>
```

---

### 📄 hive-site.xml (Configuración de Hive)

**Ubicación:** `$HIVE_HOME/conf/hive-site.xml`

**Propósito:** Configuración del data warehouse Hive.

```xml
<configuration>
    <!-- 1. Metastore Database -->
    <property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:derby:;databaseName=metastore_db;create=true</value>
        <description>
            JDBC URL de la base de datos del metastore.
            
            Metastore almacena:
            - Esquemas de tablas (columnas, tipos)
            - Particiones
            - Estadísticas
            - Ubicaciones en HDFS
            
            Opciones:
            
            Derby (desarrollo):
            jdbc:derby:;databaseName=metastore_db;create=true
            ⚠️ Solo un usuario a la vez
            
            MySQL (producción):
            jdbc:mysql://localhost:3306/metastore?createDatabaseIfNotExist=true
            
            PostgreSQL:
            jdbc:postgresql://localhost:5432/metastore
        </description>
    </property>

    <property>
        <name>javax.jdo.option.ConnectionDriverName</name>
        <value>org.apache.derby.jdbc.EmbeddedDriver</value>
        <description>
            Driver JDBC.
            
            Derby: org.apache.derby.jdbc.EmbeddedDriver
            MySQL: com.mysql.jdbc.Driver
            PostgreSQL: org.postgresql.Driver
            
            Nota: copiar JAR del driver a $HIVE_HOME/lib/
        </description>
    </property>

    <property>
        <name>javax.jdo.option.ConnectionUserName</name>
        <value>APP</value>
        <description>
            Usuario de la BD (para MySQL/PostgreSQL).
            Derby usa "APP" por defecto.
        </description>
    </property>

    <property>
        <name>javax.jdo.option.ConnectionPassword</name>
        <value>mine</value>
        <description>
            Contraseña de la BD.
            Derby: "mine" por defecto.
            Producción: usar contraseña segura.
        </description>
    </property>

    <!-- 2. Warehouse Directory -->
    <property>
        <name>hive.metastore.warehouse.dir</name>
        <value>/user/hive/warehouse</value>
        <description>
            Ubicación en HDFS de las tablas.
            
            Estructura:
            /user/hive/warehouse/
            ├── tabla1/
            │   ├── part-00000
            │   └── part-00001
            ├── tabla2/
            └── db1.db/
                └── tabla3/
            
            Cada tabla = subdirectorio
            Cada INSERT = nuevo archivo
            
            Permisos necesarios:
            hdfs dfs -chmod 1777 /user/hive/warehouse
        </description>
    </property>

    <!-- 3. Metastore URI -->
    <property>
        <name>hive.metastore.uris</name>
        <value></value>
        <description>
            URI del Metastore service.
            
            Vacío: modo embebido (un solo usuario)
            thrift://nodo1:9083: modo remoto (multiusuario)
            
            Modo remoto requiere:
            hive --service metastore
            
            Ventajas modo remoto:
            - Múltiples usuarios simultáneos
            - Metastore compartido (Spark, Presto)
        </description>
    </property>

    <!-- 4. HiveServer2 -->
    <property>
        <name>hive.server2.thrift.port</name>
        <value>10000</value>
        <description>
            Puerto de HiveServer2 (JDBC/ODBC).
            
            Permite conexiones desde:
            - Beeline (CLI de HiveServer2)
            - Herramientas BI (Tableau, Power BI)
            - Aplicaciones Java/Python
            
            Iniciar: hive --service hiveserver2
        </description>
    </property>

    <property>
        <name>hive.server2.thrift.bind.host</name>
        <value>0.0.0.0</value>
        <description>
            Interfaz donde escucha HiveServer2.
            0.0.0.0: todas las interfaces
            localhost: solo local
        </description>
    </property>

    <!-- 5. Autenticación -->
    <property>
        <name>hive.server2.authentication</name>
        <value>NONE</value>
        <description>
            Método de autenticación.
            
            Opciones:
            - NONE: sin autenticación (desarrollo)
            - KERBEROS: autenticación segura (producción)
            - LDAP: contra directorio LDAP
            - CUSTOM: implementación custom
        </description>
    </property>

    <!-- 6. Ejecución como usuario -->
    <property>
        <name>hive.server2.enable.doAs</name>
        <value>false</value>
        <description>
            Ejecutar queries como el usuario que los envía.
            
            true: usa credenciales del cliente
            false: ejecuta como usuario hive
            
            Producción con Kerberos: true
            Desarrollo: false (más simple)
        </description>
    </property>

    <!-- 7. Motor de ejecución -->
    <property>
        <name>hive.execution.engine</name>
        <value>mr</value>
        <description>
            Motor para ejecutar queries.
            
            Opciones:
            - mr: MapReduce (por defecto, lento)
            - tez: Apache Tez (más rápido, recomendado)
            - spark: Spark (requiere instalación)
            
            Tez es 3-10× más rápido que MR:
            - Menos I/O de disco
            - Pipeline de operaciones
            - Mejor uso de memoria
        </description>
    </property>

    <!-- 8. Optimizaciones -->
    <property>
        <name>hive.auto.convert.join</name>
        <value>true</value>
        <description>
            Convertir joins a map-joins automáticamente.
            
            Map-join:
            - Tabla pequeña se carga en memoria
            - Join sin fase de reduce
            - Mucho más rápido
            
            Funciona si una tabla es pequeña (< hive.mapjoin.smalltable.filesize)
        </description>
    </property>

    <property>
        <name>hive.mapjoin.smalltable.filesize</name>
        <value>25000000</value>
        <description>
            Tamaño máximo para map-join (bytes).
            Valor: 25000000 = 25 MB
            
            Tablas menores se cargan en memoria para joins.
        </description>
    </property>

    <property>
        <name>hive.vectorized.execution.enabled</name>
        <value>true</value>
        <description>
            Procesamiento vectorizado (batch de filas).
            
            true: procesa 1024 filas a la vez
            false: procesa fila por fila
            
            Vectorización es 3-5× más rápida:
            - Mejor uso de CPU cache
            - Menos overhead por fila
            
            Requiere formato ORC o Parquet
        </description>
    </property>

    <property>
        <name>hive.cbo.enable</name>
        <value>true</value>
        <description>
            Cost-Based Optimizer.
            
            true: usa estadísticas para elegir plan de ejecución
            false: usa reglas heurísticas
            
            Requiere:
            ANALYZE TABLE tabla COMPUTE STATISTICS;
        </description>
    </property>

    <!-- 9. Compresión -->
    <property>
        <name>hive.exec.compress.output</name>
        <value>true</value>
        <description>
            Comprimir salida final.
            Reduce espacio en HDFS.
        </description>
    </property>

    <property>
        <name>hive.exec.compress.intermediate</name>
        <value>true</value>
        <description>
            Comprimir datos intermedios entre etapas.
            Mejora rendimiento en queries complejos.
        </description>
    </property>

    <!-- 10. Interfaz Web -->
    <property>
        <name>hive.server2.webui.port</name>
        <value>10002</value>
        <description>
            Puerto de la UI web de HiveServer2.
            
            Acceder: http://nodo1:10002
            
            Muestra:
            - Queries activas
            - Historial de queries
            - Sesiones abiertas
            - Métricas de rendimiento
        </description>
    </property>
</configuration>
```

---

### 🔧 Archivos .env (Variables de Entorno)

Los archivos `.env` o `*-env.sh` configuran variables de entorno para cada componente.

---

### 📘 hadoop-env.sh

**Ubicación:** `$HADOOP_HOME/etc/hadoop/hadoop-env.sh`

**Propósito:** Variables de entorno generales de Hadoop.

```bash
#!/usr/bin/env bash

# 1. Ubicación de Java
export JAVA_HOME=/opt/jdk1.8.0_202
# CRÍTICO: Hadoop necesita saber dónde está Java
# Usar ruta absoluta, no relativa
# Verificar: $JAVA_HOME/bin/java -version

# 2. Ubicación de Hadoop
export HADOOP_HOME=/opt/hadoop/hadoop-3.4.0
# Opcional: algunos scripts lo usan para encontrar binarios

# 3. Directorio de configuración
export HADOOP_CONF_DIR=${HADOOP_HOME}/etc/hadoop
# Por defecto: $HADOOP_HOME/etc/hadoop
# Cambiar solo si configs están en otro lugar

# 4. Directorio de logs
export HADOOP_LOG_DIR=${HADOOP_HOME}/logs
# Por defecto: $HADOOP_HOME/logs
# Producción: usar /var/log/hadoop

# 5. Directorio PID files
export HADOOP_PID_DIR=/tmp
# Archivos .pid identifican procesos corriendo
# Producción: /var/run/hadoop

# 6. Opciones de JVM para todos los daemons
export HADOOP_OPTS="-Djava.net.preferIPv4Stack=true"
# preferIPv4Stack: evita problemas con IPv6
# Otras opciones útiles:
# -Djava.security.krb5.conf=/etc/krb5.conf (Kerberos)

# 7. NameNode JVM options
export HADOOP_NAMENODE_OPTS="-Xmx2048m -Xms2048m -XX:+UseG1GC"
# -Xmx2048m: máximo heap 2 GB
# -Xms2048m: heap inicial 2 GB (evita resize)
# -XX:+UseG1GC: garbage collector G1 (mejor para heaps grandes)
#
# Reglas de memoria NameNode:
# - 1 GB por cada millón de bloques
# - Cluster pequeño (< 10 millones bloques): 2-4 GB
# - Cluster grande: 8-32 GB

# 8. DataNode JVM options
export HADOOP_DATANODE_OPTS="-Xmx1024m -Xms1024m"
# DataNode usa menos memoria (1 GB suficiente)
# Aumentar si muchos bloques o peticiones concurrentes

# 9. SecondaryNameNode JVM options
export HADOOP_SECONDARYNAMENODE_OPTS="-Xmx2048m -Xms2048m"
# Similar al NameNode (carga fsimage en memoria)

# 10. Usuario Unix para daemons (cuando root inicia)
export HDFS_NAMENODE_USER=hadoop
export HDFS_DATANODE_USER=hadoop
export HDFS_SECONDARYNAMENODE_USER=hadoop
# Solo necesario si start-dfs.sh se ejecuta como root
# Especifica con qué usuario correr cada daemon

# 11. SSH options para inicio remoto
export HADOOP_SSH_OPTS="-o StrictHostKeyChecking=no"
# Desactiva verificación de host key
# Útil en desarrollo, inseguro en producción

# 12. Niceness (prioridad del proceso)
export HADOOP_NICENESS=0
# Valores: -20 (alta prioridad) a 19 (baja prioridad)
# 0: prioridad normal
# Producción: -5 a -10 para NameNode
```

---

### 📙 yarn-env.sh

**Ubicación:** `$HADOOP_HOME/etc/hadoop/yarn-env.sh`

**Propósito:** Variables de entorno para YARN.

```bash
#!/usr/bin/env bash

# 1. Java para YARN
export JAVA_HOME=/opt/jdk1.8.0_202
# Puede ser diferente del JAVA_HOME de HDFS

# 2. YARN Home
export HADOOP_YARN_HOME=$HADOOP_HOME
# Ubicación de instalación de YARN

# 3. Logs de YARN
export YARN_LOG_DIR=${HADOOP_YARN_HOME}/logs
# Producción: /var/log/yarn

# 4. PID directory
export YARN_PID_DIR=/tmp
# Producción: /var/run/yarn

# 5. ResourceManager JVM options
export YARN_RESOURCEMANAGER_OPTS="-Xmx2048m -Xms2048m -XX:+UseG1GC"
# ResourceManager necesita memoria para:
# - Tracking de aplicaciones
# - Scheduler state
# - Métricas
#
# Tamaño recomendado:
# - Cluster pequeño (< 100 nodos): 2-4 GB
# - Cluster mediano (100-500 nodos): 4-8 GB
# - Cluster grande (> 500 nodos): 8-16 GB

# 6. NodeManager JVM options
export YARN_NODEMANAGER_OPTS="-Xmx1024m -Xms1024m"
# NodeManager es ligero: 1 GB suficiente
# Memoria real la usan los contenedores (configurada en yarn-site.xml)

# 7. Usuario para daemons
export YARN_RESOURCEMANAGER_USER=hadoop
export YARN_NODEMANAGER_USER=hadoop

# 8. Classpath de YARN
export YARN_CLASSPATH=$HADOOP_CONF_DIR
# Incluye configuraciones en el classpath

# 9. Opciones adicionales
export YARN_OPTS="-Djava.net.preferIPv4Stack=true"
# Aplica a todos los componentes de YARN
```

---

### 📕 mapred-env.sh

**Ubicación:** `$HADOOP_HOME/etc/hadoop/mapred-env.sh`

**Propósito:** Variables para MapReduce Job History Server.

```bash
#!/usr/bin/env bash

# 1. Java
export JAVA_HOME=/opt/jdk1.8.0_202

# 2. MapReduce Home
export HADOOP_MAPRED_HOME=$HADOOP_HOME

# 3. Logs
export HADOOP_MAPRED_LOG_DIR=${HADOOP_MAPRED_HOME}/logs
# Job History Server logs

# 4. PID directory
export HADOOP_MAPRED_PID_DIR=/tmp

# 5. Job History Server JVM options
export HADOOP_JOB_HISTORYSERVER_OPTS="-Xmx1024m -Xms1024m"
# 1 GB suficiente para historial de jobs
# Aumentar si guardas historial de muchos jobs

# 6. Usuario
export HADOOP_JOB_HISTORYSERVER_USER=hadoop
```

---

### 📄 hive-env.sh

**Ubicación:** `$HIVE_HOME/conf/hive-env.sh`

**Propósito:** Variables de entorno de Hive.

```bash
#!/usr/bin/env bash

# 1. Ubicación de Hadoop
export HADOOP_HOME=/opt/hadoop/hadoop-3.4.0
# Hive necesita encontrar librerías de Hadoop

# 2. Classpath de Hadoop
export HADOOP_CLASSPATH=${HADOOP_HOME}/share/hadoop/common/lib/*:${HADOOP_HOME}/share/hadoop/common/*
# JARs de Hadoop disponibles para Hive

# 3. Configuración de Hive
export HIVE_CONF_DIR=${HIVE_HOME}/conf
# Dónde buscar hive-site.xml

# 4. JVM options para Hive CLI
export HADOOP_HEAPSIZE=1024
# Heap size en MB para hive CLI
# 1024 MB = 1 GB (suficiente para desarrollo)

# 5. JVM options para HiveServer2
export HIVE_SERVER2_HEAPSIZE=2048
# HiveServer2 necesita más memoria:
# - Múltiples conexiones simultáneas
# - Query compilation
# - Resultados en memoria
#
# Recomendado: 2-4 GB

# 6. JVM options para Metastore
export HIVE_METASTORE_HEAPSIZE=1024
# Metastore service: 1-2 GB suficiente

# 7. Directorio auxiliar
export HIVE_AUX_JARS_PATH=${HIVE_HOME}/lib
# JARs adicionales (UDFs custom, SerDes, etc.)

# 8. Opciones adicionales
export HIVE_OPTS="--hiveconf hive.root.logger=INFO,console"
# Configurar logging al iniciar Hive
# INFO: nivel de log (DEBUG para más detalle)
# console: salida a terminal (opción: DRFA para archivos)
```

---

### 📊 Resumen de Archivos de Configuración

| Archivo | Propósito | Configuraciones Clave |
|---------|-----------|----------------------|
| **core-site.xml** | Config global Hadoop | fs.defaultFS, io.buffer, compresión |
| **hdfs-site.xml** | Config de HDFS | replicación, blocksize, directorios |
| **mapred-site.xml** | Config de MapReduce | framework, memoria, paralelismo |
| **yarn-site.xml** | Config de YARN | memoria, CPU, scheduler, UI |
| **hive-site.xml** | Config de Hive | metastore, warehouse, optimizaciones |
| **hadoop-env.sh** | Env de Hadoop | JAVA_HOME, heap de daemons |
| **yarn-env.sh** | Env de YARN | heap de ResourceManager/NodeManager |
| **mapred-env.sh** | Env de MapReduce | heap de Job History Server |
| **hive-env.sh** | Env de Hive | heap de HiveServer2/Metastore |

---

### 🔍 Cómo Leer Valores por Defecto

```bash
# Ver configuración efectiva de Hadoop
hdfs getconf -confKey dfs.replication

# Ver todas las configs de HDFS
hdfs getconf -namenodes

# Ver configuración de YARN
yarn daemonlog -getlevel <hostname> <daemon> <class>

# Ver propiedades de Hive (desde hive CLI)
hive> SET;
hive> SET hive.execution.engine;
```

---

### 🎯 Jerarquía de Configuración

Hadoop usa este orden de prioridad (mayor a menor):

1. **Línea de comandos:** `-D propiedad=valor`
   ```bash
   hadoop jar job.jar -D mapreduce.job.reduces=10
   ```

2. **Archivos *-site.xml del job**
3. **Archivos *-site.xml del cluster** (etc/hadoop/)
4. **Archivos *-default.xml** (hardcoded en JARs)

---

## 🎬 Puntos Clave para el Video

### Introducción (1 min)
- Qué es Hadoop y para qué sirve
- Componentes: HDFS, YARN, MapReduce, Hive
- Arquitectura básica

### Durante la Instalación
- **Mostrar claramente** cada archivo de configuración
- **Explicar** qué hace cada componente
- **Verificar** con `jps` después de cada inicio
- **Demostrar** las interfaces web

### Demostración Final (3 min)
1. Crear archivo local
2. Subirlo a HDFS
3. Crear tabla en Hive
4. Insertar y consultar datos
5. Mostrar en YARN UI el job ejecutado

### Conclusión (1 min)
- Resumen de lo instalado
- Próximos pasos: prácticas avanzadas
- Dónde encontrar más recursos

---

## 📊 Checklist de Verificación

```bash
# Al final, verificar todo:
java -version          # ✅ Java 8
hadoop version         # ✅ Hadoop 3.4.0
jps                    # ✅ NameNode, DataNode, ResourceManager, NodeManager
hdfs dfs -ls /         # ✅ HDFS funcionando
hive --version         # ✅ Hive 3.1.3
```

**Interfaces Web:**
- HDFS: http://localhost:9870 ✅
- YARN: http://localhost:8088 ✅

---

## 💡 Tips para el Video

1. **Preparar antes de grabar:**
   - Tener archivos descargados
   - Limpiar terminal
   - Ajustar tamaño de fuente

2. **Durante la grabación:**
   - Explicar qué hace cada comando
   - Pausar para que copien comandos
   - Mostrar resultados esperados

3. **Errores comunes a mencionar:**
   - Problema de red: usar `nmcli device connect`
   - Puerto ocupado: verificar con `jps`
   - Guava incompatible: copiar versión correcta

4. **Segmentar el video:**
   - Parte 1: Java (5 min)
   - Parte 2: Hadoop (10 min)
   - Parte 3: Hive (8 min)
   - Parte 4: Demo (5 min)

---

<div align="center">

## ✅ Instalación Completa

**Entorno Big Data Funcional**

Java 8 + Hadoop 3.4.0 + Hive 3.1.3

*Guía para Video Tutorial - Febrero 2026*

</div>
