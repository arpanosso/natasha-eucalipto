
# Análise Eucalipto

## Carregando pacotes exigidos

``` r
library(readxl)
library(tidyverse)
library(janitor)
```

## Entrada de Dados

``` r
# Nutrientes
nutrientes_planta <- read_excel("data/Acumulo de nutrientes - Compartimentos.xlsx") |> 
  clean_names()
head(nutrientes_planta)
```

    ## # A tibble: 6 x 17
    ##   compartimento bloco parcela     n     p     k    n_2   p_2    k_2     ca    mg
    ##   <chr>         <chr>   <dbl> <dbl> <dbl> <dbl>  <dbl> <dbl>  <dbl>  <dbl> <dbl>
    ## 1 FOLHA         C           1     1     1     1 28032. 2206. 13905. 10051. 5165.
    ## 2 FOLHA         C           1     1     0     1 38938. 2550. 17699. 13663. 4862.
    ## 3 FOLHA         C           2     1     1     1 24969. 2073. 13090.  7580. 4013.
    ## 4 FOLHA         C           2     0     1     1 20342. 1406.  9544.  7319. 3566.
    ## 5 FOLHA         C           3     1     1     1 50073. 3256. 18691. 12046. 5589.
    ## 6 FOLHA         C           3     0     1     1 35626. 2440. 17547. 11726. 5322.
    ## # ... with 6 more variables: s <dbl>, b <dbl>, cu <dbl>, fe <dbl>, mn <dbl>,
    ## #   zn <dbl>

``` r
# Matéria Seca
materia_seca <- read_excel("data/Materia seca - volume - altura - DAP.xlsx") |> 
  clean_names()
head(materia_seca)
```

    ## # A tibble: 6 x 12
    ##   bloco parcela     n     p     k ms_folha ms_lenho ms_casca ms_galho altura
    ##   <chr>   <dbl> <dbl> <dbl> <dbl>    <dbl>    <dbl>    <dbl>    <dbl>  <dbl>
    ## 1 C           1     1     1     1    1589.   12183.     730.    1318.   6.95
    ## 2 C           1     1     0     1    1945.   12020.    1319.    1915.   7.7 
    ## 3 C           2     1     1     1    1274.    8316.     770.    1391.   6   
    ## 4 C           2     0     1     1    1072.    4969.     824.     827.   5.9 
    ## 5 C           3     1     1     1    2484.   12996.    1253.    1940.   6.61
    ## 6 C           3     0     1     1    1663.    8397.     752.     934.   6.72
    ## # ... with 2 more variables: dap <dbl>, volume <dbl>

``` r
# solo 
nutrientes_solo <- read_excel("data/teores de nutrientes no solo - 0-20.xlsx")|> 
  clean_names()
head(nutrientes_solo)
```

    ## # A tibble: 6 x 19
    ##   bloco parcela     n     p     k p_resina    mo    ph   k_2    ca    mg  h_al
    ##   <chr>   <dbl> <dbl> <dbl> <dbl>    <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
    ## 1 C           1     1     1     1       38    14   4.3   2.5     5     1    22
    ## 2 C           1     1     0     1        3    14   4     2.8     3     1    22
    ## 3 C           2     1     1     1        6    14   3.6   0.7     1     1    25
    ## 4 C           2     0     1     1        2    12   4     3.1     2     1    20
    ## 5 C           3     1     1     1       11    14   4.1   0.6     2     3    25
    ## 6 C           3     0     1     1        1    13   4     0.6     4     3    22
    ## # ... with 7 more variables: al <dbl>, sb <dbl>, ctc <dbl>, v <dbl>,
    ## #   ca_ctc <dbl>, mg_ctc <dbl>, m <dbl>

### Operação de reunião do banco de dados

``` r
materia_seca_solo <-left_join(x = materia_seca,
          y = nutrientes_solo,
          by=c("parcela","bloco", "n", "p","k"))
```

### Reunião do banco de dados com o arquivo de compartimento

``` r
dados <- right_join(materia_seca_solo, nutrientes_planta,
           by=c("parcela","bloco", "n", "p","k"))
```

