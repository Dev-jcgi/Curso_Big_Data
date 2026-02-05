# 📚 Guía de Comandos: Hadoop, HDFS, YARN y Hive

> **Referencia Completa de Comandos del Ecosistema Big Data**  
> **Para:** Sistemas Inteligentes - Práctica de Hadoop  
> **Fecha:** Febrero 2026

---

## 📋 Índice por Categoría

### 🐧 [Comandos de Linux](#comandos-de-linux)
### ☕ [Comandos de Java](#comandos-de-java)
### 🐘 [Comandos de Hadoop](#comandos-de-hadoop)
### 📁 [Comandos de HDFS](#comandos-de-hdfs)
### 🧵 [Comandos de YARN](#comandos-de-yarn)
### 🐝 [Comandos de Hive (HiveQL)](#comandos-de-hive-hiveql)
### 🔥 [Comandos de Firewall](#comandos-de-firewall)
### 🌐 [Comandos de Red](#comandos-de-red)

---

---

## 🐧 COMANDOS DE LINUX

---

### `yum` / `dnf` - Gestor de Paquetes

**Descripción:** Instala, actualiza y gestiona paquetes de software en CentOS/RHEL

**Sintaxis:**
```bash
yum [opciones] [comando] [paquete]
dnf [opciones] [comando] [paquete]
```

**Comandos Comunes:**

```bash
# Actualizar el sistema completo
sudo yum update
sudo dnf update

# Instalar un paquete
sudo yum install nombre_paquete
sudo yum install -y nombre_paquete  # -y = responde "yes" automáticamente

# Instalar múltiples paquetes
sudo yum install paquete1 paquete2 paquete3

# Instalar grupo de paquetes
sudo dnf groupinstall "Nombre del Grupo"

# Buscar paquetes
yum search palabra_clave

# Ver información de un paquete
yum info nombre_paquete

# Listar paquetes instalados
yum list installed

# Eliminar un paquete
sudo yum remove nombre_paquete
```

**Ejemplos en la Guía:**
```bash
sudo yum install net-tools    # Herramientas de red
sudo yum install wget          # Descargas desde internet
sudo yum install telnet        # Cliente Telnet
sudo dnf groupinstall "Server with GUI"  # Interfaz gráfica
```

---

### `tar` - Comprimir y Descomprimir Archivos

**Descripción:** Herramienta para empaquetar y descomprimir archivos

**Sintaxis:**
```bash
tar [opciones] archivo.tar.gz
```

**Opciones Principales:**
- `-z` : Usar compresión gzip
- `-x` : Extraer archivos
- `-v` : Modo verbose (mostrar progreso)
- `-f` : Especificar archivo
- `-c` : Crear archivo tar

**Ejemplos:**

```bash
# Descomprimir .tar.gz
tar -zxvf archivo.tar.gz

# Descomprimir en directorio específico
tar -zxvf archivo.tar.gz -C /ruta/destino/

# Crear archivo .tar.gz
tar -czvf archivo.tar.gz /carpeta/a/comprimir/

# Ver contenido sin extraer
tar -tzvf archivo.tar.gz
```

**En la Guía:**
```bash
tar -zxvf jdk-8u202-linux-x64.tar.gz
tar -zxvf hadoop-3.4.0.tar.gz
tar -zxvf apache-hive-3.1.3-bin.tar.gz
```

---

### `mkdir` - Crear Directorios

**Descripción:** Crea nuevos directorios

**Sintaxis:**
```bash
mkdir [opciones] nombre_directorio
```

**Opciones:**
- `-p` : Crear directorios padres si no existen
- `-m` : Establecer permisos

**Ejemplos:**

```bash
# Crear directorio simple
mkdir mi_carpeta

# Crear directorio con padres
mkdir -p /opt/hadoop/logs

# Crear con permisos específicos
mkdir -m 755 mi_carpeta

# Crear múltiples directorios
mkdir dir1 dir2 dir3
```

**En la Guía:**
```bash
mkdir /opt/hadoop
mkdir /datos
mkdir namenode
mkdir datanode
```

---

### `chown` - Cambiar Propietario

**Descripción:** Cambia el propietario de archivos y directorios

**Sintaxis:**
```bash
chown [opciones] usuario:grupo archivo
```

**Opciones:**
- `-R` : Recursivo (incluye subdirectorios)

**Ejemplos:**

```bash
# Cambiar propietario de archivo
chown hadoop archivo.txt

# Cambiar propietario y grupo
chown hadoop:hadoop archivo.txt

# Cambiar recursivamente
chown -R hadoop:hadoop /opt/hadoop

# Solo cambiar grupo
chown :hadoop archivo.txt
```

**En la Guía:**
```bash
chown -R hadoop:hadoop /opt/hadoop
chown -R hadoop:hadoop /opt/jdk1.8.0_202
chown -R hadoop:hadoop /datos
```

---

### `chmod` - Cambiar Permisos

**Descripción:** Modifica los permisos de archivos y directorios

**Sintaxis:**
```bash
chmod [opciones] permisos archivo
```

**Sistema de Permisos:**
- `r` (read) = 4
- `w` (write) = 2
- `x` (execute) = 1

**Formato numérico:** `[propietario][grupo][otros]`
- `755` = rwxr-xr-x (propietario: todo, otros: lectura+ejecución)
- `777` = rwxrwxrwx (todos tienen todos los permisos)
- `644` = rw-r--r-- (propietario: lectura+escritura, otros: solo lectura)

**Ejemplos:**

```bash
# Dar todos los permisos
chmod 777 archivo.txt

# Permisos estándar de directorio
chmod 755 /opt/hadoop

# Recursivo
chmod -R 755 /opt/hadoop

# Formato simbólico
chmod u+x script.sh          # Usuario: agregar ejecución
chmod g+w archivo.txt        # Grupo: agregar escritura
chmod o-r archivo.txt        # Otros: quitar lectura
chmod a+x script.sh          # All: agregar ejecución a todos
```

**En la Guía:**
```bash
chmod -R 755 /opt/hadoop
chmod g+w /tmp                    # Grupo: agregar escritura
```

---

### `useradd` - Crear Usuario

**Descripción:** Crea un nuevo usuario en el sistema

**Sintaxis:**
```bash
useradd [opciones] nombre_usuario
```

**Opciones Comunes:**
- `-m` : Crear directorio home
- `-s` : Especificar shell
- `-g` : Grupo primario
- `-G` : Grupos secundarios

**Ejemplos:**

```bash
# Crear usuario simple
useradd hadoop

# Crear con directorio home
useradd -m hadoop

# Especificar shell
useradd -s /bin/bash hadoop

# Crear con grupo específico
useradd -g users hadoop
```

**En la Guía:**
```bash
sudo useradd hadoop
```

---

### `passwd` - Cambiar Contraseña

**Descripción:** Establece o cambia la contraseña de un usuario

**Sintaxis:**
```bash
passwd [nombre_usuario]
```

**Ejemplos:**

```bash
# Cambiar contraseña del usuario actual
passwd

# Cambiar contraseña de otro usuario (como root)
sudo passwd hadoop

# Forzar cambio de contraseña en próximo login
sudo passwd -e hadoop

# Desbloquear cuenta
sudo passwd -u hadoop

# Bloquear cuenta
sudo passwd -l hadoop
```

**En la Guía:**
```bash
sudo passwd hadoop
# (Luego ingresar contraseña dos veces)
```

---

### `usermod` - Modificar Usuario

**Descripción:** Modifica propiedades de un usuario existente

**Sintaxis:**
```bash
usermod [opciones] nombre_usuario
```

**Opciones:**
- `-aG` : Agregar a grupos (sin quitar de otros)
- `-g` : Cambiar grupo primario
- `-s` : Cambiar shell

**Ejemplos:**

```bash
# Agregar a grupo wheel (sudoers)
sudo usermod -aG wheel hadoop

# Agregar a múltiples grupos
sudo usermod -aG grupo1,grupo2 hadoop

# Cambiar shell
sudo usermod -s /bin/zsh hadoop

# Cambiar directorio home
sudo usermod -d /nueva/home hadoop
```

**En la Guía:**
```bash
sudo usermod -aG wheel hadoop  # Dar permisos de administrador
```

---

### `su` - Cambiar de Usuario

**Descripción:** Cambia al contexto de otro usuario

**Sintaxis:**
```bash
su [opciones] [usuario]
```

**Opciones:**
- `-` o `-l` : Cargar entorno completo del usuario (recomendado)

**Diferencias:**

```bash
# SIN guion: mantiene variables del usuario actual
su hadoop

# CON guion: carga variables del usuario destino
su - hadoop
```

**Ejemplos:**

```bash
# Cambiar a usuario hadoop (con entorno)
su - hadoop

# Cambiar a root
su -

# Ejecutar comando como otro usuario
su - hadoop -c "hadoop version"

# Volver al usuario anterior
exit
```

