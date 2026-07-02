# Academic Figure Style Guide

## Core Rule: Style Consistency Within a Project

**All figures in the same paper/project MUST use identical style settings.** This means:
- Same font family and sizes
- Same color palette for the same semantic groups
- Same spine/grid/legend treatment
- Same figsize height for figures that will be composed together

At project start, lock down the style profile and color palette with the author. Reference these settings in every plotting script (e.g., via a shared `style_config.py` or rcParams block at the top of each script).

Colors should be academic and professional. Avoid overly saturated or neon colors. The examples below are proven palettes from published papers — use them as starting points, but confirm with the author.

## Style Profiles

This guide defines two style profiles. Select based on venue or author preference.

### Profile A: Sans-Serif (NeurIPS/ICML default)

```python
import matplotlib
matplotlib.use("Agg")
import matplotlib.pyplot as plt

plt.rcParams.update({
    'font.family': 'sans-serif',
    'font.sans-serif': ['Arial', 'Helvetica', 'DejaVu Sans'],
    'font.size': 10,
    'axes.labelsize': 11,
    'axes.titlesize': 12,
    'xtick.labelsize': 9,
    'ytick.labelsize': 9,
    'legend.fontsize': 9,
})
```

### Profile B: Serif, Clean Spines (polished style)

```python
import matplotlib
matplotlib.use("Agg")
import matplotlib.pyplot as plt

plt.rcParams.update({
    'font.family': 'serif',
    'font.serif': ['Times New Roman', 'DejaVu Serif'],
    'font.size': 11,
    'axes.labelsize': 12,
    'xtick.labelsize': 10,
    'ytick.labelsize': 10,
    'legend.fontsize': 10,
    'figure.dpi': 200,
    'savefig.dpi': 200,
    'savefig.bbox': 'tight',
    'axes.spines.top': False,
    'axes.spines.right': False,
})
```

Ask the author which profile to use. All figures in one paper must use the same profile.

## Color Palettes

