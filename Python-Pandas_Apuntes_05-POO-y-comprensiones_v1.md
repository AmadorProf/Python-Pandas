# Módulo 05 — Clases, comprensiones y generadores

Dos temas en un módulo, unidos porque los dos te hacen escribir menos código y más expresivo. La programación orientada a objetos (POO) organiza datos y comportamiento juntos. Las comprensiones convierten bucles de cuatro líneas en una. Ambos aparecen constantemente en el código de Pandas que leerás por ahí.

## Por qué existen las clases

Hasta ahora, para representar un alumno usabas un diccionario. Funciona, pero los datos van sueltos de la lógica que los usa. Una clase junta los datos (atributos) y las funciones que operan sobre ellos (métodos) en un mismo molde.

```python
class Alumno:
    def __init__(self, nombre, edad):
        self.nombre = nombre
        self.edad = edad
        self.notas = []

    def anadir_nota(self, nota):
        self.notas.append(nota)

    def media(self):
        if not self.notas:
            return 0
        return sum(self.notas) / len(self.notas)
```

`class` define el molde. A partir de él creas *objetos* (instancias):

```python
ada = Alumno("Ada", 20)
ada.anadir_nota(9)
ada.anadir_nota(7)
print(ada.nombre)    # Ada
print(ada.media())   # 8.0

luis = Alumno("Luis", 22)   # otro objeto, independiente del primero
```

## Descifrando `__init__` y `self`

Dos cosas confunden al principio:

`__init__` es el *constructor*: se ejecuta automáticamente al crear el objeto. Es donde inicializas los atributos. No lo llamas tú; se dispara con `Alumno("Ada", 20)`.

`self` es el propio objeto. Cada método recibe `self` como primer parámetro para saber sobre *qué* objeto trabaja. Cuando escribes `ada.media()`, por dentro Python lo traduce a `media(ada)`: `self` pasa a ser `ada`. Por eso `self.notas` significa "las notas de este objeto en concreto".

No pasas `self` al llamar al método; Python lo rellena solo. Sí lo escribes al definirlo.

## Herencia: especializar sin repetir

Una clase puede heredar de otra y quedarse con todo lo suyo, añadiendo o cambiando lo que necesite:

```python
class Persona:
    def __init__(self, nombre):
        self.nombre = nombre

    def presentarse(self):
        return f"Soy {self.nombre}"

class Profesor(Persona):          # Profesor hereda de Persona
    def __init__(self, nombre, asignatura):
        super().__init__(nombre)  # llama al constructor de Persona
        self.asignatura = asignatura

    def presentarse(self):        # redefine el método
        return f"Soy {self.nombre} y enseño {self.asignatura}"

p = Profesor("Amador", "DWES")
print(p.presentarse())   # Soy Amador y enseño DWES
```

`super()` da acceso a la clase de la que heredas. Aquí lo usamos para reaprovechar su `__init__` en lugar de reescribirlo.

## Mostrar un objeto de forma legible: `__str__`

Si imprimes un objeto sin más, sale algo feo (`<__main__.Alumno object at 0x...>`). Define `__str__` para controlar cómo se muestra:

```python
class Alumno:
    def __init__(self, nombre, edad):
        self.nombre = nombre
        self.edad = edad

    def __str__(self):
        return f"Alumno: {self.nombre} ({self.edad} años)"

ada = Alumno("Ada", 20)
print(ada)   # Alumno: Ada (20 años)
```

Métodos como `__init__` o `__str__`, con doble guion bajo, se llaman *dunder* (double underscore) y Python los invoca en momentos especiales.

## Comprensiones de lista: bucles en una línea

Patrón constante: crear una lista nueva transformando o filtrando otra. Con bucle:

```python
cuadrados = []
for n in range(1, 6):
    cuadrados.append(n ** 2)
# [1, 4, 9, 16, 25]
```

Con comprensión de lista, lo mismo en una línea:

```python
cuadrados = [n ** 2 for n in range(1, 6)]
```

Se lee de izquierda a derecha: "n al cuadrado, para cada n en el rango". Puedes añadir un filtro con `if`:

```python
pares = [n for n in range(20) if n % 2 == 0]
# [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]

nombres = ["ana", "luis", "marta"]
mayus = [n.upper() for n in nombres]
# ['ANA', 'LUIS', 'MARTA']

# Sobre una lista de diccionarios:
clase = [{"nombre": "Ada", "nota": 9}, {"nombre": "Luis", "nota": 4}]
aprobados = [a["nombre"] for a in clase if a["nota"] >= 5]
# ['Ada']
```

Las comprensiones son idiomáticas en Python y verás la misma idea en NumPy y Pandas, donde transformas columnas enteras de un golpe. Entender la versión con listas te prepara para esa otra.

