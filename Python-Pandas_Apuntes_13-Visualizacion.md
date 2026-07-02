# Módulo 13 — Visualización con Matplotlib y Seaborn

Un número no habla solo. Un gráfico bien hecho responde en dos segundos preguntas que una tabla de cien filas no responde en dos minutos. Este módulo cubre las dos librerías que vas a usar en análisis de datos: Matplotlib para el control total y Seaborn para gráficos estadísticos con menos código.

## Instalar

```bash
pip install matplotlib seaborn
```

```python
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd
import numpy as np
```

## Matplotlib: la base de todo

Matplotlib tiene dos formas de trabajar. La forma rápida usa `plt` directamente:

```python
x = [1, 2, 3, 4, 5]
y = [10, 25, 15, 30, 20]

plt.plot(x, y)
plt.title("Ventas por semana")
plt.xlabel("Semana")
plt.ylabel("Unidades")
plt.show()
```

La forma correcta para proyectos reales usa `Figure` y `Axes` explícitamente. Esto permite múltiples gráficos en una misma figura y da control total:

```python
fig, ax = plt.subplots()     # una figura, un gráfico
ax.plot(x, y, color="steelblue", linewidth=2, marker="o")
ax.set_title("Ventas por semana")
ax.set_xlabel("Semana")
ax.set_ylabel("Unidades")
fig.savefig("ventas.png", dpi=150, bbox_inches="tight")
plt.show()
```

`fig` es el contenedor. `ax` es el área donde se dibuja. La distinción importa cuando tienes varios gráficos juntos.

## Los tipos de gráfico más útiles

**Líneas** — para evoluciones en el tiempo:

```python
fig, ax = plt.subplots(figsize=(10, 4))
ax.plot(fechas, ventas, color="steelblue", linewidth=2)
ax.fill_between(fechas, ventas, alpha=0.1, color="steelblue")  # área bajo la curva
ax.set_title("Evolución de ventas")
```

**Barras** — para comparar categorías:

```python
categorias = ["A", "B", "C", "D"]
valores = [45, 80, 30, 60]

fig, ax = plt.subplots()
ax.bar(categorias, valores, color="coral", edgecolor="white")
ax.set_title("Ventas por categoría")
```

**Dispersión (scatter)** — para ver relación entre dos variables:

```python
fig, ax = plt.subplots()
ax.scatter(df["precio"], df["unidades"], alpha=0.5, s=30)
ax.set_xlabel("Precio")
ax.set_ylabel("Unidades vendidas")
```

**Histograma** — para ver la distribución de una variable:

```python
fig, ax = plt.subplots()
ax.hist(df["importe"], bins=20, color="mediumseagreen", edgecolor="white")
ax.set_xlabel("Importe")
ax.set_ylabel("Frecuencia")
```

**Múltiples gráficos en una figura:**

```python
fig, axes = plt.subplots(1, 2, figsize=(12, 4))   # 1 fila, 2 columnas

axes[0].plot(x, y1, color="steelblue")
axes[0].set_title("Serie A")

axes[1].bar(categorias, valores, color="coral")
axes[1].set_title("Categorías")

fig.suptitle("Panel de análisis", fontsize=14)
fig.tight_layout()   # ajusta los márgenes para que no se solapen
plt.show()
```

## Seaborn: estadística con menos código

Seaborn está construido sobre Matplotlib. Su ventaja: entiende DataFrames de Pandas directamente y añade información estadística automáticamente (intervalos de confianza, regresiones, agrupaciones por color).

```python
import seaborn as sns

# Tema visual limpio de base:
sns.set_theme(style="whitegrid")
```

**Gráfico de líneas con media e intervalo de confianza:**

```python
sns.lineplot(data=df, x="mes", y="importe", hue="categoria")
plt.show()
```

`hue` separa en colores por una variable categórica. Con una línea añades la leyenda.

**Barras agrupadas:**

```python
sns.barplot(data=df, x="categoria", y="importe", hue="año", errorbar=None)
```

**Histograma con curva de densidad:**

```python
sns.histplot(data=df, x="importe", kde=True, bins=30)
```

**Boxplot** — para ver distribución y detectar outliers:

```python
sns.boxplot(data=df, x="categoria", y="precio")
```

**Heatmap** — para matrices de correlación o tablas dinámicas:

```python
correlacion = df[["precio", "cantidad", "importe"]].corr()
sns.heatmap(correlacion, annot=True, fmt=".2f", cmap="coolwarm", center=0)
plt.title("Correlación entre variables")
```

`annot=True` escribe los valores dentro de cada celda. `cmap="coolwarm"` usa rojo para correlaciones positivas y azul para negativas.

**Pairplot** — para ver todas las relaciones entre variables numéricas de un vistazo:

```python
sns.pairplot(df[["precio", "cantidad", "importe"]], diag_kind="kde")
```

Útil para exploración inicial. No lo uses en presentaciones finales; es demasiado denso.

## Guardar figuras

Siempre usa `savefig` antes de `show()`. Después de `show()` la figura se limpia:

```python
fig.savefig("grafico.png", dpi=150, bbox_inches="tight")
fig.savefig("grafico.pdf", bbox_inches="tight")   # vectorial para informes
plt.show()
```