**En la Guía:**
```bash
su - hadoop  # El guion (-) es importante
```

---

### `cd` - Cambiar Directorio

**Descripción:** Navega entre directorios

**Sintaxis:**
```bash
cd [ruta]
```

**Ejemplos:**

```bash
# Ir al home del usuario
cd
cd ~

# Ir a directorio específico
cd /opt/hadoop

# Ir al directorio padre
cd ..

# Ir dos niveles arriba
cd ../..

# Volver al directorio anterior
cd -

# Ir a raíz del sistema
cd /
```

**En la Guía:**
```bash
cd /opt/hadoop/
cd /home/hadoop/
cd /datos/
```

---

### `ls` - Listar Archivos

**Descripción:** Lista contenido de directorios

**Sintaxis:**
```bash
ls [opciones] [ruta]
```

**Opciones:**
- `-l` : Formato largo (permisos, propietario, tamaño)
- `-h` : Tamaños legibles (KB, MB, GB)
- `-a` : Mostrar archivos ocultos
- `-t` : Ordenar por fecha de modificación
- `-S` : Ordenar por tamaño

**Ejemplos:**

```bash
# Listar archivos simples
ls

# Formato detallado
ls -l

# Con tamaños legibles
ls -lh

# Incluir archivos ocultos
ls -la

# Ordenar por fecha
ls -lt

# Recursivo
ls -R
```

**En la Guía:**
```bash
ls -l /opt/
ls -lh hadoop-3.4.0.tar.gz
```

---

### `cat` - Ver Contenido de Archivos

**Descripción:** Muestra el contenido completo de archivos de texto

**Sintaxis:**
```bash
cat [opciones] archivo
```

**Ejemplos:**

```bash
# Ver archivo
cat archivo.txt

# Ver múltiples archivos
cat archivo1.txt archivo2.txt

# Concatenar archivos y guardar
cat archivo1.txt archivo2.txt > combinado.txt

# Numerar líneas
cat -n archivo.txt

# Ver con fin de línea visible
cat -E archivo.txt
```

**En la Guía:**
```bash
cat prueba.txt
cat /tmp/usuarios_nuevos.csv
```

---

### `echo` - Imprimir Texto

**Descripción:** Imprime texto en la salida estándar

**Sintaxis:**
```bash
echo [opciones] "texto"
```

**Opciones:**
- `-e` : Interpretar secuencias de escape (\n, \t)
- `-n` : No agregar nueva línea al final

**Ejemplos:**

```bash
# Imprimir simple
echo "Hola Mundo"

# Ver variable
echo $JAVA_HOME

# Crear archivo con texto
echo "Hola Mundo Hadoop HDFS" >> prueba.txt

# Con saltos de línea
echo -e "Línea 1\nLínea 2\nLínea 3"

# Redireccionar a archivo (sobrescribir)
echo "texto" > archivo.txt

# Redireccionar a archivo (agregar)
echo "más texto" >> archivo.txt
```

**En la Guía:**
```bash
echo "Hola Mundo Hadoop HDFS" >> prueba.txt
echo $JAVA_HOME
echo $HADOOP_HOME
```

---

### `nano` / `vi` - Editores de Texto

**Descripción:** Editores de texto en terminal

#### NANO (más fácil para principiantes)

```bash
# Abrir archivo
nano archivo.txt

# Comandos dentro de nano:
Ctrl + O    # Guardar (write Out)
Ctrl + X    # Salir (eXit)
Ctrl + K    # Cortar línea
Ctrl + U    # Pegar
Ctrl + W    # Buscar
Alt + \     # Ir al inicio
Alt + /     # Ir al final
```

#### VI/VIM (más potente)

```bash
# Abrir archivo
vi archivo.txt

# Modos:
ESC         # Modo comando
i           # Modo inserción (insert)
a           # Modo inserción (append)
v           # Modo visual

# Guardar y salir:
:w          # Guardar (write)
:q          # Salir (quit)
:wq         # Guardar y salir
:q!         # Salir sin guardar

# Navegación:
gg          # Ir al inicio
G           # Ir al final
0           # Inicio de línea
$           # Fin de línea

# Edición:
dd          # Borrar línea
yy          # Copiar línea
p           # Pegar
u           # Deshacer
Ctrl + r    # Rehacer

# Búsqueda:
/palabra    # Buscar hacia adelante
?palabra    # Buscar hacia atrás
n           # Siguiente coincidencia
```

**En la Guía:**
```bash
nano .bashrc
nano /etc/hosts
vi /opt/hadoop/hadoop-3.4.0/etc/hadoop/core-site.xml
```

---

### `source` - Recargar Variables

**Descripción:** Ejecuta comandos de un archivo en el shell actual

**Sintaxis:**
```bash
source archivo
# o
. archivo
```

**Uso Principal:** Recargar variables de entorno sin cerrar sesión

**Ejemplos:**

```bash
# Recargar .bashrc
source ~/.bashrc
source /home/hadoop/.bashrc

# Alternativa con punto
. ~/.bashrc

# Cargar variables de script
source /etc/environment
```

**En la Guía:**
```bash
source /home/hadoop/.bashrc
source ~/.bashrc
```

---

### `export` - Definir Variables de Entorno

**Descripción:** Crea o modifica variables de entorno

**Sintaxis:**
```bash
export NOMBRE_VARIABLE=valor
```

**Ejemplos:**

```bash
# Definir variable simple
export JAVA_HOME=/opt/jdk1.8.0_202

# Agregar a PATH existente
export PATH=$PATH:/nueva/ruta

# Variable con espacios (usar comillas)
export MI_VAR="valor con espacios"

# Ver todas las variables exportadas
export

# Ver variable específica
echo $JAVA_HOME
```

**En la Guía (.bashrc):**
```bash
export JAVA_HOME=/opt/jdk1.8.0_202
export JRE_HOME=/opt/jdk1.8.0_202/jre
export HADOOP_HOME=/opt/hadoop/hadoop-3.4.0
export HIVE_HOME=/opt/hive
export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$HIVE_HOME/bin
```

---

### `mount` - Montar Sistemas de Archivos

**Descripción:** Monta un dispositivo o sistema de archivos

**Sintaxis:**
```bash
mount [opciones] dispositivo punto_montaje
```

**Ejemplos:**

```bash
# Montar CD/DVD
sudo mount /dev/cdrom /mnt/cdrom

# Montar USB
sudo mount /dev/sdb1 /mnt/usb

# Montar con tipo específico
sudo mount -t iso9660 /dev/cdrom /mnt/cdrom

# Ver sistemas montados
mount

# Desmontar
sudo umount /mnt/cdrom
```

**En la Guía:**
```bash
sudo mkdir -p /mnt/cdrom
sudo mount /dev/cdrom /mnt/cdrom
```

---

### `systemctl` - Controlar Servicios

**Descripción:** Gestor de servicios de systemd

**Sintaxis:**
```bash
systemctl [comando] [servicio]
```

**Comandos Comunes:**

```bash
# Ver estado de servicio
systemctl status nombre_servicio

# Iniciar servicio
sudo systemctl start nombre_servicio

# Detener servicio
sudo systemctl stop nombre_servicio

# Reiniciar servicio
sudo systemctl restart nombre_servicio

# Habilitar inicio automático
sudo systemctl enable nombre_servicio

# Deshabilitar inicio automático
sudo systemctl disable nombre_servicio

# Ver modo de arranque actual
systemctl get-default

# Cambiar a modo gráfico
sudo systemctl set-default graphical.target

# Cambiar a modo texto
sudo systemctl set-default multi-user.target

# Iniciar interfaz gráfica ahora
sudo systemctl start graphical.target
```

**En la Guía:**
```bash
systemctl get-default
sudo systemctl set-default graphical.target
sudo systemctl start graphical.target
sudo systemctl enable vboxservice
sudo systemctl start vboxservice
```

---

### `reboot` - Reiniciar Sistema

**Descripción:** Reinicia el sistema operativo

**Sintaxis:**
```bash
reboot
sudo reboot
```

**Alternativas:**

```bash
# Con systemctl
sudo systemctl reboot

# Con shutdown
sudo shutdown -r now

# Reiniciar en 5 minutos
sudo shutdown -r +5

# Apagar en lugar de reiniciar
sudo shutdown -h now
```

**En la Guía:**
```bash
sudo reboot
```

---

---

## ☕ COMANDOS DE JAVA

---

### `java` - Ejecutar Aplicaciones Java

**Descripción:** Ejecuta programas Java compilados

**Sintaxis:**
```bash
java [opciones] clase_principal
java [opciones] -jar archivo.jar
```

**Opciones Comunes:**
- `-version` : Mostrar versión de Java
- `-jar` : Ejecutar archivo JAR
- `-Xmx` : Memoria máxima heap
- `-Xms` : Memoria inicial heap

