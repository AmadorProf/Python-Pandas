# Módulo 11 — Fechas, visualización y proyecto final

El último módulo cierra dos temas que aparecen en casi todo análisis real (las fechas y los gráficos) y termina con un proyecto que junta todo lo aprendido desde el módulo 00. Si lo completas, sabes Pandas de verdad.

## Fechas: el tipo datetime

Una fecha guardada como texto ("2026-03-15") no sirve para calcular: no puedes restar dos fechas ni filtrar por mes. Hay que convertirla al tipo `datetime`:

```python
import pandas as pd

df = pd.DataFrame({
    "fecha": ["2026-01-15", "2026-02-20", "2026-02-28", "2026-03-10"],
    "ventas": [100, 250, 175, 300],
})
df["fecha"] = pd.to_datetime(df["fecha"])
print(df.dtypes)   # fecha -> datetime64[ns]
```

`pd.to_datetime` entiende muchos formatos solo. Si el tuyo es raro, se lo dices: `pd.to_datetime(col, format="%d/%m/%Y")`.

## Extraer partes de una fecha con .dt

Una vez es `datetime`, el accesor `.dt` te da acceso a sus componentes, igual que `.str` con el texto:

```python
df["anio"] = df["fecha"].dt.year       # 2026
df["mes"] = df["fecha"].dt.month       # 1, 2, 2, 3
df["dia"] = df["fecha"].dt.day
df["dia_semana"] = df["fecha"].dt.day_name()   # Monday, Tuesday...
df["nombre_mes"] = df["fecha"].dt.month_name()
```

Con eso ya puedes agrupar por mes, por día de la semana, por año... todo lo del módulo 10 aplicado al tiempo:

```python
df.groupby(df["fecha"].dt.month)["ventas"].sum()   # ventas por mes
```

## Filtrar por fechas

Con la columna en `datetime`, las comparaciones funcionan como con números:

```python
df[df["fecha"] >= "2026-02-01"]                       # desde febrero
df[(df["fecha"] >= "2026-02-01") & (df["fecha"] < "2026-03-01")]  # solo febrero
```

## El índice temporal y resample

Si pones la fecha como índice, desbloqueas `resample`, que reagrupa por periodos de tiempo (días, semanas, meses) con una sola instrucción. Es el `groupby` de las fechas:

```python
df = df.set_index("fecha")
df["ventas"].resample("ME").sum()   # total por mes ("ME" = month end)
df["ventas"].resample("W").mean()   # media semanal
```

`resample` entiende códigos como `"D"` (día), `"W"` (semana), `"ME"` (fin de mes), `"YE"` (año). Es lo que usarías para convertir ventas diarias en un resumen mensual.

## Visualizar: gráficos en una línea

Pandas dibuja directamente apoyándose en Matplotlib. Instálalo si hace falta:

```bash
pip install matplotlib
```

```python
import matplotlib.pyplot as plt

df = pd.DataFrame({
    "mes": ["Ene", "Feb", "Mar", "Abr"],
    "ventas": [100, 250, 175, 300],
})

df.plot(x="mes", y="ventas", kind="line", marker="o")
plt.title("Ventas por mes")
plt.show()
```

El parámetro `kind` elige el tipo de gráfico:

```python
df.plot(x="mes", y="ventas", kind="bar")    # barras: comparar categorías
df.plot(x="mes", y="ventas", kind="line")   # líneas: evolución temporal
df["ventas"].plot(kind="hist", bins=10)     # histograma: distribución
df["ventas"].plot(kind="box")               # caja: dispersión y atípicos
df.plot(x="precio", y="ventas", kind="scatter")  # dispersión: relación entre dos variables
```

Regla rápida para elegir: barras para comparar categorías, líneas para evolución en el tiempo, histograma para ver cómo se reparten los valores, dispersión para relacionar dos variables numéricas.

Guardar el gráfico en archivo en lugar de mostrarlo:

```python
plt.savefig("grafico.png", dpi=150, bbox_inches="tight")
```

## Graficar el resultado de un groupby

El flujo habitual: agrupas y dibujas el resultado del tirón:

```python
ventas_ciudad = df.groupby("ciudad")["importe"].sum()
ventas_ciudad.plot(kind="bar", title="Ingresos por ciudad")
plt.ylabel("Euros")
plt.tight_layout()
plt.show()
```

---

## Proyecto final

Vas a analizar un dataset de pedidos de una tienda online de principio a fin: lo generas, lo limpias, lo analizas y lo visualizas. Es el recorrido completo de un analista de datos.

### Generar los datos

Ejecuta esto para crear el dataset (incluye suciedad a propósito):

