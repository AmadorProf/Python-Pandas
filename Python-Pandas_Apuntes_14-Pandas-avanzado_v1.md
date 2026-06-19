# Módulo 14 — Pandas avanzado: apply, query, MultiIndex y rendimiento

Los módulos anteriores cubren el 80% del trabajo con Pandas. Este módulo cubre el 20% restante que marca la diferencia cuando los datasets crecen, el código se complica o necesitas algo más que un `groupby` simple. Son cuatro herramientas independientes; puedes leerlas en cualquier orden.

## apply: ejecutar una función sobre filas o columnas

`apply` te permite aplicar cualquier función a cada elemento de una Serie o a cada fila/columna de un DataFrame. Úsalo cuando ninguna función vectorizada de Pandas hace exactamente lo que necesitas.

Sobre una Serie (opera sobre cada valor):

```python
df["categoria_upper"] = df["categoria"].apply(str.upper)

# Con una función propia:
def clasificar(precio):
    if precio < 20:
        return "económico"
    elif precio < 100:
        return "medio"
    return "premium"

df["rango"] = df["precio"].apply(clasificar)
```

Sobre un DataFrame con `axis=1` (opera sobre cada fila):

```python
def etiqueta_fila(fila):
    if fila["stock"] == 0:
        return "sin stock"
    elif fila["precio"] > 100 and fila["stock"] < 10:
        return "escaso y caro"
    return "normal"

df["etiqueta"] = df.apply(etiqueta_fila, axis=1)
```

Para transformar dentro de una Serie con una función anónima:

```python
df["precio_con_iva"] = df["precio"].apply(lambda x: round(x * 1.21, 2))
```

`apply` es flexible pero más lento que las operaciones vectorizadas nativas. Si puedes hacer lo mismo con `+`, `*`, `.str.upper()` o `np.where`, hazlo así.

## map y replace: transformar valores uno a uno

`map` (en una Serie) aplica un diccionario o función a cada valor. Perfecto para recodificar categorías:

```python
equivalencias = {"A": "Alta", "B": "Media", "C": "Baja"}
df["prioridad_texto"] = df["prioridad"].map(equivalencias)
```

Los valores que no están en el diccionario quedan como `NaN`. Si prefieres mantenerlos, usa `replace`:

```python
df["estado"] = df["estado"].replace({"activo": "Activo", "baja": "Dado de baja"})
```

## query: filtrar con una cadena de texto

En lugar de `df[(df["precio"] > 50) & (df["categoria"] == "Electrónica")]` puedes escribir:

```python
resultado = df.query("precio > 50 and categoria == 'Electrónica'")
```

Es más legible, especialmente cuando tienes condiciones largas. Para usar variables externas:

```python
umbral = 50
categoria = "Electrónica"
resultado = df.query("precio > @umbral and categoria == @categoria")
```

El `@` referencia variables del entorno. `query` no sustituye al filtrado booleano en todos los casos (no funciona bien con nombres de columnas que tienen espacios ni con operaciones complejas), pero cuando encaja hace el código mucho más legible.

## Method chaining: encadenar operaciones sin variables intermedias

En lugar de guardar el resultado de cada paso en una variable:

```python
df2 = df[df["precio"] > 10]
df3 = df2.assign(importe=df2["precio"] * df2["cantidad"])
df4 = df3.groupby("categoria")["importe"].sum()
df5 = df4.sort_values(ascending=False)
result = df5.head(5)
```

Puedes encadenarlo todo:

```python
result = (
    df
    .query("precio > 10")
    .assign(importe=lambda x: x["precio"] * x["cantidad"])
    .groupby("categoria")["importe"].sum()
    .sort_values(ascending=False)
    .head(5)
)
```

`assign` devuelve siempre una copia del DataFrame con la columna nueva, lo que lo hace seguro para encadenar. Las `lambda` dentro de `assign` reciben el DataFrame en su estado actual en ese punto de la cadena.

El encadenamiento no es siempre mejor: úsalo cuando el flujo sea lineal y cada paso sea claro. Si necesitas reutilizar un estado intermedio, guarda una variable.

## MultiIndex: índices con varios niveles

Cuando haces un `groupby` con varias claves, el resultado tiene un índice jerárquico (MultiIndex):

```python
resumen = df.groupby(["categoria", "ciudad"])["importe"].sum()
print(resumen)
# categoria    ciudad
# Electrónica  Madrid     4500
#              Sevilla    2300
# Periféricos  Bilbao      890
# ...

# Acceder a un nivel:
print(resumen["Electrónica"])
print(resumen["Electrónica"]["Madrid"])

# Convertir el MultiIndex en columnas normales:
resumen_df = resumen.reset_index()
print(resumen_df)
```

`reset_index()` convierte el MultiIndex en columnas normales. Es la operación que más harás después de un `groupby` múltiple.

Para acceder con `loc` en un MultiIndex:

```python
print(resumen.loc[("Electrónica", "Madrid")])   # 4500
```

## Optimizar memoria y velocidad

Un DataFrame con un millón de filas puede usar cientos de MB de memoria innecesariamente si los tipos no son los correctos.