**Ejemplos:**

```bash
# Ver versión
java -version

# Ejecutar clase
java MiPrograma

# Ejecutar JAR
java -jar aplicacion.jar

# Con memoria específica
java -Xmx2G -Xms512M -jar aplicacion.jar

# Ver ayuda
java -help
```

**Salida Esperada:**
```
java version "1.8.0_202"
Java(TM) SE Runtime Environment (build 1.8.0_202-b08)
Java HotSpot(TM) 64-Bit Server VM (build 25.202-b08, mixed mode)
```

---

### `javac` - Compilador de Java

**Descripción:** Compila código fuente Java (.java) a bytecode (.class)

**Sintaxis:**
```bash
javac [opciones] archivo.java
```

**Ejemplos:**

```bash
# Ver versión
javac -version

# Compilar archivo
javac MiPrograma.java

# Compilar con classpath
javac -cp /ruta/libs/*.jar MiPrograma.java

# Compilar a directorio específico
javac -d ./bin MiPrograma.java
```

**Salida Esperada:**
```
javac 1.8.0_202
```

---

### `jar` - Herramienta de Archivos JAR

**Descripción:** Crea y manipula archivos JAR (Java Archive)

**Sintaxis:**
```bash
jar [opciones] archivo.jar archivos
```

**Opciones:**
- `c` : Crear JAR
- `x` : Extraer JAR
- `t` : Listar contenido
- `f` : Especificar archivo

**Ejemplos:**

```bash
# Crear JAR
jar -cf mi_programa.jar *.class

# Crear JAR con manifest
jar -cfm mi_programa.jar MANIFEST.MF *.class

# Extraer JAR
jar -xf archivo.jar

# Listar contenido
jar -tf archivo.jar

# Ver contenido de manifest
jar -xf archivo.jar META-INF/MANIFEST.MF
cat META-INF/MANIFEST.MF
```

---

### `alternatives` - Gestionar Versiones de Java

**Descripción:** Gestiona múltiples versiones de comandos (especialmente Java)

**Sintaxis:**
```bash
alternatives --install enlace nombre ruta prioridad
alternatives --config nombre
```

**Ejemplos:**

```bash
# Registrar nueva versión de Java
sudo alternatives --install /usr/bin/java java /opt/jdk1.8.0_202/bin/java 2

# Seleccionar versión activa
sudo alternatives --config java

# Registrar javac
sudo alternatives --install /usr/bin/javac javac /opt/jdk1.8.0_202/bin/javac 2

# Ver versión actual
alternatives --display java

# Eliminar alternativa
sudo alternatives --remove java /opt/jdk1.8.0_202/bin/java
```

**Pantalla de Selección:**
```
There are 2 programs which provide 'java'.

  Selection    Command
-----------------------------------------------
*+ 1           /usr/lib/jvm/java-11-openjdk/bin/java
   2           /opt/jdk1.8.0_202/bin/java

Enter to keep the current selection[+], or type selection number: 2
```

---

### `jps` - Java Process Status

**Descripción:** Lista procesos Java en ejecución (crucial para Hadoop)

**Sintaxis:**
```bash
jps [opciones]
```

**Opciones:**
- `-l` : Nombres completos de clases
- `-v` : Argumentos de JVM
- `-m` : Argumentos del main

**Ejemplos:**

```bash
# Listar procesos Java
jps

# Con información completa
jps -l

# Con argumentos JVM
jps -v

# Con argumentos del programa
jps -m
```

**Salida Esperada en Hadoop:**
```
12345 NameNode
12346 DataNode
12347 SecondaryNameNode
12348 ResourceManager
12349 NodeManager
12350 Jps
```

**Interpretación:**
- `NameNode` : Proceso maestro de HDFS ✅
- `DataNode` : Proceso de almacenamiento de HDFS ✅
- `SecondaryNameNode` : Proceso de checkpoint ✅
- `ResourceManager` : Gestor de recursos de YARN ✅
- `NodeManager` : Ejecutor de tareas de YARN ✅
- `Jps` : El comando que acabas de ejecutar

---

---

## 🐘 COMANDOS DE HADOOP

---

### `hadoop` - Comando Principal de Hadoop

**Descripción:** Interfaz principal para ejecutar comandos de Hadoop

**Sintaxis:**
```bash
hadoop [opción] [comando]
```

**Subcomandos Principales:**
- `version` : Ver versión de Hadoop
- `jar` : Ejecutar aplicaciones JAR
- `fs` : Operaciones en sistema de archivos
- `classpath` : Ver classpath de Hadoop

**Ejemplos:**

```bash
# Ver versión
hadoop version

# Ejecutar JAR de MapReduce
hadoop jar mi_programa.jar clase_principal args

# Ver classpath
hadoop classpath

# Ver ayuda
hadoop help
```

**Salida de `hadoop version`:**
```
Hadoop 3.4.0
Source code repository https://github.com/apache/hadoop.git -r ...
Compiled by ... on 2023-XX-XX
...
```

---

### `hdfs` - Comando de HDFS

**Descripción:** Interfaz específica para Hadoop Distributed File System

**Sintaxis:**
```bash
hdfs [comando] [opciones]
```

**Comandos Principales:**
- `dfs` : Operaciones de sistema de archivos
- `namenode` : Gestión del NameNode
- `datanode` : Gestión del DataNode
- `fsck` : Verificar integridad del sistema

**Ejemplos:**

```bash
# Formatear NameNode (solo primera vez)
hdfs namenode -format

# Ver reporte de HDFS
hdfs dfsadmin -report

# Ver estado de HDFS
hdfs dfsadmin -safemode get

# Verificar integridad de archivos
hdfs fsck /ruta -files -blocks -locations

# Balancear datos entre DataNodes
hdfs balancer
```

---

### `hadoop jar` - Ejecutar MapReduce

**Descripción:** Ejecuta aplicaciones MapReduce empaquetadas en JAR

**Sintaxis:**
```bash
hadoop jar archivo.jar clase_principal [args]
```

**Ejemplos:**

```bash
# WordCount (contar palabras)
hadoop jar hadoop-mapreduce-examples-3.4.0.jar wordcount /entrada /salida

# Pi (estimación de π)
hadoop jar hadoop-mapreduce-examples-3.4.0.jar pi 10 100

# Grep (buscar patrones)
hadoop jar hadoop-mapreduce-examples-3.4.0.jar grep /entrada /salida 'patrón'

# TeraGen (generar datos)
hadoop jar hadoop-mapreduce-examples-3.4.0.jar teragen 1000 /teragen_output

# TeraSort (ordenar datos)
hadoop jar hadoop-mapreduce-examples-3.4.0.jar terasort /entrada /salida

# Ver ejemplos disponibles
hadoop jar hadoop-mapreduce-examples-3.4.0.jar
```

**Parámetros de WordCount:**
```bash
hadoop jar [archivo_jar] wordcount [entrada_HDFS] [salida_HDFS]
                                   ↑                ↑
                                   |                └─ Carpeta que NO debe existir
                                   └─────────────────── Carpeta con archivos de texto
```

---

### Scripts de Inicio/Detención

#### `start-dfs.sh` - Iniciar HDFS

**Descripción:** Inicia todos los servicios de HDFS

**Sintaxis:**
```bash
start-dfs.sh
```

**Servicios que inicia:**
- NameNode
- DataNode
- Secondary NameNode

**Salida:**
```
Starting namenodes on [nodo1]
Starting datanodes
Starting secondary namenodes [nodo1]
```

---

#### `stop-dfs.sh` - Detener HDFS

**Descripción:** Detiene todos los servicios de HDFS

**Sintaxis:**
```bash
stop-dfs.sh
```

---

#### `start-yarn.sh` - Iniciar YARN

**Descripción:** Inicia servicios de gestión de recursos

**Sintaxis:**
```bash
start-yarn.sh
```

**Servicios que inicia:**
- ResourceManager
- NodeManager

---

#### `stop-yarn.sh` - Detener YARN

**Descripción:** Detiene servicios YARN

**Sintaxis:**
```bash
stop-yarn.sh
```

---

#### `start-all.sh` - Iniciar Todo

**Descripción:** Inicia HDFS + YARN en un solo comando

**Sintaxis:**
```bash
start-all.sh
```

**Equivalente a:**
```bash
start-dfs.sh
start-yarn.sh
```

---

#### `stop-all.sh` - Detener Todo

**Descripción:** Detiene todos los servicios de Hadoop

**Sintaxis:**
```bash
stop-all.sh
```

**Equivalente a:**
```bash
stop-yarn.sh
stop-dfs.sh
```

---

---

## 📁 COMANDOS DE HDFS (Sistema de Archivos Distribuido)

> **Nota:** Todos los comandos HDFS siguen el patrón: `hdfs dfs -comando`

---

### Estructura General

