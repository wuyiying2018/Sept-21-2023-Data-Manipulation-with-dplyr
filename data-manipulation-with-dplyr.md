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

## `filter`

get rid of rows using `filter()`

comparison operators (`>`, `>=`, `<`, `<=`, `==`, and `!=`).

``` r
filter(litters_data, group =="Con7")
```

    ## # A tibble: 7 × 8
    ##   group litter_number   gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>                <dbl>       <dbl>       <int>           <int>
    ## 1 Con7  #85                   19.7        34.7          20               3
    ## 2 Con7  #1/2/95/2             27          42            19               8
    ## 3 Con7  #5/5/3/83/3-3         26          41.4          19               6
    ## 4 Con7  #5/4/2/95/2           28.5        44.1          19               5
    ## 5 Con7  #4/2/95/3-3           NA          NA            20               6
    ## 6 Con7  #2/2/95/3-2           NA          NA            20               6
    ## 7 Con7  #1/5/3/83/3-3/2       NA          NA            20               9
    ## # ℹ 2 more variables: pups_dead_birth <int>, pups_survive <int>

``` r
filter(litters_data, group =="Mod8")
```

    ## # A tibble: 7 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <int>           <int>
    ## 1 Mod8  #97                 24.5        42.8          20               8
    ## 2 Mod8  #5/93               NA          41.1          20              11
    ## 3 Mod8  #5/93/2             NA          NA            19               8
    ## 4 Mod8  #7/82-3-2           26.9        43.2          20               7
    ## 5 Mod8  #7/110/3-2          27.5        46            19               8
    ## 6 Mod8  #2/95/2             28.5        44.5          20               9
    ## 7 Mod8  #82/4               33.4        52.7          20               8
    ## # ℹ 2 more variables: pups_dead_birth <int>, pups_survive <int>

``` r
filter(litters_data, group !="Con7")
```

    ## # A tibble: 42 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <int>           <int>
    ## 1 Con8  #3/83/3-3           NA            NA          20               9
    ## 2 Con8  #2/95/3             NA            NA          20               8
    ## 3 Con8  #3/5/2/2/95         28.5          NA          20               8
    ## # ℹ 39 more rows
    ## # ℹ 2 more variables: pups_dead_birth <int>, pups_survive <int>

``` r
filter(litters_data, gd0_weight>20)
```

    ## # A tibble: 30 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <int>           <int>
    ## 1 Con7  #1/2/95/2           27          42            19               8
    ## 2 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ## 3 Con7  #5/4/2/95/2         28.5        44.1          19               5
    ## # ℹ 27 more rows
    ## # ℹ 2 more variables: pups_dead_birth <int>, pups_survive <int>

``` r
filter(litters_data, gd0_weight<=20)
```

    ## # A tibble: 4 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <int>           <int>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Mod7  #59                 17          33.4          19               8
    ## 3 Mod7  #62                 19.5        35.9          19               7
    ## 4 Low8  #100                20          39.2          20               8
    ## # ℹ 2 more variables: pups_dead_birth <int>, pups_survive <int>

``` r
filter(litters_data, group=="Con7"|group=="Con8")
```

    ## # A tibble: 15 × 8
    ##    group litter_number   gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##    <chr> <chr>                <dbl>       <dbl>       <int>           <int>
    ##  1 Con7  #85                   19.7        34.7          20               3
    ##  2 Con7  #1/2/95/2             27          42            19               8
    ##  3 Con7  #5/5/3/83/3-3         26          41.4          19               6
    ##  4 Con7  #5/4/2/95/2           28.5        44.1          19               5
    ##  5 Con7  #4/2/95/3-3           NA          NA            20               6
    ##  6 Con7  #2/2/95/3-2           NA          NA            20               6
    ##  7 Con7  #1/5/3/83/3-3/2       NA          NA            20               9
    ##  8 Con8  #3/83/3-3             NA          NA            20               9
    ##  9 Con8  #2/95/3               NA          NA            20               8
    ## 10 Con8  #3/5/2/2/95           28.5        NA            20               8
    ## 11 Con8  #5/4/3/83/3           28          NA            19               9
    ## 12 Con8  #1/6/2/2/95-2         NA          NA            20               7
    ## 13 Con8  #3/5/3/83/3-3-2       NA          NA            20               8
    ## 14 Con8  #2/2/95/2             NA          NA            19               5
    ## 15 Con8  #3/6/2/2/95-3         NA          NA            20               7
    ## # ℹ 2 more variables: pups_dead_birth <int>, pups_survive <int>