```python
# Ver el uso actual:
print(df.memory_usage(deep=True).sum() / 1024**2, "MB")

# Convertir columnas numéricas al tipo más pequeño posible:
df["cantidad"] = df["cantidad"].astype("int16")    # si los valores caben en -32768..32767
df["precio"] = df["precio"].astype("float32")      # menos precisión, menos memoria

# Convertir columnas categóricas con pocos valores únicos:
df["categoria"] = df["categoria"].astype("category")
df["ciudad"] = df["ciudad"].astype("category")

print(df.memory_usage(deep=True).sum() / 1024**2, "MB")   # reducción significativa
```

Las columnas de tipo `category` funcionan igual que `object` para filtrar y agrupar, pero ocupan mucho menos si hay pocos valores únicos repetidos muchas veces (como países, categorías, estados).

`pd.to_numeric(serie, downcast="integer")` o `pd.to_numeric(serie, downcast="float")` elige automáticamente el tipo más pequeño.

## np.where y pd.cut: crear columnas condicionales sin apply

Cuando la lógica es una condición binaria, `np.where` es mucho más rápido que `apply`:

```python
import numpy as np
df["rentable"] = np.where(df["importe"] > 100, "sí", "no")
```

Para rangos, `pd.cut` discretiza una variable continua:

```python
df["tramo_precio"] = pd.cut(
    df["precio"],
    bins=[0, 20, 100, float("inf")],
    labels=["económico", "medio", "premium"]
)
```

Y `pd.qcut` divide en cuartiles (o cualquier cuantil) de forma que cada tramo tenga el mismo número de elementos:

```python
df["cuartil"] = pd.qcut(df["precio"], q=4, labels=["Q1", "Q2", "Q3", "Q4"])
```

---

## Ejercicios

**14.1** — Usando `apply` con una función propia, añade una columna `margen` que clasifique cada venta como "alto" (importe > 200), "medio" (entre 50 y 200) o "bajo" (< 50).

**14.2** — Recodifica la columna `estado` de tu DataFrame con `map`. Prueba qué pasa con valores que no están en el diccionario y luego arréglalo con `fillna`.

**14.3** — Escribe la misma consulta de dos formas: (a) con filtrado booleano clásico, (b) con `query`. Usa al menos dos condiciones combinadas con `and`/`or`.

**14.4** — Encadena al menos 4 operaciones con method chaining para calcular el top 3 de ciudades por importe medio de pedido, excluyendo los pedidos con importe inferior a 20.

**14.5** — Haz un `groupby` por `["categoria", "ciudad"]` y calcula la suma de importes. Convierte el resultado con `reset_index()`. Luego accede solo a los datos de una categoría concreta usando `loc` en el MultiIndex (antes del reset).

**14.6** — Comprueba la memoria que ocupa un DataFrame con columnas de tipo `object` y `float64`. Conviértelo a tipos más eficientes (`category` para columnas con pocos valores únicos, `float32` o `int16` para numéricas que lo permitan) y muestra el ahorro en MB.

---

<details markdown="1">
<summary>Soluciones</summary>

**14.1**
```python
def clasificar_margen(fila):
    if fila["importe"] > 200:
        return "alto"
    elif fila["importe"] >= 50:
        return "medio"
    return "bajo"

df["importe"] = df["precio_unitario"] * df["cantidad"]
df["margen"] = df.apply(clasificar_margen, axis=1)
print(df["margen"].value_counts())
```

---

**14.2**
```python
mapa = {"pendiente": "En espera", "enviado": "En tránsito", "entregado": "Completado"}
df["estado_texto"] = df["estado"].map(mapa)
# Los valores no encontrados son NaN:
print(df["estado_texto"].isna().sum())
# Rellenar los que no están en el mapa con el valor original:
df["estado_texto"] = df["estado_texto"].fillna(df["estado"])
```

---

**14.3**
```python
# (a) filtrado booleano
resultado_a = df[(df["precio_unitario"] > 50) & (df["categoria"] == "Electrónica")]

# (b) query
resultado_b = df.query("precio_unitario > 50 and categoria == 'Electrónica'")

print(resultado_a.shape == resultado_b.shape)   # True
```

---

**14.4**
```python
resultado = (
    df
    .assign(importe=lambda x: x["precio_unitario"] * x["cantidad"])
    .query("importe >= 20")
    .groupby("ciudad")["importe"]
    .mean()
    .sort_values(ascending=False)
    .head(3)
)
print(resultado)
```

---

**14.5**
```python
resumen = df.groupby(["categoria", "ciudad"])["importe"].sum()
print(resumen)

# Acceder a una categoría con loc en el MultiIndex:
print(resumen.loc["Electrónica"])

# Convertir a DataFrame plano:
resumen_df = resumen.reset_index()
print(resumen_df.head())
```

---

**14.6**
```python
print("Antes:", df.memory_usage(deep=True).sum() / 1024**2, "MB")

for col in df.select_dtypes("object").columns:
    if df[col].nunique() / len(df) < 0.1:   # menos del 10% de valores únicos
        df[col] = df[col].astype("category")

df["cantidad"] = df["cantidad"].astype("int16")
df["precio_unitario"] = df["precio_unitario"].astype("float32")

print("Después:", df.memory_usage(deep=True).sum() / 1024**2, "MB")
```

</details>
