# Módulo 07 — Pandas: Series, DataFrames y cargar datos

Aquí empieza lo que viniste a buscar. Pandas es la librería estándar para trabajar con datos tabulares en Python: lo que harías en Excel, pero programado, repetible y sin límite de filas. Este módulo te presenta sus dos estructuras y cómo meter datos dentro.

## Instalar e importar

```bash
pip install pandas
```

```python
import pandas as pd   # el alias pd es universal
import numpy as np    # casi siempre van juntos
```

## La Series: una columna con etiquetas

Una Series es una secuencia de valores, como un array de NumPy, pero con un *índice*: una etiqueta para cada valor. Piensa en ella como una sola columna de una tabla.

```python
import pandas as pd

notas = pd.Series([7, 5, 9, 6])
print(notas)
# 0    7
# 1    5
# 2    9
# 3    6
# dtype: int64
```

Esa columna de la izquierda (0, 1, 2, 3) es el índice. Por defecto son números, pero puedes ponerle etiquetas tuyas:

```python
notas = pd.Series([7, 5, 9], index=["Ada", "Luis", "Marta"])
print(notas["Ada"])   # 7    accedes por etiqueta
print(notas.mean())   # 7.0  los métodos de NumPy funcionan igual
```

## El DataFrame: la tabla completa

Un DataFrame es un conjunto de Series que comparten el mismo índice: una tabla con filas y columnas, cada columna con su nombre. Es *la* estructura de Pandas.

La forma más clara de crear uno a mano es con un diccionario, donde cada clave es una columna:

```python
df = pd.DataFrame({
    "nombre": ["Ada", "Luis", "Marta", "Juan"],
    "edad":   [20, 22, 21, 23],
    "ciclo":  ["DAW", "DAW", "ASIR", "DAW"],
    "nota":   [9, 5, 7, 4],
})
print(df)
#   nombre  edad ciclo  nota
# 0    Ada    20   DAW     9
# 1   Luis    22   DAW     5
# 2  Marta    21  ASIR     7
# 3   Juan    23   DAW     4
```

¿Reconoces la estructura? Es la lista de diccionarios del módulo 03, pero con todas las herramientas de análisis encima.

## Lo primero que haces con cualquier tabla: mirarla

Cuando cargas datos reales, no los imprimes enteros (pueden ser miles de filas). Los inspeccionas:

```python
df.head()       # las primeras 5 filas
df.head(3)      # las primeras 3
df.tail()       # las últimas 5
df.shape        # (4, 4)  -> filas, columnas
df.columns      # nombres de columnas
df.index        # el índice
df.dtypes       # el tipo de dato de cada columna
df.info()       # resumen: columnas, tipos, nulos, memoria
df.describe()   # estadísticas de las columnas numéricas
```

`df.info()` y `df.describe()` son tus dos primeras paradas con cualquier dataset. `info()` te dice si hay valores faltantes y de qué tipo es cada columna. `describe()` te da media, mínimo, máximo y cuartiles de un vistazo.

```python
print(df.describe())
#             edad      nota
# count   4.000000  4.000000
# mean   21.500000  6.250000
# std     1.290994  2.217356
# min    20.000000  4.000000
# 25%    20.750000  4.750000
# 50%    21.500000  6.000000
# 75%    22.250000  7.500000
# max    23.000000  9.000000
```

## Acceder a columnas

Una columna es una Series. La sacas por su nombre:

```python
df["nombre"]            # la columna nombre
df[["nombre", "nota"]]  # varias columnas (lista dentro de corchetes -> DataFrame)
```

Fíjate en los dobles corchetes para varias columnas: por dentro le pasas una *lista* de nombres. Un solo nombre devuelve una Series; una lista de nombres devuelve un DataFrame.

Sobre una columna numérica, todos los métodos de agregación funcionan:

```python
df["nota"].mean()     # 6.25
df["nota"].max()      # 9
df["edad"].sum()      # 86
df["ciclo"].unique()  # array(['DAW', 'ASIR'])  valores distintos
df["ciclo"].value_counts()  # cuántos de cada uno
# DAW     3
# ASIR    1
```

`value_counts()` es oro puro: te cuenta cuántas veces aparece cada valor de una columna. Lo usarás sin parar.

## Crear y modificar columnas

Una columna nueva se crea asignándola. Y como Pandas vectoriza (gracias a NumPy), operas sobre la columna entera de golpe:

```python
# Columna calculada a partir de otra
df["nota_sobre_100"] = df["nota"] * 10

# Columna a partir de varias
df["aprobado"] = df["nota"] >= 5   # columna de True/False

print(df)
#   nombre  edad ciclo  nota  nota_sobre_100  aprobado
# 0    Ada    20   DAW     9              90      True
# 1   Luis    22   DAW     5              50      True
# 2  Marta    21  ASIR     7              70      True
# 3   Juan    23   DAW     4              40     False
```

Ni un bucle. Esta es la promesa de Pandas: pensar en columnas, no en filas una por una.

## Cargar datos de verdad: leer un CSV

En la práctica no escribes los datos a mano: los lees de un archivo. El formato más común es el CSV (valores separados por comas):

```python
df = pd.read_csv("alumnos.csv")
```

`read_csv` tiene parámetros para los casos que se tuercen, que son muchos:

```python
df = pd.read_csv(
    "alumnos.csv",
    sep=";",            # si el separador es ; en vez de , (típico en España)
    encoding="utf-8",   # o "latin-1" si las tildes salen raras
    decimal=",",        # si los decimales usan coma (1,5 en vez de 1.5)
)
```

Pandas también lee Excel (`pd.read_excel("datos.xlsx")`), JSON (`pd.read_json`) y bases de datos. La estructura que obtienes siempre es un DataFrame, así que todo lo que aprendas se aplica venga de donde venga el dato.

## Guardar resultados

Cuando termines, exportas:

```python
df.to_csv("resultado.csv", index=False)   # index=False para no escribir el índice
df.to_excel("resultado.xlsx", index=False)
```

`index=False` evita que Pandas añada una columna extra con el número de fila. Casi siempre lo quieres así.

## Un dataset de práctica para los ejercicios

Para los ejercicios usa este DataFrame. Cópialo tal cual:

```python
import pandas as pd

ventas = pd.DataFrame({
    "producto":  ["Teclado", "Ratón", "Monitor", "Webcam", "Teclado", "Monitor", "Ratón", "Auriculares"],
    "categoria": ["Periférico", "Periférico", "Pantalla", "Periférico", "Periférico", "Pantalla", "Periférico", "Audio"],
    "precio":    [25, 15, 150, 40, 25, 180, 18, 60],
    "unidades":  [4, 7, 2, 3, 5, 1, 9, 6],
    "ciudad":    ["Madrid", "Sevilla", "Madrid", "Bilbao", "Sevilla", "Madrid", "Bilbao", "Sevilla"],
})
```

---

## Ejercicios

Usa el DataFrame `ventas` de arriba en todos.

**07.1** — Muestra las primeras 3 filas, la forma de la tabla (filas y columnas) y el tipo de cada columna.

**07.2** — Muestra el resumen estadístico de las columnas numéricas con `describe()`. ¿Cuál es el precio medio?

**07.3** — Crea una columna nueva `ingreso` que sea `precio * unidades`. Muestra la tabla resultante.

**07.4** — ¿Cuántos productos distintos hay en la columna `producto`? ¿Cuántas ventas hay por cada ciudad? (Pista: `unique` y `value_counts`.)

**07.5** — Calcula el ingreso total de todas las ventas (suma de la columna `ingreso` que creaste).

**07.6** — Crea una columna `cara` que valga `True` si el precio supera 50 y `False` si no. ¿Cuántos productos son "caros"?

**07.7** — Crea un DataFrame con 5 películas (título, director, año, recaudación en millones). Añade una columna `decada` calculada a partir del año. Ordena por recaudación descendente.

**07.8** — Dado un DataFrame de alumnos (nombre, nota_teoria, nota_practica), calcula `nota_final` = 40% teoría + 60% práctica, redondéala a un decimal y añade una columna `aprobado` (True si >= 5).

**07.9** — Crea una `pd.Series` con ventas de los 7 días de la semana, con los días como índice. Calcula la media solo de los días laborables (lunes–viernes) sin escribir esos valores a mano.

---

<details markdown="1">
<summary>Soluciones</summary>

**07.1**
```python
print(ventas.head(3))
print(ventas.shape)    # (8, 5)
print(ventas.dtypes)
```

---

**07.2**
```python
print(ventas.describe())
# El precio medio (mean de 'precio') es 64.125
```

---

**07.3**
```python
ventas["ingreso"] = ventas["precio"] * ventas["unidades"]
print(ventas)
```

---

**07.4**
```python
print(ventas["producto"].unique())       # 5 productos distintos
print(ventas["producto"].nunique())      # 5
print(ventas["ciudad"].value_counts())
# Madrid     3
# Sevilla    3
# Bilbao     2
```

---

**07.5**
```python
ventas["ingreso"] = ventas["precio"] * ventas["unidades"]
print(ventas["ingreso"].sum())   # 1452
```

---

**07.6**
```python
ventas["cara"] = ventas["precio"] > 50
print(ventas["cara"].sum())   # 3  (True cuenta como 1)
```


---

**07.7**
```python
import pandas as pd

peliculas = pd.DataFrame({
    "titulo": ["El Padrino", "Titanic", "Avengers", "Matrix", "Inception"],
    "director": ["Coppola", "Cameron", "Russo", "Wachowski", "Nolan"],
    "anio": [1972, 1997, 2019, 1999, 2010],
    "recaudacion": [245, 2187, 2798, 463, 836]
})
peliculas["decada"] = (peliculas["anio"] // 10) * 10
print(peliculas.sort_values("recaudacion", ascending=False))
```

---

**07.8**
```python
import pandas as pd

alumnos = pd.DataFrame({
    "nombre": ["Ada", "Leo", "Zara", "Iván", "Mía"],
    "nota_teoria": [6, 4, 8, 5, 7],
    "nota_practica": [7, 5, 9, 6, 4]
})
alumnos["nota_final"] = (
    alumnos["nota_teoria"] * 0.4 + alumnos["nota_practica"] * 0.6
).round(1)
alumnos["aprobado"] = alumnos["nota_final"] >= 5
print(alumnos)
```

---

**07.9**
```python
import pandas as pd

dias = ["Lunes", "Martes", "Miércoles", "Jueves", "Viernes", "Sábado", "Domingo"]
ventas = pd.Series([120, 95, 140, 110, 130, 200, 85], index=dias)
laborables = ventas[:"Viernes"]
print(f"Media laborables: {laborables.mean():.1f}")
```
</details>