```bash
hdfs dfs -comando [opciones] [argumentos]
```

**Similitudes con Linux:**
- La mayoría de comandos son iguales a Linux: `ls`, `cat`, `mkdir`, `rm`
- Las rutas en HDFS empiezan con `/` (como Linux)
- Permisos similares: rwxr-xr-x

**Diferencias con Linux:**
- Los archivos están distribuidos en múltiples nodos
- Tienen factor de replicación (default: 3 copias)
- Los archivos grandes se dividen en bloques (128 MB)

---

### `-ls` - Listar Archivos en HDFS

**Descripción:** Lista archivos y directorios en HDFS

**Sintaxis:**
```bash
hdfs dfs -ls [ruta_HDFS]
```

**Opciones:**
- `-h` : Tamaños legibles
- `-R` : Recursivo

**Ejemplos:**

```bash
# Listar raíz de HDFS
hdfs dfs -ls /

# Listar directorio específico
hdfs dfs -ls /user/hadoop

# Recursivo
hdfs dfs -ls -R /

# Con tamaños legibles
hdfs dfs -ls -h /datos
```

**Salida:**
```
drwxr-xr-x   - hadoop supergroup          0 2026-02-01 10:00 /prueba
-rw-r--r--   3 hadoop supergroup    1979173 2026-02-01 10:20 /datos/ratings.data
```

**Interpretación:**
- `d` : Es directorio (- es archivo)
- `rwxr-xr-x` : Permisos
- `3` : Factor de replicación (3 copias)
- `hadoop` : Usuario propietario
- `supergroup` : Grupo
- `1979173` : Tamaño en bytes
- Fecha y hora de modificación

---

### `-mkdir` - Crear Directorio

**Descripción:** Crea directorios en HDFS

**Sintaxis:**
```bash
hdfs dfs -mkdir [opciones] ruta_HDFS
```

**Opciones:**
- `-p` : Crear directorios padres si no existen

**Ejemplos:**

```bash
# Crear directorio simple
hdfs dfs -mkdir /prueba

# Crear con padres
hdfs dfs -mkdir -p /datos/entrada/2024/enero

# Crear múltiples directorios
hdfs dfs -mkdir /dir1 /dir2 /dir3
```

---

### `-put` - Subir Archivos a HDFS

**Descripción:** Copia archivos del sistema local a HDFS

**Sintaxis:**
```bash
hdfs dfs -put archivo_local ruta_HDFS
```

**Alias:** `-copyFromLocal`

**Ejemplos:**

```bash
# Subir archivo simple
hdfs dfs -put archivo.txt /datos/

# Subir con nuevo nombre
hdfs dfs -put local.txt /datos/remoto.txt

# Subir múltiples archivos
hdfs dfs -put archivo1.txt archivo2.txt /datos/

# Subir directorio completo
hdfs dfs -put /home/hadoop/datos/ /hdfs_datos/

# Sobrescribir si existe
hdfs dfs -put -f archivo.txt /datos/
```

---

### `-get` - Descargar Archivos desde HDFS

**Descripción:** Copia archivos de HDFS al sistema local

**Sintaxis:**
```bash
hdfs dfs -get ruta_HDFS destino_local
```

**Alias:** `-copyToLocal`

**Ejemplos:**

```bash
# Descargar archivo
hdfs dfs -get /datos/archivo.txt ./

# Descargar con nuevo nombre
hdfs dfs -get /datos/archivo.txt ./nuevo_nombre.txt

# Descargar directorio completo
hdfs dfs -get /datos/ ./datos_local/

# Omitir archivos CRC (checksums)
hdfs dfs -get -ignoreCrc /datos/archivo.txt ./
```

---

### `-cat` - Ver Contenido de Archivo

**Descripción:** Muestra el contenido de archivos en HDFS

**Sintaxis:**
```bash
hdfs dfs -cat ruta_HDFS
```

**Ejemplos:**

```bash
# Ver archivo completo
hdfs dfs -cat /datos/archivo.txt

# Ver múltiples archivos
hdfs dfs -cat /datos/*.txt

# Ver con paginador
hdfs dfs -cat /datos/archivo.txt | less

# Ver primeras líneas
hdfs dfs -cat /datos/archivo.txt | head -10

# Ver últimas líneas
hdfs dfs -cat /datos/archivo.txt | tail -20

# Contar líneas
hdfs dfs -cat /datos/archivo.txt | wc -l
```

---

### `-head` / `-tail` - Ver Inicio/Final de Archivo

**Descripción:** Muestra primeras o últimas líneas

**Sintaxis:**
```bash
hdfs dfs -head ruta_HDFS
hdfs dfs -tail ruta_HDFS
```

**Ejemplos:**

```bash
# Ver primeros 1KB
hdfs dfs -head /datos/archivo.txt

# Ver últimos 1KB
hdfs dfs -tail /datos/archivo.txt

# Seguir archivo en tiempo real (como tail -f)
hdfs dfs -tail -f /logs/aplicacion.log
```

---

### `-rm` - Eliminar Archivos

**Descripción:** Elimina archivos o directorios en HDFS

**Sintaxis:**
```bash
hdfs dfs -rm [opciones] ruta_HDFS
```

**Opciones:**
- `-r` : Recursivo (para directorios)
- `-skipTrash` : Eliminar permanentemente

**Ejemplos:**

```bash
# Eliminar archivo
hdfs dfs -rm /datos/archivo.txt

# Eliminar múltiples archivos
hdfs dfs -rm /datos/*.txt

# Eliminar directorio (recursivo)
hdfs dfs -rm -r /datos/

# Eliminar permanentemente (sin papelera)
hdfs dfs -rm -r -skipTrash /datos/

# Eliminar directorio vacío
hdfs dfs -rmdir /directorio_vacio
```

**Nota:** Por defecto, los archivos eliminados van a `.Trash` (papelera)

---

### `-cp` - Copiar en HDFS

**Descripción:** Copia archivos dentro de HDFS

**Sintaxis:**
```bash
hdfs dfs -cp origen_HDFS destino_HDFS
```

**Ejemplos:**

```bash
# Copiar archivo
hdfs dfs -cp /datos/original.txt /backup/copia.txt

# Copiar directorio
hdfs dfs -cp /datos/ /backup/datos/

# Sobrescribir si existe
hdfs dfs -cp -f /datos/archivo.txt /backup/
```

---

### `-mv` - Mover/Renombrar en HDFS

**Descripción:** Mueve o renombra archivos en HDFS

**Sintaxis:**
```bash
hdfs dfs -mv origen_HDFS destino_HDFS
```

**Ejemplos:**

```bash
# Renombrar archivo
hdfs dfs -mv /datos/viejo.txt /datos/nuevo.txt

# Mover archivo a otro directorio
hdfs dfs -mv /datos/archivo.txt /backup/

# Mover múltiples archivos
hdfs dfs -mv /datos/*.txt /backup/
```

---

### `-du` - Ver Uso de Disco

**Descripción:** Muestra el tamaño de archivos/directorios

**Sintaxis:**
```bash
hdfs dfs -du [opciones] ruta_HDFS
```

**Opciones:**
- `-h` : Tamaños legibles (KB, MB, GB)
- `-s` : Resumen (solo total)

**Ejemplos:**

```bash
# Ver tamaño de directorio
hdfs dfs -du /datos

# Con formato legible
hdfs dfs -du -h /datos

# Solo total
hdfs dfs -du -s -h /datos

# Ver todo el filesystem
hdfs dfs -du -h /
```

**Salida:**
```
1.9 M  5.7 M  /datos/ratings.data
```
- **1.9 M** : Tamaño del archivo
- **5.7 M** : Espacio usado (con replicación x3)

---

### `-df` - Espacio en Disco

**Descripción:** Muestra espacio disponible en HDFS

**Sintaxis:**
```bash
hdfs dfs -df [opciones] [ruta]
```

**Opciones:**
- `-h` : Formato legible

**Ejemplos:**

```bash
# Ver espacio total
hdfs dfs -df

# Con formato legible
hdfs dfs -df -h

# De un path específico
hdfs dfs -df -h /datos
```

**Salida:**
```
Filesystem           Size    Used  Available  Use%
hdfs://nodo1:9000  50.0 G  2.5 G    47.5 G     5%
```

---

### `-chmod` - Cambiar Permisos en HDFS

**Descripción:** Modifica permisos de archivos en HDFS

**Sintaxis:**
```bash
hdfs dfs -chmod [opciones] permisos ruta_HDFS
```

**Ejemplos:**

```bash
# Dar todos los permisos
hdfs dfs -chmod 777 /datos/archivo.txt

# Recursivo
hdfs dfs -chmod -R 755 /datos/

# Formato simbólico
hdfs dfs -chmod g+w /datos/         # Grupo: agregar escritura
hdfs dfs -chmod o-r /datos/         # Otros: quitar lectura
hdfs dfs -chmod u+x /scripts/run.sh # Usuario: agregar ejecución
```

