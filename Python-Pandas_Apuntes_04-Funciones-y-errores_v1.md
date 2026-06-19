# Módulo 04 — Funciones, módulos, errores y archivos

Copiar y pegar el mismo bloque de código tres veces es la señal de que necesitas una función. Este módulo te enseña a empaquetar lógica para reutilizarla, a organizarla en módulos, a manejar los errores cuando algo va mal y a leer y escribir archivos. Con esto ya tienes el Python que necesitas antes de saltar a los datos.

## Una función es código con nombre que puedes reutilizar

Defines una función con `def`, le das un nombre, recibe datos (parámetros) y devuelve un resultado (`return`):

```python
def area_rectangulo(base, altura):
    return base * altura

# Llamarla:
print(area_rectangulo(4, 5))   # 20
print(area_rectangulo(10, 2))  # 20
```

`base` y `altura` son los parámetros. `4` y `5` son los argumentos que pasas al llamarla. El `return` entrega el resultado a quien la llamó. Sin `return`, la función devuelve `None`.

Una función puede no devolver nada y limitarse a hacer algo (un efecto):

```python
def saludar(nombre):
    print(f"Hola, {nombre}")

saludar("Ada")   # imprime, pero no devuelve un valor que puedas guardar
```

## Parámetros por defecto y argumentos con nombre

Puedes dar valores por defecto a parámetros, que se usan si no pasas ese argumento:

```python
def saludar(nombre, saludo="Hola"):
    return f"{saludo}, {nombre}"

print(saludar("Ada"))                 # Hola, Ada
print(saludar("Ada", "Buenos días"))  # Buenos días, Ada
print(saludar(nombre="Ada", saludo="Hey"))  # argumentos con nombre
```

Los argumentos con nombre hacen el código más legible cuando una función tiene muchos parámetros. Pandas los usa por todas partes (`pd.read_csv("datos.csv", sep=";", encoding="utf-8")`).

## Devolver varios valores

Una función puede devolver varios valores a la vez (en realidad devuelve una tupla, que tú desempaquetas):

```python
def estadisticas(numeros):
    return min(numeros), max(numeros), sum(numeros) / len(numeros)

minimo, maximo, media = estadisticas([4, 8, 6, 2])
print(minimo, maximo, media)   # 2 8 5.0
```

## El alcance de las variables (scope)

Una variable creada dentro de una función solo existe dentro de ella. Fuera no la ves:

```python
def calcular():
    resultado = 42   # variable local
    return resultado

calcular()
# print(resultado)   # ERROR: 'resultado' no existe aquí fuera
```

Esto es bueno: cada función trabaja en su propia burbuja y no pisa variables de otras. Pasa los datos por parámetros y recoge los resultados por `return`. No dependas de variables globales.

## Documentar funciones: el docstring

Una buena costumbre es escribir, justo bajo el `def`, una cadena que explique qué hace la función:

```python
def imc(peso, altura):
    """Calcula el índice de masa corporal.

    peso en kg, altura en metros. Devuelve un float.
    """
    return peso / (altura ** 2)
```

No es obligatorio, pero `help(imc)` te lo mostrará y tu yo del futuro te lo agradecerá.

## Módulos: usar código que ya existe

Python trae cientos de funciones organizadas en módulos. Para usarlas, importas el módulo:

```python
import math
print(math.sqrt(16))   # 4.0
print(math.pi)         # 3.141592...

import random
print(random.randint(1, 6))      # un dado: número entre 1 y 6
print(random.choice(["a", "b"])) # elige uno al azar

from datetime import date
print(date.today())    # la fecha de hoy
```

`import math` trae el módulo entero (usas `math.sqrt`). `from datetime import date` trae solo lo que nombras (usas `date` directamente). Ambas formas son válidas; la segunda es habitual cuando solo necesitas una pieza.

Más adelante importarás así NumPy y Pandas, casi siempre con un alias corto:

```python
import numpy as np
import pandas as pd
```

El `as np` crea un apodo para no escribir `numpy` cada vez. Es la convención universal; respétala.

## Cuando algo falla: las excepciones

Si tu código intenta algo imposible —dividir entre cero, convertir "hola" en número, abrir un archivo que no existe— Python lanza una *excepción* y, si no la gestionas, el programa muere. Para gestionarla usas `try`/`except`:

```python
try:
    edad = int(input("Edad: "))
    print(f"El año que viene: {edad + 1}")
except ValueError:
    print("Eso no es un número válido")
```

