---
title: Python desde cero hasta Pandas
---

# Python desde cero hasta Pandas avanzado

Curso de apuntes para llegar de no haber escrito una línea de código a manejar Pandas con soltura. Está pensado para alumnado de FP de grado superior, así que cada módulo combina teoría corta, código que puedes copiar y ejecutar, y ejercicios con sus soluciones plegadas al final.

## Cómo usar estos apuntes

Lee de arriba abajo. Cada módulo asume que dominas el anterior. No saltes a Pandas sin tener claras las listas y los diccionarios: vas a sufrir. Escribe el código tú mismo, no te limites a leerlo. La diferencia entre quien aprende a programar y quien cree que lo ha aprendido es esa.

Las soluciones están dentro de cada módulo, en una sección plegable al final (`<details>`). Intenta resolver el ejercicio antes de abrirlas.

## Qué necesitas

Python 3.10 o superior y, a partir del módulo 06, las librerías NumPy y Pandas. La instalación se explica en el módulo 00. Cualquier editor sirve, pero los notebooks de Jupyter son cómodos para experimentar.

## Índice de módulos

| # | Módulo | Qué aprendes |
|---|--------|--------------|
| 00 | [Entorno](Python-Pandas_Apuntes_00-Entorno) | Instalar Python, ejecutar tu primer programa, notebooks |
| 01 | [Variables y tipos](Python-Pandas_Apuntes_01-Variables-y-tipos) | Variables, números, cadenas, booleanos, operadores |
| 02 | [Control de flujo](Python-Pandas_Apuntes_02-Control-de-flujo) | Condicionales, bucles `for` y `while` |
| 03 | [Estructuras de datos](Python-Pandas_Apuntes_03-Estructuras-de-datos) | Listas, tuplas, diccionarios, conjuntos |
| 04 | [Funciones y errores](Python-Pandas_Apuntes_04-Funciones-y-errores) | Funciones, módulos, excepciones, archivos |
| 05 | [POO y comprensiones](Python-Pandas_Apuntes_05-POO-y-comprensiones) | Clases, objetos, comprensiones, generadores |
| 06 | [NumPy](Python-Pandas_Apuntes_06-NumPy) | Arrays, vectorización, broadcasting |
| 07 | [Pandas — Series y DataFrames](Python-Pandas_Apuntes_07-Pandas-Series-y-DataFrames) | Estructuras de Pandas, cargar datos |
| 08 | [Pandas — Selección y filtrado](Python-Pandas_Apuntes_08-Pandas-Seleccion-y-filtrado) | `loc`, `iloc`, filtros booleanos |
| 09 | [Pandas — Limpieza](Python-Pandas_Apuntes_09-Pandas-Limpieza) | Nulos, duplicados, tipos, texto |
| 10 | [Pandas — GroupBy y merge](Python-Pandas_Apuntes_10-Pandas-GroupBy-y-merge) | Agrupaciones, pivot, uniones de tablas |
| 11 | [Series temporales y proyecto](Python-Pandas_Apuntes_11-Pandas-Series-temporales-y-proyecto) | Fechas, visualización, proyecto final |
| 12 | [Archivos y formatos](Python-Pandas_Apuntes_12-Archivos-y-formatos) | CSV, JSON, Excel con Pandas; rutas con pathlib |
| 13 | [Visualización](Python-Pandas_Apuntes_13-Visualizacion) | Matplotlib y Seaborn: líneas, barras, scatter, heatmap |
| 14 | [Pandas avanzado](Python-Pandas_Apuntes_14-Pandas-avanzado) | apply, query, method chaining, MultiIndex, memoria |
| 15 | [APIs y requests](Python-Pandas_Apuntes_15-APIs-y-requests) | HTTP, JSON a DataFrame, autenticación, paginación |

## Ruta recomendada

Si vienes de cero, dedica una sesión a cada módulo del 00 al 05. Los módulos de Pandas (07–11) son más densos y conviene repartirlos en dos sesiones cada uno: una para leer y otra para los ejercicios.

Si ya programas y solo quieres Pandas, repasa por encima el 03 (estructuras de datos) y el 06 (NumPy) y salta al 07.