`dpi=150` es buena resolución para pantalla. `dpi=300` para imprimir. `bbox_inches="tight"` elimina el margen blanco excesivo.

## Qué usar cuándo

| Necesitas | Usa |
|-----------|-----|
| Control total, personalización | Matplotlib (fig, ax) |
| Gráficos estadísticos rápidos | Seaborn |
| Heatmap, pairplot, boxplot | Seaborn |
| Gráficos para un dashboard o informe exportado | Matplotlib con savefig |

---

## Ejercicios

Para estos ejercicios usa el DataFrame de ventas del módulo 11 o uno propio con columnas numéricas y categóricas.

**13.1** — Crea un gráfico de barras con los ingresos totales por categoría de producto. Ponle título, etiquetas en los ejes y guárdalo como PNG.

**13.2** — Dibuja la evolución de ingresos mensuales con un gráfico de líneas. Añade el área bajo la curva con `fill_between` y una línea horizontal de referencia en la media con `ax.axhline`.

**13.3** — Usa `plt.subplots(1, 2)` para mostrar en la misma figura: (a) histograma de precios unitarios, (b) scatter de precio vs. cantidad. Ajusta con `tight_layout`.

**13.4** — Con Seaborn, dibuja un boxplot de los importes agrupados por categoría. Aplica el tema `whitegrid` e identifica visualmente si hay categorías con outliers.

**13.5** — Calcula la correlación entre las variables numéricas de tu DataFrame y muéstrala en un heatmap anotado con `seaborn`. Usa `cmap="coolwarm"`.

**13.6** — Genera un gráfico de barras horizontales (`ax.barh`) con los 10 productos con más ingresos, ordenados de mayor a menor. Etiqueta cada barra con su valor usando `ax.text`.

---

<details markdown="1">
<summary>Soluciones</summary>

**13.1**
```python
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv("ventas.csv", parse_dates=["fecha"])
df["importe"] = df["precio_unitario"] * df["cantidad"]
resumen = df.groupby("categoria")["importe"].sum().sort_values(ascending=False)

fig, ax = plt.subplots(figsize=(8, 5))
ax.bar(resumen.index, resumen.values, color="steelblue", edgecolor="white")
ax.set_title("Ingresos por categoría")
ax.set_xlabel("Categoría")
ax.set_ylabel("Ingresos (€)")
fig.savefig("ingresos_categoria.png", dpi=150, bbox_inches="tight")
plt.show()
```

---

**13.2**
```python
df["mes"] = df["fecha"].dt.to_period("M").astype(str)
mensual = df.groupby("mes")["importe"].sum()

fig, ax = plt.subplots(figsize=(10, 4))
ax.plot(mensual.index, mensual.values, color="steelblue", linewidth=2, marker="o")
ax.fill_between(mensual.index, mensual.values, alpha=0.15, color="steelblue")
ax.axhline(mensual.mean(), color="coral", linestyle="--", label="Media")
ax.set_title("Evolución mensual de ingresos")
ax.set_xlabel("Mes")
ax.set_ylabel("Ingresos (€)")
ax.legend()
plt.xticks(rotation=45)
fig.tight_layout()
plt.show()
```

---

**13.3**
```python
fig, axes = plt.subplots(1, 2, figsize=(12, 4))

axes[0].hist(df["precio_unitario"], bins=20, color="mediumseagreen", edgecolor="white")
axes[0].set_title("Distribución de precios")
axes[0].set_xlabel("Precio unitario (€)")

axes[1].scatter(df["precio_unitario"], df["cantidad"], alpha=0.4, s=25, color="coral")
axes[1].set_title("Precio vs. Cantidad")
axes[1].set_xlabel("Precio unitario (€)")
axes[1].set_ylabel("Cantidad")

fig.tight_layout()
plt.show()
```

---

**13.4**
```python
import seaborn as sns

sns.set_theme(style="whitegrid")
fig, ax = plt.subplots(figsize=(8, 5))
sns.boxplot(data=df, x="categoria", y="importe", ax=ax)
ax.set_title("Distribución de importes por categoría")
ax.set_xlabel("Categoría")
ax.set_ylabel("Importe (€)")
plt.xticks(rotation=30)
plt.show()
```

---

**13.5**
```python
import seaborn as sns

numericas = df[["precio_unitario", "cantidad", "importe"]].corr()

fig, ax = plt.subplots(figsize=(6, 5))
sns.heatmap(numericas, annot=True, fmt=".2f", cmap="coolwarm", center=0, ax=ax)
ax.set_title("Correlación entre variables")
fig.tight_layout()
plt.show()
```

---

**13.6**
```python
top10 = df.groupby("nombre")["importe"].sum().nlargest(10).sort_values()

fig, ax = plt.subplots(figsize=(8, 6))
bars = ax.barh(top10.index, top10.values, color="steelblue")
for bar, val in zip(bars, top10.values):
    ax.text(bar.get_width() + 50, bar.get_y() + bar.get_height() / 2,
            f"{val:,.0f} €", va="center", fontsize=9)
ax.set_title("Top 10 productos por ingresos")
ax.set_xlabel("Ingresos (€)")
fig.tight_layout()
plt.show()
```

</details>
