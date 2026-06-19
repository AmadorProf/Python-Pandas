# Módulo 09 — Limpiar datos: nulos, duplicados, tipos y texto

Los datos reales vienen sucios. Faltan valores, hay filas repetidas, los números llegan como texto, las fechas en cinco formatos distintos y los nombres con espacios de sobra. Limpiar es el 80% del trabajo real con datos, no una nota a pie de página. Este módulo te da las herramientas.

## El dataset sucio de trabajo

```python
import pandas as pd
import numpy as np

df = pd.DataFrame({
    "nombre":   ["  Ada", "Luis", "MARTA", "Juan", "Ada", "Eva ", None],
    "edad":     [20, 22, None, 23, 20, 19, 25],
    "ciudad":   ["Madrid", "sevilla", "Madrid", "Bilbao", "Madrid", "Sevilla", "Bilbao"],
    "salario":  ["1.200", "1.500", "2.000", None, "1.200", "1.350", "1.800"],
})
```

Tiene de todo: espacios, mayúsculas inconsistentes, un nulo, una fila duplicada y una columna numérica guardada como texto.

## Detectar valores faltantes

Pandas representa los huecos como `NaN` (Not a Number) o `None`. Primero los localizas:

```python
df.isnull()           # tabla de True/False: True donde falta
df.isnull().sum()     # cuántos nulos por columna  <- el comando clave
df.isnull().sum().sum()  # total de nulos en toda la tabla
```

`df.isnull().sum()` es lo primero que ejecutas con cualquier dataset nuevo. Te dice de un vistazo dónde están los agujeros:

```python
# nombre     1
# edad       1
# ciudad     0
# salario    1
```

## Tratar los faltantes: eliminar o rellenar

Tienes dos estrategias. **Eliminar** las filas (o columnas) con huecos:

```python
df.dropna()                    # elimina filas con ALGÚN nulo
df.dropna(subset=["edad"])     # elimina solo si falta la edad
df.dropna(axis=1)              # elimina columnas con nulos (rara vez se quiere)
```

O **rellenar** los huecos con un valor sensato:

```python
df["edad"].fillna(df["edad"].mean())   # rellena con la media
df["edad"].fillna(0)                   # rellena con un fijo
df["ciudad"].fillna("Desconocida")     # para texto
df["edad"].fillna(df["edad"].median()) # la mediana resiste mejor los valores extremos
```

¿Cuál elegir? Depende. Si te sobran datos y faltan pocos, elimina. Si cada fila cuenta, rellena con un valor que no distorsione (la mediana para números suele ser más prudente que la media). No hay regla universal; piensa qué tiene sentido para tus datos.

Cuidado: muchos métodos de Pandas **no modifican el DataFrame original**, devuelven una copia. Para que el cambio persista, reasigna:

```python
df["edad"] = df["edad"].fillna(df["edad"].median())
```

## Eliminar duplicados

```python
df.duplicated()         # marca True las filas repetidas (a partir de la 2ª)
df.duplicated().sum()   # cuántas hay
df = df.drop_duplicates()             # elimina filas idénticas
df = df.drop_duplicates(subset=["nombre"])  # duplicados según una columna
```

Antes de buscar duplicados, conviene normalizar el texto: "Ada" y " Ada" con espacio no se consideran iguales hasta que limpias los espacios. Por eso el orden importa: primero normaliza, luego deduplica.

## Limpiar texto con .str

El accesor `.str` aplica métodos de cadena a la columna entera. Es tu mejor amigo para normalizar:

```python
df["nombre"] = df["nombre"].str.strip()       # quita espacios de los extremos
df["nombre"] = df["nombre"].str.title()        # Primera Letra En Mayúscula
df["ciudad"] = df["ciudad"].str.capitalize()   # solo la primera del texto
df["ciudad"] = df["ciudad"].str.lower()        # todo minúsculas
df["nombre"] = df["nombre"].str.replace(".", "", regex=False)
```

Tras `strip()` y `title()`, "  Ada", "Ada" y todo lo demás convergen a "Ada", y *ahora* sí puedes deduplicar bien.

## Convertir tipos: cuando los números llegan como texto

La columna `salario` tiene valores como `"1.200"`: es texto, no puedes sumarlo ni promediarlo. Hay que convertirlo. Primero quitas el separador de miles, luego conviertes:

```python
df["salario"] = df["salario"].str.replace(".", "", regex=False)  # "1.200" -> "1200"
df["salario"] = pd.to_numeric(df["salario"], errors="coerce")     # texto -> número
```

`pd.to_numeric` con `errors="coerce"` convierte lo que puede y pone `NaN` en lo que no (en vez de reventar). Es la forma robusta. Para tipos concretos también tienes `astype`:

```python
df["edad"] = df["edad"].astype("int")      # a entero (falla si hay NaN)
df["salario"] = df["salario"].astype("float")
df["ciudad"] = df["ciudad"].astype("category")  # para columnas con pocos valores repetidos
```

`astype("category")` ahorra memoria cuando una columna tiene pocos valores distintos repetidos muchas veces (ciudades, categorías, sí/no).

## Renombrar columnas

