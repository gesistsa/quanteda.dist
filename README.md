
<!-- README.md is generated from README.Rmd. Please edit that file -->

# quanteda.dist

<!-- badges: start -->

<!-- badges: end -->

The goal of quanteda.dist is to hack a layer of distance vectors to the
`tokens` object of `quanteda`.

## Installation

You can install the development version of quanteda.dist like so:

``` r
remotes::install_github("gesistsa/quanteda.dist")
```

## Example

This is a basic example which shows you how to solve a common problem:

``` r
library(quanteda)
#> Package version: 3.3.1
#> Unicode version: 14.0
#> ICU version: 70.1
#> Parallel computing: 8 of 8 threads used.
#> See https://quanteda.io for tutorials and examples.
library(quanteda.dist)

testdata <- c("Turkish President Tayyip Erdogan, in his strongest comments yet on the Gaza conflict, said on Wednesday the Palestinian militant group Hamas was not a terrorist organisation but a liberation group fighting to protect Palestinian lands.",
              "EU policymakers proposed the new agency in 2021 to stop financial firms from aiding criminals and terrorists. Brussels has so far relied on national regulators with no EU authority to stop money laundering and terrorist financing running into billions of euros.")
```

``` r
res <- testdata %>% tokens() %>% tokens_tolower() %>%
    tokens_dist(targets = "turkish")
res
#> Tokens consisting of 2 documents.
#> text1 :
#>  [1] "turkish"   "president" "tayyip"    "erdogan"   ","         "in"       
#>  [7] "his"       "strongest" "comments"  "yet"       "on"        "the"      
#> [ ... and 26 more ]
#> 
#> text2 :
#>  [1] "eu"           "policymakers" "proposed"     "the"          "new"         
#>  [6] "agency"       "in"           "2021"         "to"           "stop"        
#> [11] "financial"    "firms"       
#> [ ... and 31 more ]
#> 
#> With distance vector.
#> targets:  turkish
```

You can access the distance vectors by

``` r
meta(res, "dist")
#> $text1
#>  [1]  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25
#> [26] 26 27 28 29 30 31 32 33 34 35 36 37 38
#> 
#> $text2
#>  [1] 44 44 44 44 44 44 44 44 44 44 44 44 44 44 44 44 44 44 44 44 44 44 44 44 44
#> [26] 44 44 44 44 44 44 44 44 44 44 44 44 44 44 44 44 44 44
```

A chunky example to calculate the total distance-weighted frequency of
"terror\*" words.

``` r
terror_dict <- dictionary(list(TERROR = c("terror*")))

sum(as.numeric(tokens_lookup(res, terror_dict, exclusive = FALSE)[[1]] == "TERROR") * (1 / meta(res, "dist")[[1]]))
#> [1] 0.03703704

sum(as.numeric(tokens_lookup(res, terror_dict, exclusive = FALSE)[[2]] == "TERROR") * (1 / meta(res, "dist")[[2]]))
#> [1] 0.04545455
```

How about changing the target to Hamas?

``` r
res2 <- res %>% tokens_dist(targets = "hamas")
res2
#> Tokens consisting of 2 documents.
#> text1 :
#>  [1] "turkish"   "president" "tayyip"    "erdogan"   ","         "in"       
#>  [7] "his"       "strongest" "comments"  "yet"       "on"        "the"      
#> [ ... and 26 more ]
#> 
#> text2 :
#>  [1] "eu"           "policymakers" "proposed"     "the"          "new"         
#>  [6] "agency"       "in"           "2021"         "to"           "stop"        
#> [11] "financial"    "firms"       
#> [ ... and 31 more ]
#> 
#> With distance vector.
#> targets:  hamas
```

``` r
sum(as.numeric(tokens_lookup(res2, terror_dict, exclusive = FALSE)[[1]] == "TERROR") * (1 / meta(res2, "dist")[[1]]))
#> [1] 0.2

sum(as.numeric(tokens_lookup(res2, terror_dict, exclusive = FALSE)[[2]] == "TERROR") * (1 / meta(res2, "dist")[[2]]))
#> [1] 0.04545455
```
