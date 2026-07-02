# Módulo 15 — APIs y requests: datos desde la web

Hasta ahora los datos vivían en archivos locales. En la práctica, muchos datos están en APIs: servicios que responden a peticiones HTTP con JSON. Este módulo te enseña a hacer esas peticiones, manejar las respuestas y convertirlas en DataFrames de Pandas.

## Instalar

```bash
pip install requests
```

```python
import requests
import pandas as pd
```

## HTTP en dos minutos

Una API REST funciona así: tú envías una petición HTTP a una URL, el servidor devuelve una respuesta con un código de estado y, normalmente, datos en JSON.

Los códigos más importantes:
- `200` — todo bien
- `400` — tu petición está mal formada
- `401` — no estás autenticado
- `403` — autenticado pero sin permiso
- `404` — la URL no existe
- `429` — demasiadas peticiones (rate limit)
- `500` — error en el servidor

El método más común para leer datos es `GET`. Para enviarlos, `POST`.

## Tu primera petición

```python
respuesta = requests.get("https://api.open-meteo.com/v1/forecast?latitude=40.4&longitude=-3.7&current=temperature_2m")

print(respuesta.status_code)   # 200 si fue bien
print(respuesta.json())         # el contenido como diccionario Python
```

`response.json()` convierte el cuerpo JSON en un diccionario Python. A partir de ahí lo tratas como cualquier dict.

## Parámetros en la URL

En lugar de construir la URL a mano con `?clave=valor&clave2=valor2`, pasa un diccionario:

```python
params = {
    "latitude": 40.4,
    "longitude": -3.7,
    "hourly": "temperature_2m,precipitation",
    "timezone": "Europe/Madrid",
    "forecast_days": 3
}

respuesta = requests.get("https://api.open-meteo.com/v1/forecast", params=params)
datos = respuesta.json()
```

`requests` construye la URL correctamente, codificando los caracteres especiales si hace falta.

## De JSON a DataFrame

La mayoría de APIs devuelven una lista de objetos o un objeto con una clave que contiene la lista. Una vez tienes el JSON como dict Python, el paso a Pandas es directo:

```python
# API que devuelve una lista de objetos:
datos = respuesta.json()
df = pd.DataFrame(datos)

# API que devuelve un objeto con los datos bajo una clave:
datos = respuesta.json()
df = pd.DataFrame(datos["results"])

# API que devuelve columnas y filas por separado (como Open-Meteo):
datos = respuesta.json()
df = pd.DataFrame(datos["hourly"])
df["time"] = pd.to_datetime(df["time"])
```

## Autenticación con cabeceras

Muchas APIs requieren una clave (API key) que pasas en la cabecera de la petición:

```python
API_KEY = "tu_clave_aqui"

headers = {
    "Authorization": f"Bearer {API_KEY}",
    # o según la API:
    "X-API-Key": API_KEY
}

respuesta = requests.get("https://api.ejemplo.com/datos", headers=headers, params=params)
```

Nunca escribas la clave directamente en el código que vas a compartir. Guárdala en una variable de entorno o en un archivo `.env` que no subes al repositorio:

```python
import os
API_KEY = os.environ.get("MI_API_KEY")
```

## Manejar errores correctamente

No asumas que la petición siempre funciona. Dos niveles de error:

```python
try:
    respuesta = requests.get(url, params=params, timeout=10)
    respuesta.raise_for_status()   # lanza excepción si status_code >= 400
    datos = respuesta.json()
except requests.exceptions.Timeout:
    print("La petición tardó demasiado")
except requests.exceptions.HTTPError as e:
    print(f"Error HTTP {respuesta.status_code}: {e}")
except requests.exceptions.RequestException as e:
    print(f"Error de red: {e}")
```

`raise_for_status()` es el atajo para no tener que comprobar `if respuesta.status_code != 200` manualmente. `timeout=10` evita que el script se quede colgado si el servidor no responde.

## Paginación: cuando los datos vienen en trozos

Las APIs limitan cuántos resultados devuelven por petición. Tienes que pedir las páginas una a una:

```python
todos = []
pagina = 1

while True:
    params = {"page": pagina, "per_page": 100}
    respuesta = requests.get(url, params=params, headers=headers)
    respuesta.raise_for_status()
    datos = respuesta.json()

    if not datos:   # lista vacía → no hay más páginas
        break

    todos.extend(datos)
    pagina += 1

df = pd.DataFrame(todos)
```

La condición de parada depende de la API. Algunas devuelven un campo `"next"` en null cuando no hay más páginas; otras devuelven una lista vacía.

## Rate limiting: no bombardees la API

Muchas APIs limitan las peticiones por segundo o por minuto. Si las superas recibes un `429`. Para espaciar las peticiones:

```python
import time

for id_producto in lista_ids:
    respuesta = requests.get(f"{base_url}/{id_producto}", headers=headers)
    datos = respuesta.json()
    todos.append(datos)
    time.sleep(0.2)   # 0.2 segundos entre peticiones → máximo 5 por segundo
```

Si la API devuelve cabecera `Retry-After` en el 429, espera ese tiempo antes de reintentar.

## APIs públicas sin autenticación para practicar

Estas funcionan sin registro ni clave:

| API | Qué devuelve |
|-----|-------------|
| `https://api.open-meteo.com` | Datos meteorológicos |
| `https://restcountries.com/v3.1/all` | Información de países |
| `https://pokeapi.co/api/v2/pokemon` | Datos de Pokémon |
| `https://jsonplaceholder.typicode.com/posts` | Posts de prueba |
| `https://api.coindesk.com/v1/bpi/currentprice.json` | Precio del Bitcoin |

---

## Ejercicios

**15.1** — Haz una petición a `https://restcountries.com/v3.1/all` y construye un DataFrame con estas columnas: nombre común del país, capital, población y región. Muestra los 10 países más poblados.

**15.2** — Usa la API de Open-Meteo para obtener la temperatura horaria de los próximos 3 días en una ciudad de tu elección (busca latitud y longitud). Convierte la columna `time` a datetime y muestra la temperatura máxima y mínima de cada día.

**15.3** — Haz una petición a `https://jsonplaceholder.typicode.com/posts`. Convierte la respuesta en DataFrame. Cuenta cuántos posts tiene cada `userId` y muestra el resultado ordenado.

**15.4** — Añade manejo de errores completo a una petición: captura `Timeout`, `HTTPError` y cualquier otro `RequestException`. Prueba a provocar un error pasando una URL inválida.

**15.5** — Usando paginación, descarga todos los posts de `https://jsonplaceholder.typicode.com/posts` simulando que la API devuelve 10 por página (pasa `_start` y `_limit` como parámetros de la JSONPlaceholder API). Comprueba que el total son 100 posts.

**15.6** — Crea una función `obtener_datos(url, params=None, headers=None, intentos=3)` que reintente automáticamente la petición hasta `intentos` veces si falla, esperando 1 segundo entre intentos. Devuelve el JSON o `None` si agota los intentos.

---

<details markdown="1">
<summary>Soluciones</summary>

**15.1**
```python
import requests
import pandas as pd

respuesta = requests.get("https://restcountries.com/v3.1/all", timeout=10)
respuesta.raise_for_status()
paises = respuesta.json()

filas = []
for p in paises:
    filas.append({
        "nombre": p.get("name", {}).get("common", ""),
        "capital": p.get("capital", [None])[0],
        "poblacion": p.get("population", 0),
        "region": p.get("region", "")
    })

df = pd.DataFrame(filas)
print(df.nlargest(10, "poblacion")[["nombre", "capital", "poblacion", "region"]])
```

---

**15.2**
```python
import requests, pandas as pd

params = {
    "latitude": 40.4,
    "longitude": -3.7,
    "hourly": "temperature_2m",
    "timezone": "Europe/Madrid",
    "forecast_days": 3
}

respuesta = requests.get("https://api.open-meteo.com/v1/forecast", params=params, timeout=10)
respuesta.raise_for_status()
datos = respuesta.json()

df = pd.DataFrame(datos["hourly"])
df["time"] = pd.to_datetime(df["time"])
df["fecha"] = df["time"].dt.date

resumen = df.groupby("fecha")["temperature_2m"].agg(["max", "min"])
print(resumen)
```

---

**15.3**
```python
import requests, pandas as pd

respuesta = requests.get("https://jsonplaceholder.typicode.com/posts", timeout=10)
respuesta.raise_for_status()
df = pd.DataFrame(respuesta.json())
print(df.groupby("userId").size().sort_values(ascending=False))
```

---

**15.4**
```python
import requests

def pedir(url, timeout=5):
    try:
        r = requests.get(url, timeout=timeout)
        r.raise_for_status()
        return r.json()
    except requests.exceptions.Timeout:
        print("Timeout: el servidor no respondió a tiempo")
    except requests.exceptions.HTTPError as e:
        print(f"Error HTTP {r.status_code}: {e}")
    except requests.exceptions.RequestException as e:
        print(f"Error de red: {e}")
    return None

# Prueba con URL inválida:
datos = pedir("https://url-que-no-existe-jamas.xyz/api")   # Error de red
datos = pedir("https://httpbin.org/status/404")            # Error HTTP 404
datos = pedir("https://jsonplaceholder.typicode.com/posts/1")   # OK
```

---

**15.5**
```python
import requests, pandas as pd

todos = []
inicio = 0
limite = 10

while True:
    params = {"_start": inicio, "_limit": limite}
    r = requests.get("https://jsonplaceholder.typicode.com/posts", params=params, timeout=10)
    r.raise_for_status()
    pagina = r.json()
    if not pagina:
        break
    todos.extend(pagina)
    inicio += limite

df = pd.DataFrame(todos)
print(f"Total posts: {len(df)}")   # 100
```

---

**15.6**
```python
import requests, time

def obtener_datos(url, params=None, headers=None, intentos=3):
    for intento in range(1, intentos + 1):
        try:
            r = requests.get(url, params=params, headers=headers, timeout=10)
            r.raise_for_status()
            return r.json()
        except requests.exceptions.RequestException as e:
            print(f"Intento {intento}/{intentos} fallido: {e}")
            if intento < intentos:
                time.sleep(1)
    return None

datos = obtener_datos("https://jsonplaceholder.typicode.com/posts/1")
print(datos)
```

</details>
