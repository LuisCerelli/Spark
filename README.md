 # <p align="center">¿Qué es Apache Spark?</p>

<p align='center'; padding="10">
  <img src=imagenes/05.webp></p>

Apache Spark es un motor de procesamiento de datos de código abierto diseñado para ejecutar aplicaciones de análisis de datos a gran escala. Lo que hace que Spark sea especial es que puede procesar grandes volúmenes de datos de forma rápida y eficiente al dividir el trabajo en muchas partes y distribuirlas a través de varios nodos (computadoras) en un clúster.

### En términos simples:

Spark es una herramienta para procesar grandes volúmenes de datos en paralelo, es decir, en muchos núcleos de procesamiento a la vez.<br>
Se usa para tareas como análisis de datos, aprendizaje automático (machine learning), procesamiento de streams en tiempo real, entre otros.
## Características clave de Spark:
<ins>**Velocidad:**</ins> Spark es mucho más rápido que otros motores de procesamiento como Hadoop, especialmente porque procesa datos en memoria (RAM) en lugar de en disco duro. Esto reduce significativamente el tiempo que toma leer y escribir datos.

Ejemplo: Si quieres analizar terabytes (TB) de datos de transacciones bancarias, Spark puede hacerlo en minutos u horas, en lugar de días.

<ins>**Distribuido:**</ins> Spark trabaja distribuyendo las tareas a través de varios nodos en un clúster. Un clúster es un conjunto de computadoras conectadas entre sí que pueden trabajar en equipo para realizar tareas grandes.

Ejemplo: Si tienes 100 GB de datos, Spark podría dividirlos en 10 partes y procesarlos en 10 computadoras diferentes al mismo tiempo.

<ins>**Multilenguaje:**</ins>: Spark admite varios lenguajes de programación, incluidos Scala, Python, Java y R. Esto lo hace muy flexible para diferentes desarrolladores y científicos de datos.

<ins>**API unificada:**</ins> Spark tiene una única API para hacer varias cosas:

- Procesar datos en lotes (Batch Processing).
- Procesar flujos de datos en tiempo real (Streaming).
- Realizar consultas interactivas con SQL (Spark SQL).
- Hacer análisis avanzados como Machine Learning y gráficos (Spark MLlib y GraphX).

### ¿Cómo funciona Spark?
El concepto más importante para entender cómo funciona Spark es el de <ins>**RDD (Resilient Distributed Dataset**</ins>, o conjunto de datos distribuidos y resilientes).

### ¿Qué es un RDD?
Un RDD es la estructura de datos fundamental en Spark. Es básicamente una colección distribuida de objetos que puede procesarse en paralelo. Los RDDs son:

- Distribuidos: Se dividen en múltiples fragmentos que pueden procesarse en diferentes nodos del clúster.
- Resilientes: Si alguna parte del procesamiento falla, Spark puede recuperar los datos y recomenzar desde el punto donde ocurrió el fallo.
#### Ejemplo sencillo con RDD:
Imagina que tienes un archivo enorme con millones de registros de ventas. Puedes usar Spark para cargar esos datos en un RDD y luego aplicar operaciones como:

- Map: Para transformar los datos (ej., convertir los precios de una moneda a otra).
- Filter: Para seleccionar solo los registros de ventas que superen un determinado monto.
- Reduce: Para sumar los ingresos totales de todas las ventas.
```
# Código en Python con Spark
data = sc.textFile("ventas.txt")  # Cargar el archivo en un RDD
ventas = data.map(lambda x: float(x.split(",")[2]))  # Extraer la columna de precios
ventas_altas = ventas.filter(lambda x: x > 1000)  # Filtrar ventas mayores a 1000
total = ventas_altas.reduce(lambda a, b: a + b)  # Sumar todas las ventas altas

print(total)

```
##### En este ejemplo:

- `sc.textFile("ventas.txt")` carga el archivo de ventas en un RDD distribuido.
- `map` toma cada línea, extrae el precio y lo convierte a un número.
- `filter` selecciona solo las ventas cuyo monto supera los 1000.
- `reduce` suma todos esos valores filtrados.
  
### Operaciones en Spark
Existen dos tipos principales de operaciones que puedes realizar sobre RDDs en Spark:

- Transformaciones: Estas crean nuevos RDDs a partir de otros, como map o filter. Las transformaciones son perezosas (lazy), es decir, no se ejecutan de inmediato, sino que se almacenan y se ejecutan cuando realmente se necesitan.

  - Ejemplo: map, filter, flatMap, groupBy, etc.
    
- Acciones: Estas devuelven un resultado y desencadenan la ejecución de las transformaciones. Ejemplos de acciones son reduce, collect, count, etc.

  - Ejemplo: reduce para sumar, collect para obtener los datos en el programa.
   
### Modo de procesamiento
###### Procesamiento en lote (Batch Processing)

Spark se puede usar para procesar grandes cantidades de datos a la vez, lo que se llama procesamiento en lote. Esto es ideal para trabajar con bases de datos grandes o archivos enormes que deben ser analizados.

###### Procesamiento en tiempo real (Streaming)
Spark también es capaz de procesar datos en tiempo real a medida que llegan, usando una librería llamada Spark Streaming. Esto es útil, por ejemplo, para analizar en vivo datos provenientes de redes sociales, sensores, o logs de servidores.

- Ejemplo: Imagina que quieres monitorear los tweets que mencionan un producto de tu empresa. Usando Spark Streaming, podrías capturar los tweets a medida que se publican y analizar su tono (positivo, negativo) en tiempo real.
  
### ¿Cómo se usa Apache Spark en la práctica?
##### Ejemplo 1: Procesamiento de Big Data
Imagina una empresa que tiene una base de datos gigantesca con información de compras de miles de clientes en todo el mundo. Los datos están almacenados en varios archivos y la empresa quiere saber qué productos son los más vendidos, segmentando por región.

Con Spark, puedes cargar esos archivos en un clúster, procesar los datos en paralelo y obtener un resumen de las ventas de forma muy eficiente, incluso si los datos ocupan terabytes.

```
# Código en Python
from pyspark.sql import SparkSession

# Crear una sesión de Spark
spark = SparkSession.builder.appName("VentasGlobales").getOrCreate()

# Cargar los datos en un DataFrame
df = spark.read.csv("ventas_mundiales.csv", header=True, inferSchema=True)

# Agrupar y contar ventas por región
ventas_por_region = df.groupBy("region").count()

# Mostrar el resultado
ventas_por_region.show()
```

#### Ejemplo 2: Machine Learning con Spark
Spark también tiene una librería llamada MLlib que facilita el uso de algoritmos de Machine Learning en grandes volúmenes de datos. Por ejemplo, podrías usar Spark para entrenar un modelo de predicción de ventas usando datos históricos.

```
from pyspark.ml.feature import VectorAssembler
from pyspark.ml.regression import LinearRegression

# Seleccionar las características y la variable objetivo
assembler = VectorAssembler(inputCols=["feature1", "feature2"], outputCol="features")
df_features = assembler.transform(df)

# Crear un modelo de regresión lineal
lr = LinearRegression(featuresCol="features", labelCol="ventas")

# Entrenar el modelo
modelo = lr.fit(df_features)

# Hacer predicciones
predicciones = modelo.transform(df_features)
predicciones.select("features", "ventas", "prediction").show()
```

En este ejemplo:

- VectorAssembler convierte las características en una columna de "features" para el modelo.
- LinearRegression es el algoritmo de aprendizaje automático que estamos usando para predecir las ventas.
  
### ¿Para qué empresas o sectores es útil Spark?
Spark es útil en cualquier empresa que maneje grandes volúmenes de datos y necesite procesarlos rápidamente, como:

- Bancos: Analizar millones de transacciones en tiempo real para detectar fraudes.
- E-commerce: Analizar los comportamientos de compra y personalizar recomendaciones de productos.
- Tecnología: Procesar grandes volúmenes de logs de servidores para monitorear el rendimiento de las aplicaciones.
- Salud: Analizar datos de pacientes para descubrir patrones y tendencias que ayuden a mejorar tratamientos médicos.
## Conclusión
Apache Spark es una herramienta poderosa para procesar grandes cantidades de datos, tanto en lotes como en tiempo real. Gracias a su capacidad de procesamiento distribuido y a su velocidad, es una solución popular en áreas como análisis de Big Data, Machine Learning y procesamiento en tiempo real.
