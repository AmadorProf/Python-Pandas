# Módulo 10 — Agrupar, pivotar y unir tablas

Aquí Pandas pasa de "Excel programado" a herramienta de análisis seria. Agrupar (`groupby`) responde a "¿cuánto vendió cada ciudad?". Pivotar reorganiza una tabla para verla desde otro ángulo. Unir (`merge`) combina dos tablas relacionadas, como un JOIN de SQL. Estas tres operaciones son las que separan a quien sabe Pandas de quien solo lo usa para mirar datos.

## El dataset de trabajo

```python
import pandas as pd

ventas = pd.DataFrame({
    "fecha":     ["2026-01", "2026-01", "2026-02", "2026-02", "2026-01", "2026-03", "2026-02", "2026-03"],
    "vendedor":  ["Ada", "Luis", "Ada", "Marta", "Marta", "Luis", "Ada", "Marta"],
    "ciudad":    ["Madrid", "Sevilla", "Madrid", "Bilbao", "Bilbao", "Sevilla", "Madrid", "Bilbao"],
    "producto":  ["Monitor", "Teclado", "Monitor", "Ratón", "Teclado", "Monitor", "Ratón", "Teclado"],
    "importe":   [450, 75, 600, 90, 50, 300, 120, 100],
})
```

## groupby: el patrón "dividir, aplicar, combinar"

`groupby` hace tres cosas: divide la tabla en grupos según una columna, aplica una operación a cada grupo y combina los resultados en una tabla nueva. La pregunta "¿cuánto ha vendido cada ciudad?" se traduce directamente:

```python
ventas.groupby("ciudad")["importe"].sum()
# ciudad
# Bilbao     240
# Madrid    1170
# Sevilla    375
```

Lee la línea por partes: agrupa por `ciudad`, coge la columna `importe`, y suma dentro de cada grupo. Cambia `sum` por lo que necesites:

```python
ventas.groupby("ciudad")["importe"].mean()    # ticket medio por ciudad
ventas.groupby("ciudad")["importe"].count()   # número de ventas por ciudad
ventas.groupby("ciudad")["importe"].max()     # venta más grande de cada ciudad
ventas.groupby("vendedor")["importe"].sum()   # total por vendedor
```

## Agrupar por varias columnas

Pasa una lista para agrupar por combinaciones. "¿Cuánto vendió cada vendedor en cada ciudad?":

```python
ventas.groupby(["vendedor", "ciudad"])["importe"].sum()
# vendedor  ciudad
# Ada       Madrid     1170
# Luis      Sevilla     375
# Marta     Bilbao      240
```

El resultado tiene un índice de dos niveles. Para volverlo una tabla plana, añade `reset_index()`:

```python
resumen = ventas.groupby(["vendedor", "ciudad"])["importe"].sum().reset_index()
```

`reset_index()` convierte el índice del groupby otra vez en columnas normales. Lo usarás casi siempre después de agrupar, porque deja el resultado listo para seguir trabajando o exportar.

## agg: varias métricas a la vez

Cuando quieres más de un resumen por grupo, `agg` los calcula todos juntos:

```python
ventas.groupby("ciudad")["importe"].agg(["sum", "mean", "count", "max"])
#         sum   mean  count  max
# ciudad
# Bilbao  240   80.0      3   100
# Madrid 1170  390.0      3   600
# Sevilla 375  187.5      2   300
```

Y puedes aplicar distintas funciones a distintas columnas, con nombres a medida:

```python
ventas.groupby("vendedor").agg(
    total=("importe", "sum"),
    media=("importe", "mean"),
    operaciones=("importe", "count"),
)
```

Esta forma con nombres (`nombre=("columna", "función")`) produce tablas limpias y legibles. Es la que conviene usar en informes.

## Tablas dinámicas con pivot_table

Una tabla dinámica reorganiza los datos: una variable a las filas, otra a las columnas, y los valores agregados en las celdas. Es lo mismo que las tablas dinámicas de Excel, pero reproducible:

```python
ventas.pivot_table(
    index="vendedor",      # filas
    columns="ciudad",      # columnas
    values="importe",      # qué agregar
    aggfunc="sum",         # cómo agregar
    fill_value=0,          # rellenar huecos con 0 en vez de NaN
)
# ciudad    Bilbao  Madrid  Sevilla
# vendedor
# Ada            0    1170        0
# Luis           0       0      375
# Marta        240       0        0
```

De un vistazo ves quién vende dónde. Cambia `index`, `columns` y `aggfunc` para mirar los mismos datos desde otro ángulo. `pivot_table` es `groupby` con presentación bidimensional.

## crosstab: contar combinaciones

Para contar cuántas veces aparece cada combinación de dos variables, `crosstab` es el atajo:

```python
pd.crosstab(ventas["ciudad"], ventas["producto"])
# producto  Monitor  Ratón  Teclado
# ciudad
# Bilbao          0      1        2
# Madrid          2      1        0
# Sevilla         1      0        1
```

## Unir tablas: merge (el JOIN de Pandas)

Los datos suelen estar repartidos en varias tablas. Una con las ventas, otra con los datos de cada vendedor. `merge` las combina por una columna común, igual que un JOIN en SQL (terreno que ya conoces de tus clases de bases de datos).

```python
vendedores = pd.DataFrame({
    "vendedor": ["Ada", "Luis", "Marta", "Iván"],
    "equipo":   ["Norte", "Sur", "Norte", "Sur"],
    "antiguedad": [5, 2, 8, 1],
})

combinado = ventas.merge(vendedores, on="vendedor")
```

`on="vendedor"` es la columna por la que casan las dos tablas. Ahora cada venta lleva pegado el equipo y la antigüedad de su vendedor, y puedes agrupar por equipo:

```python
combinado.groupby("equipo")["importe"].sum()
```

## Los cuatro tipos de unión

Igual que en SQL, importa qué haces con las filas que no casan. Lo controla `how`:

```python
ventas.merge(vendedores, on="vendedor", how="inner")  # solo los que están en AMBAS (por defecto)
ventas.merge(vendedores, on="vendedor", how="left")   # todas las de ventas, completa lo que pueda
ventas.merge(vendedores, on="vendedor", how="right")  # todas las de vendedores
ventas.merge(vendedores, on="vendedor", how="outer")  # todas las de las dos
```

`inner` se queda solo con las coincidencias. `left` conserva todas las filas de la tabla izquierda (las ventas) aunque no encuentren pareja: ahí Iván, que no tiene ventas, no aparece con `inner` pero sí con `outer`. El que más usarás es `left`: "quiero todas mis ventas, enriquecidas con datos del vendedor cuando los haya".

Si las columnas se llaman distinto en cada tabla, usa `left_on` y `right_on`:

```python
ventas.merge(otra, left_on="vendedor", right_on="nombre_empleado")
```

## concat: apilar tablas

`merge` une por columnas (a lo ancho). `concat` apila filas (a lo largo): juntar las ventas de enero con las de febrero, por ejemplo:

```python
enero = ventas[ventas["fecha"] == "2026-01"]
febrero = ventas[ventas["fecha"] == "2026-02"]
todo = pd.concat([enero, febrero], ignore_index=True)
```

`ignore_index=True` renumera el índice de 0 en adelante en lugar de arrastrar los índices originales.

## Encadenar operaciones: el flujo real de análisis

En la práctica encadenas varias operaciones para responder a una pregunta concreta. "Top 3 de vendedores por ingresos del primer trimestre":

```python
(ventas
    .groupby("vendedor")["importe"].sum()
    .sort_values(ascending=False)
    .head(3))
```

Los paréntesis exteriores te dejan partir la cadena en líneas para que se lea bien. Cada paso recibe el resultado del anterior. Así se escribe análisis de datos legible.

---

## Ejercicios

Usa la tabla `ventas` (y `vendedores` donde se indique).

**10.1** — Calcula el importe total vendido por cada vendedor.

**10.2** — Calcula el importe medio y el número de operaciones por ciudad, en una sola tabla con `agg`.

**10.3** — ¿Cuánto se vendió de cada producto en cada mes (`fecha`)? Usa `groupby` con dos columnas.

**10.4** — Crea una tabla dinámica con los productos en las filas, las ciudades en las columnas y la suma de importes en las celdas. Rellena los huecos con 0.