---

### `-chown` - Cambiar Propietario en HDFS

**Descripción:** Cambia el propietario de archivos en HDFS

**Sintaxis:**
```bash
hdfs dfs -chown [opciones] usuario:grupo ruta_HDFS
```

**Ejemplos:**

```bash
# Cambiar propietario
hdfs dfs -chown hadoop /datos/archivo.txt

# Cambiar propietario y grupo
hdfs dfs -chown hadoop:hadoop /datos/archivo.txt

# Recursivo
hdfs dfs -chown -R hadoop:supergroup /datos/

# Solo grupo
hdfs dfs -chgrp hadoop /datos/archivo.txt
```

---

### `-stat` - Estadísticas de Archivo

**Descripción:** Muestra información detallada de archivos

**Sintaxis:**
```bash
hdfs dfs -stat [formato] ruta_HDFS
```

**Formatos:**
- `%b` : Tamaño en bytes
- `%r` : Factor de replicación
- `%n` : Nombre del archivo
- `%o` : Tamaño de bloque
- `%y` : Fecha de modificación

**Ejemplos:**

```bash
# Información completa
hdfs dfs -stat "%b bytes, %r replicas, %o bloques" /datos/archivo.txt

# Solo tamaño
hdfs dfs -stat %b /datos/archivo.txt

# Solo replicación
hdfs dfs -stat %r /datos/archivo.txt
```

---

### `-text` - Ver Archivos Comprimidos

**Descripción:** Muestra contenido de archivos de texto (incluso comprimidos)

**Sintaxis:**
```bash
hdfs dfs -text ruta_HDFS
```

**Ejemplos:**

```bash
# Ver archivo normal
hdfs dfs -text /datos/archivo.txt

# Ver archivo gzip
hdfs dfs -text /datos/archivo.txt.gz

# Ver archivo bzip2
hdfs dfs -text /datos/archivo.txt.bz2
```

---

### `-count` - Contar Archivos y Directorios

**Descripción:** Cuenta directorios, archivos y bytes

**Sintaxis:**
```bash
hdfs dfs -count [opciones] ruta_HDFS
```

**Opciones:**
- `-q` : Incluir cuotas
- `-h` : Formato legible

**Ejemplos:**

```bash
# Contar en directorio
hdfs dfs -count /datos

# Con formato legible
hdfs dfs -count -h /datos
```

**Salida:**
```
DIR_COUNT   FILE_COUNT  CONTENT_SIZE  PATHNAME
    1           15         1979173     /datos
```

---

### `-setrep` - Cambiar Factor de Replicación

**Descripción:** Modifica el número de réplicas de un archivo

**Sintaxis:**
```bash
hdfs dfs -setrep [-R] num_replicas ruta_HDFS
```

**Ejemplos:**

```bash
# Cambiar a 2 réplicas
hdfs dfs -setrep 2 /datos/archivo.txt

# Cambiar recursivamente
hdfs dfs -setrep -R 1 /datos/

# Aumentar replicación
hdfs dfs -setrep 5 /datos/importante.txt
```

---

### `-touchz` - Crear Archivo Vacío

**Descripción:** Crea un archivo vacío en HDFS

**Sintaxis:**
```bash
hdfs dfs -touchz ruta_HDFS
```

**Ejemplos:**

```bash
# Crear archivo vacío
hdfs dfs -touchz /datos/nuevo_archivo.txt

# Crear múltiples archivos
hdfs dfs -touchz /datos/archivo1.txt /datos/archivo2.txt
```

---

### `hdfs fsck` - Verificar Integridad

**Descripción:** File System Check - verifica la salud del sistema de archivos

**Sintaxis:**
```bash
hdfs fsck ruta_HDFS [opciones]
```

**Opciones:**
- `-files` : Mostrar archivos
- `-blocks` : Mostrar bloques
- `-locations` : Mostrar ubicación de bloques
- `-racks` : Mostrar topología de racks

**Ejemplos:**

```bash
# Verificar todo HDFS
hdfs fsck /

# Verificar archivo específico
hdfs fsck /datos/archivo.txt

# Con información detallada
hdfs fsck /datos/archivo.txt -files -blocks -locations

# Ver archivos corruptos
hdfs fsck / | grep CORRUPT
```

**Salida:**
```
/datos/ratings.data 1979173 bytes, 1 block(s):  OK
 0. BP-123456-127.0.0.1-123456:blk_1073741825_1001 len=1979173 repl=3
    [DataNode1, DataNode2, DataNode3]

Status: HEALTHY
 Total size:    1979173 B
 Total dirs:    2
 Total files:   1
 Total blocks:  1
 Minimally replicated blocks: 1
 Over-replicated blocks: 0
 Under-replicated blocks: 0
 Mis-replicated blocks: 0
 Default replication factor: 3
 Average block replication: 3.0
 Corrupt blocks: 0
 Missing replicas: 0
```

---

---

## 🧵 COMANDOS DE YARN (Gestión de Recursos)

---

### `yarn` - Comando Principal de YARN

**Descripción:** Interfaz para gestión de aplicaciones y recursos

**Sintaxis:**
```bash
yarn [comando] [opciones]
```

**Subcomandos:**
- `application` : Gestionar aplicaciones
- `node` : Gestionar nodos
- `logs` : Ver logs de aplicaciones
- `jar` : Ejecutar JAR

---

### `yarn application` - Gestionar Aplicaciones

**Descripción:** Lista y gestiona aplicaciones MapReduce

**Comandos:**

```bash
# Listar todas las aplicaciones
yarn application -list

# Listar solo aplicaciones en ejecución
yarn application -list -appStates RUNNING

# Listar aplicaciones finalizadas
yarn application -list -appStates FINISHED

# Listar aplicaciones fallidas
yarn application -list -appStates FAILED

# Ver estado de aplicación específica
yarn application -status application_1234567890_0001

# Matar aplicación
yarn application -kill application_1234567890_0001
```

**Salida de `-list`:**
```
Total number of applications (application-types: [], states: [RUNNING]):1
Application-Id              Application-Name   Application-Type  User    State     Final-State  Progress  
application_1234567890_0001 word count         MAPREDUCE        hadoop  RUNNING   UNDEFINED    45%
```

---

### `yarn logs` - Ver Logs de Aplicaciones

**Descripción:** Muestra logs de aplicaciones ejecutadas

**Sintaxis:**
```bash
yarn logs -applicationId <app_id> [opciones]
```

**Opciones:**
- `-log_files` : Especificar archivos de log
- `-size` : Limitar tamaño de salida
- `-containerId` : Logs de contenedor específico

**Ejemplos:**

```bash
# Ver logs completos de aplicación
yarn logs -applicationId application_1234567890_0001

# Ver logs con límite de tamaño (últimos 100KB)
yarn logs -applicationId application_1234567890_0001 -size -102400

# Ver solo stderr
yarn logs -applicationId application_1234567890_0001 -log_files stderr

# Logs de contenedor específico
yarn logs -applicationId application_1234567890_0001 -containerId container_1234567890_0001_01_000001
```

---

### `yarn node` - Gestionar Nodos

**Descripción:** Lista y gestiona NodeManagers

**Ejemplos:**

```bash
# Listar todos los nodos
yarn node -list

# Listar nodos con detalles
yarn node -list -all

# Ver estado de nodo específico
yarn node -status nodo1:8041

# Ver nodos activos
yarn node -list -states RUNNING
```

**Salida:**
```
Total Nodes:1
         Node-Id             Node-State Node-Http-Address       Number-of-Running-Containers
nodo1:8041                   RUNNING    nodo1:8042              2
```

---

### `yarn jar` - Ejecutar Aplicación JAR

**Descripción:** Ejecuta aplicaciones MapReduce con YARN

**Sintaxis:**
```bash
yarn jar archivo.jar clase_principal [argumentos]
```

**Ejemplos:**

```bash
# WordCount
yarn jar hadoop-mapreduce-examples-3.4.0.jar wordcount /entrada /salida

# Con más memoria
yarn jar mi_aplicacion.jar \
  -Dmapreduce.map.memory.mb=4096 \
  -Dmapreduce.reduce.memory.mb=4096 \
  MiClase /entrada /salida
```

---

### Comandos de Configuración YARN

```bash
# Ver configuración actual de YARN
yarn rmadmin -getServiceState rm1

# Refrescar nodos
yarn rmadmin -refreshNodes

# Ver topología del cluster
yarn rmadmin -printTopology

# Ver estado del ResourceManager
yarn rmadmin -getServiceState rm1
```

---

---

## 🐝 COMANDOS DE HIVE (HiveQL)

---

### Iniciar Hive

**Comandos de Inicio:**

