# ⚡ Módulo Spark

> Aprende a procesar Big Data en memoria con Apache Spark y compararlo con Pandas

---

## 📊 Dataset: E-commerce

Ubicación: `data/`

| Archivo | Registros | Descripción |
|---------|-----------|-------------|
| orders.csv | 206,000 | Pedidos de clientes |
| order_products__prior.csv | 3.2M | Productos en cada pedido |
| products.csv | 49,000 | Catálogo de productos |
| aisles.csv | 134 | Pasillos de supermercado |
| departments.csv | 21 | Departamentos de productos |

**Fuente:** Dataset tipo Instacart (e-commerce de supermercado)

---

## 📚 Notebooks

### 1️⃣ [Big_Data.ipynb](Big_Data.ipynb)
**Conceptos de Big Data y caso de estudio**

**Contenido:**
- ¿Qué es Big Data? (Volumen, Variedad, Velocidad)
- Desafíos del análisis de datos masivos
- Contexto del problema: empresa e-commerce
- Metodología de análisis con Big Data
- Desarrollo práctico con Pandas (6 visualizaciones)

**Duración:** ~2 horas

---

### 2️⃣ [Analisis_Pandas.ipynb](Analisis_Pandas.ipynb)
**Análisis exploratorio con Pandas (enfoque tradicional)**

**Análisis realizados:**

📌 **1. Carga y preprocesamiento**
- Importar CSVs con Pandas
- Limpieza de datos (valores nulos, duplicados)
- Merge de múltiples tablas

📌 **2. Distribución de pedidos**
- Pedidos por usuario
- Productos más comprados
- Histogramas y distribuciones

📌 **3. Patrones temporales**
- Pedidos por día de la semana
- Pedidos por hora del día
- Visualización con gráficos de barras

📌 **4. Top productos**
- 5 productos más vendidos
- Análisis por departamento
- Productos por pasillo

📌 **5. Market Basket Analysis**
- Productos comprados juntos
- Grafo de asociaciones con NetworkX
- Visualización de patrones

**Técnicas:** Pandas, Matplotlib, Seaborn, NetworkX

**Duración:** ~4 horas

---

### 3️⃣ [Analisis_Spark.ipynb](Analisis_Spark.ipynb)
**Mismo análisis con PySpark (enfoque distribuido)**

**Conceptos Spark:**
- **SparkSession:** Punto de entrada a Spark
- **DataFrame:** Estructura de datos distribuida
- **RDD:** Resilient Distributed Dataset
- **Transformations:** Lazy operations (map, filter, select)
- **Actions:** Ejecutan el DAG (count, show, collect)
- **DAG:** Directed Acyclic Graph (plan de ejecución)

**Análisis (igual que Pandas):**
- Carga de CSVs con Spark
- Transformaciones y agregaciones distribuidas
- Joins optimizados
- Window Functions (técnica avanzada)
- UDFs (User Defined Functions)

**Optimizaciones:**
- `cache()`: Persistir DataFrames en memoria
- `broadcast()`: Replicar tablas pequeñas
- `repartition()`: Balancear particiones
- `persist()`: Control de almacenamiento

**Comparación de rendimiento:**
- Pandas: Mejor para datasets pequeños (<1GB)
- Spark: Mejor para datasets grandes (>5GB)

**Duración:** ~5 horas

---

## 🎯 Objetivos de Aprendizaje

✅ **Pandas:**
- Manipulación de DataFrames
- Análisis exploratorio de datos (EDA)
- Visualización con Matplotlib
- Limitaciones con datos grandes

✅ **PySpark:**
- Arquitectura distribuida de Spark
- Transformations vs Actions
- Optimización de queries
- Cuándo usar Spark vs Pandas

✅ **Comparación:**
- Velocidad de procesamiento
- Uso de memoria
- Escalabilidad
- Casos de uso ideales

---

## 🚀 Inicio Rápido

### Instalar dependencias

```bash
pip install pandas matplotlib seaborn networkx pyspark jupyter
```

### Ejecutar notebooks

```bash
# Iniciar Jupyter
jupyter notebook

# Abrir en orden:
# 1. Big_Data.ipynb
# 2. Analisis_Pandas.ipynb
# 3. Analisis_Spark.ipynb
```

---

## 📈 Análisis Realizados

| Análisis | Pandas | PySpark |
|----------|--------|---------|
| 📊 Distribución de pedidos | ✅ | ✅ |
| 📅 Patrones temporales | ✅ | ✅ |
| 🏆 Top 5 productos | ✅ | ✅ |
| 🗂️ Productos por departamento | ✅ | ✅ |
| 🔗 Market Basket Analysis | ✅ | ✅ |
| 🪟 Window Functions | ❌ | ✅ |
| ⚡ Optimizaciones distribuidas | ❌ | ✅ |

---

## 💡 Tips

### Para Pandas:
- Usar `dtypes` para verificar tipos de datos
- `info()` muestra memoria usada
- `describe()` para estadísticas rápidas
- `head()` para inspeccionar datos

### Para PySpark:
- Siempre crear `SparkSession` al inicio
- Usar `cache()` en DataFrames reutilizados
- `explain()` muestra el plan de ejecución
- `show()` limita filas por defecto (20)
- Detener sesión: `spark.stop()`

---

## 🔍 Conceptos Clave

**Lazy Evaluation:** Spark no ejecuta hasta llamar una acción

**DAG:** Plan de ejecución optimizado automáticamente

**Particiones:** Datos divididos para procesamiento paralelo

**Broadcast:** Copia tablas pequeñas a todos los workers

**Window Functions:** Operaciones sobre ventanas de datos (RANK, LEAD, LAG)

---

## 📦 Estructura del Dataset

```
data/
├── orders.csv                    # Pedidos (order_id, user_id, ...)
├── order_products__prior.csv     # Productos por pedido
├── products.csv                  # Catálogo (product_id, name)
├── aisles.csv                    # Pasillos (aisle_id, name)
└── departments.csv               # Departamentos (dept_id, name)
```

**Relaciones:**
- orders → order_products (order_id)
- order_products → products (product_id)
- products → aisles (aisle_id)
- products → departments (department_id)

---

## 🎓 Orden de Estudio Recomendado

1. **Big_Data.ipynb** → Entender el contexto y conceptos
2. **Analisis_Pandas.ipynb** → Aprender análisis tradicional
3. **Analisis_Spark.ipynb** → Comparar con enfoque distribuido

**Tiempo total estimado:** ~11 horas

---

**¡Éxito con Spark! ⚡**
