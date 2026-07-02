# Módulo 06 — NumPy, el motor que mueve Pandas

Pandas está construido sobre NumPy. Cada columna de un DataFrame es, por dentro, un array de NumPy. Entender NumPy primero hace que Pandas deje de parecer magia. Y la idea central es una sola: operar sobre colecciones enteras de números de golpe, sin bucles. Se llama *vectorización* y es lo que hace que procesar un millón de filas sea instantáneo.

## Instalar e importar

```bash
pip install numpy
```

```python
import numpy as np   # el alias np es universal, úsalo siempre
```

## El array: una lista con superpoderes

Un array de NumPy se parece a una lista, pero todos sus elementos son del mismo tipo y las operaciones se aplican a todos a la vez.

```python
import numpy as np

a = np.array([1, 2, 3, 4, 5])
print(a)          # [1 2 3 4 5]
print(type(a))    # <class 'numpy.ndarray'>
```

La diferencia clave con una lista: operar sobre el array opera sobre cada elemento sin escribir un bucle.

```python
lista = [1, 2, 3, 4]
# lista * 2  ->  [1, 2, 3, 4, 1, 2, 3, 4]   ¡repite la lista!

a = np.array([1, 2, 3, 4])
print(a * 2)      # [2 4 6 8]    multiplica cada elemento
print(a + 10)     # [11 12 13 14]
print(a ** 2)     # [1 4 9 16]
```

Esto es la vectorización. Donde antes escribías un `for`, ahora escribes una operación. Más corto y muchísimo más rápido.

## Crear arrays sin escribirlos a mano

```python
np.zeros(5)            # [0. 0. 0. 0. 0.]
np.ones(3)             # [1. 1. 1.]
np.arange(0, 10, 2)    # [0 2 4 6 8]   como range, pero array
np.linspace(0, 1, 5)   # [0.   0.25 0.5  0.75 1.  ]  5 valores equiespaciados
np.random.rand(3)      # 3 números aleatorios entre 0 y 1
np.random.randint(1, 7, 10)   # 10 dados (entre 1 y 6)
```

`arange` y `linspace` confunden: `arange` controla el *paso* entre valores; `linspace` controla *cuántos* valores quieres entre dos extremos.

## Arrays de dos dimensiones: matrices

Un array puede tener filas y columnas. Es la antesala de una tabla:

```python
m = np.array([[1, 2, 3],
              [4, 5, 6]])
print(m.shape)   # (2, 3)   2 filas, 3 columnas
print(m.ndim)    # 2        dimensiones
print(m.size)    # 6        total de elementos
```

`shape` es el atributo que más mirarás: te dice las dimensiones. En Pandas, `df.shape` te dirá cuántas filas y columnas tiene tu tabla.

## Indexar y cortar arrays

Igual que en listas, pero con un truco extra para dos dimensiones:

```python
a = np.array([10, 20, 30, 40, 50])
print(a[0])     # 10
print(a[-1])    # 50
print(a[1:4])   # [20 30 40]

m = np.array([[1, 2, 3],
              [4, 5, 6]])
print(m[0, 1])  # 2    fila 0, columna 1
print(m[1])     # [4 5 6]   toda la fila 1
print(m[:, 0])  # [1 4]     toda la columna 0 (: significa "todas las filas")
```

Esa sintaxis `m[:, 0]` para coger una columna entera reaparece en Pandas. Familiarízate.

## Filtrado booleano: la idea más importante para Pandas

Puedes comparar un array entero contra un valor y obtener un array de `True`/`False`. Y puedes usar ese array de booleanos para seleccionar elementos. Esto es el corazón del filtrado en Pandas, así que párate aquí.

```python
edades = np.array([15, 22, 17, 30, 12, 45])

print(edades >= 18)
# [False  True False  True False  True]   una máscara booleana

mayores = edades[edades >= 18]
print(mayores)   # [22 30 45]   solo los que cumplen
```

Lee la última línea así: "dame los elementos de `edades` donde la condición sea verdadera". En Pandas escribirás casi lo mismo: `df[df["edad"] >= 18]`. Es el mismo mecanismo.

Combina condiciones con `&` (y) y `|` (o), **siempre con paréntesis**:

```python
print(edades[(edades >= 18) & (edades < 40)])   # [22 30]
```

Ojo: en NumPy y Pandas se usan `&` y `|`, no `and` y `or`. Y los paréntesis alrededor de cada condición son obligatorios.

## Operaciones de agregación: resumir un array en un número

```python
ventas = np.array([100, 250, 175, 300, 125])

print(ventas.sum())    # 950
print(ventas.mean())   # 190.0   media
print(ventas.max())    # 300
print(ventas.min())    # 100
print(ventas.std())    # desviación típica
print(np.median(ventas))  # 175.0  mediana
```

Estos mismos métodos (`.sum()`, `.mean()`, `.max()`...) existen igual en Pandas. Lo que aprendes aquí se transfiere directo.

En matrices puedes agregar por filas o por columnas con `axis`:

```python
m = np.array([[1, 2, 3],
              [4, 5, 6]])
print(m.sum())          # 21   todo
print(m.sum(axis=0))    # [5 7 9]   suma por columnas (hacia abajo)
print(m.sum(axis=1))    # [6 15]    suma por filas (hacia los lados)
```

