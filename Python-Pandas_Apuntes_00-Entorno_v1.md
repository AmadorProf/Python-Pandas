# Módulo 00 — Montar el entorno y ejecutar tu primer programa

Antes de programar necesitas dos cosas: Python instalado y un sitio donde escribir código. Este módulo resuelve ambas y te deja un primer programa funcionando. Nada de teoría todavía.

## Instalar Python

Python es el intérprete: el programa que lee tu código y lo ejecuta. Necesitas la versión 3.10 o más reciente.

- **Windows / macOS**: descarga el instalador desde [python.org/downloads](https://www.python.org/downloads/). En Windows, marca la casilla **"Add Python to PATH"** durante la instalación. Si no la marcas, el sistema no encontrará Python después y vas a perder media hora buscando por qué.
- **Linux**: casi seguro ya lo tienes. Comprueba con `python3 --version`.

Para verificar que quedó bien instalado, abre una terminal (CMD o PowerShell en Windows, Terminal en macOS/Linux) y escribe:

```bash
python --version
```

Si responde algo como `Python 3.12.1`, listo. Si dice que el comando no existe, prueba con `python3 --version`. En muchos sistemas el comando se llama `python3`.

## La terminal interactiva: tu primer "Hola"

Escribe `python` (o `python3`) en la terminal y pulsa Enter. Aparece el prompt `>>>`. Eso es el intérprete interactivo, esperando órdenes. Prueba:

```python
>>> print("Hola, mundo")
Hola, mundo
>>> 2 + 2
4
>>> nombre = "Amador"
>>> print("Hola,", nombre)
Hola, Amador
```

El intérprete ejecuta cada línea al momento. Es perfecto para probar cosas rápidas. Para salir, escribe `exit()` o pulsa Ctrl+D (macOS/Linux) / Ctrl+Z y Enter (Windows).

## Scripts: código que se guarda

El intérprete interactivo olvida todo al cerrarse. Para programas de verdad, guardas el código en un archivo con extensión `.py` y lo ejecutas entero.

Crea un archivo `saludo.py` con este contenido:

```python
nombre = "Amador"
edad = 34
print(f"Me llamo {nombre} y tengo {edad} años")
```

Y ejecútalo desde la terminal:

```bash
python saludo.py
```

Salida:

```
Me llamo Amador y tengo 34 años
```

Esa `f` antes de las comillas es una *f-string*: permite meter variables dentro del texto usando `{}`. La verás en cada módulo a partir de aquí.

## Notebooks de Jupyter: el laboratorio para Pandas

Cuando lleguemos a NumPy y Pandas, lo cómodo no es ejecutar scripts enteros, sino probar trozos de código por separado y ver tablas y gráficos al instante. Para eso están los notebooks.

Un notebook es un documento que mezcla celdas de código (que ejecutas una a una) con texto. Para instalarlo:

```bash
pip install jupyterlab
```

Y lo arrancas con:

```bash
jupyter lab
```

Se abre en el navegador. Cada celda se ejecuta con Mayús+Enter. Si prefieres no instalar nada, [Google Colab](https://colab.research.google.com) te da notebooks gratis en la nube, con Pandas ya instalado. Para estos apuntes, Colab es la opción con menos fricción.

## pip: cómo instalar librerías

Python trae mucho de serie, pero NumPy y Pandas no. Se instalan con `pip`, el gestor de paquetes:

```bash
pip install numpy pandas
```

Lo harás una vez y quedan disponibles para siempre. Si usas Colab, ya vienen instaladas y te saltas este paso.

## Buenas costumbres desde el día uno

Comenta tu código. Un comentario empieza con `#` y Python lo ignora. Sirve para explicarte a ti mismo (o a quien lea) qué hace cada parte:

```python
# Calcula el precio con el 21% de IVA
precio_base = 100
precio_final = precio_base * 1.21  # 1.21 = 100% + 21% de IVA
print(precio_final)  # 121.0
```

No comentes lo obvio (`x = x + 1  # suma uno a x` no aporta nada). Comenta el *por qué*, no el *qué*.

---

## Ejercicios

**00.1** — Instala Python y comprueba la versión desde la terminal. Anota qué comando funciona en tu sistema: `python` o `python3`.

**00.2** — En el intérprete interactivo, calcula cuántos segundos tiene un día. Hazlo en una sola línea.

**00.3** — Crea un script `presentacion.py` que guarde tu nombre, tu edad y tu ciclo formativo en tres variables, y los imprima en una frase usando una f-string.

**00.4** — Modifica el script anterior para que calcule e imprima en qué año naciste, restando tu edad al año actual (2026). No escribas el año de nacimiento a mano: que lo calcule el programa.

**00.5** — Abre el intérprete interactivo y comprueba el tipo de estos valores: `3`, `3.0`, `"3"`, `True`, `None`. Usa `type()` sobre cada uno y anota los resultados.

**00.6** — Escribe un script que importe `sys` y muestre tres cosas: la versión de Python (`sys.version`), la ruta del ejecutable (`sys.executable`) y los tres primeros directorios de búsqueda de módulos (`sys.path[:3]`).

**00.7** — Instala la librería `rich` con pip (`pip install rich`). Úsala para imprimir un mensaje con color: `from rich import print; print("[bold green]Python listo[/bold green]")`. Si ya la tienes, prueba a formatear un número con `rich.pretty`.

---

<details markdown="1">
<summary>Soluciones</summary>

**00.1** — En Windows suele ser `python`; en macOS y muchas distros de Linux es `python3`. Ambos comandos pueden coexistir. La salida correcta es algo como `Python 3.12.x`.

---

**00.2**
```python
>>> 60 * 60 * 24
86400
```

---

**00.3**
```python
nombre = "Amador"
edad = 34
ciclo = "DAW"
print(f"Soy {nombre}, tengo {edad} años y estudio el ciclo de {ciclo}")
```

---

**00.4**
```python
nombre = "Amador"
edad = 34
ciclo = "DAW"
anio_actual = 2026
anio_nacimiento = anio_actual - edad
print(f"Soy {nombre}, estudio {ciclo} y nací en {anio_nacimiento}")
```


---

**00.5**
```python
>>> type(3)
<class 'int'>
>>> type(3.0)
<class 'float'>
>>> type("3")
<class 'str'>
>>> type(True)
<class 'bool'>
>>> type(None)
<class 'NoneType'>
```

---

**00.6**
```python
import sys
print(sys.version)
print(sys.executable)
print(sys.path[:3])
```

---

**00.7**
```bash
pip install rich
```
```python
from rich import print
print("[bold green]Python listo[/bold green]")
```
</details>