```bash
# Iniciar consola interactiva
hive

# Ejecutar query desde línea de comando
hive -e "SELECT * FROM tabla;"

# Ejecutar script SQL
hive -f script.hql

# Modo silencioso (sin logs)
hive -S -e "SELECT COUNT(*) FROM tabla;"

# Modo verbose (con información detallada)
hive -v -e "SELECT * FROM tabla;"

# Con base de datos específica
hive --database nombre_db
```

---

### Comandos DDL (Data Definition Language)

#### Bases de Datos

```sql
-- Ver bases de datos
SHOW DATABASES;

-- Crear base de datos
CREATE DATABASE nombre_db;
CREATE DATABASE IF NOT EXISTS nombre_db;

-- Crear con ubicación personalizada
CREATE DATABASE nombre_db
LOCATION '/mi/ruta/hdfs/';

-- Crear con comentario
CREATE DATABASE nombre_db
COMMENT 'Descripción de la base de datos';

-- Usar base de datos
USE nombre_db;

-- Ver base de datos actual
SELECT current_database();

-- Eliminar base de datos
DROP DATABASE nombre_db;
DROP DATABASE IF EXISTS nombre_db CASCADE;  -- CASCADE elimina tablas también

-- Ver información de base de datos
DESCRIBE DATABASE nombre_db;
DESCRIBE DATABASE EXTENDED nombre_db;
```

---

#### Tablas

**Crear Tabla Simple:**

```sql
CREATE TABLE nombre_tabla (
    id INT,
    nombre STRING,
    edad INT
);
```

**Crear Tabla Externa:**

```sql
CREATE EXTERNAL TABLE nombre_tabla (
    id INT,
    nombre STRING,
    edad INT
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE
LOCATION '/ruta/hdfs/';
```

**Crear con Particiones:**

```sql
CREATE TABLE ventas (
    id INT,
    producto STRING,
    precio DOUBLE
)
PARTITIONED BY (year INT, month INT)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ',';
```

**Crear desde Consulta (CTAS):**

```sql
CREATE TABLE tabla_nueva AS
SELECT * FROM tabla_existente WHERE condicion;
```

**Ver Tablas:**

```sql
-- Listar tablas
SHOW TABLES;

-- Listar tablas con patrón
SHOW TABLES LIKE 'ventas*';

-- Ver estructura de tabla
DESCRIBE nombre_tabla;
DESC nombre_tabla;

-- Ver información detallada
DESCRIBE FORMATTED nombre_tabla;
DESCRIBE EXTENDED nombre_tabla;

-- Ver DDL de creación
SHOW CREATE TABLE nombre_tabla;
```

**Modificar Tablas:**

```sql
-- Renombrar tabla
ALTER TABLE viejo_nombre RENAME TO nuevo_nombre;

-- Agregar columna
ALTER TABLE nombre_tabla ADD COLUMNS (nueva_columna STRING);

-- Cambiar tipo de columna
ALTER TABLE nombre_tabla CHANGE columna columna BIGINT;

-- Cambiar ubicación
ALTER TABLE nombre_tabla SET LOCATION '/nueva/ruta/';
```

**Eliminar Tablas:**

```sql
-- Eliminar tabla
DROP TABLE nombre_tabla;
DROP TABLE IF EXISTS nombre_tabla;

-- Truncar tabla (eliminar datos, mantener estructura)
TRUNCATE TABLE nombre_tabla;
```

---

### Comandos DML (Data Manipulation Language)

#### SELECT - Consultar Datos

```sql
-- Selección básica
SELECT * FROM tabla;

-- Selección con columnas específicas
SELECT columna1, columna2 FROM tabla;

-- Con alias
SELECT columna1 AS col1, columna2 AS col2 FROM tabla;

-- Limitar resultados
SELECT * FROM tabla LIMIT 10;

-- Eliminar duplicados
SELECT DISTINCT columna FROM tabla;
```

#### WHERE - Filtrar

```sql
-- Filtro simple
SELECT * FROM tabla WHERE edad > 18;

-- Múltiples condiciones
SELECT * FROM tabla WHERE edad > 18 AND ciudad = 'Madrid';

-- Con OR
SELECT * FROM tabla WHERE ciudad = 'Madrid' OR ciudad = 'Barcelona';

-- Con IN
SELECT * FROM tabla WHERE ciudad IN ('Madrid', 'Barcelona', 'Valencia');

-- Con LIKE (patrones)
SELECT * FROM tabla WHERE nombre LIKE 'Juan%';    -- Empieza con Juan
SELECT * FROM tabla WHERE nombre LIKE '%ez';      -- Termina con ez
SELECT * FROM tabla WHERE nombre LIKE '%ar%';     -- Contiene ar

-- Con BETWEEN
SELECT * FROM tabla WHERE edad BETWEEN 18 AND 65;

-- Con NULL
SELECT * FROM tabla WHERE columna IS NULL;
SELECT * FROM tabla WHERE columna IS NOT NULL;
```

#### ORDER BY - Ordenar

```sql
-- Orden ascendente
SELECT * FROM tabla ORDER BY columna ASC;

-- Orden descendente
SELECT * FROM tabla ORDER BY columna DESC;

-- Múltiples columnas
SELECT * FROM tabla ORDER BY columna1 DESC, columna2 ASC;
```

#### GROUP BY - Agrupar

```sql
-- Agrupar simple
SELECT categoria, COUNT(*) FROM tabla GROUP BY categoria;

-- Con múltiples agregaciones
SELECT 
    categoria,
    COUNT(*) AS cantidad,
    AVG(precio) AS precio_promedio,
    SUM(ventas) AS total_ventas
FROM tabla
GROUP BY categoria;

-- Con múltiples columnas
SELECT ciudad, categoria, COUNT(*) 
FROM tabla 
GROUP BY ciudad, categoria;

-- Con HAVING (filtrar después de agrupar)
SELECT categoria, COUNT(*) AS cantidad
FROM tabla
GROUP BY categoria
HAVING COUNT(*) > 100;
```

#### Funciones de Agregación

```sql
-- COUNT (contar)
SELECT COUNT(*) FROM tabla;
SELECT COUNT(DISTINCT columna) FROM tabla;

-- SUM (sumar)
SELECT SUM(precio) FROM ventas;

-- AVG (promedio)
SELECT AVG(edad) FROM usuarios;

-- MIN / MAX
SELECT MIN(precio), MAX(precio) FROM productos;

-- STDDEV (desviación estándar)
SELECT STDDEV(calificacion) FROM ratings;

-- PERCENTILE_APPROX (percentiles)
SELECT PERCENTILE_APPROX(precio, 0.5) AS mediana FROM productos;
```

#### INSERT - Insertar Datos

```sql
-- Insertar valores individuales
INSERT INTO tabla VALUES (1, 'Juan', 25);

-- Insertar múltiples filas
INSERT INTO tabla VALUES 
    (1, 'Juan', 25),
    (2, 'María', 30),
    (3, 'Pedro', 35);

-- Insertar desde consulta
INSERT INTO tabla_destino
SELECT * FROM tabla_origen WHERE condicion;

-- Sobrescribir tabla
INSERT OVERWRITE TABLE tabla
SELECT * FROM otra_tabla;
```

#### LOAD - Cargar Archivos

```sql
-- Cargar desde archivo local
LOAD DATA LOCAL INPATH '/home/hadoop/datos.csv' 
INTO TABLE nombre_tabla;

-- Cargar desde HDFS
LOAD DATA INPATH '/hdfs/ruta/datos.csv'
INTO TABLE nombre_tabla;

-- Sobrescribir datos existentes
LOAD DATA LOCAL INPATH '/home/hadoop/datos.csv'
OVERWRITE INTO TABLE nombre_tabla;

-- Cargar en partición específica
LOAD DATA INPATH '/hdfs/datos.csv'
INTO TABLE ventas
PARTITION (year=2024, month=01);
```

---

### Funciones de Cadena

```sql
-- Concatenar
SELECT CONCAT(nombre, ' ', apellido) FROM usuarios;

-- Longitud
SELECT LENGTH(nombre) FROM usuarios;

-- Mayúsculas / Minúsculas
SELECT UPPER(nombre), LOWER(nombre) FROM usuarios;

-- Substring
SELECT SUBSTR(nombre, 1, 3) FROM usuarios;

-- Reemplazar
SELECT REPLACE(texto, 'viejo', 'nuevo') FROM tabla;

-- Trim (quitar espacios)
SELECT TRIM(nombre) FROM usuarios;
```

---

### Funciones de Fecha