```python
import pandas as pd
import numpy as np

np.random.seed(42)   # para que los datos salgan iguales cada vez

n = 200
productos = ["Portátil", "Móvil", "Tablet", "Auriculares", "Cargador"]
categorias = {"Portátil": "Informática", "Móvil": "Telefonía", "Tablet": "Informática",
              "Auriculares": "Audio", "Cargador": "Accesorios"}
ciudades = ["Madrid", "Barcelona", "Sevilla", "Valencia", "Bilbao"]

pedidos = pd.DataFrame({
    "id_pedido": range(1000, 1000 + n),
    "fecha": pd.to_datetime("2026-01-01") + pd.to_timedelta(np.random.randint(0, 180, n), unit="D"),
    "producto": np.random.choice(productos, n),
    "ciudad": np.random.choice(ciudades, n),
    "precio_unitario": np.random.choice([299, 599, 399, 49, 19], n),
    "cantidad": np.random.randint(1, 5, n),
})
pedidos["categoria"] = pedidos["producto"].map(categorias)

# Ensuciamos los datos a propósito:
pedidos.loc[5:10, "ciudad"] = None                       # algunos nulos
pedidos.loc[20:23, "cantidad"] = None
pedidos = pd.concat([pedidos, pedidos.iloc[:5]], ignore_index=True)  # 5 duplicados

pedidos.to_csv("pedidos.csv", index=False)
print(pedidos.head())
print(pedidos.shape)
```

### Tareas

Resuelve estas preguntas. Las soluciones están al final, pero pelea cada una antes de mirar.

**P1 — Inspección.** Carga `pedidos.csv`. Muestra la forma, los tipos de cada columna y cuántos nulos hay por columna.

**P2 — Limpieza.** Elimina las filas duplicadas. Rellena la `ciudad` faltante con "Desconocida" y la `cantidad` faltante con la mediana. Comprueba que ya no quedan nulos.

**P3 — Columna calculada.** Crea una columna `importe` = `precio_unitario * cantidad`.

**P4 — Fechas.** Asegúrate de que `fecha` es de tipo datetime y crea una columna `mes` con el nombre del mes.

**P5 — Ingresos por categoría.** ¿Cuánto ha facturado cada categoría de producto? Ordénalas de mayor a menor.

**P6 — Ranking de ciudades.** ¿Cuáles son las 3 ciudades con más ingresos? (Ignora "Desconocida".)

**P7 — Evolución mensual.** Calcula los ingresos totales por mes y dibújalos en un gráfico de líneas.

**P8 — Tabla dinámica.** Crea una tabla dinámica con la categoría en las filas, la ciudad en las columnas y la suma de importes en las celdas.

**P9 — Ticket medio.** ¿Cuál es el importe medio por pedido? ¿Y el producto con el ticket medio más alto?

**P10 — Informe.** Exporta a un CSV llamado `resumen_categorias.csv` una tabla con, por cada categoría: número de pedidos, ingreso total e ingreso medio.

---

<details>
<summary>Soluciones del proyecto</summary>

**P1**
```python
import pandas as pd
df = pd.read_csv("pedidos.csv")
print(df.shape)
print(df.dtypes)
print(df.isnull().sum())
```

**P2**
```python
df = df.drop_duplicates()
df["ciudad"] = df["ciudad"].fillna("Desconocida")
df["cantidad"] = df["cantidad"].fillna(df["cantidad"].median())
print(df.isnull().sum())   # todo a 0
```

**P3**
```python
df["importe"] = df["precio_unitario"] * df["cantidad"]
```

**P4**
```python
df["fecha"] = pd.to_datetime(df["fecha"])
df["mes"] = df["fecha"].dt.month_name()
```

**P5**
```python
print(df.groupby("categoria")["importe"].sum().sort_values(ascending=False))
```

**P6**
```python
sin_desc = df[df["ciudad"] != "Desconocida"]
print(sin_desc.groupby("ciudad")["importe"].sum().sort_values(ascending=False).head(3))
```

**P7**
```python
import matplotlib.pyplot as plt
por_mes = df.groupby(df["fecha"].dt.month)["importe"].sum()
por_mes.plot(kind="line", marker="o", title="Ingresos por mes")
plt.xlabel("Mes")
plt.ylabel("Euros")
plt.tight_layout()
plt.savefig("ingresos_mensuales.png", dpi=150)
plt.show()
```

**P8**
```python
print(df.pivot_table(index="categoria", columns="ciudad",
                     values="importe", aggfunc="sum", fill_value=0))
```

**P9**
```python
print("Ticket medio global:", df["importe"].mean())
print(df.groupby("producto")["importe"].mean().sort_values(ascending=False).head(1))
```

**P10**
```python
resumen = df.groupby("categoria").agg(
    pedidos=("id_pedido", "count"),
    ingreso_total=("importe", "sum"),
    ingreso_medio=("importe", "mean"),
).reset_index()
resumen.to_csv("resumen_categorias.csv", index=False)
print(resumen)
```

</details>

---

## Hasta aquí llega el curso

Empezaste sin saber qué era una variable y terminas limpiando, agrupando y graficando un dataset de 200 pedidos. Lo que falta a partir de aquí ya no es Pandas básico: es práctica con datos reales y, si te interesa seguir, librerías como Seaborn (gráficos más bonitos), Scikit-learn (machine learning) o Plotly (gráficos interactivos). Pero la base —pensar en columnas en vez de en bucles— ya la tienes.

El mejor siguiente paso: coge un CSV que te importe de verdad (tus notas, un export de tu banco, datos abiertos de tu ayuntamiento) y hazle las mismas preguntas que al proyecto. Ahí es donde se consolida.
