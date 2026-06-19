# Módulo 02 — Decisiones y repeticiones

Hasta ahora tu código se ejecuta de arriba abajo, línea a línea, sin desviarse. Eso sirve para poco. Un programa útil decide (`if`) y repite (`for`, `while`). Este módulo te da el control del flujo.

## La indentación no es decorativa: es sintaxis

En la mayoría de lenguajes los bloques se marcan con llaves `{}`. En Python se marcan con la **sangría** (los espacios al principio de la línea). Cuatro espacios por nivel. Si te equivocas con la sangría, el programa falla o hace algo distinto a lo que esperas.

```python
if True:
    print("Esto está dentro del if")   # 4 espacios -> pertenece al if
print("Esto está fuera del if")        # sin sangría -> siempre se ejecuta
```

Acostúmbrate desde ya. La indentación en Python *es* la estructura.

## Condicionales: if, elif, else

Un `if` ejecuta un bloque solo si una condición es verdadera:

```python
edad = 20
if edad >= 18:
    print("Eres mayor de edad")
```

Con `else` añades el caso contrario, y con `elif` (else if) encadenas varias condiciones. Python evalúa de arriba abajo y ejecuta **el primer bloque cuya condición sea verdadera**; el resto se ignora:

```python
nota = 7
if nota >= 9:
    print("Sobresaliente")
elif nota >= 7:
    print("Notable")
elif nota >= 5:
    print("Aprobado")
else:
    print("Suspenso")
```

Con `nota = 7` imprime "Notable" y para. Aunque `nota >= 5` también es cierto, no llega a evaluarlo.

## Operadores de comparación y lógicos

Comparaciones, que devuelven `True` o `False`:

```python
5 == 5   # True   igualdad (¡dos signos! uno solo es asignación)
5 != 3   # True   distinto
5 > 3    # True
5 <= 5   # True
```

El error más típico del principiante: usar `=` (asignar) cuando quería `==` (comparar). `if x = 5` da error; lo correcto es `if x == 5`.

Para combinar condiciones, los operadores lógicos `and`, `or`, `not`:

```python
edad = 25
tiene_carnet = True

if edad >= 18 and tiene_carnet:
    print("Puede conducir")

if edad < 6 or edad > 65:
    print("Entrada gratuita")

if not tiene_carnet:
    print("Necesita sacarse el carnet")
```

`and` exige que **ambas** sean ciertas. `or` con que **una** lo sea. `not` invierte.

## El bucle for recorre colecciones

`for` repite un bloque una vez por cada elemento de una secuencia. La forma más limpia de iterar:

```python
for letra in "Sol":
    print(letra)
# S
# o
# l

nombres = ["Ana", "Luis", "Marta"]
for nombre in nombres:
    print(f"Hola, {nombre}")
```

Cuando necesitas repetir un número fijo de veces, usas `range()`:

```python
for i in range(5):
    print(i)        # 0, 1, 2, 3, 4   (empieza en 0, termina antes del 5)

for i in range(1, 6):
    print(i)        # 1, 2, 3, 4, 5

for i in range(0, 10, 2):
    print(i)        # 0, 2, 4, 6, 8   (de 2 en 2)
```

`range(inicio, fin, paso)`: el `fin` nunca se incluye. Esto despista al principio, pero es coherente con el slicing que viste en el módulo 01.

## El bucle while repite mientras se cumpla algo

`for` lo usas cuando sabes sobre qué iterar. `while` cuando repites hasta que pase algo, sin saber cuántas vueltas serán:

```python
contador = 0
while contador < 5:
    print(contador)
    contador += 1   # += suma y reasigna; equivale a contador = contador + 1
```

**Cuidado con el bucle infinito.** Si la condición nunca se vuelve falsa, el programa no para nunca. Aquí, si olvidas `contador += 1`, `contador` siempre vale 0 y el `while` no termina jamás. Si te pasa, corta con Ctrl+C.