## Comprensiones de diccionario y de conjunto

La misma idea sirve para crear diccionarios y conjuntos:

```python
# Diccionario: número -> su cuadrado
cuadrados = {n: n ** 2 for n in range(1, 6)}
# {1: 1, 2: 4, 3: 9, 4: 16, 5: 25}

# Conjunto de longitudes únicas de unas palabras
palabras = ["sol", "luna", "mar", "cielo"]
longitudes = {len(p) for p in palabras}
# {3, 4, 5}
```

## Generadores: producir valores sin guardarlos todos

Una lista guarda todos sus elementos en memoria. Si son millones, gastas mucha memoria. Un generador produce los valores uno a uno, bajo demanda. Se escribe igual que una comprensión, pero con paréntesis:

```python
suma = sum(n ** 2 for n in range(1_000_000))   # no crea una lista de un millón
```

O con `yield` dentro de una función, que pausa y entrega un valor cada vez que se le pide:

```python
def contar_hasta(n):
    i = 1
    while i <= n:
        yield i
        i += 1

for numero in contar_hasta(5):
    print(numero)   # 1, 2, 3, 4, 5
```

No te obsesiones con los generadores ahora. Basta con que reconozcas la sintaxis cuando la veas y sepas que sirven para no cargar todo en memoria de golpe.

---

## Ejercicios

**05.1** — Crea una clase `CuentaBancaria` con un saldo inicial, y métodos `ingresar(cantidad)` y `retirar(cantidad)`. Que `retirar` no permita dejar el saldo en negativo (que avise y no haga nada en ese caso).

**05.2** — Crea una clase `Rectangulo` con `base` y `altura`, y métodos `area()` y `perimetro()`. Añade un `__str__` que lo describa.

**05.3** — Usando una comprensión de lista, genera la lista de los múltiplos de 3 entre 1 y 50.

**05.4** — Tienes `precios = [10, 25, 8, 100, 47, 3]`. Con una comprensión, crea una lista con los precios que superan 20, aplicándoles además un 10% de descuento.

**05.5** — Con una comprensión de diccionario, crea un diccionario que asocie cada palabra de `["python", "datos", "pandas"]` con su número de letras.

**05.6** — Crea una clase `Termometro` que guarde una lista de temperaturas. Métodos: `registrar(temp)`, `media()`, `maxima()` y `minima()`. Registra varias temperaturas y muestra el resumen.

---

<details markdown="1">
<summary>Soluciones</summary>

**05.1**
```python
class CuentaBancaria:
    def __init__(self, saldo=0):
        self.saldo = saldo

    def ingresar(self, cantidad):
        self.saldo += cantidad

    def retirar(self, cantidad):
        if cantidad > self.saldo:
            print("Saldo insuficiente")
        else:
            self.saldo -= cantidad

cuenta = CuentaBancaria(100)
cuenta.ingresar(50)
cuenta.retirar(200)   # Saldo insuficiente
cuenta.retirar(120)
print(cuenta.saldo)   # 30
```

---

**05.2**
```python
class Rectangulo:
    def __init__(self, base, altura):
        self.base = base
        self.altura = altura

    def area(self):
        return self.base * self.altura

    def perimetro(self):
        return 2 * (self.base + self.altura)

    def __str__(self):
        return f"Rectángulo {self.base}x{self.altura} (área {self.area()})"

r = Rectangulo(4, 5)
print(r)              # Rectángulo 4x5 (área 20)
print(r.perimetro()) # 18
```

---

**05.3**
```python
multiplos = [n for n in range(1, 51) if n % 3 == 0]
print(multiplos)
```

---

**05.4**
```python
precios = [10, 25, 8, 100, 47, 3]
rebajados = [p * 0.9 for p in precios if p > 20]
print(rebajados)   # [22.5, 90.0, 42.3]
```

---

**05.5**
```python
palabras = ["python", "datos", "pandas"]
longitudes = {p: len(p) for p in palabras}
print(longitudes)   # {'python': 6, 'datos': 5, 'pandas': 6}
```

---

**05.6**
```python
class Termometro:
    def __init__(self):
        self.temperaturas = []

    def registrar(self, temp):
        self.temperaturas.append(temp)

    def media(self):
        return sum(self.temperaturas) / len(self.temperaturas)

    def maxima(self):
        return max(self.temperaturas)

    def minima(self):
        return min(self.temperaturas)

t = Termometro()
for temp in [22, 19, 25, 30, 18]:
    t.registrar(temp)
print(f"Media: {t.media()}, Máx: {t.maxima()}, Mín: {t.minima()}")
```

</details>
