# Matplotlib Plotting: A Practical Guide

This guide introduces the basic plotting methods in `matplotlib.pyplot` (`plt`) and explains how each one changes a chart. It is intended to accompany the notebooks in this folder, especially [01_matplotlib_fundamentals.ipynb](01_matplotlib_fundamentals.ipynb).

## What Matplotlib puts on the screen

Matplotlib organizes a visualization into a small number of objects:

- **Figure (`Figure`)**: the complete canvas or image. A figure can contain one or more plots.
- **Axes (`Axes`)**: one plotting area inside a figure. It holds the data, title, axis labels, ticks, grid, and legend. The plural name `Axes` refers to a single plotting area; it is not the same as an `Axis`.
- **Axis (`Axis`)**: the x- or y-axis object that controls its scale, limits, tick marks, and tick labels.
- **Artists**: the visible elements drawn on a figure, such as lines, bars, text, markers, and legends.

The usual starting point is:

```python
import matplotlib.pyplot as plt

fig, ax = plt.subplots()
```

`plt.subplots()` creates a `Figure` and an `Axes`. You can add data and formatting to `ax`, then display or save the containing `fig`.

### Two ways to call plotting methods

`pyplot` provides convenient functions that act on the **current** figure and axes:

```python
plt.plot(x, y)
plt.title("A title")
plt.xlabel("Time")
```

The object-oriented interface calls methods on a specific axes:

```python
fig, ax = plt.subplots()
ax.plot(x, y)
ax.set_title("A title")
ax.set_xlabel("Time")
```

Both interfaces use Matplotlib. The `plt` style is convenient for a quick, single chart; the `ax` style makes it clear which chart is being changed and is recommended for multiple subplots or reusable code. For example, `plt.title(...)` changes the title of the current axes, while `ax.set_title(...)` changes the title of the specific `ax`.

## A first line plot

```python
import matplotlib.pyplot as plt

days = [1, 2, 3, 4, 5]
temperatures = [18, 20, 19, 23, 25]

fig, ax = plt.subplots()
ax.plot(days, temperatures)
plt.show()
```

`plot(x, y)` draws y-values against x-values and connects the points with a line by default. If only one sequence is supplied, Matplotlib uses its positions (`0`, `1`, `2`, ...) as the x-values:

```python
plt.plot([18, 20, 19, 23, 25])
```

The x and y sequences should have compatible lengths. A line plot is useful when order or continuity matters, such as measurements over time.

### Line appearance and labels

```python
ax.plot(
    days,
    temperatures,
    color="teal",
    linestyle="--",
    marker="o",
    linewidth=2,
    markersize=6,
    label="Daily high",
)
```

Common `plot` options include:

| Option | What it changes | Example |
|---|---|---|
| `color` or `c` | Line/marker color | `color="teal"` |
| `linestyle` or `ls` | Line pattern (`"-"`, `"--"`, `"-."`, `":"`, or `"None"`) | `linestyle="--"` |
| `linewidth` or `lw` | Line thickness | `linewidth=2` |
| `marker` | Symbol drawn at each data point | `marker="o"` |
| `markersize` or `ms` | Marker size | `markersize=6` |
| `label` | Name used by the legend | `label="Daily high"` |
| `alpha` | Transparency from `0` (transparent) to `1` (opaque) | `alpha=0.7` |

You can draw more than one series on the same axes by calling `plot` more than once. Give each series a `label`, then call `ax.legend()`.

## Titles and axis labels

Titles and labels add context; they do not change the plotted data.

### `title` and `set_title`

```python
plt.title("Daily temperatures")
# Object-oriented equivalent:
ax.set_title("Daily temperatures")
```

Sets the title above **one Axes** (one plot area). You can set its position and appearance:

```python
ax.set_title("Daily temperatures", loc="left", fontsize=14, pad=10)
```

`loc` can be `"left"`, `"center"`, or `"right"`. `fontsize` controls the text size, and `pad` controls the gap above the axes.

### `suptitle`

```python
fig.suptitle("Weather report", fontsize=16)
```

