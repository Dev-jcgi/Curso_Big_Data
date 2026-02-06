# 🚀 Instalación: CentOS + Hadoop 3.4.0 + Hive 3.1.3

> **Curso:** Sistemas Inteligentes
> **Objetivo:** Configurar un entorno Big Data completo desde cero
> **Modo:** Pseudo-distribuido (un solo nodo)
> **Fecha:** Febrero 2026

---

## 📋 Tabla de Contenido

### PARTE I: Configuración de CentOS

1. [Requisitos Previos y Descargas](#1-requisitos-previos-y-descargas)
2. [Configuración de Red](#2-configuración-de-red-y-actualización)
3. [Instalación de Interfaz Gráfica](#3-instalación-de-interfaz-gráfica-gui)
4. [VirtualBox Guest Additions](#4-instalación-de-virtualbox-guest-additions)

### PARTE II: Instalación de Hadoop 3.4.0

5. [Instalación de Paquetes Base](#5-instalación-de-paquetes-base)
6. [Crear Usuario Hadoop](#6-crear-usuario-hadoop)
7. [Configurar SSH para Usuario Hadoop](#7-configurar-ssh-para-usuario-hadoop)
8. [Instalación de Java 8](#8-instalación-de-java-8-openjdk)
9. [Registrar Java en el Sistema](#9-registrar-jdk-en-el-sistema-de-alternativas)
10. [Configurar Variables de Entorno Java](#10-configurar-java_home)
11. [Descargar Hadoop](#11-descarga-de-hadoop-340)
12. [Descomprimir Hadoop](#12-descompresión-y-organización)
13. [Variables de Entorno Hadoop](#13-configurar-variables-de-entorno-hadoop)
14. [Configurar Hostname](#14-cambiar-hostname-a-nodo1)
15. [Configurar core-site.xml](#15-configurar-core-sitexml)
16. [Configurar hdfs-site.xml](#16-configurar-hdfs-sitexml-y-crear-carpetas)
17. [Formatear NameNode](#17-formatear-el-namenode)
18. [Verificar Interfaz Web HDFS](#18-interfaz-web-browse-directory)
19. [Operaciones Básicas HDFS](#19-operaciones-básicas-en-hdfs)
20. [Configurar YARN](#20-configuración-de-yarn)
21. [Ejecutar MapReduce](#21-ejecutar-mapreduce-wordcount)

### PARTE III: Instalación de Apache Hive

22. [Instalar Hive 3.1.3](#22-instalación-de-apache-hive-313)
23. [Configurar Variables Hive](#23-configurar-variables-de-entorno-hive)
24. [Resolver Conflictos de Librerías](#24-resolver-conflictos-de-librerías-guava)
25. [Configurar Almacenamiento HDFS](#25-configurar-directorios-hdfs-para-hive)
26. [Configurar Metastore](#26-configurar-hive-sitexml-metastore)
27. [Inicializar Esquema](#27-inicializar-esquema-de-metadatos)
28. [Probar Hive](#28-ejecutar-y-probar-hive)

---

## 🎯 PARTE I: CONFIGURACIÓN DE CENTOS

---

## 1. Requisitos Previos y Descargas

### 📥 Software Necesario

| Componente                      | Fuente                                         | Descripción                    |
| ------------------------------- | ---------------------------------------------- | ------------------------------- |
| **CentOS 10 (Imagen VM)** | https://www.osboxes.org/centos/#centos-10-info | Máquina virtual preconfigurada |
| **VirtualBox**            | https://www.virtualbox.org/wiki/Downloads      | Software de virtualización     |

### ⚙️ Configuración Inicial

1. **Descomprimir** la unidad de máquina virtual descargada
2. **Abrir VirtualBox** → Nueva Máquina Virtual
3. Seleccionar: **"Usar un archivo de disco duro virtual existente"**
4. **Red**: Configurar como **Adaptador Puente** o **NAT**
5. **Credenciales por defecto:**
   - Usuario: `osboxes`
   - Password: `osboxes.org`

---

## 2. Configuración de Red y Actualización

### 🔄 Actualizar el Sistema

```bash
sudo yum update
```

O alternativamente:

```bash
sudo dnf update
```

---

### 🌐 Solución de Problemas de Conexión

#### Paso 1: Probar Conectividad

```bash
ping -c 4 8.8.8.8
```

**Si recibes:** `Network is unreachable` → Tu interfaz de red está desactivada

---

#### Paso 2: Identificar y Activar la Interfaz

```bash
# Ver interfaces disponibles
ip addr
```

Busca tu interfaz (generalmente `enp0s3`):

```bash
# Método 1: Con nmcli
sudo nmcli device connect enp0s3

# Método 2: Con ip link
sudo ip link set enp0s3 up
```

---

#### Paso 3: Configurar Inicio Automático (NMTUI)

```bash
# Abrir configurador de red
sudo nmtui
```

**Navegación en NMTUI:**

1. Seleccionar: **"Edit a connection"**
2. Seleccionar tu interfaz (ej: `enp0s3`)
3. Seleccionar: **"Edit"**
4. Marcar con `[X]`: **"Automatically connect"**
5. **OK** → **Back** → **Quit**

---

#### Paso 4: Solicitar IP (DHCP)

```bash
sudo dhclient
```

**Verificar conexión:**

```bash
ping -c 4 google.com
```

---

## 3. Instalación de Interfaz Gráfica (GUI)

> **Nota:** Si prefieres trabajar con entorno gráfico en lugar de solo terminal

### 📦 Instalar GNOME Desktop

```bash
# Instalar grupo de paquetes (800MB - 1GB)
sudo dnf groupinstall "Server with GUI"
```

---

### 🖥️ Cambiar Modo de Arranque

```bash
# Verificar modo actual
systemctl get-default

# Cambiar a modo gráfico
sudo systemctl set-default graphical.target

# Iniciar interfaz gráfica ahora (sin reiniciar)
sudo systemctl start graphical.target
```

---

## 4. Instalación de VirtualBox Guest Additions

> **Beneficios:** Mejor resolución de pantalla, compartir carpetas, portapapeles compartido

### 📦 Paso A: Instalar Dependencias

```bash
# Instalar repositorio EPEL
sudo dnf install -y epel-release

# Instalar herramientas de desarrollo
sudo dnf install -y gcc make perl kernel-devel kernel-headers bzip2

# Actualizar kernel (IMPORTANTE)
sudo dnf update kernel-*
```

> ⚠️ **Si el kernel se actualiza, reinicia la VM antes de continuar:**

```bash
sudo reboot
```

---

### 💿 Paso B: Montar e Instalar Guest Additions

**En el menú de VirtualBox:**

- **Dispositivos** → **Insertar imagen de CD de las Guest Additions**

**En la terminal de CentOS:**

```bash
# Crear punto de montaje
sudo mkdir -p /mnt/cdrom

# Montar el CD
sudo mount /dev/cdrom /mnt/cdrom

# Ejecutar instalador
cd /mnt/cdrom
sudo ./VBoxLinuxAdditions.run

# Reiniciar para aplicar cambios
sudo reboot
```

---

### ⚠️ Solución de Errores Comunes

Si la instalación falla, ejecuta estos comandos de refuerzo:

```bash
# Instalar herramientas de desarrollo completas
sudo dnf groupinstall "Development Tools"
sudo dnf install kernel-devel dkms

# Instalar módulos específicos de VirtualBox
sudo dnf install centos-release-kmods
sudo dnf install virtualbox-guest-additions

# Habilitar y arrancar el servicio
sudo systemctl enable vboxservice
sudo systemctl start vboxservice
```

---

### 💡 Tip de Estudio

> Crea **snapshots (instantáneas)** en VirtualBox antes de realizar cambios críticos para poder volver atrás si algo falla.

---

---

## 🐘 PARTE II: INSTALACIÓN DE HADOOP 3.4.0

> **Basado en:** Playlist "Big Data Hadoop Español" 🔥
> **Enlace:** https://www.youtube.com/playlist?list=PLG1t8jaLbxA_DG_cmlBYgkGW-TZw5DP3B

---

## 5. Instalación de Paquetes Base

> **Video #5:** https://www.youtube.com/watch?v=49f8rpFV8BY&list=PLG1t8jaLbxA_DG_cmlBYgkGW-TZw5DP3B&index=5

### 📦 Instalar Herramientas de Red

```bash
# Herramientas de red (ifconfig, netstat, etc.)
sudo yum install net-tools

# Wget para descargas desde internet
sudo yum install wget

# Telnet para pruebas de conectividad
sudo yum install telnet
```

---

### 🌐 Obtener IP de la Máquina Virtual

```bash
ifconfig
```

**Anota tu IP** (algo como `10.0.2.15` o `192.168.x.x`), la necesitarás más adelante.

---

## 6. Crear Usuario Hadoop

> **Video #6:** https://www.youtube.com/watch?v=B9x3v4fD-4E&list=PLG1t8jaLbxA_DG_cmlBYgkGW-TZw5DP3B&index=6

### 👤 Crear Usuario y Asignar Permisos

```bash
# Crear el usuario hadoop
sudo useradd hadoop

# Asignar contraseña
sudo passwd hadoop
# (Ingresa la contraseña dos veces - los caracteres no se verán)
```

---

### 🔐 Darle Permisos de Administrador (Sudo/Root)

> **⚠️ IMPORTANTE:** Esto permite que el usuario hadoop ejecute comandos con privilegios de root usando `sudo`.
> **Recomendación:** Solo para entornos de **desarrollo/aprendizaje**. En **producción**, limitar permisos específicos.

```bash
# OPCIÓN 1: Agregar al grupo wheel (método recomendado en CentOS/RHEL)
sudo usermod -aG wheel hadoop

# OPCIÓN 2: Agregar entrada en sudoers (método alternativo)
# Editar archivo sudoers de forma segura
sudo visudo

# Agregar esta línea al final del archivo:
# hadoop ALL=(ALL) NOPASSWD: ALL
# (NOPASSWD permite ejecutar sudo sin pedir contraseña - útil para scripts)

# OPCIÓN 3: Sin contraseña solo para comandos específicos
# hadoop ALL=(ALL) NOPASSWD: /opt/hadoop/hadoop-3.4.0/sbin/*, /usr/bin/systemctl
```

**💡 Explicación de Opciones:**

| Opción                                     | Seguridad | Uso                    | Requiere Password |
| ------------------------------------------- | --------- | ---------------------- | ----------------- |
| **Opción 1** (wheel)                 | Media     | Desarrollo/Producción | ✅ Sí            |
| **Opción 2** (NOPASSWD: ALL)         | Baja      | Solo desarrollo        | ❌ No             |
| **Opción 3** (comandos específicos) | Alta      | Producción            | ❌ No             |

**✅ Verificar permisos:**

```bash
# Ver grupos del usuario hadoop
groups hadoop
# Esperado: hadoop wheel

# Probar sudo (desde usuario hadoop)
sudo whoami
# Esperado: root

# Verificar entrada en sudoers
sudo grep hadoop /etc/sudoers
```

---

### 📁 Crear Carpeta y Asignar Permisos

```bash
# Crear carpeta en /opt
sudo mkdir /opt/hadoop

# Cambiar propietario al usuario hadoop
sudo chown -R hadoop:hadoop /opt/hadoop

# Dar permisos de lectura, escritura y ejecución
sudo chmod -R 755 /opt/hadoop
```

---

### 🔄 Cambiar al Usuario Hadoop

```bash
# Cambiar de root a hadoop (el guion '-' es importante)
su - hadoop
```

> **Tip:** El guion `-` carga las variables de entorno y te sitúa en `/home/hadoop`

---

## 7. Configurar SSH para Usuario Hadoop

> **⚠️ CRÍTICO:** Esta configuración **DEBE** hacerse con el **usuario hadoop**, NO con root u otro usuario.
> **Si no se configura correctamente, YARN y HDFS NO podrán iniciar los servicios** y aparecerán errores de "Permission denied".

### 🔑 ¿Por Qué es Necesario SSH?

Hadoop utiliza SSH para comunicarse entre procesos (NameNode, DataNode, ResourceManager, NodeManager) incluso en modo pseudo-distribuido (un solo nodo). Sin SSH configurado, los servicios no pueden iniciarse automáticamente.

---

### 📋 Pasos de Configuración

> **IMPORTANTE:** Ejecutar TODOS estos comandos como **usuario hadoop**

```bash
# Asegurarse de estar como usuario hadoop
whoami
# Debe mostrar: hadoop

# Si no estás como hadoop, cambiar:
su - hadoop
```

---

### Paso 1: Generar la Llave RSA

Este comando crea la identidad digital del usuario. El parámetro `-P ""` asegura que no se pida una frase de paso, lo cual es vital para la automatización de Hadoop.

```bash
ssh-keygen -t rsa -P "" -f ~/.ssh/id_rsa
```

**Salida esperada:**

```
Generating public/private rsa key pair.
Created directory '/home/hadoop/.ssh'.
Your identification has been saved in /home/hadoop/.ssh/id_rsa
Your public key has been saved in /home/hadoop/.ssh/id_rsa.pub
```

---

### Paso 2: Configurar el Acceso de Confianza (Self-Login)

Hadoop necesita conectarse a `localhost` y al hostname (en este caso `nodo1`) para levantar los demonios de HDFS y YARN.

```bash
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

**💡 Explicación:** Este comando agrega tu llave pública a la lista de llaves autorizadas, permitiendo que el usuario hadoop se conecte a sí mismo sin contraseña.

---

### Paso 3: Establecer Permisos de Seguridad Estrictos

SSH ignorará las llaves si los permisos son demasiado abiertos, manteniendo el error de "Permission denied".

```bash
# Permisos del directorio .ssh (solo el propietario puede acceder)
chmod 700 ~/.ssh

# Permisos del archivo de llaves autorizadas (solo el propietario puede leer/escribir)
chmod 600 ~/.ssh/authorized_keys
```

---

### Paso 4: Validar el Acceso al Hostname

Es crucial que realices una conexión manual **la primera vez** para registrar el "fingerprint" del nodo en el archivo `known_hosts`. Ejecuta estos comandos y **escribe `yes`** cuando se te pregunte:

```bash
# Conectar a localhost
ssh localhost
# Escribir: yes
# Salir de la sesión SSH
exit

# Conectar a nodo1 (hostname que configurarás después)
ssh nodo1
# Escribir: yes
# Salir de la sesión SSH
exit
```

**Mensaje esperado en primera conexión:**

```
The authenticity of host 'localhost (127.0.0.1)' can't be established.
RSA key fingerprint is SHA256:xxxxxxxxxxxxxxxxxxxxxxxxxxxx.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'localhost' (RSA) to the list of known hosts.
```

---

### ✅ Verificar Configuración SSH

```bash
# Probar conexión sin contraseña a localhost
ssh localhost "echo 'SSH funcionando correctamente'"
```

**Salida esperada:**

```
SSH funcionando correctamente
```

**Si NO pide contraseña y muestra el mensaje, la configuración es correcta.**

---

### 🔧 Verificar Archivos Creados

```bash
# Ver estructura del directorio .ssh
ls -la ~/.ssh/
```

**Salida esperada:**

```
drwx------  2 hadoop hadoop 4096 Feb  5 10:00 .
drwx------. 3 hadoop hadoop 4096 Feb  5 09:50 ..
-rw-------  1 hadoop hadoop 1679 Feb  5 10:00 id_rsa
-rw-r--r--  1 hadoop hadoop  394 Feb  5 10:00 id_rsa.pub
-rw-------  1 hadoop hadoop  394 Feb  5 10:00 authorized_keys
-rw-r--r--  1 hadoop hadoop  444 Feb  5 10:05 known_hosts
```

---

### ⚠️ Errores Comunes y Soluciones

#### Error: "Permission denied (publickey)"

**Causa:** Permisos incorrectos en archivos SSH

**Solución:**

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
```

---

#### Error: "Could not create directory '/home/hadoop/.ssh'"

**Causa:** No tienes permisos en tu directorio home

**Solución:**

```bash
# Como root, arreglar permisos
sudo chown -R hadoop:hadoop /home/hadoop
sudo chmod 755 /home/hadoop

# Volver a intentar como hadoop
su - hadoop
ssh-keygen -t rsa -P "" -f ~/.ssh/id_rsa
```

---

#### Error: "Host key verification failed"

**Causa:** No se ha aceptado el fingerprint del host

**Solución:**

```bash
# Conectarse manualmente y aceptar con 'yes'
ssh localhost
# Escribir: yes
exit
```

---

### 🎯 Resumen de Comandos (Copy-Paste Rápido)

```bash
# 1. Cambiar a usuario hadoop (si no lo estás)
su - hadoop

# 2. Generar llave RSA
ssh-keygen -t rsa -P "" -f ~/.ssh/id_rsa

# 3. Configurar acceso de confianza
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

# 4. Establecer permisos
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys

# 5. Validar acceso (aceptar con 'yes')
ssh localhost
exit

# 6. Verificar que funciona sin contraseña
ssh localhost "echo 'SSH OK'"
```

---

### 💡 Importancia para Hadoop

✅ **Con SSH configurado:**

- `start-dfs.sh` inicia NameNode, DataNode y SecondaryNameNode automáticamente
- `start-yarn.sh` inicia ResourceManager y NodeManager automáticamente
- Los servicios pueden comunicarse entre sí

❌ **Sin SSH configurado:**

- Los scripts de inicio fallan con "Permission denied"
- Necesitas iniciar cada servicio manualmente
- YARN no puede gestionar recursos correctamente
- MapReduce jobs fallarán

---

## 8. Instalación de Java 8 OpenJDK

> **Video #7:** https://www.youtube.com/watch?v=1rpJHLYim_k&list=PLG1t8jaLbxA_DG_cmlBYgkGW-TZw5DP3B&index=7

### 📥 Descargar Java 8

**Fuente:** https://www.oracle.com/latam/java/technologies/javase/javase8-archive-downloads.html

> **Nota:** Necesitas registrarte en Oracle para descargar

**Archivo a descargar:** `jdk-8u202-linux-x64.tar.gz`

---

### 📂 Mover y Descomprimir

```bash
# Mover el archivo desde Downloads a /opt
sudo mv /home/hadoop/Downloads/jdk-8u202-linux-x64.tar.gz /opt/

# Entrar a /opt
cd /opt/

# Descomprimir
sudo tar -zxvf jdk-8u202-linux-x64.tar.gz

# Verificar carpeta resultante
ls -l /opt/
# Deberías ver: jdk1.8.0_202
```

---

### 🔐 Asignar Permisos al Usuario Hadoop

```bash
sudo chown -R hadoop:hadoop /opt/jdk1.8.0_202
```

---

### 🧹 Limpiar Archivo Comprimido (Opcional)

```bash
# Eliminar el .tar.gz para liberar espacio
sudo rm /opt/jdk-8u202-linux-x64.tar.gz
```

---

## 9. Registrar JDK en el Sistema de Alternativas

> **Video #8:** https://www.youtube.com/watch?v=fNKL7IZs0LA&list=PLG1t8jaLbxA_DG_cmlBYgkGW-TZw5DP3B&index=8

### 🔧 Registrar Ejecutables de Java

```bash
# Registrar 'java'
sudo alternatives --install /usr/bin/java java /opt/jdk1.8.0_202/bin/java 2

# Configurar java como predeterminado
sudo alternatives --config java
# Selecciona el número correspondiente a /opt/jdk1.8.0_202/bin/java

# Registrar 'jar'
sudo alternatives --install /usr/bin/jar jar /opt/jdk1.8.0_202/bin/jar 2

# Registrar 'javac' (compilador - necesario para Hadoop)
sudo alternatives --install /usr/bin/javac javac /opt/jdk1.8.0_202/bin/javac 2
```

---

### ✅ Verificar Instalación

```bash
# Verificar versión de Java
java -version
# Salida esperada: java version "1.8.0_202"

# Verificar compilador
javac -version
# Salida esperada: javac 1.8.0_202
```

---

## 10. Configurar JAVA_HOME

> **Video #9:** https://www.youtube.com/watch?v=0VPMiQifwfo&list=PLG1t8jaLbxA_DG_cmlBYgkGW-TZw5DP3B&index=9

### 🔧 Editar .bashrc del Usuario Hadoop

```bash
# Cambiar al usuario hadoop
su - hadoop

# Ir al home
cd /home/hadoop/

# Editar archivo de configuración
nano .bashrc
```

---

### 📝 Agregar al Final del Archivo

```bash
# Configuración de Java
export JAVA_HOME=/opt/jdk1.8.0_202
export JRE_HOME=/opt/jdk1.8.0_202/jre
export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
```

**Guardar:** `Ctrl + O` → `Enter` → `Ctrl + X`

---

### 🔄 Recargar Variables de Entorno

```bash
source /home/hadoop/.bashrc

# Verificar que se cargó correctamente
echo $JAVA_HOME
# Salida esperada: /opt/jdk1.8.0_202
```

---

## 11. Descarga de Hadoop 3.4.0

> **Video #10:** https://www.youtube.com/watch?v=wi_DoY8jirI&list=PLG1t8jaLbxA_DG_cmlBYgkGW-TZw5DP3B&index=10

### 📥 Descargar Hadoop

```bash
# Entrar a la carpeta de hadoop
cd /opt/hadoop/

# Descargar con wget
wget https://dlcdn.apache.org/hadoop/common/hadoop-3.4.0/hadoop-3.4.0.tar.gz

# Verificar descarga
ls -lh hadoop-3.4.0.tar.gz
# Deberías ver el archivo (~600 MB)
```

---

## 12. Descompresión y Organización

> **Video #11:** https://www.youtube.com/watch?v=d3k-LZQgPoI&list=PLG1t8jaLbxA_DG_cmlBYgkGW-TZw5DP3B&index=11

### 📦 Descomprimir Hadoop

```bash
# Descomprimir en /opt/hadoop
tar -zxvf hadoop-3.4.0.tar.gz

# Verificar carpeta resultante
ls -l
# Deberías ver: hadoop-3.4.0/

# Eliminar archivo comprimido (opcional)
rm hadoop-3.4.0.tar.gz
```

---

## 13. Configurar Variables de Entorno Hadoop

> **Video #12:** https://www.youtube.com/watch?v=8jEmG80fG8U&list=PLG1t8jaLbxA_DG_cmlBYgkGW-TZw5DP3B&index=12

### 🔧 Editar .bashrc

```bash
# Ir al home del usuario hadoop
cd /home/hadoop/

# Editar .bashrc
nano .bashrc
```

---

### 📝 Agregar Variables de Hadoop

```bash
# Configuración de Hadoop
export HADOOP_HOME=/opt/hadoop/hadoop-3.4.0
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```

**Guardar:** `Ctrl + O` → `Enter` → `Ctrl + X`

---

### 🔄 Recargar y Verificar

```bash
# Recargar variables
source /home/hadoop/.bashrc

# Verificar versión de Hadoop
hadoop version
```

**Salida esperada:**

```
Hadoop 3.4.0
Source code repository https://github.com/apache/hadoop.git -r ...
Compiled by ... on ...
```

---

## 14. Cambiar Hostname a nodo1

> **Video #14:** https://www.youtube.com/watch?v=tYBytDlwWIQ&list=PLG1t8jaLbxA_DG_cmlBYgkGW-TZw5DP3B&index=14

### 🖥️ Configurar Nombre del Host

```bash
# Establecer hostname
sudo nmcli general hostname nodo1

# Verificar cambio
hostname
# Salida esperada: nodo1
```

---

### 📝 Actualizar Archivo /etc/hosts

```bash
# Editar archivo de hosts
sudo nano /etc/hosts
```

**Agregar o modificar estas líneas:**

```
127.0.0.1   localhost nodo1
::1         localhost nodo1
10.0.2.15   nodo1
```

> **Importante:** Reemplaza `10.0.2.15` con la IP que obtuviste con `ifconfig`

**Guardar:** `Ctrl + O` → `Enter` → `Ctrl + X`

---

## 15. Configurar core-site.xml

> **Video #16:** https://www.youtube.com/watch?v=rvloXElUp2w&list=PLG1t8jaLbxA_DG_cmlBYgkGW-TZw5DP3B&index=16

### 📝 Editar Archivo de Configuración

```bash
# Editar core-site.xml
vi /opt/hadoop/hadoop-3.4.0/etc/hadoop/core-site.xml
```

---

### 🔧 Contenido de core-site.xml

**Agregar dentro de `<configuration>`:**

```xml
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://nodo1:9000</value>
        <description>NameNode URI</description>
    </property>
</configuration>
```

**💡 Explicación de la Configuración:**

- **`fs.defaultFS`**: Define el sistema de archivos predeterminado de Hadoop
  - `hdfs://nodo1:9000`: URI completo del NameNode
  - `nodo1`: Hostname del servidor donde corre el NameNode
  - `9000`: Puerto de comunicación del NameNode
  - Esta configuración permite que todos los componentes de Hadoop sepan dónde encontrar el NameNode para acceder a HDFS

---

### 🔥 Abrir Puerto en Firewall

```bash
# Abrir puerto 9000 para HDFS
sudo firewall-cmd --zone=public --add-port=9000/tcp --permanent

# Recargar firewall
sudo firewall-cmd --reload

# Verificar puertos abiertos
sudo firewall-cmd --list-ports
```

---

## 16. Configurar hdfs-site.xml y Crear Carpetas

> **Video #17:** https://www.youtube.com/watch?v=eIYX_IXVoU0&list=PLG1t8jaLbxA_DG_cmlBYgkGW-TZw5DP3B&index=17

### 📝 Editar hdfs-site.xml

```bash
vi /opt/hadoop/hadoop-3.4.0/etc/hadoop/hdfs-site.xml
```

---

### 🔧 Contenido de hdfs-site.xml

```xml
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
        <description>Factor de replicación (1 para un solo nodo)</description>
    </property>
  
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>/datos/namenode</value>
        <description>Directorio de metadatos del NameNode</description>
    </property>
  
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>/datos/datanode</value>
        <description>Directorio de almacenamiento del DataNode</description>
    </property>
</configuration>
```

**💡 Explicación de la Configuración:**

- **`dfs.replication`**: Número de réplicas de cada bloque de datos en HDFS

  - Valor `1`: Solo una copia (apropiado para entorno de un solo nodo)
  - En clusters reales se usa `3` para alta disponibilidad
  - Más réplicas = mayor tolerancia a fallos, pero más espacio usado
- **`dfs.namenode.name.dir`**: Directorio donde el NameNode almacena metadatos

  - `/datos/namenode`: Ruta física en el disco local
  - Contiene información sobre la estructura de archivos HDFS
  - **Crítico**: Si se pierde, se pierde toda la información de HDFS
  - En producción se recomienda usar múltiples directorios para redundancia
- **`dfs.datanode.data.dir`**: Directorio donde el DataNode almacena los bloques de datos

  - `/datos/datanode`: Ruta física para almacenamiento de datos
  - Aquí se guardan los bloques reales de archivos (datos)
  - Puede configurarse con múltiples directorios separados por comas para usar varios discos

---

### 📁 Crear Carpetas de Datos

```bash
# Ir a la raíz del sistema
cd /

# Crear estructura de directorios
sudo mkdir /datos
cd /datos
sudo mkdir namenode
sudo mkdir datanode

# Asignar permisos al usuario hadoop
sudo chown -R hadoop:hadoop /datos

# Verificar
ls -l /datos
```

---

## 17. Formatear el NameNode

> **Video #18:** https://www.youtube.com/watch?v=rJFrPBtciXY&list=PLG1t8jaLbxA_DG_cmlBYgkGW-TZw5DP3B&index=18

### 🔧 Inicializar HDFS

```bash
# Formatear el NameNode (solo la primera vez)
hdfs namenode -format
```

**Salida esperada:**

```
Storage directory /datos/namenode has been successfully formatted.
```

---

### 🚀 Iniciar Servicios HDFS

```bash
# Iniciar HDFS (NameNode, DataNode, SecondaryNameNode)
start-dfs.sh
```

**El comando iniciará:**

- NameNode en `nodo1`
- DataNode en `nodo1`
- Secondary NameNode en `nodo1`

---

### ✅ Verificar Servicios Activos

```bash
# Ver procesos Java en ejecución
jps
```

**Salida esperada:**

```
12345 NameNode
12346 DataNode
12347 SecondaryNameNode
12348 Jps
```

---

## 18. Interfaz Web HDFS (Browse Directory)

> **Video HDFS Hadoop #1:** https://www.youtube.com/watch?v=38DgYWd7fYg&list=PLG1t8jaLbxA_DG_cmlBYgkGW-TZw5DP3B&index=19

### 🌐 Acceder a la Interfaz Web

**Abrir en navegador:**

```
http://localhost:9870
```

O desde otra máquina:

```
http://tu_ip_centos:9870
```

> **Nota:** En versiones anteriores de Hadoop el puerto era `50070`

---

### 🔥 Abrir Puerto para Interfaz Web

```bash
# Abrir puerto 9870
sudo firewall-cmd --zone=public --add-port=9870/tcp --permanent
sudo firewall-cmd --reload
```

---

### 📊 Funciones de la Interfaz Web

| Sección                      | Descripción                                         |
| ----------------------------- | ---------------------------------------------------- |
| **Overview**            | Estado general del cluster, espacio usado/disponible |
| **Datanodes**           | Lista de DataNodes activos                           |
| **Utilities → Browse** | Explorador de archivos HDFS                          |
| **Logs**                | Logs del NameNode                                    |

---

## 19. Operaciones Básicas en HDFS

> **Video HDFS Hadoop #2.1:** https://www.youtube.com/watch?v=PtcoKR9x0t0&list=PLG1t8jaLbxA_DG_cmlBYgkGW-TZw5DP3B&index=20

### 📄 Crear Archivo de Prueba

```bash
# Crear archivo local
echo "Hola Mundo Hadoop HDFS" >> prueba.txt

# Ver contenido
cat prueba.txt
```

---

### 📁 Crear Carpeta en HDFS

```bash
# Crear directorio en HDFS
hdfs dfs -mkdir /prueba

# Listar contenido de raíz
hdfs dfs -ls /
```

---

### 📤 Subir Archivo a HDFS

```bash
# Subir archivo local a HDFS
hdfs dfs -put prueba.txt /prueba/

# Verificar
hdfs dfs -ls /prueba
```

---

### 📥 Descargar Archivo desde HDFS

```bash
# Descargar archivo de HDFS a local
hdfs dfs -get /prueba/prueba.txt ./prueba_descargado.txt

# Verificar
cat prueba_descargado.txt
```

---

### 👁️ Ver Contenido de Archivo en HDFS

```bash
# Ver contenido completo
hdfs dfs -cat /prueba/prueba.txt

# Ver primeras líneas
hdfs dfs -head /prueba/prueba.txt
```

---

### 🗑️ Eliminar Archivos y Carpetas

```bash
# Eliminar archivo específico
hdfs dfs -rm /prueba/prueba.txt

# Eliminar carpeta y contenido (recursivo)
hdfs dfs -rm -r /prueba

# Eliminar permanentemente (sin papelera)
hdfs dfs -rm -r -skipTrash /prueba
```

---

### 📊 Comandos Útiles Adicionales

```bash
# Ver tamaño de archivo
hdfs dfs -du -h /ruta/archivo

# Copiar archivo dentro de HDFS
hdfs dfs -cp /origen/archivo /destino/

# Mover archivo dentro de HDFS
hdfs dfs -mv /origen/archivo /destino/

# Ver espacio usado/disponible
hdfs dfs -df -h

# Ver información de bloques
hdfs fsck /ruta/archivo -files -blocks -locations
```

---

## 20. Configuración de YARN

> **Video MapReduce con Hadoop #1:** https://www.youtube.com/watch?v=R7w8FAlnhAw&list=PLG1t8jaLbxA_DG_cmlBYgkGW-TZw5DP3B&index=23

### 📝 Configurar mapred-site.xml

```bash
vi /opt/hadoop/hadoop-3.4.0/etc/hadoop/mapred-site.xml
```

**Contenido:**

```xml
<configuration>
        <property>
                <name>yarn.resourcemanager.hostname</name>
                 <value>nodo1</value>
         </property>
         <property>
                 <name>yarn.nodemanager.aux-services</name>
                 <value>mapreduce_shuffle</value>
         </property>
         <property>
                 <name>yarn.nodemanager.aux-services.mapreduce_shuffle.class</name>
                 <value>org.apache.hadoop.mapred.ShuffleHandler</value>
         </property>
</configuration>
```

**💡 Explicación de la Configuración:**

- **`yarn.resourcemanager.hostname`**: Especifica el host donde corre el ResourceManager de YARN

  - Valor `nodo1`: Nombre del nodo que gestionará los recursos del cluster
  - YARN necesita saber dónde está el ResourceManager para coordinar jobs
- **`yarn.nodemanager.aux-services`**: Servicios auxiliares que ejecuta cada NodeManager

  - Valor `mapreduce_shuffle`: Habilita el servicio de shuffle para MapReduce
  - Shuffle: Fase donde se redistribuyen datos entre Map y Reduce
  - Esencial para que MapReduce funcione correctamente
- **`yarn.nodemanager.aux-services.mapreduce_shuffle.class`**: Clase Java que implementa el servicio de shuffle

  - `org.apache.hadoop.mapred.ShuffleHandler`: Manejador oficial de Apache
  - Gestiona la transferencia de datos entre mappers y reducers

---

### 📝 Configurar yarn-site.xml

```bash
vi /opt/hadoop/hadoop-3.4.0/etc/hadoop/yarn-site.xml
```

**Contenido:**

```xml
<configuration>
    <property>
        <name>yarn.resourcemanager.hostname</name>
        <value>nodo1</value>
        <description>Host del ResourceManager</description>
    </property>
  
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
        <description>Servicio auxiliar para MapReduce</description>
    </property>
  
    <property>
        <name>yarn.nodemanager.aux-services.mapreduce_shuffle.class</name>
        <value>org.apache.hadoop.mapred.ShuffleHandler</value>
    </property>
  
    <property>
        <name>yarn.resourcemanager.webapp.address</name>
        <value>0.0.0.0:8088</value>
        <description>Puerto de la interfaz web de YARN</description>
    </property>
  
    <property>
        <name>yarn.nodemanager.vmem-check-enabled</name>
        <value>false</value>
        <description>Deshabilitar verificación de memoria virtual</description>
    </property>
</configuration>
```

**💡 Explicación de la Configuración:**

- **`yarn.resourcemanager.hostname`**: Host del ResourceManager (cerebro de YARN)

  - Coordina la asignación de recursos (CPU, memoria) a las aplicaciones
  - Todos los NodeManagers se comunican con él
- **`yarn.nodemanager.aux-services`**: Servicios auxiliares para MapReduce

  - `mapreduce_shuffle`: Permite la fase de shuffle en jobs MapReduce
  - Sin esto, los jobs MapReduce fallarán
- **`yarn.nodemanager.aux-services.mapreduce_shuffle.class`**: Implementación del shuffle

  - Define qué clase Java maneja el intercambio de datos
- **`yarn.resourcemanager.webapp.address`**: Puerto de la interfaz web de YARN

  - `0.0.0.0:8088`: Escucha en todas las interfaces en el puerto 8088
  - Permite monitorear jobs y recursos desde http://localhost:8088
- **`yarn.nodemanager.vmem-check-enabled`**: Desactiva verificación de memoria virtual

  - Valor `false`: Evita errores por exceso de memoria virtual
  - En entornos de desarrollo previene problemas de límites de memoria
  - En producción se recomienda `true` para mejor control de recursos

---

### 🔥 Abrir Puerto YARN

```bash
# Abrir puerto 8088 para YARN
sudo firewall-cmd --zone=public --add-port=8088/tcp --permanent
sudo firewall-cmd --reload
```

---

### 🚀 Iniciar Servicios YARN

```bash
# Detener HDFS primero
stop-dfs.sh

# Reiniciar HDFS
start-dfs.sh

# Iniciar YARN
start-yarn.sh
```

---

### ✅ Verificar Servicios

```bash
# Ver procesos activos
jps
```

**Salida esperada:**

```
12345 NameNode
12346 DataNode
12347 SecondaryNameNode
12348 ResourceManager
12349 NodeManager
12350 Jps
```

---

### 🌐 Interfaz Web de YARN

**Abrir en navegador:**

```
http://localhost:8088/cluster/
```

O desde otra máquina:

```
http://tu_ip_centos:8088/cluster/
```

---

### 🎯 Comandos de Inicio/Cierre Rápido

```bash
# Iniciar todo (HDFS + YARN)
start-all.sh

# Detener todo
stop-all.sh

# Solo HDFS
start-dfs.sh
stop-dfs.sh

# Solo YARN
start-yarn.sh
stop-yarn.sh
```

---

## 21. Ejecutar MapReduce (WordCount)

> **Video MapReduce con Hadoop #3:** https://www.youtube.com/watch?v=woUzV_liwto&list=PLG1t8jaLbxA_DG_cmlBYgkGW-TZw5DP3B&index=25

### 📄 Preparar Datos de Entrada

```bash
# Descargar archivo de texto (ejemplo: Los Miserables)
# Puedes usar cualquier archivo .txt grande

# Crear carpeta en HDFS
hdfs dfs -mkdir /libros

# Subir archivo
hdfs dfs -put Los_Miserables.txt /libros/

# Verificar
hdfs dfs -ls /libros
```

---

### 🚀 Ejecutar Job de MapReduce

```bash
# Ir a la carpeta de ejemplos
cd /opt/hadoop/hadoop-3.4.0/share/hadoop/mapreduce/

# Ejecutar WordCount
hadoop jar hadoop-mapreduce-examples-3.4.0.jar wordcount /libros /libros_salida
```

---

### 📊 Flujo de MapReduce

**Lo que sucede internamente:**

1. **Map Phase:** Divide el texto y asigna "1" a cada palabra

   - Ejemplo: "Hadoop" → ("Hadoop", 1)
2. **Shuffle & Sort:** YARN agrupa palabras iguales

   - Ejemplo: "Hadoop" → [1, 1, 1]
3. **Reduce Phase:** Suma los valores

   - Ejemplo: "Hadoop" → 3

---

### 📥 Revisar Resultados

```bash
# Listar archivos generados
hdfs dfs -ls /libros_salida

# Ver contenido del resultado
hdfs dfs -cat /libros_salida/part-r-00000

# Ver primeras líneas
hdfs dfs -cat /libros_salida/part-r-00000 | head -20
```

**Salida ejemplo:**

```
hadoop  150
mapreduce  89
yarn  67
hdfs  234
...
```

---

### 🌐 Monitoreo en YARN Web UI

Mientras el job se ejecuta, abre:

```
http://localhost:8088
```

Verás:

- Estado: **RUNNING** → **FINISHED**
- Progreso: Map % → Shuffle % → Reduce %
- Memoria usada
- Tiempo de ejecución

---

### 🔄 Ejecutar de Nuevo

> **Nota:** MapReduce nunca sobreescribe carpetas de salida

```bash
# Eliminar carpeta de salida anterior
hdfs dfs -rm -r /libros_salida

# Ejecutar nuevamente
hadoop jar hadoop-mapreduce-examples-3.4.0.jar wordcount /libros /libros_salida
```

---

### 📊 Otros Ejemplos de MapReduce

```bash
# Pi (estimación de π usando Monte Carlo)
hadoop jar hadoop-mapreduce-examples-3.4.0.jar pi 10 100

# Grep (buscar patrones en archivos)
hadoop jar hadoop-mapreduce-examples-3.4.0.jar grep /libros /grep_salida 'hadoop'

# TeraSort (ordenar grandes volúmenes de datos)
hadoop jar hadoop-mapreduce-examples-3.4.0.jar teragen 1000 /tera_input
hadoop jar hadoop-mapreduce-examples-3.4.0.jar terasort /tera_input /tera_output
```

---

---

## 🐝 PARTE III: INSTALACIÓN DE APACHE HIVE

---

## 22. Instalación de Apache Hive 3.1.3

### 📥 Descargar Hive

```bash
# Ir a /opt
cd /opt

# Descargar Hive 3.1.3
sudo wget https://archive.apache.org/dist/hive/hive-3.1.3/apache-hive-3.1.3-bin.tar.gz

# Descomprimir
sudo tar -zxvf apache-hive-3.1.3-bin.tar.gz

# Renombrar para simplificar
sudo mv apache-hive-3.1.3-bin /opt/hive

# Asignar permisos al usuario hadoop
sudo chown -R hadoop:hadoop /opt/hive
```

---

## 23. Configurar Variables de Entorno Hive

### 🔧 Editar .bashrc

```bash
# Cambiar a usuario hadoop
su - hadoop

# Editar .bashrc
nano /home/hadoop/.bashrc
```

**Agregar al final:**

```bash
# Configuración de Hive
export HIVE_HOME=/opt/hive
export PATH=$PATH:$HIVE_HOME/bin
```

**Guardar y recargar:**

```bash
source /home/hadoop/.bashrc

# Verificar
echo $HIVE_HOME
# Salida esperada: /opt/hive
```

---

## 24. Resolver Conflictos de Librerías (Guava)

> **Problema:** Hive 3.1.3 incluye Guava 19.0 que es incompatible con Hadoop 3.4.0

### 🔧 Fix de Guava

```bash
# Eliminar librería obsoleta de Hive
rm /opt/hive/lib/guava-19.0.jar

# Copiar versión compatible desde Hadoop
cp /opt/hadoop/hadoop-3.4.0/share/hadoop/common/lib/guava-*.jar /opt/hive/lib/

# Verificar
ls /opt/hive/lib/ | grep guava
# Salida esperada: guava-27.0-jre.jar (o versión superior)
```

---

## 25. Configurar Directorios HDFS para Hive

### 📁 Crear Carpetas en HDFS

```bash
# Asegurar que HDFS está corriendo
start-dfs.sh

# Crear carpeta temporal
hdfs dfs -mkdir -p /tmp

# Crear warehouse (almacén de tablas)
hdfs dfs -mkdir -p /user/hive/warehouse

# Dar permisos de escritura
hdfs dfs -chmod g+w /tmp
hdfs dfs -chmod g+w /user/hive/warehouse

# Verificar
hdfs dfs -ls /user/hive/
```

---

## 26. Configurar hive-site.xml (Metastore)

### 📝 Crear Archivo de Configuración

```bash
# Crear hive-site.xml
nano /opt/hive/conf/hive-site.xml
```

**Contenido completo:**

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
    <!-- Configuración de Metastore con Derby -->
    <property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:derby:;databaseName=metastore_db;create=true</value>
        <description>URL de conexión a la base de datos Derby embebida</description>
    </property>
  
    <!-- Directorio warehouse en HDFS -->
    <property>
        <name>hive.metastore.warehouse.dir</name>
        <value>/user/hive/warehouse</value>
        <description>Ubicación del warehouse en HDFS</description>
    </property>
  
    <!-- Configuración adicional -->
    <property>
        <name>hive.metastore.uris</name>
        <value/>
        <description>URI del metastore (vacío para modo embebido)</description>
    </property>
  
    <property>
        <name>hive.server2.enable.doAs</name>
        <value>false</value>
        <description>Deshabilitar suplantación de usuario</description>
    </property>
</configuration>
```

**💡 Explicación de la Configuración:**

- **`javax.jdo.option.ConnectionURL`**: URL de conexión a la base de datos del metastore

  - `jdbc:derby:`: Usa Apache Derby (base de datos embebida en Java)
  - `databaseName=metastore_db`: Nombre de la base de datos para metadatos
  - `create=true`: Crea la base de datos automáticamente si no existe
  - El metastore guarda información sobre tablas, columnas, particiones, etc.
  - **Nota**: Derby es solo para desarrollo; en producción usar MySQL/PostgreSQL
- **`hive.metastore.warehouse.dir`**: Directorio raíz en HDFS donde Hive almacena datos de tablas

  - `/user/hive/warehouse`: Ubicación predeterminada
  - Cada tabla creará un subdirectorio aquí
  - Ejemplo: tabla `usuarios` estará en `/user/hive/warehouse/usuarios`
- **`hive.metastore.uris`**: URI del servicio metastore (vacío para modo embebido)

  - Vacío: Metastore embebido (corre en el mismo proceso que Hive)
  - En producción se configura un metastore remoto para compartir entre usuarios
- **`hive.server2.enable.doAs`**: Suplantación de identidad de usuario

  - `false`: Todas las operaciones se ejecutan como el usuario que inició Hive
  - `true`: Cada usuario ejecutaría operaciones con su propia identidad
  - En desarrollo `false` simplifica la configuración

---

## 27. Inicializar Esquema de Metadatos

### 🔧 Formatear Base de Datos de Metastore

```bash
# Inicializar esquema con Derby
schematool -initSchema -dbType derby
```

**Salida esperada:**

```
Initialization script completed
schemaTool completed
```

---

### ⚠️ Solución de Errores Comunes

#### Error: "schematool: command not found"

```bash
# Verificar PATH
echo $PATH | grep hive

# Si no aparece, recargar .bashrc
source ~/.bashrc
```

#### Error: "metastore_db already exists"

```bash
# Eliminar base de datos antigua
rm -rf metastore_db/

# Reinicializar
schematool -initSchema -dbType derby
```

---

## 28. Ejecutar y Probar Hive

### 🚀 Iniciar Hive

```bash
# Asegurar que HDFS y YARN están corriendo
start-all.sh

# Iniciar consola de Hive
hive
```

**Prompt esperado:**

```
hive>
```

---

### 📊 Comandos Básicos de HiveQL

#### Ver Bases de Datos

```sql
-- Ver bases de datos disponibles
SHOW DATABASES;

-- Usar base de datos
USE default;
```

---

#### Crear Tabla

```sql
-- Crear tabla simple
CREATE TABLE usuarios (
    id INT,
    nombre STRING
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE;
```

---

#### Insertar Datos

```sql
-- Insertar registros individuales
INSERT INTO usuarios VALUES (1, 'Gemini');
INSERT INTO usuarios VALUES (2, 'Hadoop');
INSERT INTO usuarios VALUES (3, 'Spark');
INSERT INTO usuarios VALUES (4, 'Kafka');
INSERT INTO usuarios VALUES (5, 'Flink');
INSERT INTO usuarios VALUES (6, 'HBase');
```

> **Nota:** Cada INSERT ejecuta un job MapReduce (lento). En producción se cargan archivos masivos.

---

#### Consultar Datos

```sql
-- Ver todos los registros
SELECT * FROM usuarios;

-- Filtrar por ID
SELECT * FROM usuarios WHERE id > 3;

-- Buscar por nombre (LIKE)
SELECT * FROM usuarios WHERE nombre LIKE 'H%';

-- Ordenar resultados
SELECT * FROM usuarios ORDER BY id DESC;

-- Contar registros
SELECT COUNT(*) AS total FROM usuarios;

-- Agregación
SELECT COUNT(*) AS cantidad, AVG(id) AS promedio_id FROM usuarios;
```

---

#### Ver Tablas y Estructura

```sql
-- Ver todas las tablas
SHOW TABLES;

-- Ver estructura de tabla
DESCRIBE usuarios;

-- Ver información detallada
DESCRIBE FORMATTED usuarios;
```

---

#### Cargar Datos desde Archivo

```sql
-- Crear archivo CSV local
-- En terminal (fuera de Hive):
echo -e "7,Presto\n8,Druid\n9,Airflow" > /tmp/usuarios_nuevos.csv

-- En Hive:
LOAD DATA LOCAL INPATH '/tmp/usuarios_nuevos.csv' INTO TABLE usuarios;

-- Verificar
SELECT * FROM usuarios;
```

---

#### Crear Tabla desde Consulta (CTAS)

```sql
-- Crear tabla con resultados de consulta
CREATE TABLE usuarios_filtrados AS
SELECT * FROM usuarios WHERE id > 5;

-- Ver resultado
SELECT * FROM usuarios_filtrados;
```

---

### 📂 Ver Datos Físicos en HDFS

**Salir de Hive:**

```sql
quit;
```

**En terminal:**

```bash
# Ver archivos de la tabla en HDFS
hdfs dfs -ls /user/hive/warehouse/usuarios

# Ver contenido de los archivos
hdfs dfs -cat /user/hive/warehouse/usuarios/*
```

---

### 🗑️ Eliminar Tablas

```sql
-- Eliminar tabla (mantiene datos en HDFS si es EXTERNAL)
DROP TABLE IF EXISTS usuarios;

-- Verificar
SHOW TABLES;
```

---

## 📚 Conceptos Clave de Hive

| Concepto                   | Descripción                                                    |
| -------------------------- | --------------------------------------------------------------- |
| **HiveQL**           | Lenguaje SQL que se traduce a MapReduce/Tez                     |
| **Metastore**        | Base de datos con el catálogo de tablas (esquema)              |
| **Warehouse**        | Ubicación física en HDFS donde se guardan los datos           |
| **Tabla Externa**    | Tabla que referencia datos existentes en HDFS                   |
| **Tabla Gestionada** | Tabla donde Hive controla los datos (se eliminan al hacer DROP) |
| **Particionamiento** | Dividir tablas por columnas para consultas más rápidas        |
| **Bucketing**        | Agrupar datos en buckets para optimizar joins                   |

---

---

## 🎓 RESUMEN Y MEJORES PRÁCTICAS

---

### ✅ Checklist de Instalación Completa

| Componente             | Verificación   | Comando                                 |
| ---------------------- | --------------- | --------------------------------------- |
| **Java 8**       | ✅ Instalado    | `java -version`                       |
| **Hadoop 3.4.0** | ✅ Instalado    | `hadoop version`                      |
| **HDFS**         | ✅ Corriendo    | `jps` → NameNode, DataNode           |
| **YARN**         | ✅ Corriendo    | `jps` → ResourceManager, NodeManager |
| **Hive 3.1.3**   | ✅ Instalado    | `hive --version`                      |
| **Metastore**    | ✅ Inicializado | `ls metastore_db/`                    |

---

### 🔧 Comandos Esenciales para el Día a Día

#### Iniciar/Detener Servicios

```bash
# Iniciar todo
start-all.sh

# Detener todo
stop-all.sh

# Ver servicios activos
jps
```

---

#### HDFS

```bash
# Listar archivos
hdfs dfs -ls /ruta

# Subir archivo
hdfs dfs -put archivo.txt /ruta/

# Descargar archivo
hdfs dfs -get /ruta/archivo.txt ./

# Ver contenido
hdfs dfs -cat /ruta/archivo.txt

# Eliminar
hdfs dfs -rm -r /ruta
```

---

#### Hive

```bash
# Iniciar Hive
hive

# Ejecutar query desde archivo
hive -f script.hql

# Ejecutar query desde línea de comandos
hive -e "SELECT * FROM tabla;"

# Modo silencioso (sin logs)
hive -S -e "SELECT * FROM tabla;"
```

---

### 🌐 URLs de Monitoreo

| Servicio                       | URL                    | Puerto |
| ------------------------------ | ---------------------- | ------ |
| **HDFS NameNode**        | http://localhost:9870  | 9870   |
| **YARN ResourceManager** | http://localhost:8088  | 8088   |
| **Job History**          | http://localhost:19888 | 19888  |

---

### ⚠️ Errores Comunes y Soluciones

#### 1. "Connection refused" al acceder a interfaces web

```bash
# Verificar que los servicios están corriendo
jps

# Verificar puertos abiertos en firewall
sudo firewall-cmd --list-ports

# Abrir puertos necesarios
sudo firewall-cmd --zone=public --add-port=9870/tcp --permanent
sudo firewall-cmd --zone=public --add-port=8088/tcp --permanent
sudo firewall-cmd --reload
```

---

#### 2. "Address already in use"

```bash
# Ver qué proceso usa el puerto
sudo netstat -tulpn | grep 9000

# Detener servicios y reiniciar
stop-all.sh
start-all.sh
```

---

#### 3. "Cannot create directory /user/hive/warehouse"

```bash
# Crear directorio manualmente
hdfs dfs -mkdir -p /user/hive/warehouse

# Dar permisos
hdfs dfs -chmod -R 777 /user/hive/warehouse
```

---

#### 4. Hive no encuentra Guava

```bash
# Copiar librería correcta
cp /opt/hadoop/hadoop-3.4.0/share/hadoop/common/lib/guava-*.jar /opt/hive/lib/

# Eliminar versión antigua
rm /opt/hive/lib/guava-19.0.jar
```

---

### 📊 Estructura de Directorios Final

```
/opt/
├── jdk1.8.0_202/          # Java 8
├── hadoop/
│   └── hadoop-3.4.0/      # Hadoop
│       ├── bin/           # Ejecutables
│       ├── etc/hadoop/    # Configuración
│       └── share/         # Librerías
└── hive/                  # Hive
    ├── bin/               # Ejecutables
    ├── conf/              # Configuración
    └── lib/               # Librerías

/datos/
├── namenode/              # Metadatos HDFS
└── datanode/              # Datos HDFS

/home/hadoop/              # Home del usuario
└── .bashrc                # Variables de entorno
```

---

### 🎯 Variables de Entorno Completas

**Archivo:** `/home/hadoop/.bashrc`

```bash
# Configuración de Java
export JAVA_HOME=/opt/jdk1.8.0_202
export JRE_HOME=/opt/jdk1.8.0_202/jre

# Configuración de Hadoop
export HADOOP_HOME=/opt/hadoop/hadoop-3.4.0

# Configuración de Hive
export HIVE_HOME=/opt/hive

# PATH completo
export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$HIVE_HOME/bin
```

---

### 💡 Tips de Rendimiento

1. **Aumentar memoria para YARN:**

   - Editar `yarn-site.xml`
   - Aumentar `yarn.nodemanager.resource.memory-mb`
2. **Usar formatos columnares:**

   - ORC o Parquet en lugar de TEXTFILE
   - Mejora rendimiento en consultas analíticas
3. **Particionar tablas grandes:**

   - `PARTITIONED BY (year, month)`
   - Reduce escaneo de datos
4. **Usar compresión:**

   - Snappy, Gzip, LZO
   - Reduce espacio en disco y red

---

### 🔒 Seguridad Básica

> **⚠️ Importante:** Estos son configuraciones básicas. En producción, implementar medidas de seguridad adicionales.

```bash
# Cambiar permisos de directorios sensibles
hdfs dfs -chmod 700 /user/hadoop

# Deshabilitar permisos globales (para producción)
hdfs dfs -chmod g-w /tmp
hdfs dfs -chmod g-w /user/hive/warehouse

# Usar Kerberos para autenticación (avanzado)
# Configurar en core-site.xml y hdfs-site.xml
```

---

### 📖 Recursos Adicionales

| Recurso                           | URL                                                                      |
| --------------------------------- | ------------------------------------------------------------------------ |
| **Documentación Hadoop**   | https://hadoop.apache.org/docs/r3.4.0/                                   |
| **Documentación Hive**     | https://cwiki.apache.org/confluence/display/Hive/                        |
| **Playlist YouTube (base)** | https://www.youtube.com/playlist?list=PLG1t8jaLbxA_DG_cmlBYgkGW-TZw5DP3B |
| **Apache Downloads**        | https://downloads.apache.org/                                            |

---

### 🎓 Próximos Pasos

Una vez completada esta guía, puedes continuar con:

1. **Práctica con Dataset Real:**

   - Consulta: `Practica_Hadoop_Hive_MovieLens.md`
   - Dataset: MovieLens 100K (100,000 ratings)
2. **Integración con Spark:**

   - Instalar Apache Spark sobre Hadoop
   - Usar Spark SQL en lugar de Hive
3. **Cluster Multi-Nodo:**

   - Configurar múltiples máquinas virtuales
   - Simular cluster distribuido real
4. **Herramientas Adicionales:**

   - HBase (base de datos NoSQL)
   - Pig (scripting para Hadoop)
   - Sqoop (importar/exportar desde RDBMS)
   - Flume (ingestión de logs)

---

<div align="center">

## 🎉 ¡Instalación Completada!

**Ecosistema Big Data Funcional en CentOS**

✅ Java 8 OpenJDK configurado
✅ Hadoop 3.4.0 en modo pseudo-distribuido
✅ HDFS operativo con replicación
✅ YARN gestionando recursos
✅ MapReduce ejecutando jobs
✅ Hive 3.1.3 con metastore Derby
✅ Interfaces web de monitoreo activas

---

### 📚 Para Estudiar

1. Practica con archivos grandes en HDFS
2. Ejecuta diferentes ejemplos de MapReduce
3. Crea tablas particionadas en Hive
4. Monitorea jobs en las interfaces web
5. Experimenta con consultas HiveQL complejas

---

*Guía de Instalación CentOS + Hadoop + Hive*
*Versión 2.0 - Febrero 2026*
*Curso: Sistemas Inteligentes*

</div>
