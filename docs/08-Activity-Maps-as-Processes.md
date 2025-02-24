# Activity 3: Maps as Processes

*NOTE*: The source files for this book are available with companion package [{isdas}](https://paezha.github.io/isdas/). The source files are in Rmarkdown format and packed as templates. These files allow you execute code within the notebook, so that you can work interactively with the notes. 

## Practice Questions

Answer the following questions:

1. What is a Geographic Information System?
2. What distinguishes a statistical map from other types of mapping techniques?
3. What is a null landscape?

## Learning Objectives

In this activity, you will:

1. Simulate landscapes using various types of processes.
2. Discuss the difference between random and non-random landscapes.
3. Think about ways to decide whether a landscape is random.

## Suggested Reading

O'Sullivan D and Unwin D (2010) Geographic Information Analysis, 2nd Edition, Chapter 4. John Wiley & Sons: New Jersey.

## Preliminaries

It is good practice to begin with a clean session to make sure that you do not have extraneous items there when you begin your work. The best practice is to restart the `R` session, which can be accomplished for example with `command/ctrl + shift + F10`. An alternative to _only_ purge user-created objects from memory is to use the `R` command `rm` (for "remove"), followed by a list of items to be removed. To clear the workspace from _all_ objects, do the following:

``` r
rm(list = ls())
```

Note that `ls()` lists all objects currently on the worspace.

Load the libraries you will use in this activity:

``` r
library(tidyverse) # Easily Install and Load the 'Tidyverse'
```

In the practice that preceded this activity, you learned how to simulate null landscapes and spatial processes. 

## Activity

**NOTE**: Activities include technical "how to" tasks/questions. Usually, these ask you to practice using the software to organize data, create plots, and so on in support of analysis and interpretation. The second type of questions ask you to activate your brainware and to think geographically and statistically.

::: {.infobox .software data-latex="{software}"}
**Activity Part I**
:::

1. (*)Simulate and plot a landscape using a random, stochastic, or deterministic process. It is your choice whether to simulate a point pattern or a continuous variable. Identify the key parameters that make a landscape more or less random. Repeat several times changing those parameters.

::: {.infobox .brainware data-latex="{brainware}"}
**Activity Part II**
:::

2. Recreate any one of the maps you created and share the map with a fellow student. Ask them to guess whether the map is random or non-random.

3. Repeat step 2 several times (between two and four times).

4. Propose one or more ways to decide whether a landscape is random, and explain your reasoning. The approach does not need to be the same for point patterns and continuous variables!

