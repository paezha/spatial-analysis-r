# Activity 12: Area Data IV

*NOTE*: The source files for this book are available with companion package [{isdas}](https://paezha.github.io/isdas/). The source files are in Rmarkdown format and packed as templates. These files allow you execute code within the notebook, so that you can work interactively with the notes. 

## Practice questions

Answer the following questions:

1. How are row-standardized and binary spatial weights interpreted?
2. What is the reason for using a Bonferroni correction for multiple tests?
3. What types of spatial patterns can the local version of Moran's I detect?
4. What types of spatial patterns can the $G_i(d)$ statistic detect?
5. What is the utility of detecting hot and cold spatial spots?

## Learning objectives

In this activity, you will:

1. Calculate Moran's I coefficient of autocorrelation for area data.
2. Create Moran's scatterplots.
2. Examine the results of the tests/scatterplots for further insights.
3. Think about ways to decide whether a landscape is random when working with area data.

## Suggested reading

O'Sullivan D and Unwin D (2010) Geographic Information Analysis, 2nd Edition, Chapter 7. John Wiley & Sons: New Jersey. 

## Preliminaries

Before you begin your work restart the `R` session, or at least clear the working space to make sure that you do not have extraneous items there when you begin your work. The command in `R` to clear the workspace is `rm` (for "remove"), followed by a list of items to be removed. To clear the workspace from _all_ objects, do the following:

``` r
rm(list = ls())
```

Note that `ls()` lists all objects currently on the workspace.

Load the libraries you will use in this activity. 

In addition to `tidyverse`, you will need `sf`, a package that implements simple features in R (you can learn about `sf` [here](https://cran.r-project.org/web/packages/sf/vignettes/sf1.html)) and `spdep`, a package that implements several spatial statistical methods (you can learn more about it [here](https://cran.r-project.org/web/packages/spdep/index.html)):

``` r
library(isdas)
library(sf)
library(spdep)
library(tidyverse)
```

Begin by loading the data that you will use in this activity:

``` r
data(Hamilton_CT)
```

This is a `sf` object with census tracts and selected demographic variables for the Hamilton CMA in Canada.
You can obtain new (calculated) variables as follows. For instance, to obtain the proportion of residents who are between 20 and 34 years old, and between 35 and 49:

``` r
Hamilton_CT <- mutate(Hamilton_CT, Prop20to34 = (AGE_20_TO_24 + AGE_25_TO_29 + AGE_30_TO_34)/POPULATION, Prop35to49 = (AGE_35_TO_39 + AGE_40_TO_44 + AGE_45_TO_49)/POPULATION)
```

This function is used to create local Moran maps:

``` r
localmoran.map <- function(p, listw, VAR, by){
  # p is a simple features object
  require(tidyverse)
  require(spdep)
  require(plotly)
  
  df_msc <- p |> 
    rename(VAR = as.name(VAR),
              key = as.name(by)) |>
    transmute(key,
              VAR,
              Z = (VAR - mean(VAR)) / var(VAR),
              SMA = lag.listw(listw, Z),
              Type = case_when(Z < 0 & SMA < 0 ~ "LL",
                               Z > 0 & SMA > 0 ~ "HH",
                               TRUE ~ "HL/LH"))
  
  local_I <- localmoran(df_msc$VAR, listw)
  
  colnames(local_I) <- c("Ii", "E.Ii", "Var.Ii", "Z.Ii", "p.val")
  
  df_msc <- left_join(df_msc, 
                      data.frame(key = df_msc$key, 
                                 local_I),
                      by = "key")
  
  plot_ly(df_msc) |>
    add_sf(type = "scatter",
           split = ~(p.val < 0.05), 
           color = ~Type, 
           colors = c("red", 
                      "khaki1",
                      "dodgerblue", 
                      "dodgerblue4")) 
}
```

This function is used to create $G_i^*$ maps:

``` r
gistar.map <- function(p = p, listw = listw, VAR = VAR, by = by){
  require(tidyverse)
  require(spdep)
  require(sf)
  require(plotly)
  
  p <- mutate(p, key = p[[by]])
  
  df.lg <- localG(p[[VAR]], listw)
  df.lg <- as.numeric(df.lg)
  df.lg <- data.frame(Gstar = df.lg, p.val = 2 * pnorm(abs(df.lg), lower.tail = FALSE))
  
  df.lg <- mutate(df.lg, 
              Type = case_when(Gstar < 0 & p.val <= 0.05 ~ "Low Concentration",
                               Gstar > 0 & p.val <= 0.05 ~ "High Concentration",
                               TRUE ~ "Not Signicant"))

  p <- left_join(p, 
                  data.frame(key = p[[by]], df.lg))
  
  plot_ly(p) |>
    add_sf(split = ~(p.val < 0.05), color = ~Type, colors = c("red", "dodgerblue", "gray"))
}
```

Create spatial weights.

1) By contiguity:

``` r
Hamilton_CT.w <- nb2listw(poly2nb(pl = Hamilton_CT))
```

2) Binary, by distance (3 km threshold) _including self_.

``` r
Hamilton_CT.3knb <- Hamilton_CT |> 
  st_centroid() |>
  dnearneigh(d1 = 0, d2 = 3)
```

```
## Warning: st_centroid assumes attributes are constant over geometries
```

```
## Warning in dnearneigh(st_centroid(Hamilton_CT), d1 = 0, d2 = 3): neighbour
## object has 188 sub-graphs
```

``` r
Hamilton_CT.3kw <- nb2listw(include.self(Hamilton_CT.3knb), style = "B")
```

You are now ready for the next activity.

## Activity

**NOTE**: Activities include technical "how to" tasks/questions. Usually, these ask you to practice using the software to organize data, create plots, and so on in support of analysis and interpretation. The second type of questions ask you to activate your brainware and to think geographically and statistically.

::: {.infobox .software data-latex="{software}"}
**Activity Part I**
:::

1. Create local Moran maps for the population in age group 20-34 and _proportion_ of population in age group 20-34. 

2.  Use the $G_i^*$ statistic to analyze the population _and_ proportion of population in the age group 20-34. Then, use the `gistar.map` function to create $G_i^*$ maps.

3. Create local Moran maps for the population and _population density_ in the age group 20-34. 

::: {.infobox .brainware data-latex="{brainware}"}
**Activity Part II**
:::

4. Concerning the analysis in Question 3: What is the difference between using population (absolute) and population density (rate)?

5. Concerning the analysis in Question 1: What is the difference between using population (absolute) and proportion of population (rate)? Is there a reason to prefer either variable in analysis? Discuss.

6. More generally, what do you think should guide the decision of whether to analyze variables as absolute values or rates?
