# Módulo 01 — Variables, tipos y operadores

Programar es manipular datos. Antes de manipularlos hay que saber guardarlos y de qué clase son. Eso son las variables y los tipos. Este módulo es la base de todo lo demás, así que no lo despaches rápido.

## Una variable es una etiqueta, no una caja

Cuando escribes `x = 5`, no estás metiendo el 5 en una caja llamada `x`. Estás pegando la etiqueta `x` a un valor que vive en memoria. Da igual la imagen mental mientras entiendas esto: una variable apunta a un valor, y puedes reapuntarla cuando quieras.

```python
edad = 30
edad = 31        # ahora apunta a otro valor
edad = edad + 1  # lee el valor actual (31), suma 1, y reapunta a 32
print(edad)      # 32
```

Reglas para nombrar variables: solo letras, números y guion bajo; no pueden empezar por número; distinguen mayúsculas (`edad` y `Edad` son distintas). Por convención en Python se usa `snake_case`: minúsculas y guiones bajos, como `precio_total` o `numero_de_alumnos`.

## Python deduce el tipo solo

No declaras el tipo de una variable. Python lo deduce del valor que le asignas. Esto se llama *tipado dinámico*.

```python
x = 5        # int (entero)
x = 5.0      # ahora es float (decimal)
x = "cinco"  # ahora es str (cadena de texto)
```

Para saber de qué tipo es algo, usa `type()`:

```python
print(type(5))        # <class 'int'>
print(type(5.0))      # <class 'float'>
print(type("hola"))   # <class 'str'>
print(type(True))     # <class 'bool'>
```

## Los cuatro tipos básicos

**Enteros (`int`)** — números sin decimales. Tan grandes como quieras, Python no se queja:

```python
poblacion = 47000000
negativo = -8
```

**Decimales (`float`)** — números con parte fraccionaria. Ojo: la aritmética decimal del ordenador no es exacta.

```python
precio = 19.99
print(0.1 + 0.2)  # 0.30000000000000004  <- no es un bug, es cómo funcionan los floats
```

Ese resultado raro aparece porque los ordenadores guardan los decimales en binario y algunos números no tienen representación exacta. Para dinero, más adelante verás soluciones; por ahora, que no te sorprenda.

**Cadenas (`str`)** — texto, entre comillas simples o dobles (da igual cuáles, pero sé coherente):

```python
nombre = "Amador"
ciclo = 'DAW'
frase = "Dijo: \"hola\""   # \" escapa la comilla
multilinea = """Esto ocupa
varias líneas"""
```

**Booleanos (`bool`)** — solo dos valores: `True` y `False`. Son la base de toda decisión en un programa.

```python
mayor_de_edad = True
tiene_descuento = False
```

## Operadores aritméticos: cuidado con dos de ellos

```python
print(7 + 3)   # 10   suma
print(7 - 3)   # 4    resta
print(7 * 3)   # 21   multiplicación
print(7 / 3)   # 2.333...  división (SIEMPRE devuelve float)
print(7 // 3)  # 2    división entera (descarta decimales)
print(7 % 3)   # 1    módulo (el resto de la división)
print(7 ** 3)  # 343  potencia (7 al cubo)
```

Los dos que más confunden al principio: `//` te da el cociente entero y `%` te da el resto. El módulo es utilísimo: `n % 2 == 0` comprueba si `n` es par.

## Operar con cadenas: concatenar y repetir

```python
nombre = "Ada"
apellido = "Lovelace"
print(nombre + " " + apellido)  # Ada Lovelace
print("=" * 20)                 # ====================
```

Pero no puedes mezclar tipos sin convertir:

```python
edad = 30
# print("Edad: " + edad)   # ERROR: no se puede sumar str + int
print("Edad: " + str(edad))  # bien, convertimos el int a str
print(f"Edad: {edad}")       # mejor: f-string
```

## Convertir entre tipos

