# TidierPlots.jl

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://github.com/TidierOrg/Tidier.jl/blob/main/LICENSE)

<img src="/assets/logo.png" align="right" style="padding-left:10px;" width="150"/>

## What is TidierPlots.jl?
TidierPlots.jl is a 100% Julia implementation of the R package ggplot in Julia. Powered by the AlgebraOfGraphics.jl, Makie.jl, and Julia’s extensive meta-programming capabilities, TidierPlots.jl is an R user’s love
letter to data visualization in Julia.

`TidierPlots.jl` has three goals, which differentiate it from other plotting packages in Julia:

1.  **Stick as closely to tidyverse syntax and behaviour as possible:** Whereas other
    meta-packages introduce Julia-centric idioms for working with
    plots, this package’s goal is to reimplement ggplot
    in Julia. This means that `TidierPlots.jl` uses *tidy expressions* as opposed
    to idiomatic Julia expressions. An example of a tidy expression is
    `a = mean(b)`.

2.  **Stay as compatible as possible with AlgebraOfGraphics.jl** This package is meant
    to be a thin wrapper around AoG's syntax to help introduce R users to plotting in 
    Julia. `geom`s declared in TidierPlots.jl can be easily converted to AoG `Layer`
    objects using `geom_to_layer()`, and `ggplot`s can be converted to AoG `Layers` objects
    using `ggplot_to_layers()`.

3. **To Extend ggplot using julia-specific features where appropriate** as long as this does
    not confict with the first two goals. The package aims to behave exactly like ggplot
    unless told otherwise. Additional options and parameters that are not present in ggplot 
    may be added, but options that are present in R's ggplot should behave the way they do in R. 

## Installation

For the development version:

```julia
using Pkg
Pkg.add(url="https://github.com/TidierOrg/TidierPlots.jl")
```

## What functions does TidierPlots.jl support?

To support R-style programming, TidierPlots.jl is implemented using macros.

TidierPlots.jl currently supports the top-level macro `@ggplot()`, plus:

Geoms:

- `@geom_point()`
- `@geom_smooth()`
- `@geom_bar()`

Makie Themes (Note that these are **not macros**):

- `theme_ggplot2()` (the default)
- `theme_dark()`
- `theme_black()`
- `theme_white()`
- `theme_minimal()`

Additional Elements:

- `@scale_[x|y]_[continuous|log[ |2|10]|logit|pseudolog10|sqrt|reverse]`
- `@labs()`
- `@lims()`

## Example

Let's make some plots using the Palmer Penguins data from `PalmerPenguins.jl`:

```julia
using TidierPlots
using DataFrames
using PalmerPenguins

penguins = dropmissing(DataFrame(PalmerPenguins.load()))

@ggplot(data = penguins) + 
    @geom_bar(aes(x = species)) +
    @labs(x = "Species")
```
![](assets/example_col.png)


```julia
@ggplot(data = penguins) +
           @geom_bar(aes(x = species, color = island), position = "dodge") +
           @labs(x = "Species", y = "Count", color = "Island of Origin")
```
![](assets/example_col_color.png)

```julia
@ggplot(data = penguins) + 
    @geom_bar(aes(x = species, color = island), position = "stack") +
    @labs(x = "Species")
```
![](assets/example_col_stack.png)


```julia
@ggplot(penguins, aes(x = bill_length_mm, y = bill_depth_mm, color = species)) + 
    @geom_point() + 
    @geom_smooth(method = "lm") +
    @labs(x = "Bill Length (mm)", y = "Bill Width (mm)", 
        title = "Bill Length vs. Bill Width", 
        subtitle = "Using geom_point and geom_smooth") +
    theme_dark()
```

![](assets/example_point_smooth.png)

```julia
 @ggplot(penguins, aes(x = bill_length_mm, y = bill_depth_mm, color = species)) + 
    @geom_point(shape = diamond, 
                size = 20, 
                stroke = 1, 
                strokecolour = "black",
                alpha = 0.8) +
    @labs(x = "Bill Length (mm)", y = "Bill Width (mm)") +
    @lims(x = c(40, 60), y = c(15, 20)) +
    theme_minimal()
```
![](assets/geom_point_customize.png)

```julia
@ggplot(penguins, aes(x = bill_length_mm, y = bill_depth_mm, color = species)) + 
    @geom_point() + 
    @geom_smooth(method = "lm") +
    @scale_x_log10(name = "Log10 Scaled Bill Length") + 
    @scale_y_reverse(name = "Reversed Bill Width")
```
![](assets/scales.png)