Caso típico de `while`: pedir datos hasta que sean válidos:

```python
clave = ""
while clave != "secreto":
    clave = input("Contraseña: ")
print("Acceso concedido")
```

## Romper y saltar: break y continue

`break` sale del bucle inmediatamente. `continue` salta a la siguiente vuelta sin terminar la actual:

```python
for n in range(1, 11):
    if n == 7:
        break       # al llegar a 7, abandona el bucle
    print(n)        # imprime 1..6

for n in range(1, 11):
    if n % 2 == 0:
        continue    # si es par, sáltatelo
    print(n)        # imprime solo impares: 1, 3, 5, 7, 9
```

## Combinar bucles y condiciones: contar y acumular

Dos patrones que repetirás mil veces. **Acumular** una suma:

```python
numeros = [10, 25, 7, 42]
total = 0
for n in numeros:
    total += n
print(total)   # 84
```

**Contar** cuántos cumplen algo:

```python
edades = [12, 20, 17, 45, 8]
mayores = 0
for edad in edades:
    if edad >= 18:
        mayores += 1
print(f"Mayores de edad: {mayores}")   # 2
```

Interioriza estos dos esqueletos. Cuando pases a NumPy y Pandas verás que existen formas más rápidas de hacer lo mismo, pero entender el bucle a mano es lo que te deja comprender qué hacen esas herramientas por debajo.

---

## Ejercicios

**02.1** — Pide un número y di si es positivo, negativo o cero. Usa `if`/`elif`/`else`.

**02.2** — Imprime la tabla de multiplicar de un número que pida el usuario, del 1 al 10, con un bucle `for`.

**02.3** — Calcula la suma de todos los números del 1 al 100 con un bucle. (El resultado es 5050.)

**02.4** — Pide números al usuario uno a uno con un `while`. Cuando escriba `fin`, deja de pedir y muestra cuántos números introdujo y su media.

**02.5** — Imprime los números del 1 al 50, pero para los múltiplos de 3 escribe "Fizz" en lugar del número, y para los múltiplos de 5 escribe "Buzz" (para los múltiplos de ambos, "FizzBuzz"). Es el clásico ejercicio de entrevista.

**02.6** — Dada la lista `[3, 8, 1, 9, 4, 7]`, encuentra el número mayor sin usar la función `max()`. Recórrela con un `for` y ve guardando el mayor visto hasta el momento.

---

<details>
<summary>Soluciones</summary>

**02.1**
```python
n = float(input("Número: "))
if n > 0:
    print("Positivo")
elif n < 0:
    print("Negativo")
else:
    print("Cero")
```

**02.2**
```python
n = int(input("Número: "))
for i in range(1, 11):
    print(f"{n} x {i} = {n * i}")
```

**02.3**
```python
total = 0
for i in range(1, 101):
    total += i
print(total)   # 5050
```

**02.4**
```python
suma = 0
cantidad = 0
while True:
    entrada = input("Número (o 'fin'): ")
    if entrada == "fin":
        break
    suma += float(entrada)
    cantidad += 1

if cantidad > 0:
    print(f"Introdujiste {cantidad} números. Media: {suma / cantidad}")
else:
    print("No introdujiste ningún número")
```

**02.5**
```python
for n in range(1, 51):
    if n % 3 == 0 and n % 5 == 0:
        print("FizzBuzz")
    elif n % 3 == 0:
        print("Fizz")
    elif n % 5 == 0:
        print("Buzz")
    else:
        print(n)
```
Importante el orden: la condición de "ambos" va primero, porque si no, nunca se alcanzaría.

**02.6**
```python
numeros = [3, 8, 1, 9, 4, 7]
mayor = numeros[0]   # empezamos suponiendo que el primero es el mayor
for n in numeros:
    if n > mayor:
        mayor = n
print(mayor)   # 9
```

</details>
