Data Manipulation with dplyr
================
Yiying Wu
2023-09-21

## tidyverse

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.2     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.2     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.2     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.1     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

## import data

Import FAS litters and pups.

``` r
options(tibble.print_min = 3)

litters_data = read_csv("./data/FAS_litters.csv",
  col_types = "ccddiiii")
litters_data = janitor::clean_names(litters_data)

pups_data = read_csv("./data/FAS_pups.csv",
  col_types = "ciiiii")
pups_data = janitor::clean_names(pups_data)
```

## `select`

`select` is used to select columns

``` r
select(litters_data, group, litter_number, gd0_weight, pups_born_alive)
```

    ## # A tibble: 49 × 4
    ##   group litter_number gd0_weight pups_born_alive
    ##   <chr> <chr>              <dbl>           <int>
    ## 1 Con7  #85                 19.7               3
    ## 2 Con7  #1/2/95/2           27                 8
    ## 3 Con7  #5/5/3/83/3-3       26                 6
    ## # ℹ 46 more rows

`starts_with` select the cols starts with what you want:

``` r
select(litters_data, group, starts_with("pups"))
```

    ## # A tibble: 49 × 4
    ##   group pups_born_alive pups_dead_birth pups_survive
    ##   <chr>           <int>           <int>        <int>
    ## 1 Con7                3               4            3
    ## 2 Con7                8               0            7
    ## 3 Con7                6               0            5
    ## # ℹ 46 more rows

specify the specify a range of columns to keep:

``` r
select(litters_data, group:gd_of_birth)
```

    ## # A tibble: 49 × 5
    ##   group litter_number gd0_weight gd18_weight gd_of_birth
    ##   <chr> <chr>              <dbl>       <dbl>       <int>
    ## 1 Con7  #85                 19.7        34.7          20
    ## 2 Con7  #1/2/95/2           27          42            19
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19
    ## # ℹ 46 more rows

You can also specify columns you’d like to remove:

``` r
select(litters_data, -pups_survive)
```

    ## # A tibble: 49 × 7
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <int>           <int>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Con7  #1/2/95/2           27          42            19               8
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ## # ℹ 46 more rows
    ## # ℹ 1 more variable: pups_dead_birth <int>

You can rename variables as part of this process:

``` r
select(litters_data, GROUP = group, LiTtEr_NuMbEr = litter_number)
```

    ## # A tibble: 49 × 2
    ##   GROUP LiTtEr_NuMbEr
    ##   <chr> <chr>        
    ## 1 Con7  #85          
    ## 2 Con7  #1/2/95/2    
    ## 3 Con7  #5/5/3/83/3-3
    ## # ℹ 46 more rows

`everything()`keep everything `select` can select the cols in the order
you write them

``` r
select(litters_data, group, gd0_weight, everything())
```

    ## # A tibble: 49 × 8
    ##   group gd0_weight litter_number gd18_weight gd_of_birth pups_born_alive
    ##   <chr>      <dbl> <chr>               <dbl>       <int>           <int>
    ## 1 Con7        19.7 #85                  34.7          20               3
    ## 2 Con7        27   #1/2/95/2            42            19               8
    ## 3 Con7        26   #5/5/3/83/3-3        41.4          19               6
    ## # ℹ 46 more rows
    ## # ℹ 2 more variables: pups_dead_birth <int>, pups_survive <int>

`rename` function: change col names

``` r
rename(litters_data,litter_id=litter_number)
```

    ## # A tibble: 49 × 8
    ##   group litter_id     gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <int>           <int>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Con7  #1/2/95/2           27          42            19               8
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ## # ℹ 46 more rows
    ## # ℹ 2 more variables: pups_dead_birth <int>, pups_survive <int>

`relocate` put the cols into the order you want

``` r
relocate(litters_data,litter_id=litter_number)
```

    ## # A tibble: 49 × 8
    ##   litter_id     group gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr>         <chr>      <dbl>       <dbl>       <int>           <int>
    ## 1 #85           Con7        19.7        34.7          20               3
    ## 2 #1/2/95/2     Con7        27          42            19               8
    ## 3 #5/5/3/83/3-3 Con7        26          41.4          19               6
    ## # ℹ 46 more rows
    ## # ℹ 2 more variables: pups_dead_birth <int>, pups_survive <int>

`select` vs `pull`

``` r
select(litters_data,group)
```

    ## # A tibble: 49 × 1
    ##   group
    ##   <chr>
    ## 1 Con7 
    ## 2 Con7 
    ## 3 Con7 
    ## # ℹ 46 more rows

``` r
pull(litters_data,group)
```

    ##  [1] "Con7" "Con7" "Con7" "Con7" "Con7" "Con7" "Con7" "Con8" "Con8" "Con8"
    ## [11] "Con8" "Con8" "Con8" "Con8" "Con8" "Mod7" "Mod7" "Mod7" "Mod7" "Mod7"
    ## [21] "Mod7" "Mod7" "Mod7" "Mod7" "Mod7" "Mod7" "Mod7" "Low7" "Low7" "Low7"
    ## [31] "Low7" "Low7" "Low7" "Low7" "Low7" "Mod8" "Mod8" "Mod8" "Mod8" "Mod8"
    ## [41] "Mod8" "Mod8" "Low8" "Low8" "Low8" "Low8" "Low8" "Low8" "Low8"
