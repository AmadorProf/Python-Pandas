# Módulo 03 — Listas, tuplas, diccionarios y conjuntos

Una variable guarda un dato. Pero rara vez tienes un solo dato: tienes la lista de alumnos, las notas del trimestre, la ficha de un cliente. Para eso están las estructuras de datos. Domina este módulo, porque un DataFrame de Pandas no es más que una versión sofisticada de un diccionario de listas.

## Listas: colecciones ordenadas que puedes modificar

Una lista guarda varios valores en orden, entre corchetes:

```python
notas = [7, 5, 9, 6, 8]
mezcla = [1, "hola", True, 3.14]   # puede contener tipos distintos
vacia = []
```

Accedes por posición (empieza en 0, como las cadenas) y puedes cambiar elementos:

```python
notas = [7, 5, 9, 6, 8]
print(notas[0])    # 7    primero
print(notas[-1])   # 8    último
notas[1] = 10      # cambia el segundo elemento
print(notas)       # [7, 10, 9, 6, 8]
```

El slicing funciona igual que en cadenas:

```python
print(notas[1:3])  # [10, 9]
print(notas[:2])   # [7, 10]
print(notas[-2:])  # [6, 8]
```

## Métodos de lista que usarás a diario

```python
notas = [7, 5, 9]
notas.append(8)        # añade al final -> [7, 5, 9, 8]
notas.insert(0, 10)    # inserta en posición 0 -> [10, 7, 5, 9, 8]
notas.remove(5)        # elimina el primer 5 -> [10, 7, 9, 8]
ultimo = notas.pop()   # saca y devuelve el último -> ultimo=8
notas.sort()           # ordena en el sitio -> [7, 9, 10]
notas.reverse()        # invierte el orden
print(len(notas))      # cantidad de elementos
print(9 in notas)      # True si 9 está en la lista
```

Y funciones útiles que trabajan sobre listas de números:

```python
notas = [7, 5, 9, 6, 8]
print(sum(notas))      # 35
print(max(notas))      # 9
print(min(notas))      # 5
print(sum(notas) / len(notas))  # 7.0  la media
```

## Recorrer una lista, con y sin índice

```python
nombres = ["Ana", "Luis", "Marta"]

for nombre in nombres:          # forma normal: solo el valor
    print(nombre)

for i, nombre in enumerate(nombres):   # cuando necesitas la posición
    print(f"{i}: {nombre}")
# 0: Ana
# 1: Luis
# 2: Marta
```

`enumerate` te da posición y valor a la vez. Más limpio que llevar un contador a mano.

## Tuplas: como listas, pero intocables

Una tupla es una lista que no se puede modificar. Se escribe con paréntesis:

```python
punto = (3, 5)
print(punto[0])   # 3
# punto[0] = 9    # ERROR: las tuplas no se pueden cambiar
```

¿Para qué sirve algo que no se puede cambiar? Para datos que no deben cambiar: coordenadas, una fecha (día, mes, año), valores que quieres proteger de modificaciones accidentales. Además, permiten un truco elegante, el *desempaquetado*:

```python
punto = (3, 5)
x, y = punto       # x=3, y=5 de una vez
a, b = b, a        # intercambia dos variables sin variable auxiliar
```

## Diccionarios: datos con nombre, no con número

En una lista accedes por posición. En un diccionario accedes por **clave**: un nombre que tú eliges. Es la estructura más importante de este módulo.

```python
alumno = {
    "nombre": "Ada",
    "edad": 20,
    "ciclo": "DAW",
    "notas": [7, 8, 9]
}

print(alumno["nombre"])   # Ada
print(alumno["notas"])    # [7, 8, 9]
```

Modificas y añades claves directamente:

```python
alumno["edad"] = 21          # modifica
alumno["email"] = "ada@x.es" # añade una clave nueva
del alumno["ciclo"]          # elimina una clave
```

Acceder a una clave que no existe da error. Para evitarlo, usa `.get()`, que devuelve `None` (o un valor por defecto) si no la encuentra:

```python
print(alumno.get("telefono"))          # None, sin error
print(alumno.get("telefono", "N/D"))   # "N/D"
```

## Recorrer un diccionario

```python
alumno = {"nombre": "Ada", "edad": 20, "ciclo": "DAW"}

for clave in alumno:                 # por defecto recorre las claves
    print(clave)

for clave, valor in alumno.items():  # claves y valores a la vez
    print(f"{clave}: {valor}")

print(alumno.keys())     # las claves
print(alumno.values())   # los valores
```

`.items()` es el que más usarás. Recorre pares clave-valor de golpe.

## Estructuras anidadas: el paso a los datos reales

Los datos del mundo real son listas de diccionarios. Esta es exactamente la forma que tendrá una tabla antes de cargarla en Pandas:

```python
clase = [
    {"nombre": "Ada",   "nota": 9},
    {"nombre": "Luis",  "nota": 5},
    {"nombre": "Marta", "nota": 7},
]

# ¿Quién ha aprobado?
for alumno in clase:
    if alumno["nota"] >= 5:
        print(f"{alumno['nombre']} aprobó con {alumno['nota']}")

# Nota media de la clase
total = 0
for alumno in clase:
    total += alumno["nota"]
print(f"Media: {total / len(clase)}")
```