Si el usuario escribe "treinta", `int()` falla con un `ValueError`, pero en lugar de reventar, salta al `except`. Cada tipo de error tiene su nombre: `ValueError`, `ZeroDivisionError`, `KeyError`, `FileNotFoundError`, `TypeError`. Captura el que esperas:

```python
def dividir(a, b):
    try:
        return a / b
    except ZeroDivisionError:
        print("No se puede dividir entre cero")
        return None

print(dividir(10, 2))   # 5.0
print(dividir(10, 0))   # imprime el aviso y devuelve None
```

Hay un bloque extra, `finally`, que se ejecuta siempre, haya error o no. Se usa para limpiar recursos (cerrar un archivo, una conexión).

## Leer y escribir archivos

Los datos viven en archivos. Para escribir uno:

```python
with open("notas.txt", "w", encoding="utf-8") as f:
    f.write("Ada: 9\n")
    f.write("Luis: 5\n")
```

Para leerlo:

```python
with open("notas.txt", "r", encoding="utf-8") as f:
    contenido = f.read()      # todo el archivo como una cadena
    print(contenido)

# O línea a línea:
with open("notas.txt", "r", encoding="utf-8") as f:
    for linea in f:
        print(linea.strip())   # strip quita el salto de línea final
```

El `with` se encarga de cerrar el archivo solo, aunque haya un error. Úsalo siempre así. El modo `"w"` sobrescribe, `"a"` añade al final, `"r"` lee. Y `encoding="utf-8"` evita problemas con tildes y eñes: ponlo siempre.

Cuando llegues a Pandas, leer un CSV de mil filas será una sola línea (`pd.read_csv`), pero entender qué es abrir un archivo te quita el miedo a lo que pasa por debajo.

---

## Ejercicios

**04.1** — Escribe una función `es_primo(n)` que devuelva `True` si `n` es primo y `False` si no. Pruébala con varios números.

**04.2** — Escribe una función `celsius_a_fahrenheit(c)` y otra `fahrenheit_a_celsius(f)`. La fórmula: F = C × 9/5 + 32.

**04.3** — Escribe una función `contar_vocales(texto)` que devuelva cuántas vocales hay en una cadena. Ignora mayúsculas/minúsculas.

**04.4** — Escribe una función `media_segura(numeros)` que calcule la media de una lista, pero que devuelva 0 (sin reventar) si la lista está vacía. Usa `try`/`except`.

**04.5** — Escribe en un archivo `cuadrados.txt` los cuadrados de los números del 1 al 10, uno por línea. Luego vuelve a abrir el archivo, léelo y suma todos los valores.

**04.6** — Escribe una función `aplicar_descuento(precio, porcentaje=10)` con un descuento por defecto del 10%. Que devuelva el precio rebajado. Pruébala con y sin pasar el porcentaje.

---

<details markdown="1">
<summary>Soluciones</summary>

**04.1**
```python
def es_primo(n):
    if n < 2:
        return False
    for i in range(2, int(n ** 0.5) + 1):
        if n % i == 0:
            return False
    return True

for x in [2, 4, 7, 9, 13, 100]:
    print(x, es_primo(x))
```
Solo hace falta comprobar divisores hasta la raíz cuadrada de `n`: si no hay divisor por debajo de ahí, no lo hay por encima.

---

**04.2**
```python
def celsius_a_fahrenheit(c):
    return c * 9 / 5 + 32

def fahrenheit_a_celsius(f):
    return (f - 32) * 5 / 9

print(celsius_a_fahrenheit(100))   # 212.0
print(fahrenheit_a_celsius(32))    # 0.0
```

---

**04.3**
```python
def contar_vocales(texto):
    vocales = "aeiou"
    contador = 0
    for letra in texto.lower():
        if letra in vocales:
            contador += 1
    return contador

print(contar_vocales("Murciélago"))   # 5
```

---

**04.4**
```python
def media_segura(numeros):
    try:
        return sum(numeros) / len(numeros)
    except ZeroDivisionError:
        return 0

print(media_segura([4, 6, 8]))   # 6.0
print(media_segura([]))          # 0
```

---

**04.5**
```python
with open("cuadrados.txt", "w", encoding="utf-8") as f:
    for i in range(1, 11):
        f.write(f"{i ** 2}\n")

total = 0
with open("cuadrados.txt", "r", encoding="utf-8") as f:
    for linea in f:
        total += int(linea.strip())
print(total)   # 385
```

---

**04.6**
```python
def aplicar_descuento(precio, porcentaje=10):
    return precio - precio * porcentaje / 100

print(aplicar_descuento(100))      # 90.0  (descuento por defecto)
print(aplicar_descuento(100, 25))  # 75.0
```

</details>
