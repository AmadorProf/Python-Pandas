# Módulo 12 — Leer y escribir datos: CSV, JSON y Excel

El módulo 04 te enseñó a abrir archivos de texto con `open()`. Eso funciona para ficheros simples, pero en análisis de datos los formatos habituales son CSV, JSON y Excel. Pandas los lee y escribe en una sola línea cada uno. Este módulo cubre los tres y añade `pathlib` para gestionar rutas sin romperte la cabeza con barras y sistemas operativos.

## CSV: el formato que más vas a ver

Un CSV es un archivo de texto donde cada línea es una fila y los valores se separan con comas (o punto y coma si el separador decimal es la coma, como en español).

```python
import pandas as pd

df = pd.read_csv("ventas.csv")
print(df.head())
```

`read_csv` tiene muchos parámetros, pero estos son los que vas a necesitar:

```python
df = pd.read_csv(
    "ventas.csv",
    sep=";",                   # separador (por defecto coma)
    encoding="utf-8",          # codificación (ponlo siempre para evitar problemas con tildes)
    decimal=",",               # si el decimal usa coma en lugar de punto
    usecols=["fecha", "importe", "ciudad"],   # leer solo esas columnas
    parse_dates=["fecha"],     # convertir esa columna a datetime al cargar
    na_values=["N/A", "-", ""], # cadenas que se tratan como nulo
    nrows=1000,                # leer solo las primeras N filas (útil para archivos grandes)
)
```

No necesitas usarlos todos a la vez. Añade los que el archivo requiera.

Para exportar:

```python
df.to_csv("resultado.csv", index=False, encoding="utf-8")
```

`index=False` evita que Pandas escriba la columna del índice (0, 1, 2…) como primera columna. Ponlo casi siempre.

## JSON: datos que vienen de APIs o configuraciones

JSON es el formato de intercambio de datos en la web. Pandas puede leerlo cuando está orientado a registros (cada elemento es una fila) o cuando la API devuelve un objeto con una clave que contiene la lista de datos.

```python
# JSON con una lista de objetos
df = pd.read_json("datos.json")

# Si el JSON tiene estructura anidada, mejor usar el módulo json y normalizar
import json

with open("respuesta.json", "r", encoding="utf-8") as f:
    datos = json.load(f)

# Si los datos están bajo una clave:
df = pd.DataFrame(datos["resultados"])

# O con json_normalize para objetos anidados:
from pandas import json_normalize
df = json_normalize(datos["resultados"])
```

Para escribir:

```python
df.to_json("salida.json", orient="records", force_ascii=False, indent=2)
```

`orient="records"` produce una lista de objetos, que es lo más útil para APIs. `force_ascii=False` mantiene las tildes.

Para trabajar con JSON sin Pandas (guardar configuración, logs, resultados intermedios):

```python
import json

config = {"umbral": 0.75, "modo": "producción", "columnas": ["a", "b"]}

with open("config.json", "w", encoding="utf-8") as f:
    json.dump(config, f, ensure_ascii=False, indent=2)

with open("config.json", "r", encoding="utf-8") as f:
    config = json.load(f)
print(config["umbral"])   # 0.75
```

## Excel: el formato que siempre aparece en los proyectos reales

Para leer Excel necesitas `openpyxl` (para `.xlsx`) instalado:

```bash
pip install openpyxl
```

```python
df = pd.read_excel("informe.xlsx", sheet_name="Ventas")
```

Si no especificas `sheet_name`, lee la primera hoja. También puedes pasar un índice (0, 1…) o `None` para leer todas las hojas como un diccionario de DataFrames.

Los mismos parámetros útiles que en CSV funcionan aquí: `usecols`, `nrows`, `na_values`.

Para escribir:

```python
df.to_excel("resultado.xlsx", sheet_name="Resumen", index=False)
```

Para escribir varios DataFrames en distintas hojas del mismo archivo:

```python
with pd.ExcelWriter("informe_completo.xlsx", engine="openpyxl") as writer:
    df_ventas.to_excel(writer, sheet_name="Ventas", index=False)
    df_resumen.to_excel(writer, sheet_name="Resumen", index=False)
    df_clientes.to_excel(writer, sheet_name="Clientes", index=False)
```

## Rutas sin sorpresas: pathlib