Adds a figure-level title. Use it as a shared heading above several subplots; it is different from the individual title set with `ax.set_title(...)`. If the figure has multiple axes, leave room for the figure title, for example by creating the layout with `layout="constrained"`.

### `xlabel` / `set_xlabel` and `ylabel` / `set_ylabel`

```python
ax.set_xlabel("Day of the week")
ax.set_ylabel("Temperature (°C)")
```

These name the quantities represented by the x- and y-axes. Include units where useful. The `plt` equivalents are `plt.xlabel(...)` and `plt.ylabel(...)`. Labels can also be styled:

```python
ax.set_xlabel("Day", fontsize=11, labelpad=8)
ax.set_ylabel("Temperature (°C)", fontsize=11)
```

### `text` and `annotate`

`text` places text at data coordinates:

```python
ax.text(3, 19.5, "Midweek")
```

`annotate` labels a particular point and can draw an arrow to it:

```python
ax.annotate(
    "Warmest day",
    xy=(5, 25),          # point to identify, in data coordinates
    xytext=(3.5, 26),     # where to put the text, also in data coordinates
    arrowprops={"arrowstyle": "->"},
)
```

Use annotations for explaining a notable value or event, rather than labeling every point.

## Ticks, tick labels, and number formatting

**Ticks** are the small marks along an axis. **Tick labels** are the values or words printed next to those marks. Ticks help a reader estimate positions; they are separate from the axis label.

### `xticks` and `yticks`

The `pyplot` functions can inspect or set tick positions and labels on the current axes:

```python
plt.xticks([1, 2, 3, 4, 5])  # put x ticks at these data positions
plt.yticks([15, 20, 25, 30])
```

You can provide labels for the selected positions:

```python
plt.xticks(
    [1, 2, 3, 4, 5],
    ["Mon", "Tue", "Wed", "Thu", "Fri"],
    rotation=30,
)
```

For an explicit `Axes`, use:

```python
ax.set_xticks([1, 2, 3, 4, 5], labels=["Mon", "Tue", "Wed", "Thu", "Fri"])
ax.set_yticks([15, 20, 25, 30])
```

`rotation` rotates tick-label text, which is useful when labels overlap. Set tick positions and their matching labels together. Manually setting labels alone can leave them misaligned with automatically chosen tick positions; fixed tick locations are also less suitable for data whose range changes.

To style ticks without choosing their positions, use `tick_params`:

```python
ax.tick_params(
    axis="x",
    which="major",
    labelsize=10,
    labelrotation=30,
    length=5,
    direction="out",
)
```

`axis` selects `"x"`, `"y"`, or `"both"`; `which` selects `"major"`, `"minor"`, or `"both"`. This is generally preferable to styling only the tick objects currently present.

For numeric axes, a formatter changes how tick values are *printed* without changing the underlying data:

```python
from matplotlib.ticker import StrMethodFormatter

ax.yaxis.set_major_formatter(StrMethodFormatter("${x:,.0f}"))
```

For example, a value of `1250` is displayed as `$1,250`. Formatters are useful for currencies, percentages, and units; use a label such as `"Revenue ($)"` as well so the units remain clear.

### Limits, scales, and grid

```python
ax.set_xlim(1, 5)
ax.set_ylim(15, 30)
ax.set_yscale("linear")
```

- `set_xlim(left, right)` and `set_ylim(bottom, top)` control the visible data range. In the `pyplot` interface use `plt.xlim(...)` and `plt.ylim(...)`. Setting a limit turns autoscaling off for that axis.
- `set_xscale(...)` and `set_yscale(...)` choose how values are mapped to positions. Common scales are `"linear"` and `"log"`; logarithmic scales are appropriate only for positive values.
- `grid(...)` shows reference lines aligned with ticks:

```python
ax.grid(True, axis="y", linestyle=":", alpha=0.5)
# Or disable the grid:
ax.grid(False)
```

`axis` can be `"x"`, `"y"`, or `"both"`. Gridlines can be useful for reading values, but too many strong gridlines can distract from the data.

## Legends

