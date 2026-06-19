# Python desde cero hasta Pandas avanzado

Curso de apuntes para llegar de no haber escrito una línea de código a manejar Pandas con soltura. Está pensado para alumnado de FP de grado superior, así que cada módulo combina teoría corta, código que puedes copiar y ejecutar, y ejercicios con sus soluciones plegadas al final.

## Cómo usar estos apuntes

Lee de arriba abajo. Cada módulo asume que dominas el anterior. No saltes a Pandas sin tener claras las listas y los diccionarios: vas a sufrir. Escribe el código tú mismo, no te limites a leerlo. La diferencia entre quien aprende a programar y quien cree que lo ha aprendido es esa.

Las soluciones están dentro de cada `.md`, en una sección plegable al final (`<details>`). Intenta resolver el ejercicio antes de abrirlas.

## Qué necesitas

Python 3.10 o superior y, a partir del módulo 06, las librerías NumPy y Pandas. La instalación se explica en el módulo 00. Cualquier editor sirve, pero los notebooks de Jupyter son cómodos para experimentar.

## Índice de módulos

| # | Archivo | Qué aprendes |
|---|---------|--------------|
| 00 | `..._00-Entorno_v1.md` | Instalar Python, ejecutar tu primer programa, notebooks |
| 01 | `..._01-Variables-y-tipos_v1.md` | Variables, números, cadenas, booleanos, operadores |
| 02 | `..._02-Control-de-flujo_v1.md` | Condicionales, bucles `for` y `while` |
| 03 | `..._03-Estructuras-de-datos_v1.md` | Listas, tuplas, diccionarios, conjuntos |
| 04 | `..._04-Funciones-y-errores_v1.md` | Funciones, módulos, excepciones, archivos |
| 05 | `..._05-POO-y-comprensiones_v1.md` | Clases, objetos, comprensiones, generadores |
| 06 | `..._06-NumPy_v1.md` | Arrays, vectorización, broadcasting |
| 07 | `..._07-Pandas-Series-y-DataFrames_v1.md` | Estructuras de Pandas, cargar datos |
| 08 | `..._08-Pandas-Seleccion-y-filtrado_v1.md` | `loc`, `iloc`, filtros booleanos |
| 09 | `..._09-Pandas-Limpieza_v1.md` | Nulos, duplicados, tipos, texto |
| 10 | `..._10-Pandas-GroupBy-y-merge_v1.md` | Agrupaciones, pivot, uniones de tablas |
| 11 | `..._11-Pandas-Series-temporales-y-proyecto_v1.md` | Fechas, visualización, proyecto final |
| 12 | `..._12-Archivos-y-formatos_v1.md` | CSV, JSON, Excel con Pandas; rutas con pathlib |
| 13 | `..._13-Visualizacion_v1.md` | Matplotlib y Seaborn: gráficos de líneas, barras, scatter, heatmap |
| 14 | `..._14-Pandas-avanzado_v1.md` | apply, query, method chaining, MultiIndex, optimización de memoria |
| 15 | `..._15-APIs-y-requests_v1.md` | Peticiones HTTP, JSON a DataFrame, autenticación, paginación |

## Ruta recomendada

Si vienes de cero, dedica una sesión a cada módulo del 00 al 05. Los módulos de Pandas (07–11) son más densos y conviene repartirlos en dos sesiones cada uno: una para leer y otra para los ejercicios.

Los módulos 12–15 son independientes entre sí y pueden hacerse en cualquier orden una vez dominado el bloque de Pandas.

Si ya programas y solo quieres Pandas, repasa el 03 (estructuras de datos) y el 06 (NumPy) y salta al 07.