**10.5** — Une `ventas` con `vendedores` por la columna `vendedor`. Después, calcula el importe total por equipo.

**10.6** — Usa un `merge` con `how="left"` y otro con `how="inner"` entre `ventas` y `vendedores`. Explica por qué Iván aparece o no en cada caso.

**10.7** — Encuentra, para cada vendedor, su venta más alta (el importe máximo) y ordénalos de mayor a menor.

**10.8** — ¿Qué ciudad tiene el ticket medio (importe medio por venta) más alto?

**10.9** — Usa `pd.merge` con `how="outer"` entre dos DataFrames con algunas filas sin par. Identifica qué filas están solo en cada uno (columnas del otro lado serán NaN).

**10.10** — Crea una `pivot_table` que muestre el importe total por vendedor (filas) y ciudad (columnas). Añade totales con `margins=True`.

**10.11** — Usa `groupby` + `transform("mean")` para añadir al DataFrame original una columna con la media del importe del grupo de cada fila, sin reducir el número de filas.

---

<details markdown="1">
<summary>Soluciones</summary>

**10.1**
```python
print(ventas.groupby("vendedor")["importe"].sum())
# Ada     1170
# Luis     375
# Marta    240
```

---

**10.2**
```python
print(ventas.groupby("ciudad")["importe"].agg(["mean", "count"]))
```

---

**10.3**
```python
print(ventas.groupby(["fecha", "producto"])["importe"].sum())
```

---

**10.4**
```python
print(ventas.pivot_table(index="producto", columns="ciudad",
                         values="importe", aggfunc="sum", fill_value=0))
```

---

**10.5**
```python
vendedores = pd.DataFrame({
    "vendedor": ["Ada", "Luis", "Marta", "Iván"],
    "equipo":   ["Norte", "Sur", "Norte", "Sur"],
    "antiguedad": [5, 2, 8, 1],
})
combinado = ventas.merge(vendedores, on="vendedor")
print(combinado.groupby("equipo")["importe"].sum())
# Norte    1410   (Ada + Marta)
# Sur       375   (Luis)
```

---

**10.6**
```python
izq = ventas.merge(vendedores, on="vendedor", how="left")
inter = ventas.merge(vendedores, on="vendedor", how="inner")
```
Iván no tiene ninguna venta. Como `merge` parte de las filas de `ventas`, Iván no aparece en *ninguno* de los dos (no hay filas de ventas suyas que casar). Si hicieras `how="right"` o `how="outer"`, sí saldría Iván, con sus importes a `NaN`. La diferencia entre `left` e `inner` se notaría si hubiera una venta con un vendedor que no está en la tabla `vendedores`: `left` la conserva, `inner` la descarta.

---

**10.7**
```python
print(ventas.groupby("vendedor")["importe"].max().sort_values(ascending=False))
# Ada     600
# Luis    300
# Marta   100
```

---

**10.8**
```python
print(ventas.groupby("ciudad")["importe"].mean().sort_values(ascending=False).head(1))
# Madrid    390.0
```


---

**10.9**
```python
import pandas as pd

df1 = pd.DataFrame({"id": [1, 2, 3, 4], "valor_a": ["a1", "a2", "a3", "a4"]})
df2 = pd.DataFrame({"id": [2, 3, 5, 6], "valor_b": ["b2", "b3", "b5", "b6"]})

outer = pd.merge(df1, df2, on="id", how="outer")
print(outer)
print("Solo en df1:", outer[outer["valor_b"].isna()]["id"].tolist())
print("Solo en df2:", outer[outer["valor_a"].isna()]["id"].tolist())
```

---

**10.10**
```python
tabla = pd.pivot_table(
    ventas,
    values="importe",
    index="vendedor",
    columns="ciudad",
    aggfunc="sum",
    fill_value=0,
    margins=True,
    margins_name="Total"
)
print(tabla)
```

---

**10.11**
```python
ventas["media_ciudad"] = ventas.groupby("ciudad")["importe"].transform("mean")
print(ventas[["ciudad", "importe", "media_ciudad"]].head(8))
```
`transform` devuelve una Serie del mismo tamaño que el DataFrame original, a diferencia de `agg` que reduce a una fila por grupo.
</details>