Two-group contrastive:
- Group A (correction/positive): `steelblue` (#4682B4)
- Group B (compliance/negative): `coral` (#FF7F50)

Three-group contrastive (e.g., success/partial/fail):
- Success: `#C03D3E` (dark red)
- Partial: `#E1812C` (orange)
- Fail: `#3274A1` (blue)

Sweep/method curves:
- `#e74c3c` (red) — primary metric
- `#e67e22` (orange) — secondary metric
- `#2ecc71` (green) — tertiary metric

Multi-pair line comparison (with distinct line styles):
- `color='#C03D3E'`, `linewidth=2.5`, `linestyle='-'` — primary pair
- `color='#3274A1'`, `linewidth=1.8`, `linestyle='--'` — secondary pair
- `color='#E1812C'`, `linewidth=1.8`, `linestyle='-.'` — tertiary pair

Reference lines:
- `green`, `linestyle='--'`, `alpha=0.5` — baseline
- `gray`, `linestyle='--'`, `alpha=0.7` — selected value marker

## Chart Type Recipes

### Scatter Plots

```python
ax.scatter(x, y, c='steelblue', alpha=0.7, s=60,
           edgecolors='black', linewidths=0.5, label='Label')
```

- Use jitter for overlapping categorical positions: `np.random.uniform(-0.2, 0.2, n)`
- Show per-group mean as solid horizontal bars

### Line + Shaded Error Band

```python
ax.plot(x, mean, '-o', color=COLOR, label=label, markersize=5, linewidth=2)
ax.fill_between(x, mean - sem, mean + sem, alpha=0.15, color=COLOR)
```

### Violin + Box + Strip Combo

```python
vp = ax.violinplot(data, showmeans=False, showmedians=False, showextrema=False)
for body, c in zip(vp['bodies'], colors):
    body.set_facecolor(c); body.set_alpha(0.3); body.set_edgecolor(c)
bp = ax.boxplot(data, widths=0.15, patch_artist=True, showfliers=False,
                medianprops=dict(color='black', linewidth=1.5))
# Add strip (jittered scatter) on top
rng = np.random.default_rng(42)
jitter = rng.normal(0, 0.04, n)
ax.scatter(pos + jitter, values, c=color, alpha=0.5, s=15,
           edgecolors='white', linewidths=0.3, zorder=3)
# Diamond marker for group mean
ax.scatter(pos, mean_val, marker='D', s=60, color='black', zorder=5,
           edgecolors='white', linewidths=0.8)
ax.annotate(f'{mean_val:.2f}', (pos, mean_val), textcoords='offset points',
            xytext=(15, 0), fontsize=10, fontweight='bold', va='center')
```

### KDE Density

```python
from scipy import stats
kde_x = np.linspace(lo, hi, 200)
kde = stats.gaussian_kde(vals, bw_method=0.3)
ax.fill_between(kde_x, kde(kde_x), alpha=0.25, color=COLOR)
ax.plot(kde_x, kde(kde_x), color=COLOR, linewidth=2, label=label)
ax.axvline(vals.mean(), color=COLOR, linestyle='--', linewidth=1.2, alpha=0.8)
```

### Heatmap (seaborn)

```python
import seaborn as sns
sns.heatmap(matrix.T, ax=ax, cmap='RdBu_r', center=0,
            xticklabels=layer_labels, yticklabels=range(n_heads))
# Highlight top-k cells
for li, hi in top_cells:
    ax.add_patch(plt.Rectangle((li-0.5, hi-0.5), 1, 1,
                                fill=False, edgecolor='black', linewidth=1.5))
```

### Bar + Error Bar

```python
ax.bar(x, means, yerr=sems, capsize=2, color=colors, alpha=0.8,
       edgecolor='white', linewidth=0.5, error_kw={'linewidth': 0.8})
ax.axhline(0, color='gray', linewidth=0.5)
```

### Line Plots (Sweep)

```python
ax.plot(x, y, 'o-', color='#e74c3c', linewidth=2, markersize=7)
```

- Mark selected operating point with `axvline`
- Use LaTeX for parameter names: `r"$\alpha$"`, `r"$\gamma$"`

## Annotation Techniques

### Highlight Zone

```python
ax.axvspan(start, end, alpha=0.08, color='red', zorder=0)
ax.text(mid, y_pos, 'Zone Name', ha='center', fontsize=9,
        color='#8B0000', style='italic')
```

### Arrow Annotation for Peak/Key Value

```python
ax.annotate(f'Peak L{idx}\n({val:.2f})', xy=(idx, val),
            xytext=(idx + 4, val + offset),
            fontsize=9, fontweight='bold', color=COLOR,
            arrowprops=dict(arrowstyle='->', color=COLOR, lw=1.2))
```

## Grid & Axes

```python
ax.grid(True, alpha=0.3)           # default
ax.grid(True, alpha=0.3, axis='y') # y-only for categorical x
```

## Layout

- `plt.tight_layout()` always
- `bbox_inches='tight'` on save

Common figsize patterns:
- Single panel: `(6, 4.5)` — standard for independent sub-figures
- Single wide plot: `(12, 4)` or `(10, 6)`
- Side-by-side pair: `(12, 6)` with `1, 2` subplots
- 2x4 grid: `(18, 9)`
- 1x3 sweep panels: `(15, 4.5)`

## Output

```python
# Exploration phase (with title)
ax.set_title('Descriptive Title for Discussion')
plt.savefig('fig_explore_name.png', dpi=150, bbox_inches='tight')

# Final phase (no title, independent PDF per sub-figure)
plt.savefig('fig3a_short_description.pdf', bbox_inches='tight')
plt.savefig('fig3a_short_description.png', dpi=150, bbox_inches='tight')
```

## Multi-Figure Sizing for LaTeX Composition

When figures will be composed via `\subfigure` in LaTeX:
- All sub-figures in one group should use the **same height**
- Width can vary if content demands, but prefer uniform
- Typical single-column width: `figsize=(6, 4.5)`
- Typical two-column width: `figsize=(w, 4)` where w varies

## Legend

- `frameon=True, fancybox=False, edgecolor='#cccccc'` for framed legend
- `frameon=False` for clean look
- Place inside plot if space allows, else use `bbox_to_anchor`
- For multi-subplot figures with shared legend, use `fig.legend()` once
- Keep legend entries concise (1-3 words)
