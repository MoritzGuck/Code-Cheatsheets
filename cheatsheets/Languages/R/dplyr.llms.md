# Dplyr 

R

data science

Dplyr is a data transformation library in the collection Tidyverse.

## Data Wrangling with dplyr

Load dplyr  
\`\`\`R library(dplyr)

&nbsp;

    The dplyr package uses the pipe-command to pass the result of one transformation to the next:
    ```R
    dataset %>%
        filter(column1 == "Value") %>%
        arrange(column2)

Filter  
\`\`\`R filter(column1 == “Value”)

&nbsp;

    Order / Sort
    : ```R
    arrange(col1) %>% # ascending
        arrange(desc(col2)) # descending

Mutate / Change / Add columns  
\`\`\`R mutate(resultCol = col / 1000)

&nbsp;

    ### Aggregation

    Summarize / aggregate
    : ```R
    dataset %>%
        summarize(medianCol1 = median(col1))

| Aggregation functions |                        |
|-----------------------|------------------------|
| sum                   | `sum()`                |
| mean                  | `mean()`               |
| median                | `median()`             |
| minimum / maximum     | `min()` / `max()`      |
| first / last position | `first()` / `last()`   |
| counts                | `n()` / `n_distinct()` |

### Group-by

``` r
dataset %>%
    group_by(year_col, continent_col) %>% 
    summarize(mean(gpd_col))
```

aggregates only for the groups defined before

### Combine tables

Stack horizontally (new column)

``` r
dataset %>%
    bind_cols(new_dataset)
```

Join tables  
``` r
dataset_1 %>% left_join(dataset_2, by = join_by(col1 == col2), relationship = "one-to-one") 
... %>% right_join(...)
... %>% inner_join(...) # only keeps matching samples
... %>% full_join(...) # keeps all samples in both datasets
```

relationships checks can be: `"one-to-one"`, `"one-to-many"`, `"many-to-one"` and `"many-to-many"` (does not make a check)