Cuando llegues a Pandas, esa lista de diccionarios se convierte en un DataFrame con una sola línea, y la media se calcula con `df["nota"].mean()`. Pero entender la versión a mano es lo que te permite confiar en la versión automática.

## Conjuntos: colecciones sin duplicados

Un conjunto (`set`) guarda valores únicos, sin orden. Su gracia es eliminar duplicados y comparar grupos:

```python
numeros = [1, 2, 2, 3, 3, 3, 4]
unicos = set(numeros)     # {1, 2, 3, 4}
print(len(unicos))        # 4   cuántos valores distintos hay

a = {1, 2, 3}
b = {3, 4, 5}
print(a & b)   # {3}        intersección: lo que está en ambos
print(a | b)   # {1,2,3,4,5} unión: todo
print(a - b)   # {1, 2}     lo que está en a pero no en b
```

Quitar duplicados de una lista es su uso estrella: `list(set(mi_lista))`.

## Tabla resumen: cuándo usar cada una

| Estructura | Sintaxis | ¿Ordenada? | ¿Modificable? | ¿Duplicados? | Para qué |
|------------|----------|------------|---------------|--------------|----------|
| Lista | `[1, 2, 3]` | Sí | Sí | Sí | Secuencia de elementos |
| Tupla | `(1, 2, 3)` | Sí | No | Sí | Datos fijos, coordenadas |
| Diccionario | `{"a": 1}` | Sí (3.7+) | Sí | Claves no | Datos con nombre |
| Conjunto | `{1, 2, 3}` | No | Sí | No | Valores únicos, comparar |

---

## Ejercicios

**03.1** — Tienes la lista `temperaturas = [22, 19, 25, 30, 18, 27]`. Calcula e imprime la máxima, la mínima y la media.

**03.2** — Dada la lista de notas `[4, 7, 9, 3, 6, 8, 5]`, crea una nueva lista solo con las aprobadas (>= 5) usando un bucle.

**03.3** — Crea un diccionario con los datos de un libro (título, autor, año, páginas) e imprime cada par clave-valor en una línea, con `.items()`.

**03.4** — Cuenta cuántas veces aparece cada palabra en la frase `"el gato y el perro y el pez"`. El resultado debe ser un diccionario `{"el": 3, "gato": 1, ...}`. Pista: parte la frase con `split()` y ve sumando en un diccionario.

**03.5** — Dada una lista con nombres repetidos `["Ana", "Luis", "Ana", "Marta", "Luis", "Ana"]`, averigua cuántos nombres distintos hay y cuáles son.

**03.6** — Tienes esta lista de diccionarios:
```python
ventas = [
    {"producto": "Teclado", "precio": 25, "unidades": 4},
    {"producto": "Ratón",   "precio": 15, "unidades": 7},
    {"producto": "Monitor", "precio": 150, "unidades": 2},
]
```
Calcula el ingreso total (precio × unidades sumado de todos) y muestra qué producto generó más ingresos.

---

<details markdown="1">
<summary>Soluciones</summary>

**03.1**
```python
temperaturas = [22, 19, 25, 30, 18, 27]
print("Máxima:", max(temperaturas))
print("Mínima:", min(temperaturas))
print("Media:", sum(temperaturas) / len(temperaturas))
```

---

**03.2**
```python
notas = [4, 7, 9, 3, 6, 8, 5]
aprobadas = []
for n in notas:
    if n >= 5:
        aprobadas.append(n)
print(aprobadas)   # [7, 9, 6, 8, 5]
```

---

**03.3**
```python
libro = {
    "titulo": "El Quijote",
    "autor": "Cervantes",
    "anio": 1605,
    "paginas": 863,
}
for clave, valor in libro.items():
    print(f"{clave}: {valor}")
```

---

**03.4**
```python
frase = "el gato y el perro y el pez"
conteo = {}
for palabra in frase.split():
    if palabra in conteo:
        conteo[palabra] += 1
    else:
        conteo[palabra] = 1
print(conteo)
# {'el': 3, 'gato': 1, 'y': 2, 'perro': 1, 'pez': 1}
```

---

**03.5**
```python
nombres = ["Ana", "Luis", "Ana", "Marta", "Luis", "Ana"]
distintos = set(nombres)
print(f"Hay {len(distintos)} nombres distintos: {distintos}")
```

---

**03.6**
```python
ventas = [
    {"producto": "Teclado", "precio": 25, "unidades": 4},
    {"producto": "Ratón",   "precio": 15, "unidades": 7},
    {"producto": "Monitor", "precio": 150, "unidades": 2},
]

total = 0
mejor_producto = ""
mejor_ingreso = 0

for v in ventas:
    ingreso = v["precio"] * v["unidades"]
    total += ingreso
    if ingreso > mejor_ingreso:
        mejor_ingreso = ingreso
        mejor_producto = v["producto"]

print(f"Ingreso total: {total} €")              # 505 €
print(f"Mejor producto: {mejor_producto} ({mejor_ingreso} €)")  # Monitor (300 €)
```

</details>