```sql
-- Fecha actual
SELECT CURRENT_DATE();
SELECT CURRENT_TIMESTAMP();

-- Extraer partes de fecha
SELECT YEAR(fecha), MONTH(fecha), DAY(fecha) FROM tabla;

-- Convertir Unix timestamp a fecha
SELECT FROM_UNIXTIME(timestamp_unix) FROM tabla;

-- Formatear fecha
SELECT FROM_UNIXTIME(timestamp_unix, 'yyyy-MM-dd') FROM tabla;

-- Convertir fecha a Unix timestamp
SELECT UNIX_TIMESTAMP('2024-01-15 10:30:00');

-- Diferencia entre fechas
SELECT DATEDIFF('2024-12-31', '2024-01-01');

-- Agregar días a fecha
SELECT DATE_ADD('2024-01-01', 30);
```

---

### Window Functions (Funciones de Ventana)

```sql
-- ROW_NUMBER (numerar filas)
SELECT 
    nombre,
    ROW_NUMBER() OVER (ORDER BY precio DESC) AS ranking
FROM productos;

-- RANK (ranking con empates)
SELECT 
    nombre,
    precio,
    RANK() OVER (ORDER BY precio DESC) AS ranking
FROM productos;

-- PARTITION BY (ventanas por grupo)
SELECT 
    categoria,
    producto,
    precio,
    RANK() OVER (PARTITION BY categoria ORDER BY precio DESC) AS ranking_categoria
FROM productos;

-- LAG / LEAD (valor anterior/siguiente)
SELECT 
    fecha,
    ventas,
    LAG(ventas, 1) OVER (ORDER BY fecha) AS ventas_dia_anterior
FROM ventas_diarias;
```

---

### Configuración en Hive

```sql
-- Ver configuración
SET;

-- Ver variable específica
SET hive.exec.mode.local.auto;

-- Cambiar configuración
SET hive.exec.mode.local.auto=false;
SET mapreduce.framework.name=yarn;

-- Configuraciones comunes para optimización
SET hive.exec.dynamic.partition=true;
SET hive.exec.dynamic.partition.mode=nonstrict;
SET hive.exec.max.dynamic.partitions=100000;
SET hive.exec.parallel=true;
SET hive.exec.compress.output=true;
```

---

### Comandos de Sistema en Hive

```sql
-- Ejecutar comando shell
!ls /home/hadoop;
!pwd;

-- Ver sistema de archivos HDFS
dfs -ls /;
dfs -cat /datos/archivo.txt;

-- Salir de Hive
exit;
quit;
```

---

### `schematool` - Gestión de Metastore

**Descripción:** Herramienta para inicializar y gestionar el metastore de Hive

**Sintaxis:**
```bash
schematool [opciones]
```

**Comandos Comunes:**

```bash
# Inicializar esquema (primera vez)
schematool -initSchema -dbType derby

# Inicializar con MySQL
schematool -initSchema -dbType mysql

# Ver información del esquema
schematool -info -dbType derby

# Validar esquema
schematool -validate -dbType derby

# Upgrade de esquema
schematool -upgradeSchema -dbType derby
```

---

---

## 🔥 COMANDOS DE FIREWALL

---

### `firewall-cmd` - Gestión de Firewall

**Descripción:** Herramienta para configurar el firewall en CentOS/RHEL

**Sintaxis:**
```bash
firewall-cmd [opciones] [comando]
```

**Comandos Principales:**

```bash
# Ver estado del firewall
sudo firewall-cmd --state

# Listar puertos abiertos
sudo firewall-cmd --list-ports

# Listar servicios permitidos
sudo firewall-cmd --list-services

# Ver toda la configuración
sudo firewall-cmd --list-all

# Abrir puerto TCP
sudo firewall-cmd --zone=public --add-port=9000/tcp --permanent

# Abrir puerto UDP
sudo firewall-cmd --zone=public --add-port=9000/udp --permanent

# Abrir rango de puertos
sudo firewall-cmd --zone=public --add-port=8000-9000/tcp --permanent

# Cerrar puerto
sudo firewall-cmd --zone=public --remove-port=9000/tcp --permanent

# Agregar servicio
sudo firewall-cmd --zone=public --add-service=http --permanent

# Recargar configuración (aplicar cambios)
sudo firewall-cmd --reload

# Ver zonas disponibles
sudo firewall-cmd --get-zones

# Ver zona activa
sudo firewall-cmd --get-active-zones

# Ver configuración de zona específica
sudo firewall-cmd --zone=public --list-all
```

**Puertos de Hadoop:**

```bash
# HDFS NameNode (default)
sudo firewall-cmd --zone=public --add-port=9000/tcp --permanent

# HDFS NameNode Web UI
sudo firewall-cmd --zone=public --add-port=9870/tcp --permanent

# YARN ResourceManager Web UI
sudo firewall-cmd --zone=public --add-port=8088/tcp --permanent

# Job History Server
sudo firewall-cmd --zone=public --add-port=19888/tcp --permanent

# Recargar después de agregar todos
sudo firewall-cmd --reload
```

**Opciones:**
- `--permanent` : Hacer cambio persistente (sobrevive reinicios)
- `--zone=public` : Especificar zona (public, dmz, work, home, etc.)
- `--reload` : Aplicar cambios sin reiniciar firewall

---

---

## 🌐 COMANDOS DE RED

---

### `ping` - Probar Conectividad

**Descripción:** Envía paquetes ICMP para verificar conectividad

**Sintaxis:**
```bash
ping [opciones] destino
```

**Opciones:**
- `-c` : Número de paquetes a enviar
- `-i` : Intervalo entre paquetes

**Ejemplos:**

```bash
# Ping básico (Ctrl+C para detener)
ping google.com

# Enviar solo 4 paquetes
ping -c 4 8.8.8.8

# Ping con intervalo de 2 segundos
ping -i 2 google.com

# Ping a IP local
ping 192.168.1.1
```

**Salida:**
```
PING google.com (172.217.0.0) 56(84) bytes of data.
64 bytes from lga25s60-in-f0.1e100.net: icmp_seq=1 ttl=117 time=10.2 ms
64 bytes from lga25s60-in-f0.1e100.net: icmp_seq=2 ttl=117 time=9.8 ms
```

---

### `ifconfig` - Configuración de Interfaces de Red

**Descripción:** Muestra y configura interfaces de red

**Sintaxis:**
```bash
ifconfig [interfaz] [opciones]
```

**Ejemplos:**

```bash
# Ver todas las interfaces
ifconfig

# Ver interfaz específica
ifconfig enp0s3

# Activar interfaz
sudo ifconfig enp0s3 up

# Desactivar interfaz
sudo ifconfig enp0s3 down

# Asignar IP
sudo ifconfig enp0s3 192.168.1.100 netmask 255.255.255.0
```

**Salida:**
```
enp0s3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.0.2.15  netmask 255.255.255.0  broadcast 10.0.2.255
        inet6 fe80::a00:27ff:fe8d:c04d  prefixlen 64  scopeid 0x20<link>
        ether 08:00:27:8d:c0:4d  txqueuelen 1000  (Ethernet)
```

**Interpretación:**
- `inet 10.0.2.15` : Dirección IP
- `netmask 255.255.255.0` : Máscara de red
- `ether 08:00:27:8d:c0:4d` : Dirección MAC

---

### `ip` - Gestión de Red (Moderno)

**Descripción:** Reemplazo moderno de ifconfig

**Sintaxis:**
```bash
ip [objeto] [comando]
```

**Comandos Comunes:**

```bash
# Ver interfaces
ip addr
ip a

# Ver interfaz específica
ip addr show enp0s3

# Ver solo IPs
ip -4 addr

# Activar/desactivar interfaz
sudo ip link set enp0s3 up
sudo ip link set enp0s3 down

# Asignar IP
sudo ip addr add 192.168.1.100/24 dev enp0s3

# Eliminar IP
sudo ip addr del 192.168.1.100/24 dev enp0s3

# Ver rutas
ip route
ip r

# Agregar ruta
sudo ip route add default via 192.168.1.1
```

---

### `nmcli` - Network Manager CLI

**Descripción:** Herramienta de línea de comandos para NetworkManager

**Sintaxis:**
```bash
nmcli [objeto] [comando]
```

**Comandos Comunes:**

```bash
# Ver estado general
nmcli general status

# Ver dispositivos
nmcli device status

# Ver conexiones
nmcli connection show

# Conectar interfaz
sudo nmcli device connect enp0s3

# Desconectar interfaz
sudo nmcli device disconnect enp0s3

# Ver detalles de conexión
nmcli connection show "System enp0s3"

# Modificar conexión (autoconnect)
sudo nmcli connection modify "System enp0s3" connection.autoconnect yes

# Activar conexión
sudo nmcli connection up "System enp0s3"

# Ver hostname
nmcli general hostname

# Cambiar hostname
sudo nmcli general hostname nuevo_nombre
```

---

### `nmtui` - Network Manager TUI

**Descripción:** Interfaz de texto interactiva para configurar red

**Sintaxis:**
```bash
nmtui
```