Las columnas de datos reales vienen con nombres horribles: espacios, mayúsculas, acentos. Renómbralas:

```python
df = df.rename(columns={"salario": "salario_eur", "edad": "edad_anios"})

# O reemplazar todos los nombres de golpe:
df.columns = ["nombre", "edad", "ciudad", "salario"]

# Truco para normalizar todos a la vez:
df.columns = df.columns.str.strip().str.lower().str.replace(" ", "_")
```

## Reemplazar y mapear valores

Para sustituir valores concretos o traducir categorías:

```python
df["ciudad"] = df["ciudad"].replace("Madird", "Madrid")   # corregir un error
df["nivel"] = df["edad"].map(lambda x: "joven" if x < 21 else "adulto")
```

`map` aplica una función a cada valor de la columna. Aquí usamos una *función lambda* (una función anónima de una línea): `lambda x: ...` es una mini-función sin nombre, cómoda para transformaciones rápidas. Para reglas con diccionario:

```python
traduccion = {"DAW": "Desarrollo Web", "ASIR": "Sistemas", "DAM": "Multiplataforma"}
df["ciclo_largo"] = df["ciclo"].map(traduccion)
```

## apply: transformaciones a medida

Cuando la transformación es más compleja, `apply` ejecuta una función sobre cada elemento de una columna (o cada fila):

```python
def clasificar(nota):
    if nota >= 9:   return "Sobresaliente"
    elif nota >= 7: return "Notable"
    elif nota >= 5: return "Aprobado"
    else:           return "Suspenso"

df["calificacion"] = df["nota"].apply(clasificar)
```

`apply` es flexible pero más lento que las operaciones vectorizadas. Si puedes hacer algo con `&`, `|` o aritmética de columnas, hazlo así; reserva `apply` para lo que no se puede vectorizar fácil.

## Una rutina de limpieza típica, de principio a fin

```python
# 1. Inspeccionar
print(df.info())
print(df.isnull().sum())

# 2. Normalizar texto
df["nombre"] = df["nombre"].str.strip().str.title()
df["ciudad"] = df["ciudad"].str.strip().str.capitalize()

# 3. Convertir tipos
df["salario"] = pd.to_numeric(df["salario"].str.replace(".", "", regex=False),
                              errors="coerce")

# 4. Tratar nulos
df["edad"] = df["edad"].fillna(df["edad"].median())
df = df.dropna(subset=["nombre"])

# 5. Eliminar duplicados
df = df.drop_duplicates()

# 6. Comprobar
print(df.isnull().sum())
print(df.dtypes)
```

Este esqueleto te sirve para casi cualquier dataset. Adáptalo y guárdalo.

---

## Ejercicios

Parte del `df` sucio del principio del módulo.

**09.1** — Cuenta cuántos valores nulos hay en cada columna y cuántos en total.

**09.2** — Normaliza la columna `nombre`: quita los espacios y pon cada nombre con la primera letra en mayúscula. Luego comprueba cuántos nombres únicos quedan.

**09.3** — Normaliza la columna `ciudad` para que todas tengan el mismo formato (primera letra mayúscula, resto minúscula). ¿Cuántas ventas hay por ciudad después?

**09.4** — Convierte la columna `salario` a números de verdad (quita el punto de los miles y conviértela a numérica). Calcula el salario medio.

**09.5** — Rellena los valores faltantes de `edad` con la mediana de la columna, y los de `salario` con su media.

**09.6** — Tras normalizar el texto, elimina las filas duplicadas. ¿Cuántas filas quedan?

**09.7** — Crea una columna `franja` que clasifique la edad en "menor de 21" o "21 o más", usando `apply` o `map`.

---

<details markdown="1">
<summary>Soluciones</summary>

**09.1**
```python
print(df.isnull().sum())
print("Total:", df.isnull().sum().sum())   # 3
```

---

**09.2**
```python
df["nombre"] = df["nombre"].str.strip().str.title()
print(df["nombre"].nunique())   # cuenta nombres únicos (los None no cuentan)
```

---

**09.3**
```python
df["ciudad"] = df["ciudad"].str.strip().str.capitalize()
print(df["ciudad"].value_counts())
# Madrid     3
# Bilbao     2
# Sevilla    2
```

---

**09.4**
```python
df["salario"] = df["salario"].str.replace(".", "", regex=False)
df["salario"] = pd.to_numeric(df["salario"], errors="coerce")
print(df["salario"].mean())   # ~1508.33 (ignora el NaN)
```

---

**09.5**
```python
df["edad"] = df["edad"].fillna(df["edad"].median())
df["salario"] = df["salario"].fillna(df["salario"].mean())
print(df.isnull().sum())
```

---

**09.6**
```python
df["nombre"] = df["nombre"].str.strip().str.title()
df = df.drop_duplicates()
print(len(df))
```
Tras normalizar, "  Ada" y "Ada" pasan a ser iguales, así que la fila repetida de Ada se elimina.

---

**09.7**
```python
df["franja"] = df["edad"].apply(lambda x: "menor de 21" if x < 21 else "21 o más")
print(df[["edad", "franja"]])
```

</details>