```python
int("42")     # 42    texto a entero
float("3.14") # 3.14  texto a decimal
str(42)       # "42"  número a texto
int(3.9)      # 3     trunca, NO redondea
round(3.9)    # 4     esto sí redondea
bool(0)       # False
bool(5)       # True  (cualquier número distinto de 0 es True)
```

La conversión de texto a número es constante cuando lees datos de un archivo o de un usuario: todo entra como texto y tú lo conviertes.

## Leer datos del usuario

`input()` muestra un mensaje y devuelve lo que el usuario escriba, **siempre como cadena**:

```python
nombre = input("¿Cómo te llamas? ")
edad = int(input("¿Qué edad tienes? "))  # convertimos a int para poder operar
print(f"El año que viene tendrás {edad + 1}")
```

Si olvidas el `int()`, `edad + 1` falla porque estarías sumando un texto y un número.

## Trabajar con cadenas: métodos imprescindibles

Las cadenas traen métodos (funciones que se aplican con un punto) para transformarlas:

```python
texto = "  Hola Mundo  "
print(texto.strip())        # "Hola Mundo"   quita espacios de los extremos
print(texto.lower())        # "  hola mundo  "
print(texto.upper())        # "  HOLA MUNDO  "
print(texto.replace("o", "0"))  # "  H0la Mund0  "
print("hola".capitalize())  # "Hola"
print("a,b,c".split(","))   # ['a', 'b', 'c']   parte en una lista
print(len("hola"))          # 4    longitud
```

`split()` y `strip()` los usarás muchísimo cuando limpies datos en Pandas. Tenlos presentes.

## Acceder a partes de una cadena (indexación y slicing)

Cada carácter tiene una posición que empieza en 0:

```python
palabra = "Python"
print(palabra[0])    # 'P'   primer carácter
print(palabra[-1])   # 'n'   último (los negativos cuentan desde el final)
print(palabra[0:3])  # 'Pyt' del 0 al 3 sin incluir el 3
print(palabra[2:])   # 'thon' del 2 hasta el final
print(palabra[:2])   # 'Py'  desde el principio hasta el 2 sin incluir
```

Este corte con `[inicio:fin]` se llama *slicing* y es uno de los conceptos más reutilizados en Python. Funciona igual en listas y en Pandas.

---

## Ejercicios

**01.1** — Pide al usuario dos números y muestra su suma, resta, producto, división y resto. Recuerda convertir el `input`.

**01.2** — Pide un número y di si es par o impar usando el operador módulo. (Pista: un número es par si `n % 2 == 0`.)

**01.3** — Pide al usuario una frase y muéstrala en mayúsculas, en minúsculas, su longitud y la frase con los espacios de los extremos eliminados.

**01.4** — Calcula el precio final de un producto: pide el precio base y aplícale un 21% de IVA. Muestra el resultado redondeado a 2 decimales (usa `round(valor, 2)`).

**01.5** — Pide un nombre completo ("Ada Lovelace") y muestra solo las iniciales ("A.L."). Pista: `split()` separa por el espacio y luego coges el primer carácter de cada parte.

---

<details>
<summary>Soluciones</summary>

**01.1**
```python
a = float(input("Primer número: "))
b = float(input("Segundo número: "))
print(f"Suma: {a + b}")
print(f"Resta: {a - b}")
print(f"Producto: {a * b}")
print(f"División: {a / b}")
print(f"Resto: {a % b}")
```

**01.2**
```python
n = int(input("Número: "))
if n % 2 == 0:
    print("Es par")
else:
    print("Es impar")
```
(El `if`/`else` se ve a fondo en el módulo 02; aquí adelantamos su uso.)

**01.3**
```python
frase = input("Escribe una frase: ")
print(frase.upper())
print(frase.lower())
print(len(frase))
print(frase.strip())
```

**01.4**
```python
base = float(input("Precio base: "))
final = base * 1.21
print(f"Precio final: {round(final, 2)} €")
```

**01.5**
```python
nombre = input("Nombre completo: ")
partes = nombre.split(" ")
iniciales = partes[0][0] + "." + partes[1][0] + "."
print(iniciales)
```

</details>