A legend explains which plotted series corresponds to which visual style. Set a label on each series and add a legend:

```python
ax.plot(days, temperatures, label="Daily high")
ax.legend()
```

The legend uses artists with labels that do not start with an underscore. You can choose a position:

```python
ax.legend(loc="upper left", title="Measurements")
```

Common `loc` values include `"best"`, `"upper left"`, `"upper right"`, `"lower left"`, and `"lower right"`. `"best"` chooses a position intended to reduce overlap, but for a busy chart it may be better to select a known position yourself.

## Common basic chart methods

Choose the plotting method to match the type of data and the question:

| Method | What it draws | Typical use |
|---|---|---|
| `plot(x, y)` | Lines and/or markers | Trends or ordered measurements |
| `scatter(x, y)` | Individual points | Relationship between two numeric variables |
| `bar(x, height)` | Rectangular bars | Comparing category values |
| `barh(y, width)` | Horizontal bars | Comparing categories with long names |
| `hist(values, bins=...)` | Binned counts or density | Distribution of a numeric variable |
| `boxplot(data)` | Median, quartiles, whiskers, and outliers | Comparing distributions |
| `pie(values, labels=...)` | Wedges sized by values | A small number of parts of a whole; bars are often easier to compare |
| `imshow(image)` | A matrix or image | Displaying image or grid-like data |

Examples:

```python
# Scatter: each pair is a point; color can encode a third numeric variable.
ax.scatter(days, temperatures, color="darkorange", s=50, alpha=0.8)

# Bars: one bar for each category and corresponding height.
ax.bar(["A", "B", "C"], [12, 19, 15], color="steelblue")

# Histogram: bins group numeric values and bars show how many fall in each.
ax.hist([18, 20, 19, 23, 25, 20, 22, 24], bins=5, edgecolor="white")
```

The x and y arguments to `scatter` are paired observations. For a histogram, `bins` controls the grouping: it can be an integer number of bins or a sequence of bin edges. Unlike `bar`, `hist` computes the counts from raw observations.

## Complete example: a labeled and annotated plot

The following example brings together the most-used methods:

```python
import matplotlib.pyplot as plt

days = [1, 2, 3, 4, 5]
highs = [18, 20, 19, 23, 25]
lows = [11, 12, 13, 14, 16]
day_names = ["Mon", "Tue", "Wed", "Thu", "Fri"]

fig, ax = plt.subplots(figsize=(8, 5), layout="constrained")

ax.plot(days, highs, marker="o", linewidth=2, label="High")
ax.plot(days, lows, marker="s", linestyle="--", label="Low")

ax.set_title("Temperatures during the week")
ax.set_xlabel("Day")
ax.set_ylabel("Temperature (°C)")
ax.set_xticks(days, labels=day_names)
ax.set_ylim(0, 30)
ax.grid(True, axis="y", alpha=0.3)
ax.legend(title="Measurement")

ax.annotate(
    "Weekly high",
    xy=(5, 25),
    xytext=(3.7, 27),
    arrowprops={"arrowstyle": "->"},
)

plt.show()
# To save instead of (or as well as) display:
# fig.savefig("weekly_temperatures.png", dpi=150, bbox_inches="tight")
```

This creates one figure containing one axes. The two `plot` calls add two line artists to that axes; the `set_*` calls configure that axes; `legend` describes the two lines; and `annotate` adds text and an arrow pointing at the Friday high. `show` displays the result in a script or interactive session. Jupyter usually displays the final figure from a cell automatically.

## Multiple plots and layout

Use `plt.subplots()` to create several axes in one figure:

```python
fig, axes = plt.subplots(1, 2, figsize=(10, 4), layout="constrained")

axes[0].plot(days, highs, marker="o")
axes[0].set_title("High temperatures")

axes[1].bar(day_names, lows)
axes[1].set_title("Low temperatures")

fig.suptitle("Weekly temperatures")
plt.show()
```

Here, `axes[0]` and `axes[1]` are separate `Axes` objects. Configure each one through its own variable to avoid accidentally changing the wrong plot. `layout="constrained"` helps make room for labels and titles.

