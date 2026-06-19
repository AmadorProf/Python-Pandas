# Módulo 08 — Seleccionar y filtrar: loc, iloc y máscaras booleanas

Tener los datos cargados sirve de poco si no sabes extraer justo la parte que te interesa: las filas de Madrid, los productos caros, las tres primeras columnas. Este módulo es el que más vas a usar en tu día a día con Pandas. Tómatelo en serio.

## El dataset de trabajo

Todos los ejemplos usan esta tabla:

```python
import pandas as pd

df = pd.DataFrame({
    "nombre": ["Ada", "Luis", "Marta", "Juan", "Eva", "Iván"],
    "edad":   [20, 22, 21, 23, 19, 25],
    "ciclo":  ["DAW", "DAW", "ASIR", "DAW", "ASIR", "DAM"],
    "nota":   [9, 5, 7, 4, 8, 6],
    "ciudad": ["Madrid", "Sevilla", "Madrid", "Bilbao", "Sevilla", "Madrid"],
})
df = df.set_index("nombre")   # usamos el nombre como índice
```

`set_index` convierte una columna en el índice de la tabla. Útil cuando una columna identifica cada fila, como aquí el nombre.

## Dos formas de seleccionar filas: loc e iloc

Pandas separa seleccionar por **etiqueta** (el nombre del índice) de seleccionar por **posición** (el número de fila). Confundirlos es el error número uno de quien empieza.

- **`loc`** trabaja con etiquetas: `df.loc["Ada"]`.
- **`iloc`** trabaja con posiciones: `df.iloc[0]` (la `i` es de *integer*).

```python
df.loc["Ada"]     # la fila cuya etiqueta es "Ada"
df.iloc[0]        # la primera fila, sea cual sea su etiqueta

df.loc["Marta", "nota"]   # valor concreto: nota de Marta -> 7
df.iloc[2, 2]             # fila 2, columna 2 por posición -> 7
```

## Seleccionar rangos con loc e iloc

```python
# Por etiqueta (loc INCLUYE el final):
df.loc["Ada":"Marta"]          # de Ada a Marta, ambas incluidas
df.loc["Ada":"Marta", "edad":"nota"]   # subtabla de filas y columnas

# Por posición (iloc EXCLUYE el final, como el slicing normal):
df.iloc[0:3]        # filas 0, 1, 2 (la 3 no)
df.iloc[0:3, 1:3]   # filas 0-2, columnas 1-2

# Columnas concretas:
df.loc[:, ["edad", "nota"]]    # todas las filas, dos columnas
df.iloc[:, [0, 2]]             # todas las filas, columnas 0 y 2
```

Recuerda la diferencia que más despista: con `loc` el final del rango **se incluye**; con `iloc` **no**, igual que el slicing normal de Python. Tiene sentido: con etiquetas, "de Ada a Marta" significa hasta Marta inclusive.

## Filtrado booleano: seleccionar filas que cumplen una condición

Esto es lo que hace a Pandas tan potente, y es exactamente la máscara booleana de NumPy del módulo 06. Comparas una columna contra un valor y obtienes una Series de `True`/`False`. Metes esa máscara entre corchetes y te quedas con las filas `True`:

```python
df[df["nota"] >= 7]
#        edad ciclo  nota  ciudad
# nombre
# Ada      20   DAW     9  Madrid
# Marta    21  ASIR     7  Madrid
# Eva      19  ASIR     8  Sevilla
```

Léelo así: `df["nota"] >= 7` produce la máscara; `df[...]` se queda con las filas verdaderas. Otros ejemplos:

```python
df[df["ciudad"] == "Madrid"]        # solo los de Madrid
df[df["edad"] < 21]                 # menores de 21
df[df["ciclo"] != "DAW"]            # los que no son de DAW
```

## Combinar varias condiciones

Igual que en NumPy: `&` para "y", `|` para "o", y **cada condición entre paréntesis**. Olvidar los paréntesis es un error clásico que da mensajes crípticos.

```python
# Aprobados de Madrid:
df[(df["nota"] >= 5) & (df["ciudad"] == "Madrid")]

# De DAW o de DAM:
df[(df["ciclo"] == "DAW") | (df["ciclo"] == "DAM")]

# Negación con ~ (suspensos = NO aprobados):
df[~(df["nota"] >= 5)]
```

Para comprobar si un valor está en una lista, `isin()` es más limpio que encadenar muchos `|`:

```python
df[df["ciudad"].isin(["Madrid", "Bilbao"])]
df[df["ciclo"].isin(["DAW", "DAM"])]
```

## Filtrar por texto: el accesor .str

Para condiciones sobre cadenas, `.str` da acceso a los métodos de texto aplicados a toda la columna:

```python
df[df["ciclo"].str.startswith("DA")]   # ciclos que empiezan por "DA"
df[df["ciudad"].str.contains("a")]     # ciudades que contienen una "a"
df["ciudad"].str.upper()               # toda la columna en mayúsculas
```

Esto lo profundizamos en el módulo 09 (limpieza), pero ténlo en el radar: `.str` te deja aplicar lo del módulo 01 a una columna entera.

## Combinar selección de filas y columnas con loc

`loc` acepta a la vez una condición de filas y una lista de columnas. Es la forma más expresiva de pedir "estas columnas, de las filas que cumplan esto":

```python
df.loc[df["nota"] >= 7, ["edad", "nota"]]
#         edad  nota
# nombre
# Ada       20     9
# Marta     21     7
# Eva       19     8
```

## Modificar valores con loc

`loc` no solo lee: también escribe. Para cambiar valores que cumplen una condición:

```python
# Subir 1 punto a todos los de DAW:
df.loc[df["ciclo"] == "DAW", "nota"] = df.loc[df["ciclo"] == "DAW", "nota"] + 1

# Marcar una categoría:
df.loc[df["nota"] >= 9, "mencion"] = "Honor"
```

Hazlo siempre con `loc`. Si intentas modificar sobre una selección encadenada (`df[df["x"] > 0]["y"] = ...`), Pandas te avisa con un `SettingWithCopyWarning` y puede que el cambio no se aplique. La regla: para asignar, usa `loc`.

## Ordenar los resultados

```python
df.sort_values("nota")                  # de menor a mayor nota
df.sort_values("nota", ascending=False) # de mayor a menor
df.sort_values(["ciclo", "nota"])       # primero por ciclo, dentro por nota
df.sort_index()                         # ordena por el índice (los nombres)
```

Combinado con filtrado, respondes preguntas reales en una línea: "los tres mejores aprobados" es filtrar por nota >= 5, ordenar descendente y coger `head(3)`.

---

## Ejercicios

Usa el `df` de alumnos del principio (antes de las modificaciones de la sección "Modificar valores").

**08.1** — Selecciona la fila de "Juan" con `loc`, y la cuarta fila (posición 3) con `iloc`. Comprueba que son la misma.

**08.2** — Muestra solo las columnas `edad` y `nota` de todos los alumnos.

**08.3** — Filtra los alumnos que han aprobado (nota >= 5).

**08.4** — Filtra los alumnos de Madrid que además tengan más de 20 años.

**08.5** — Muestra los alumnos que estudian DAW o ASIR, ordenados por nota de mayor a menor.

**08.6** — Muestra el nombre y la nota de los dos alumnos con mejor nota. (Pista: ordena descendente y usa `head(2)`.)

**08.7** — Usando `isin`, filtra los alumnos cuyas ciudades sean Madrid o Sevilla.

**08.8** — ¿Cuál es la nota media de los alumnos de DAW? (Filtra y luego `.mean()` sobre la columna nota.)

---

<details markdown="1">
<summary>Soluciones</summary>

**08.1**
```python
print(df.loc["Juan"])
print(df.iloc[3])
```

---

**08.2**
```python
print(df[["edad", "nota"]])
# o equivalente:
print(df.loc[:, ["edad", "nota"]])
```

---

**08.3**
```python
print(df[df["nota"] >= 5])
```

---

**08.4**
```python
print(df[(df["ciudad"] == "Madrid") & (df["edad"] > 20)])
```

---

**08.5**
```python
seleccion = df[df["ciclo"].isin(["DAW", "ASIR"])]
print(seleccion.sort_values("nota", ascending=False))
```

---

**08.6**
```python
print(df.sort_values("nota", ascending=False).head(2)[["nota"]])
# (el nombre es el índice, así que sale solo)
```

---

**08.7**
```python
print(df[df["ciudad"].isin(["Madrid", "Sevilla"])])
```

---

**08.8**
```python
print(df[df["ciclo"] == "DAW"]["nota"].mean())   # 6.0
```

</details>