**Opciones del Menú:**
1. **Edit a connection** : Modificar conexiones existentes
2. **Activate a connection** : Activar/desactivar conexiones
3. **Set system hostname** : Cambiar nombre del host

**Navegación:**
- `Tab` : Moverse entre opciones
- `Espacio` : Seleccionar/deseleccionar
- `Enter` : Confirmar
- `Esc` : Volver/cancelar

---

### `dhclient` - Cliente DHCP

**Descripción:** Solicita configuración de red vía DHCP

**Sintaxis:**
```bash
dhclient [interfaz]
```

**Ejemplos:**

```bash
# Solicitar IP para todas las interfaces
sudo dhclient

# Para interfaz específica
sudo dhclient enp0s3

# Liberar IP actual
sudo dhclient -r enp0s3

# Renovar IP
sudo dhclient -r enp0s3 && sudo dhclient enp0s3
```

---

### `hostname` - Ver/Cambiar Nombre del Host

**Descripción:** Muestra o cambia el nombre del host

**Sintaxis:**
```bash
hostname [nuevo_nombre]
```

**Ejemplos:**

```bash
# Ver hostname actual
hostname

# Ver FQDN (Fully Qualified Domain Name)
hostname -f

# Ver IP asociada
hostname -I

# Cambiar hostname (temporal)
sudo hostname nuevo_nombre

# Cambiar hostname (permanente con nmcli)
sudo nmcli general hostname nuevo_nombre
```

---

### `netstat` - Estadísticas de Red

**Descripción:** Muestra conexiones de red, tablas de enrutamiento, estadísticas

**Sintaxis:**
```bash
netstat [opciones]
```

**Opciones:**
- `-t` : Conexiones TCP
- `-u` : Conexiones UDP
- `-l` : Puertos en escucha (listening)
- `-p` : Mostrar proceso
- `-n` : Mostrar números (no resolver nombres)

**Ejemplos:**

```bash
# Ver todas las conexiones
netstat -a

# Ver puertos en escucha
netstat -tuln

# Ver con procesos
sudo netstat -tulpn

# Ver estadísticas
netstat -s

# Ver tabla de enrutamiento
netstat -r
```

**Salida:**
```
Proto  Recv-Q Send-Q Local Address    Foreign Address  State       PID/Program
tcp    0      0      0.0.0.0:9000     0.0.0.0:*        LISTEN      12345/java
tcp    0      0      0.0.0.0:8088     0.0.0.0:*        LISTEN      12348/java
```

---

### `telnet` - Cliente Telnet (Probar Puertos)

**Descripción:** Cliente para probar conectividad a puertos específicos

**Sintaxis:**
```bash
telnet host puerto
```

**Ejemplos:**

```bash
# Probar puerto 9000 (HDFS)
telnet localhost 9000

# Probar puerto 8088 (YARN)
telnet localhost 8088

# Probar conexión remota
telnet 192.168.1.100 22

# Salir de telnet
Ctrl + ]
quit
```

**Uso para diagnóstico:**
- Si conecta: "Connected to localhost" → Puerto abierto ✅
- Si falla: "Connection refused" → Puerto cerrado o servicio no corriendo ❌

---

### `wget` - Descargar Archivos

**Descripción:** Descarga archivos desde internet vía HTTP/HTTPS/FTP

**Sintaxis:**
```bash
wget [opciones] URL
```

**Opciones:**
- `-O` : Guardar con nombre específico
- `-c` : Continuar descarga interrumpida
- `-q` : Modo silencioso
- `-P` : Guardar en directorio específico

**Ejemplos:**

```bash
# Descarga simple
wget https://dlcdn.apache.org/hadoop/common/hadoop-3.4.0/hadoop-3.4.0.tar.gz

# Con nombre personalizado
wget -O hadoop.tar.gz https://dlcdn.apache.org/hadoop/common/hadoop-3.4.0/hadoop-3.4.0.tar.gz

# Continuar descarga interrumpida
wget -c https://example.com/archivo_grande.iso

# Descargar en directorio específico
wget -P /opt/descargas/ https://example.com/archivo.tar.gz

# Modo silencioso
wget -q https://example.com/archivo.txt

# Ver solo headers
wget --spider https://example.com/archivo.txt
```

---

### `curl` - Transferir Datos desde/hacia Servidor

**Descripción:** Herramienta para transferir datos con URLs (más versátil que wget)

**Sintaxis:**
```bash
curl [opciones] URL
```

**Ejemplos:**

```bash
# Ver contenido de URL
curl https://example.com

# Descargar archivo
curl -O https://example.com/archivo.tar.gz

# Descargar con nombre personalizado
curl -o mi_archivo.tar.gz https://example.com/archivo.tar.gz

# Ver headers HTTP
curl -I https://example.com

# Seguir redirecciones
curl -L https://example.com

# POST request
curl -X POST -d "dato1=valor1&dato2=valor2" https://api.example.com/endpoint

# Con autenticación
curl -u usuario:password https://example.com/api

# Ver progreso
curl -# -O https://example.com/archivo_grande.iso
```

---

---

## 📊 RESUMEN DE COMANDOS POR CASO DE USO

---

### 🚀 Iniciar/Detener Hadoop

```bash
# Iniciar todo
start-all.sh

# Detener todo
stop-all.sh

# Solo HDFS
start-dfs.sh
stop-dfs.sh

# Solo YARN
start-yarn.sh
stop-yarn.sh

# Verificar servicios
jps
```

---

### 📁 Operaciones Básicas en HDFS

```bash
# Crear directorio
hdfs dfs -mkdir /carpeta

# Subir archivo
hdfs dfs -put archivo.txt /carpeta/

# Listar archivos
hdfs dfs -ls /carpeta

# Ver archivo
hdfs dfs -cat /carpeta/archivo.txt

# Descargar archivo
hdfs dfs -get /carpeta/archivo.txt ./

# Eliminar
hdfs dfs -rm -r /carpeta
```

---

### 🧵 Monitoreo de YARN

```bash
# Listar aplicaciones
yarn application -list

# Ver estado de aplicación
yarn application -status application_ID

# Ver logs
yarn logs -applicationId application_ID

# Ver nodos
yarn node -list
```

---

### 🐝 Operaciones Básicas en Hive

```bash
# Iniciar Hive
hive

# Ver bases de datos
SHOW DATABASES;

# Crear tabla
CREATE TABLE tabla (id INT, nombre STRING);

# Insertar datos
INSERT INTO tabla VALUES (1, 'Juan');

# Consultar
SELECT * FROM tabla;

# Salir
quit;
```

---

### 🔥 Configurar Firewall

```bash
# Abrir puertos Hadoop
sudo firewall-cmd --zone=public --add-port=9000/tcp --permanent   # HDFS
sudo firewall-cmd --zone=public --add-port=9870/tcp --permanent   # HDFS Web
sudo firewall-cmd --zone=public --add-port=8088/tcp --permanent   # YARN Web

# Aplicar cambios
sudo firewall-cmd --reload

# Verificar
sudo firewall-cmd --list-ports
```

---

### 🌐 Diagnóstico de Red

```bash
# Ver IP
ifconfig
ip addr

# Probar conectividad
ping -c 4 8.8.8.8

# Ver puertos abiertos
sudo netstat -tulpn

# Probar puerto específico
telnet localhost 9000

# Ver hostname
hostname

# Ver DNS
cat /etc/hosts
```

---

## 💡 Tips de Uso

### Variables de Entorno Importantes

```bash
echo $JAVA_HOME        # /opt/jdk1.8.0_202
echo $HADOOP_HOME      # /opt/hadoop/hadoop-3.4.0
echo $HIVE_HOME        # /opt/hive
echo $PATH             # Rutas de ejecutables
```

### Comandos de Ayuda

```bash
# Ayuda de comando específico
hadoop --help
hdfs dfs --help
yarn --help
hive --help

# Manual de comando
man comando

# Información de comando
info comando

# Ver ubicación de ejecutable
which hadoop
which java
```

### Atajos de Terminal

```bash
Ctrl + C    # Cancelar comando en ejecución
Ctrl + Z    # Suspender comando (background)
Ctrl + D    # Salir de sesión / EOF
Ctrl + L    # Limpiar pantalla
Ctrl + R    # Buscar en historial de comandos
Ctrl + A    # Ir al inicio de línea
Ctrl + E    # Ir al final de línea
!!          # Repetir último comando
!n          # Ejecutar comando número n del historial
history     # Ver historial de comandos
```

---

<div align="center">

## 🎓 Fin de la Guía de Comandos

**Referencia Completa para Hadoop, HDFS, YARN y Hive**

✅ 100+ comandos explicados con ejemplos  
✅ Sintaxis detallada y opciones  
✅ Casos de uso prácticos  
✅ Salidas esperadas  
✅ Tips y trucos  

---

*Guía de Comandos - Sistemas Inteligentes*  
*Febrero 2026*

</div>