``` r
exp_N <- 1:4
exp_P <- c(1,5:7)
exp_K <- c(1,8:10)

dados |> 
  filter(parcela %in% exp_N)
```

    ## # A tibble: 64 x 38
    ##    bloco parcela     n     p     k ms_folha ms_lenho ms_casca ms_galho altura
    ##    <chr>   <dbl> <dbl> <dbl> <dbl>    <dbl>    <dbl>    <dbl>    <dbl>  <dbl>
    ##  1 C           1     1     1     1    1589.   12183.     730.    1318.   6.95
    ##  2 C           1     1     1     1    1589.   12183.     730.    1318.   6.95
    ##  3 C           1     1     1     1    1589.   12183.     730.    1318.   6.95
    ##  4 C           1     1     1     1    1589.   12183.     730.    1318.   6.95
    ##  5 C           1     1     0     1    1945.   12020.    1319.    1915.   7.7 
    ##  6 C           1     1     0     1    1945.   12020.    1319.    1915.   7.7 
    ##  7 C           1     1     0     1    1945.   12020.    1319.    1915.   7.7 
    ##  8 C           1     1     0     1    1945.   12020.    1319.    1915.   7.7 
    ##  9 C           2     1     1     1    1274.    8316.     770.    1391.   6   
    ## 10 C           2     1     1     1    1274.    8316.     770.    1391.   6   
    ## # ... with 54 more rows, and 28 more variables: dap <dbl>, volume <dbl>,
    ## #   p_resina <dbl>, mo <dbl>, ph <dbl>, k_2.x <dbl>, ca.x <dbl>, mg.x <dbl>,
    ## #   h_al <dbl>, al <dbl>, sb <dbl>, ctc <dbl>, v <dbl>, ca_ctc <dbl>,
    ## #   mg_ctc <dbl>, m <dbl>, compartimento <chr>, n_2 <dbl>, p_2 <dbl>,
    ## #   k_2.y <dbl>, ca.y <dbl>, mg.y <dbl>, s <dbl>, b <dbl>, cu <dbl>, fe <dbl>,
    ## #   mn <dbl>, zn <dbl>

``` r
dados |> 
  filter(parcela %in% exp_P) 
```

    ## # A tibble: 64 x 38
    ##    bloco parcela     n     p     k ms_folha ms_lenho ms_casca ms_galho altura
    ##    <chr>   <dbl> <dbl> <dbl> <dbl>    <dbl>    <dbl>    <dbl>    <dbl>  <dbl>
    ##  1 C           1     1     1     1    1589.   12183.     730.    1318.   6.95
    ##  2 C           1     1     1     1    1589.   12183.     730.    1318.   6.95
    ##  3 C           1     1     1     1    1589.   12183.     730.    1318.   6.95
    ##  4 C           1     1     1     1    1589.   12183.     730.    1318.   6.95
    ##  5 C           1     1     0     1    1945.   12020.    1319.    1915.   7.7 
    ##  6 C           1     1     0     1    1945.   12020.    1319.    1915.   7.7 
    ##  7 C           1     1     0     1    1945.   12020.    1319.    1915.   7.7 
    ##  8 C           1     1     0     1    1945.   12020.    1319.    1915.   7.7 
    ##  9 C           5     1     1     1    1576.   11029.    1111.    1237.   7.2 
    ## 10 C           5     1     1     1    1576.   11029.    1111.    1237.   7.2 
    ## # ... with 54 more rows, and 28 more variables: dap <dbl>, volume <dbl>,
    ## #   p_resina <dbl>, mo <dbl>, ph <dbl>, k_2.x <dbl>, ca.x <dbl>, mg.x <dbl>,
    ## #   h_al <dbl>, al <dbl>, sb <dbl>, ctc <dbl>, v <dbl>, ca_ctc <dbl>,
    ## #   mg_ctc <dbl>, m <dbl>, compartimento <chr>, n_2 <dbl>, p_2 <dbl>,
    ## #   k_2.y <dbl>, ca.y <dbl>, mg.y <dbl>, s <dbl>, b <dbl>, cu <dbl>, fe <dbl>,
    ## #   mn <dbl>, zn <dbl>

``` r
dados |> 
  filter(parcela %in% exp_K)
```

    ## # A tibble: 64 x 38
    ##    bloco parcela     n     p     k ms_folha ms_lenho ms_casca ms_galho altura
    ##    <chr>   <dbl> <dbl> <dbl> <dbl>    <dbl>    <dbl>    <dbl>    <dbl>  <dbl>
    ##  1 C           1     1     1     1    1589.   12183.     730.    1318.   6.95
    ##  2 C           1     1     1     1    1589.   12183.     730.    1318.   6.95
    ##  3 C           1     1     1     1    1589.   12183.     730.    1318.   6.95
    ##  4 C           1     1     1     1    1589.   12183.     730.    1318.   6.95
    ##  5 C           1     1     0     1    1945.   12020.    1319.    1915.   7.7 
    ##  6 C           1     1     0     1    1945.   12020.    1319.    1915.   7.7 
    ##  7 C           1     1     0     1    1945.   12020.    1319.    1915.   7.7 
    ##  8 C           1     1     0     1    1945.   12020.    1319.    1915.   7.7 
    ##  9 C           8     1     1     1    1276.    6750.     741.    2139.   5.3 
    ## 10 C           8     1     1     1    1276.    6750.     741.    2139.   5.3 
    ## # ... with 54 more rows, and 28 more variables: dap <dbl>, volume <dbl>,
    ## #   p_resina <dbl>, mo <dbl>, ph <dbl>, k_2.x <dbl>, ca.x <dbl>, mg.x <dbl>,
    ## #   h_al <dbl>, al <dbl>, sb <dbl>, ctc <dbl>, v <dbl>, ca_ctc <dbl>,
    ## #   mg_ctc <dbl>, m <dbl>, compartimento <chr>, n_2 <dbl>, p_2 <dbl>,
    ## #   k_2.y <dbl>, ca.y <dbl>, mg.y <dbl>, s <dbl>, b <dbl>, cu <dbl>, fe <dbl>,
    ## #   mn <dbl>, zn <dbl>
