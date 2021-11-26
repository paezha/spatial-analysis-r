# Activity 8: Point Pattern Analysis V

Remember, you can download the source file for this activity from [here](https://github.com/paezha/Spatial-Statistics-Course).

## Practice questions

Answer the following questions:

1. Describe the process to use simulation for hypothesis testing
2. Why is the selection of an appropriate region critical for the analysis of point patterns?
3. Discuss the issues associated with the edges of a region.
4. What is a sampled point pattern?

## Learning objectives

In this activity, you will:

1. Explore a dataset using single scale distance-based techniques.
2. Explore the characteristics of a point pattern at multiple scales.
3. Discuss ways to evaluate how confident you are that a pattern is random.

## Suggested reading

O'Sullivan D and Unwin D (2010) Geographic Information Analysis, 2nd Edition, Chapter 5. John Wiley & Sons: New Jersey.

## Preliminaries

For this activity you will need the following:

* An R markdown notebook version of this document (the source file).

* A package called `geog4ga3`.

It is good practice to clear the working space to make sure that you do not have extraneous items there when you begin your work. The command in R to clear the workspace is `rm` (for "remove"), followed by a list of items to be removed. To clear the workspace from _all_ objects, do the following:

```r
rm(list = ls())
```

Note that `ls()` lists all objects currently on the workspace.

Load the libraries you will use in this activity. In addition to `tidyverse`, you will need `spatstat`, a package designed for the analysis of point patterns (you can learn about `spatstat` [here](https://cran.r-project.org/web/packages/spatstat/vignettes/getstart.pdf) and [here](http://spatstat.org/resources/spatstatJSSpaper.pdf)):

```r
library(geog4ga3)
```

```
## Warning: replacing previous import 'plotly::filter' by 'stats::filter' when
## loading 'geog4ga3'
```

```
## Warning: replacing previous import 'dplyr::lag' by 'stats::lag' when loading
## 'geog4ga3'
```

```r
library(spatstat)
library(tidyverse)
```

Load a dataset of your choice. It could be one of the datasets that we have used before (Toronto Business Points, Bear GPS Locations), or one of the datasets included with the package `spatstat`. To see what datasets are available through the package, do the following:

```r
vcdExtra::datasets("spatstat.data")
```

```
##                               Item class   dim
## 1                           Kovesi  list 41x13
## 2                         amacrine   ppp     6
## 3                         anemones   ppp     6
## 4                             ants   ppp     6
## 5                ants.extra (ants)  list     7
## 6                         austates  list     4
## 7                          bdspots  list     3
## 8                              bei   ppp     5
## 9                  bei.extra (bei)  list     2
## 10                       betacells   ppp     6
## 11                    bramblecanes   ppp     6
## 12                    bronzefilter   ppp     6
## 13                             btb   ppp     6
## 14                 btb.extra (btb)  list     2
## 15                           cells   ppp     5
## 16                       cetaceans  list   9x4
## 17     cetaceans.extra (cetaceans)  list     1
## 18                         chicago   ppx     3
## 19                         chorley   ppp     6
## 20         chorley.extra (chorley)  list     2
## 21                        clmfires   ppp     6
## 22       clmfires.extra (clmfires)  list     2
## 23                        concrete   ppp     5
## 24                          copper  list     7
## 25                       demohyper  list   3x3
## 26                         demopat   ppp     6
## 27                        dendrite   ppx     3
## 28                        finpines   ppp     6
## 29                             flu  list  41x4
## 30                         ganglia   ppp     6
## 31                          gordon   ppp     5
## 32                        gorillas   ppp     6
## 33       gorillas.extra (gorillas)  list     7
## 34                         hamster   ppp     6
## 35                         heather  list     3
## 36                      humberside   ppp     6
## 37  humberside.convex (humberside)   ppp     6
## 38                        hyytiala   ppp     6
## 39                   japanesepines   ppp     5
## 40                         lansing   ppp     6
## 41                         letterR  owin     5
## 42                        longleaf   ppp     6
## 43                          mucosa   ppp     6
## 44          mucosa.subwin (mucosa)  owin     4
## 45                       murchison  list     3
## 46                         nbfires   ppp     6
## 47         nbfires.extra (nbfires)  list     2
## 48              nbw.rect (nbfires)  owin     4
## 49               nbw.seg (nbfires)  list     5
## 50                         nztrees   ppp     5
## 51                           osteo  list  40x5
## 52                         paracou   ppp     6
## 53                       ponderosa   ppp     5
## 54     ponderosa.extra (ponderosa)  list     2
## 55                       pyramidal  list  31x2
## 56                         redwood   ppp     5
## 57                        redwood3   ppp     5
## 58                     redwoodfull   ppp     5
## 59 redwoodfull.extra (redwoodfull)  list     5
## 60                  residualspaper  list     7
## 61                         shapley   ppp     6
## 62         shapley.extra (shapley)  list     3
## 63                           simba  list  10x2
## 64                          simdat   ppp     5
## 65                       simplenet  list    10
## 66                         spiders   ppx     3
## 67                     sporophores   ppp     6
## 68                         spruces   ppp     6
## 69                    swedishpines   ppp     5
## 70                         urkiola   ppp     6
## 71                        vesicles   ppp     5
## 72       vesicles.extra (vesicles)  list     4
## 73                            waka   ppp     6
## 74                   waterstriders  list     3
##                                                                                        Title
## 1                                          Colour Sequences with Uniform Perceptual Contrast
## 2                                                                 Hughes' Amacrine Cell Data
## 3                                                                      Beadlet Anemones Data
## 4                                                            Harkness-Isham ants' nests data
## 5                                                            Harkness-Isham ants' nests data
## 6                                                 Australian States and Mainland Territories
## 7                                               Breakdown Spots in Microelectronic Materials
## 8                                                                 Tropical rain forest trees
## 9                                                                 Tropical rain forest trees
## 10                                                         Beta Ganglion Cells in Cat Retina
## 11                                                             Hutchings' Bramble Canes data
## 12                                                               Bronze gradient filter data
## 13                                                                  Bovine Tuberculosis Data
## 14                                                                  Bovine Tuberculosis Data
## 15                                                            Biological Cells Point Pattern
## 16                                            Point patterns of whale and dolphin sightings.
## 17                                            Point patterns of whale and dolphin sightings.
## 18                                                                        Chicago Crime Data
## 19                                                                Chorley-Ribble Cancer Data
## 20                                                                Chorley-Ribble Cancer Data
## 21                                                           Castilla-La Mancha Forest Fires
## 22                                                           Castilla-La Mancha Forest Fires
## 23                                                                   Air Bubbles in Concrete
## 24                                                   Berman-Huntington points and lines data
## 25                                       Demonstration Example of Hyperframe of Spatial Data
## 26                                                             Artificial Data Point Pattern
## 27                                                                     Dendritic Spines Data
## 28                                                                 Pine saplings in Finland.
## 29                                                                  Influenza Virus Proteins
## 30                                            Beta Ganglion Cells in Cat Retina, Old Version
## 31                                                                   People in Gordon Square
## 32                                                                     Gorilla Nesting Sites
## 33                                                                     Gorilla Nesting Sites
## 34                                                              Aherne's hamster tumour data
## 35                                                                     Diggle's Heather Data
## 36                                       Humberside Data on Childhood Leukaemia and Lymphoma
## 37                                       Humberside Data on Childhood Leukaemia and Lymphoma
## 38                                                   Scots pines and other trees at Hyytiala
## 39                                                              Japanese Pines Point Pattern
## 40                                                               Lansing Woods Point Pattern
## 41                                                               Window in Shape of Letter R
## 42                                                              Longleaf Pines Point Pattern
## 43                                                                   Cells in Gastric Mucosa
## 44                                                                   Cells in Gastric Mucosa
## 45                                                                   Murchison gold deposits
## 46                                              Point Patterns of New Brunswick Forest Fires
## 47                                              Point Patterns of New Brunswick Forest Fires
## 48                                              Point Patterns of New Brunswick Forest Fires
## 49                                              Point Patterns of New Brunswick Forest Fires
## 50                                                           New Zealand Trees Point Pattern
## 51                       Osteocyte Lacunae Data: Replicated Three-Dimensional Point Patterns
## 52                                                   Kimboto trees at Paracou, French Guiana
## 53                                                         Ponderosa Pine Tree Point Pattern
## 54                                                         Ponderosa Pine Tree Point Pattern
## 55                                                     Pyramidal Neurons in Cingulate Cortex
## 56                                       California Redwoods Point Pattern (Ripley's Subset)
## 57                                       California Redwoods Point Pattern (Ripley's Subset)
## 58                                        California Redwoods Point Pattern (Entire Dataset)
## 59                                        California Redwoods Point Pattern (Entire Dataset)
## 60                                     Data and Code From JRSS Discussion Paper on Residuals
## 61                                                      Galaxies in the Shapley Supercluster
## 62                                                      Galaxies in the Shapley Supercluster
## 63            Simulated data from a two-group experiment with replication within each group.
## 64                                                                   Simulated Point Pattern
## 65                                                          Simple Example of Linear Network
## 66                                               Spider Webs on Mortar Lines of a Brick Wall
## 67                                                                          Sporophores Data
## 68                                                                     Spruces Point Pattern
## 69                                                               Swedish Pines Point Pattern
## 70                                                               Urkiola Woods Point Pattern
## 71                                                                             Vesicles Data
## 72                                                                             Vesicles Data
## 73                                                               Trees in Waka national park
## 74 Waterstriders data.  Three independent replications of a point pattern formed by insects.
```

Load a dataset of your choice.

You can do this by using the `load()` function if the dataset is in your drive (e.g., the GPS coordinates of the bear).

On the other hand, if the dataset is included with the `spatstat` package you can do the following, for example to load the `gorillas` dataset:

```r
gorillas.ppp <- gorillas
```

As usual, you can check the object by means of the `summary` function:

```r
summary(gorillas.ppp)
```

```
## Marked planar point pattern:  647 points
## Average intensity 3.255566e-05 points per square metre
## 
## *Pattern contains duplicated points*
## 
## Coordinates are given to 2 decimal places
## i.e. rounded to the nearest multiple of 0.01 metres
## 
## Mark variables: group, season, date
## Summary:
##     group              season               date           
##  Length:647         Length:647         Min.   :2006-01-06  
##  Class :character   Class :character   1st Qu.:2007-03-15  
##  Mode  :character   Mode  :character   Median :2008-02-05  
##                                        Mean   :2007-12-14  
##                                        3rd Qu.:2008-09-23  
##                                        Max.   :2009-05-31  
## 
## Window: polygonal boundary
## single connected closed polygon with 21 vertices
## enclosing rectangle: [580457.9, 585934] x [674172.8, 678739.2] metres
##                      (5476 x 4566 metres)
## Window area = 19873700 square metres
## Unit of length: 1 metre
## Fraction of frame area: 0.795
```

## Activity

1. Partner with a fellow student to analyze the chosen dataset.

2. Discuss whether the pattern is random, and how confident you are in your decision.

3. The analysis of the pattern is meant to provide insights about the underlying process. Create a hypothesis using the data generated and can you answer that hypothesis using the plots generated?

4. Discuss the limitations of the analysis, for instance, choice of modeling parameters (size of region, kernel bandwidths, edge effects, etc.)