``` r
filter(litters_data, group %in% c("Con7","Con8"))
```

    ## # A tibble: 15 × 8
    ##    group litter_number   gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##    <chr> <chr>                <dbl>       <dbl>       <int>           <int>
    ##  1 Con7  #85                   19.7        34.7          20               3
    ##  2 Con7  #1/2/95/2             27          42            19               8
    ##  3 Con7  #5/5/3/83/3-3         26          41.4          19               6
    ##  4 Con7  #5/4/2/95/2           28.5        44.1          19               5
    ##  5 Con7  #4/2/95/3-3           NA          NA            20               6
    ##  6 Con7  #2/2/95/3-2           NA          NA            20               6
    ##  7 Con7  #1/5/3/83/3-3/2       NA          NA            20               9
    ##  8 Con8  #3/83/3-3             NA          NA            20               9
    ##  9 Con8  #2/95/3               NA          NA            20               8
    ## 10 Con8  #3/5/2/2/95           28.5        NA            20               8
    ## 11 Con8  #5/4/3/83/3           28          NA            19               9
    ## 12 Con8  #1/6/2/2/95-2         NA          NA            20               7
    ## 13 Con8  #3/5/3/83/3-3-2       NA          NA            20               8
    ## 14 Con8  #2/2/95/2             NA          NA            19               5
    ## 15 Con8  #3/6/2/2/95-3         NA          NA            20               7
    ## # ℹ 2 more variables: pups_dead_birth <int>, pups_survive <int>

``` r
filter(litters_data, !(group=="Con7"))
```

    ## # A tibble: 42 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <int>           <int>
    ## 1 Con8  #3/83/3-3           NA            NA          20               9
    ## 2 Con8  #2/95/3             NA            NA          20               8
    ## 3 Con8  #3/5/2/2/95         28.5          NA          20               8
    ## # ℹ 39 more rows
    ## # ℹ 2 more variables: pups_dead_birth <int>, pups_survive <int>

``` r
filter(litters_data, group %in% c("Con7","Con8"),gd0_weight>20)
```

    ## # A tibble: 5 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <int>           <int>
    ## 1 Con7  #1/2/95/2           27          42            19               8
    ## 2 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ## 3 Con7  #5/4/2/95/2         28.5        44.1          19               5
    ## 4 Con8  #3/5/2/2/95         28.5        NA            20               8
    ## 5 Con8  #5/4/3/83/3         28          NA            19               9
    ## # ℹ 2 more variables: pups_dead_birth <int>, pups_survive <int>

what about missing rows? drop NAs in the whole dataset

``` r
drop_na(litters_data)
```

    ## # A tibble: 31 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <int>           <int>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Con7  #1/2/95/2           27          42            19               8
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ## # ℹ 28 more rows
    ## # ℹ 2 more variables: pups_dead_birth <int>, pups_survive <int>

drop NAs in specific col

``` r
drop_na(litters_data,gd0_weight)
```

    ## # A tibble: 34 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <int>           <int>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Con7  #1/2/95/2           27          42            19               8
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ## # ℹ 31 more rows
    ## # ℹ 2 more variables: pups_dead_birth <int>, pups_survive <int>

## `mutate`

this is used to add or change variables

`group=str_to_lower(variable1)` is going to convert the data in
`variable1` into lowercases

``` r
mutate(litters_data,
  wt_gain = gd18_weight - gd0_weight,
  group = str_to_lower(group)
)
```

    ## # A tibble: 49 × 9
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <int>           <int>
    ## 1 con7  #85                 19.7        34.7          20               3
    ## 2 con7  #1/2/95/2           27          42            19               8
    ## 3 con7  #5/5/3/83/3-3       26          41.4          19               6
    ## # ℹ 46 more rows
    ## # ℹ 3 more variables: pups_dead_birth <int>, pups_survive <int>, wt_gain <dbl>

## `arrange`

arrange in ascending order by gd0_weight

``` r
arrange(litters_data,gd0_weight)
```

    ## # A tibble: 49 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <int>           <int>
    ## 1 Mod7  #59                 17          33.4          19               8
    ## 2 Mod7  #62                 19.5        35.9          19               7
    ## 3 Con7  #85                 19.7        34.7          20               3
    ## # ℹ 46 more rows
    ## # ℹ 2 more variables: pups_dead_birth <int>, pups_survive <int>

arrange in alphabetical order by group then by gd0_weight

``` r
arrange(litters_data,group,gd0_weight)
```

    ## # A tibble: 49 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <int>           <int>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ## 3 Con7  #1/2/95/2           27          42            19               8
    ## # ℹ 46 more rows
    ## # ℹ 2 more variables: pups_dead_birth <int>, pups_survive <int>

## pipes

Note that by default RStudio will insert the “traditional” pipe `%>%`,
and you can update to the native in pipe through Global Preferences \>
Code \> Use Native Pipe Operator.

``` r
litters_data = 
  read_csv("./data/FAS_litters.csv", col_types = "ccddiiii") |> 
  janitor::clean_names() |> 
  select(-pups_survive) |> 
  mutate(
    wt_gain = gd18_weight - gd0_weight,
    group = str_to_lower(group)) |> 
  drop_na(wt_gain)
```