Concatenar rutas con strings es frágil y depende del sistema operativo (`/` en Linux/Mac, `\` en Windows). `pathlib.Path` resuelve eso:

```python
from pathlib import Path

directorio = Path("datos")
archivo = directorio / "ventas_2024.csv"   # el operador / construye rutas
print(archivo)           # datos/ventas_2024.csv

# Comprobar si existe antes de leer:
if archivo.exists():
    df = pd.read_csv(archivo)

# Crear el directorio si no existe:
directorio.mkdir(parents=True, exist_ok=True)

# Listar todos los CSV de una carpeta:
for csv in Path("datos").glob("*.csv"):
    print(csv.name)

# Información de la ruta:
print(archivo.stem)      # ventas_2024   (nombre sin extensión)
print(archivo.suffix)    # .csv
print(archivo.parent)    # datos
```

`Path` funciona igual en Windows, Mac y Linux. Úsala siempre que trabajes con rutas en código que va a moverse entre máquinas.

## Cuándo usar cada formato

| Situación | Formato |
|-----------|---------|
| Intercambio de datos, tablas planas | CSV |
| APIs, configuración, datos anidados | JSON |
| Informes para clientes o compañeros | Excel |
| Pipeline de datos entre scripts Python | CSV o Parquet (avanzado) |

---

## Ejercicios

**12.1** — Crea a mano un DataFrame con 5 productos (nombre, precio, stock). Expórtalo a CSV sin índice. Luego léelo de nuevo y muestra las filas donde el stock es menor que 20.

**12.2** — Dado el JSON siguiente guardado en un archivo `productos.json`, léelo con Pandas y calcula el precio medio por categoría:
```json
[
  {"nombre": "Teclado", "categoria": "Periféricos", "precio": 45},
  {"nombre": "Ratón", "categoria": "Periféricos", "precio": 25},
  {"nombre": "Monitor", "categoria": "Pantallas", "precio": 199},
  {"nombre": "Webcam", "categoria": "Periféricos", "precio": 60},
  {"nombre": "Tablet", "categoria": "Pantallas", "precio": 349}
]
```

**12.3** — Descarga un CSV del INE o usa cualquier CSV público de más de 500 filas. Léelo pasando `nrows=100` y `usecols` para quedarte solo con 3 columnas. Exporta esas 100 filas a Excel en una hoja llamada "Muestra".

**12.4** — Escribe un script que use `pathlib` para listar todos los archivos `.csv` en una carpeta llamada `datos/` (créala si no existe). Para cada archivo, imprime su nombre y el número de filas que tiene.

**12.5** — Lee el archivo de ventas de tu elección. Usa `pd.ExcelWriter` para exportarlo a un Excel con dos hojas: una con los datos completos y otra con un resumen (total de filas, media de precios, valor máximo).

**12.6** — Crea un diccionario de configuración con al menos 4 parámetros (umbrales, nombres de columnas, rutas). Guárdalo como JSON con indentación. Luego cárgalo y úsalo para filtrar un DataFrame.

---

<details markdown="1">
<summary>Soluciones</summary>

**12.1**
```python
import pandas as pd

productos = pd.DataFrame({
    "nombre": ["Teclado", "Ratón", "Monitor", "Webcam", "USB Hub"],
    "precio": [45, 25, 199, 60, 15],
    "stock": [30, 15, 8, 50, 12]
})
productos.to_csv("productos.csv", index=False, encoding="utf-8")

df = pd.read_csv("productos.csv")
print(df[df["stock"] < 20])
```

---

**12.2**
```python
import json, pandas as pd
from pathlib import Path

datos = [
    {"nombre": "Teclado", "categoria": "Periféricos", "precio": 45},
    {"nombre": "Ratón", "categoria": "Periféricos", "precio": 25},
    {"nombre": "Monitor", "categoria": "Pantallas", "precio": 199},
    {"nombre": "Webcam", "categoria": "Periféricos", "precio": 60},
    {"nombre": "Tablet", "categoria": "Pantallas", "precio": 349}
]
Path("productos.json").write_text(json.dumps(datos, ensure_ascii=False), encoding="utf-8")

df = pd.read_json("productos.json")
print(df.groupby("categoria")["precio"].mean())
# Pantallas      274.0
# Periféricos     43.3...
```

---

**12.3**
```python
import pandas as pd

# Ejemplo con cualquier CSV público grande
df = pd.read_csv("datos_grandes.csv", nrows=100, usecols=["col1", "col2", "col3"])
df.to_excel("muestra.xlsx", sheet_name="Muestra", index=False)
print(df.shape)   # (100, 3)
```

---

**12.4**
```python
from pathlib import Path
import pandas as pd

carpeta = Path("datos")
carpeta.mkdir(parents=True, exist_ok=True)

for archivo in carpeta.glob("*.csv"):
    df = pd.read_csv(archivo)
    print(f"{archivo.name}: {len(df)} filas")
```

---

**12.5**
```python
import pandas as pd

df = pd.read_csv("ventas.csv")
resumen = pd.DataFrame({
    "total_filas": [len(df)],
    "media_precio": [df["precio_unitario"].mean()],
    "max_precio": [df["precio_unitario"].max()]
})

with pd.ExcelWriter("informe.xlsx", engine="openpyxl") as writer:
    df.to_excel(writer, sheet_name="Datos", index=False)
    resumen.to_excel(writer, sheet_name="Resumen", index=False)
```

---

**12.6**
```python
import json, pandas as pd
from pathlib import Path

config = {
    "columna_precio": "precio_unitario",
    "columna_fecha": "fecha",
    "umbral_minimo": 50,
    "categorias_activas": ["Electrónica", "Periféricos"]
}

Path("config.json").write_text(
    json.dumps(config, ensure_ascii=False, indent=2), encoding="utf-8"
)

with open("config.json", encoding="utf-8") as f:
    cfg = json.load(f)

df = pd.read_csv("ventas.csv")
filtrado = df[df[cfg["columna_precio"]] >= cfg["umbral_minimo"]]
print(filtrado.shape)
```

</details>