`axis=0` opera a lo largo de las filas (resultado por columna); `axis=1` a lo largo de las columnas (resultado por fila). Cuesta memorizarlo; la regla práctica: `axis=0` "aplasta verticalmente", `axis=1` "aplasta horizontalmente". Reaparece en Pandas.

## Broadcasting: operar entre tamaños distintos

NumPy estira automáticamente el array pequeño para que encaje con el grande. Por eso `a + 10` funciona: el 10 se "reparte" a cada elemento.

```python
precios = np.array([100, 200, 300])
con_iva = precios * 1.21      # el 1.21 se aplica a los tres
print(con_iva)                # [121. 242. 363.]

# Restar la media a cada elemento (centrar los datos):
datos = np.array([10, 20, 30, 40])
print(datos - datos.mean())   # [-15.  -5.   5.  15.]
```

Centrar restando la media, normalizar dividiendo... son operaciones que harás sobre columnas enteras en análisis de datos. NumPy las hace sin un solo bucle.

## Por qué esto importa: velocidad

Sumar un millón de números con un bucle de Python tarda; con NumPy es casi instantáneo, porque las operaciones corren en código optimizado por debajo. Esta es la razón por la que el análisis de datos en Python usa NumPy y Pandas en vez de listas y bucles. No es comodidad: es rendimiento.

---

## Ejercicios

**06.1** — Crea un array con los números del 1 al 20 (usa `arange`). Multiplícalo por 3 y muestra el resultado.

**06.2** — Dado `temperaturas = np.array([18, 25, 30, 15, 22, 28, 12])`, calcula la media, la máxima y la mínima. Luego, usando filtrado booleano, muestra solo las temperaturas mayores que la media.

**06.3** — Genera 1000 lanzamientos de un dado (`randint`) y comprueba que la media se acerca a 3.5. Cuenta cuántos seises salieron usando filtrado booleano y `.sum()` sobre la máscara.

**06.4** — Tienes las notas `np.array([4, 7, 9, 3, 6, 8, 5, 2])`. Calcula cuántas están aprobadas (>= 5) y la media solo de las aprobadas.

**06.5** — Crea una matriz 3×3 con `np.arange(1, 10).reshape(3, 3)`. Muestra la suma total, la suma de cada fila y la suma de cada columna.

**06.6** — Tienes los precios `np.array([19.99, 5.50, 100, 45.30, 8])` sin IVA. Crea un nuevo array con el precio con IVA (21%) redondeado a 2 decimales (usa `np.round`).

**06.7** — Simula 10.000 lanzamientos de dos dados y cuenta cuántas veces sale cada suma posible (del 2 al 12). Muestra los resultados.

**06.8** — Dado `distancias = np.array([10, 22, 30, 45, 55])` en km (una medición por hora), calcula la velocidad entre cada par de puntos consecutivos usando `np.diff`.

**06.9** — Genera una matriz 5×5 de enteros aleatorios entre 0 y 100. Binarízala sin bucles: los valores < 50 pasan a 0 y los >= 50 pasan a 1.

---

<details markdown="1">
<summary>Soluciones</summary>

**06.1**
```python
import numpy as np
a = np.arange(1, 21)
print(a * 3)
```

---

**06.2**
```python
temperaturas = np.array([18, 25, 30, 15, 22, 28, 12])
media = temperaturas.mean()
print(f"Media: {media}, Máx: {temperaturas.max()}, Mín: {temperaturas.min()}")
print(temperaturas[temperaturas > media])
```

---

**06.3**
```python
tiradas = np.random.randint(1, 7, 1000)
print("Media:", tiradas.mean())          # cercano a 3.5
print("Seises:", (tiradas == 6).sum())   # alrededor de 166
```
`(tiradas == 6)` es una máscara de True/False; sumarla cuenta los True (True vale 1).

---

**06.4**
```python
notas = np.array([4, 7, 9, 3, 6, 8, 5, 2])
aprobadas = notas[notas >= 5]
print("Aprobadas:", len(aprobadas))       # 5
print("Media de aprobadas:", aprobadas.mean())  # 7.0
```

---

**06.5**
```python
m = np.arange(1, 10).reshape(3, 3)
print(m)
print("Total:", m.sum())            # 45
print("Por filas:", m.sum(axis=1))  # [ 6 15 24]
print("Por columnas:", m.sum(axis=0))  # [12 15 18]
```

---

**06.6**
```python
precios = np.array([19.99, 5.50, 100, 45.30, 8])
con_iva = np.round(precios * 1.21, 2)
print(con_iva)   # [ 24.19   6.66 121.    54.81   9.68]
```


---

**06.7**
```python
import numpy as np

dado1 = np.random.randint(1, 7, 10000)
dado2 = np.random.randint(1, 7, 10000)
sumas = dado1 + dado2

for v in range(2, 13):
    conteo = (sumas == v).sum()
    print(f"{v:2d}: {conteo:5d}  {'█' * (conteo // 100)}")
```

---

**06.8**
```python
import numpy as np

distancias = np.array([10, 22, 30, 45, 55])
velocidades = np.diff(distancias)
print("Velocidades (km/h):", velocidades)   # [12  8 15 10]
```

---

**06.9**
```python
import numpy as np

m = np.random.randint(0, 101, (5, 5))
print("Original:\n", m)
binarizada = (m >= 50).astype(int)
print("Binarizada:\n", binarizada)
```
</details>
