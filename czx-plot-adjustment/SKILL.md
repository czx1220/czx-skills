---
name: czx-plot-adjustment
description: >
  Unify and polish a batch of existing figures to publication-ready quality.
  Only triggered by explicit user invocation. Do NOT auto-trigger.
  Covers: style unification across all figures (color, font size, line width, figsize, legend, etc.),
  individual figure adjustments (chart type change, data presentation, annotations),
  and ensuring all figures in a paper look like they belong to the same project.
  Every decision defers to the author. Agent proposes options, author confirms one by one.
---

# Plot Adjustment

## Language Rule

**All communication with the author must be in Chinese (中文).** This includes: status reports, questions, options, analysis, explanations, and all non-code output.

## Activation Notice

When this skill is triggered, immediately display:

```
========================================
  [Skill Activated] Plot Adjustment
  Phase 1 → 4 workflow engaged.
  All decisions defer to the author.
  Currently at: Phase 1 — Inventory & Status
========================================
```

Update the "Currently at" line at each phase transition.

## Core Principle

**All figures in one paper must look like they belong together. Every adjustment is confirmed by the author one by one.**

Rules:
- **Style consistency is the top priority.** Color palette, font size, line width, marker style, legend style, figsize — all must be unified across the entire paper.
- **One figure at a time.** Present the current state, propose changes, get author confirmation, then move to the next.
- **Author decides.** Agent proposes options (multiple when appropriate), author picks. Never apply changes without confirmation.
- **Deep involvement.** Agent actively participates in every decision — suggesting improvements, questioning choices, proposing alternatives. But always defer to the author's final judgment.
- **Academic and professional.** Every figure must look like it belongs in a top-venue paper — clean, informative, not cluttered.

## Code Organization

**Every figure must have its own dedicated plotting function in a shared plotting directory.**

- All plotting scripts live in one folder (e.g., `scripts/plot/` or `plots/`)
- Each figure has its own file or function: e.g., `plot_fig1_pca.py`, `plot_fig2_attention.py`
- **No one-off inline code.** Every figure is produced by calling its function — reproducible and modifiable.
- **To adjust a figure = modify its plotting function.** No ad-hoc code, no copy-paste-tweak.
- Style parameters should be centralized (e.g., a shared `style_config.py` or a dict at the top of each file) so global style changes propagate easily.

## Phase 1: Inventory & Status

**Goal: understand what figures exist and their current state.**

- List all existing figures (file paths, what each shows)
- Identify current style differences across figures:
  - Color palettes used
  - Font sizes (axis labels, tick labels, legends, annotations)
  - Figure sizes
  - Line widths, marker sizes
  - Legend placement and style
  - Axis style (spines, grid, ticks)
- Report to the author: "Here are N figures. The following style inconsistencies exist: [list]. I recommend we align on a unified style first."

## Phase 2: Style Alignment

**Goal: establish a unified style specification for all figures in the paper.**

Discuss with the author and decide on:

**rcParams 全局设置（confirmed）：**
```python
plt.rcParams.update({
    'font.family': 'sans-serif',
    'font.sans-serif': ['Arial', 'Helvetica', 'DejaVu Sans'],
    'font.size': 10,
    'axes.labelsize': 11,
    'axes.titlesize': 12,
    'xtick.labelsize': 9,
    'ytick.labelsize': 9,
    'legend.fontsize': 9,
    'figure.dpi': 150,
    'savefig.dpi': 150,
    'savefig.bbox': 'tight',
    'axes.spines.top': False,
    'axes.spines.right': False,
})
```

**Layout（confirmed）：**
- 1×2 子图: `figsize=(9, 4)`
- 始终使用 `plt.tight_layout()`
- 无 suptitle（终稿用 LaTeX caption）

**Line & marker（confirmed）：**
- markersize: 6
- linewidth: 默认（不显式设置，即 ~1.5）
- marker 风格: `'o-'`（圆形）、`'s-'`（方形）、`'D'`（菱形）

**Baseline 参考线（confirmed）：**
- `linestyle='--'`（虚线）
- `color='gray', linewidth=1, alpha=0.7`

**Legend（confirmed）：**
- `fontsize=8`
- `handlelength=3.0`
- `handletextpad=0.4`
- `borderpad=0.3`
- `markerscale=0.7`
- `frameon=True, fancybox=False, edgecolor='#cccccc'`
- 位置根据数据分布选择（不遮挡数据点为准）
- 图注应紧凑，不抢数据视觉注意力
- 标签用全称（如 "JS divergence", "Wasserstein-1"），不用缩写符号

**Axes & grid（confirmed）：**
- 去掉 top/right spines
- `ax.grid(True, alpha=0.3)`
- x 轴线性刻度（刻度间距符合真实数值比例），不用 log 刻度（除非数据跨多个数量级）
- 用 `set_xlim` 控制 x 轴范围，留适当边距

**Color palette：**
- Two-group: `'#1f77b4'`（蓝）/ `'#d62728'`（红）
- Three-group: 加 `'#ff7f0e'`（橙）或 `'#7f7f7f'`（灰）
- Multi-group: 从 matplotlib 默认色环取，确保色盲友好

**Output format（confirmed）：**
- 同时输出 PDF（论文用）+ PNG（预览用）
- DPI: 150
- `bbox_inches='tight'`

Once agreed, this becomes the **style spec** — applied to all figures.

## Phase 3: Per-Figure Adjustment

**Goal: adjust each figure individually to match the style spec, and address content/presentation changes.**

For each figure, one at a time:

1. **Show current state**: describe what the figure looks like now
2. **Identify issues**: what deviates from the style spec, what could be improved in terms of content/presentation
3. **Propose changes** — may include:
   - Style alignment (apply the unified spec)
   - Chart type change (e.g., bar → violin, line → scatter)
   - Data presentation change (e.g., show error bands, add annotations, highlight zones)
   - Layout change (e.g., adjust aspect ratio, rearrange sub-figures)
   - Annotation additions (axvspan highlights, arrow annotations, value labels)
   - Remove titles (final figures use LaTeX captions, not matplotlib titles)
4. **Get author confirmation** before applying
5. **Apply changes** and show the result
6. **Author confirms** the result is satisfactory, or requests further adjustment

Repeat until the author is satisfied with each figure.

## Phase 4: Final Unified Review

**Goal: review all figures together as a collection.**

- Display all figures side by side (or in sequence) for the author to see the full set
- Check: do they all look cohesive? Same color language, same font sizes, same visual weight?
- Check: are there any remaining inconsistencies the author wants to address?
- Check: naming convention is clear (e.g., `fig3a_pca_layer_separation.pdf`)

Get final author confirmation that the full set is ready for the paper.

## Style Reference

See [czx-mechanism-analysis-plotting style_guide.md](../czx-mechanism-analysis-plotting/references/style_guide.md) for detailed style specifications including:
- Color palettes (two-group, three-group, sweep curves)
- Chart type recipes (scatter, line+CI, violin+box+strip, KDE, heatmap, bar+error bar)
- Annotation techniques
- Figsize patterns

Use this as the starting reference — author may customize for their specific paper.

## Completion Criteria

This skill is DONE when:
- All figures share a unified style
- Each figure has been individually reviewed and confirmed by the author
- The full set looks cohesive and publication-ready
- All figures are exported as independent PDFs with clear naming
