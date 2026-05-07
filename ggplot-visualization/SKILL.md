---
name: ggplot-visualization
description: ggplot2 visualization style rules. Apply whenever writing or modifying any ggplot2 plotting code in R.
user-invocable: false
---

# ggplot2 Visualization Style

Always follow these rules when writing any ggplot2 code:

## Theme

Use `theme_minimal(base_size=14)` or `theme_classic(base_size=14)`. Never use `theme_gray()`.

## Colors

### Categorical data
Use muted RColorBrewer palettes: `Set2`, `Set3`, `Pastel1`, `Pastel2`, `Dark2`. Never use default ggplot2 colors.

### Continuous data
Use `viridis`, `magma`, `plasma`, `inferno`, or `cividis` (colorblind-friendly).

### Diverging data
Use `RdBu`, `RdYlBu`, `Spectral`, `PuOr`, or `BrBG`, centered at a meaningful value.

## Visual Parameters

- Points: `size >= 2.5`, `alpha = 0.8`
- Lines: `linewidth >= 0.8`

## Export

Always save with `width=5, height=4, dpi=800`.

```r
ggsave("plot.png", p, width=5, height=4, dpi=800)
```

## Example

```r
library(ggplot2)

p = ggplot(data, aes(x=x_var, y=y_var, color=group)) +
  geom_point(size=2.5, alpha=0.8) +
  geom_line(linewidth=0.8) +
  scale_color_brewer(palette="Set2") +
  theme_minimal(base_size=14) +
  labs(x="X Label", y="Y Label", title="Title") +
  theme(plot.margin=margin(10,10,10,10))

ggsave("plot.png", p, width=5, height=4, dpi=800)
```

## Automatic Corrections

When editing existing ggplot code, always apply these fixes:
- `theme_gray()` → `theme_minimal(base_size=14)`
- `<-` → `=`
- Missing color scale → add appropriate `scale_color_*` or `scale_fill_*`
- Missing/suboptimal dimensions → set `width=5, height=4, dpi=800`
- Raw variable names in labels → humanize (e.g. `log_fc` → `"Log Fold Change"`)