## Displaying, saving, and closing figures

- **`plt.show()`** displays open figures. A notebook commonly renders figures automatically; scripts generally call `show()` to open a display window.
- **`fig.savefig("chart.png")`** saves a figure to a file. The filename extension selects the format (such as PNG, PDF, or SVG). For sharper raster images, specify a higher `dpi`; `bbox_inches="tight"` can trim extra whitespace:

  ```python
  fig.savefig("chart.png", dpi=150, bbox_inches="tight")
  fig.savefig("chart.svg", bbox_inches="tight")
  ```

- **`plt.close(fig)`** closes a figure and releases it from pyplot's open-figure list. This is useful in scripts that create many figures.

Save the figure before closing it. In notebooks, use a distinct filename or path if you do not want to overwrite an existing image.

## Jupyter Markdown, headings, and plot titles

Markdown is a feature of the notebook interface, **not a Matplotlib method**. A Markdown cell can introduce a chart, define terms, or explain what to look for; running it does not change a `Figure` or `Axes`.

For example, write this in a notebook Markdown cell:

```markdown
## Comparing daily temperatures

The chart below compares each day's high and low.
```

The `##` makes a level-two heading in the notebook page. It does not become the plot title. To put a heading on a chart, use `ax.set_title(...)`; to add one shared across subplots, use `fig.suptitle(...)`. A notebook heading is sometimes called a *header*, but there is no basic `plt.header()` plotting function.

## Frequent beginner mistakes

- **Confusing `Axes` and `Axis`:** `ax` is the plot area; its x- and y-axes are the coordinate scales along its edges.
- **Confusing labels and tick labels:** `set_xlabel` names the variable; `set_xticks` sets tick positions and optionally their printed labels.
- **Setting tick labels without fixed tick positions:** the labels can end up attached to unintended positions. Set locations and labels together.
- **Using `plt` with many subplots:** pyplot changes whichever axes is current. Prefer `ax` methods and keep the axes returned by `plt.subplots()`.
- **Choosing a chart that hides the message:** use a line for ordered data, scatter for paired measurements, bars for category comparisons, and a histogram for a numeric distribution.
- **Forgetting units or context:** add informative axis labels and a useful title.
- **Saving before finishing the plot:** add titles, labels, legends, and layout before calling `savefig`.

## Official documentation

This guide follows the Matplotlib stable documentation. Consult the API pages for all parameters and return values:

- [Quick start guide](https://matplotlib.org/stable/users/explain/quick_start.html) — introductory patterns and figure components.
- [Introduction to Figures](https://matplotlib.org/stable/users/explain/figure/figure_intro.html) — figures, axes, and displaying or saving figures.
- [`pyplot.subplots`](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.subplots.html) — create a figure and axes.
- [`pyplot.plot`](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.plot.html) — line plots and line style options.
- [`pyplot.xticks`](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.xticks.html) — inspect or set x tick positions and labels; see also [`pyplot.yticks`](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.yticks.html).
- [`pyplot.tick_params`](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.tick_params.html) — style ticks, labels, and gridlines.
- [`pyplot.xlabel`](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.xlabel.html), [`pyplot.ylabel`](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.ylabel.html), [`pyplot.title`](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.title.html), and [`pyplot.suptitle`](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.suptitle.html) — labels and axes/figure titles.
- [`pyplot.grid`](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.grid.html) and [`pyplot.legend`](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.legend.html) — reference lines and series keys.
- [`pyplot.scatter`](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.scatter.html), [`pyplot.bar`](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.bar.html), and [`pyplot.hist`](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.hist.html) — common chart types.
- [`pyplot.boxplot`](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.boxplot.html), [`pyplot.pie`](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.pie.html), and [`pyplot.imshow`](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.imshow.html) — other basic visualization methods.
- [Tick locators and formatters](https://matplotlib.org/stable/api/ticker_api.html) — automatic tick placement and label formatting.
- [`pyplot.annotate`](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.annotate.html) and [`pyplot.savefig`](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.savefig.html) — annotation and image export.
