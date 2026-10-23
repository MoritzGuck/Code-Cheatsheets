# R plotting 

R

data science

visualize

R has powerful builtin plotting capabilities

This cheatsheet uses ggplot2. For more info on the R-built-in graphics-package see: [rdocumentation.org](https://www.rdocumentation.org/packages/graphics/versions/3.6.2)

## dot & line charts

Using dataframes:

``` r
library(ggplot2)
my_plot = ggplot(myDataframe, aes(x = col1, y = col2)) + geom_point()
print(my_plot)
```

The logic behind: You define the aesthetics (*aes*) and then add (*+*) a layer with points (*geom_points*)

Using vectors:

``` r
ggplot(data = NULL, aes(x = c(1,2,3,4,5), y = c(25,16,9,4,1))) + geom_point()
```

| Styles of plot |  |
|----|----|
| points | `... + geom_point()` |
| line | `... + geom_line()` |
| color | `aes(x = ..., y = ..., color = cat_col)...` different colors for different categories |

| Axis           |                              |
|----------------|------------------------------|
| log scale on x | `... + scale_x_log10()`      |
| log scale on y | `... + scale_y_log10()`      |
| include 0      | `... + expand_limits(y = 0)` |

## Column charts

plot the values as columns:

``` r
ggplot(df, aes(x = factor(cat_col), y = value_col)) + geom_col()
```

plot the counts as columns:

``` r
ggplot(df, aes(x = factor(value_col))) + geom_bar()
```

### Histogram

Plot distribution of values:

``` r
ggplot(df, aes(x = value_col)) + geom_histogram(binwidth=3)
```

## Box Plot

Compare different distributions:

``` r
ggplot(df, aes(x = cat_col, y = value_col)) +  geom_boxplot()
```

## Plot functions

To smoothly plot a curved function:

``` r
my_func <- function(x) sqrt(x) # - x**2
x_val_df = data.frame(x_val = c(0, 10))
ggplot(x_val_df, aes(x = x_val)) + stat_function(fun = my_func, geom = "line")
```

More info in this e-book: [r-graphics.org/](https://r-graphics.org/)
