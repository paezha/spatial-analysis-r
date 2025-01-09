# Part IV: Data in Areal Units {-}

![](images/part-04.jpg)<!-- -->

# Area Data I

*NOTE*: The source files for this book are available with companion package [{isdas}](https://paezha.github.io/isdas/). The source files are in Rmarkdown format and packed as templates. These files allow you execute code within the notebook, so that you can work interactively with the notes. 

## Learning Objectives

In last few practices/sessions, you learned about spatial point patterns. The next few sessions will concentrate on _area data_.

In this practice, you will learn:

1. A formal definition of area data.
2. Processes and area data.
3. Visualizing area data: Choropleth maps.
4. Visualizing area data: Cartograms.

## Suggested Readings

- Bailey TC and Gatrell AC [-@Bailey1995] Interactive Spatial Data Analysis, Chapter 7. Longman: Essex.
- Bivand RS, Pebesma E, and Gomez-Rubio V [-@Bivand2008] Applied Spatial Data Analysis with R, Chapter 9. Springer: New York.
- Brunsdon C and Comber L [-@Brunsdon2015R] An Introduction to R for Spatial Analysis and Mapping, Chapter 7. Sage: Los Angeles.
- O'Sullivan D and Unwin D [-@Osullivan2010] Geographic Information Analysis, 2nd Edition, Chapter 7. John Wiley & Sons: New Jersey.

## Preliminaries

As usual, it is good practice to begin with a clean session to make sure that you do not have extraneous items there when you begin your work. The best practice is to restart the `R` session, which can be accomplished for example with `command/ctrl + shift + F10`. An alternative to _only_ purge user-created objects from memory is to use the `R` command `rm` (for "remove"), followed by a list of items to be removed. To clear the workspace from _all_ objects, do the following:

``` r
rm(list = ls())
```

Note that `ls()` lists all objects currently on the workspace.

Load the libraries you will use in this activity:

``` r
library(cartogram) # Create Cartograms with R
library(isdas) # Companion Package for Book An Introduction to Spatial Data Analysis and Statistics
library(gridExtra) # Miscellaneous Functions for "Grid" Graphics
library(plotly) # Create Interactive Web Graphics via 'plotly.js'
library(sf) # Simple Features for R
library(tidyverse) # Easily Install and Load the 'Tidyverse'
```

Read the data used in this chapter.

``` r
data("Hamilton_CT")
```

The data are an object of class `sf` that includes the spatial information for the census tracts in the Hamilton Census Metropolitan Area in Canada and a series of demographic variables from the 2011 Census of Canada.

You can quickly verify the contents of the dataframe by means of `summary`:

``` r
summary(Hamilton_CT)
```

```
##        ID               AREA             TRACT             POPULATION   
##  Min.   : 919807   Min.   :  0.3154   Length:188         Min.   :    5  
##  1st Qu.: 927964   1st Qu.:  0.8552   Class :character   1st Qu.: 2639  
##  Median : 948130   Median :  1.4157   Mode  :character   Median : 3595  
##  Mean   : 948710   Mean   :  7.4578                      Mean   : 3835  
##  3rd Qu.: 959722   3rd Qu.:  2.7775                      3rd Qu.: 4692  
##  Max.   :1115750   Max.   :138.4466                      Max.   :11675  
##   POP_DENSITY         AGE_LESS_20      AGE_20_TO_24    AGE_25_TO_29  
##  Min.   :    2.591   Min.   :   0.0   Min.   :  0.0   Min.   :  0.0  
##  1st Qu.: 1438.007   1st Qu.: 528.8   1st Qu.:168.8   1st Qu.:135.0  
##  Median : 2689.737   Median : 750.0   Median :225.0   Median :215.0  
##  Mean   : 2853.078   Mean   : 899.3   Mean   :253.9   Mean   :232.8  
##  3rd Qu.: 3783.889   3rd Qu.:1110.0   3rd Qu.:311.2   3rd Qu.:296.2  
##  Max.   :14234.286   Max.   :3285.0   Max.   :835.0   Max.   :915.0  
##   AGE_30_TO_34     AGE_35_TO_39     AGE_40_TO_44     AGE_45_TO_49  
##  Min.   :   0.0   Min.   :   0.0   Min.   :   0.0   Min.   :  0.0  
##  1st Qu.: 135.0   1st Qu.: 145.0   1st Qu.: 170.0   1st Qu.:203.8  
##  Median : 195.0   Median : 200.0   Median : 230.0   Median :282.5  
##  Mean   : 228.2   Mean   : 239.6   Mean   : 268.7   Mean   :310.6  
##  3rd Qu.: 281.2   3rd Qu.: 280.0   3rd Qu.: 325.0   3rd Qu.:385.0  
##  Max.   :1320.0   Max.   :1200.0   Max.   :1105.0   Max.   :880.0  
##   AGE_50_TO_54    AGE_55_TO_59    AGE_60_TO_64  AGE_65_TO_69    AGE_70_TO_74  
##  Min.   :  0.0   Min.   :  0.0   Min.   :  0   Min.   :  0.0   Min.   :  0.0  
##  1st Qu.:203.8   1st Qu.:175.0   1st Qu.:140   1st Qu.:115.0   1st Qu.: 90.0  
##  Median :280.0   Median :240.0   Median :220   Median :157.5   Median :130.0  
##  Mean   :300.3   Mean   :257.7   Mean   :229   Mean   :174.2   Mean   :139.7  
##  3rd Qu.:375.0   3rd Qu.:325.0   3rd Qu.:295   3rd Qu.:221.2   3rd Qu.:180.0  
##  Max.   :740.0   Max.   :625.0   Max.   :540   Max.   :625.0   Max.   :540.0  
##   AGE_75_TO_79     AGE_80_TO_84     AGE_MORE_85              geometry  
##  Min.   :  0.00   Min.   :  0.00   Min.   :  0.00   POLYGON      :188  
##  1st Qu.: 68.75   1st Qu.: 50.00   1st Qu.: 35.00   epsg:26917   :  0  
##  Median :100.00   Median : 77.50   Median : 70.00   +proj=utm ...:  0  
##  Mean   :118.32   Mean   : 95.05   Mean   : 87.71                      
##  3rd Qu.:160.00   3rd Qu.:120.00   3rd Qu.:105.00                      
##  Max.   :575.00   Max.   :420.00   Max.   :400.00
```

## Area Data

Every phenomena can be measured at a location (ask yourself, what exists outside of space?).

In point pattern analysis, the _unit of support_ is the point, and the source of randomness is the location itself. Many other forms of data are also collected at points. For instance, when the census collects information on population, at its most basic, the information can be georeferenced to an address, that is, a point.

In numerous applications, however, data are not reported at their fundamental unit of support, but rather are aggregated to some other geometry, for instance an area. This is done for several reasons, including the privacy and confidentiality of the data. Instead of reporting individual-level information, the information is reported for zoning systems that often are devised without consideration to any underlying social, natural, or economic processes.

Census data, for example, are reported at different levels of geography. In Canada, the smallest publicly available geography is called a _Dissemination Area_ or [DA](http://www12.statcan.gc.ca/census-recensement/2011/ref/dict/geo021-eng.cfm). A DA in Canada contains a population between 400 and 700 persons. Thus, instead of reporting that one person (or more) are located at a point (i.e., an address), the census reports the population for the DA. Other data are aggregated in similar ways (income, residential status, etc.)

At the highest level of aggregation, national level statistics are reported, such as Gross Domestic Product, or GDP. Economic production is not evenly distributed across space; however, the national GDP does not distinguish regional variations in this process.

Ideally, a data analyst would work with data in its most fundamental support. This is not always possible, and therefore many techniques have been developed to work with data that have been aggregated to zones.

When working with areas, it is less practical to identify the area with the coordinates (as we did with points). After all, areas will be composed of lines and reporting all the relevant coordinates is impractical. Sometimes the geometric centroids of the areas are used instead.

More commonly, areas are assigned an index or unique identifier, so that a region will typically consist of a set of $n$ areas as follows:
$$
R = A_1 \cup A_2 \cup A_3 \cup ...\cup A_n.
$$

The above is read as "the Region R is the union of Areas 1 to n".

Regions can have a set of $k$ attributes or variables associated with them, for instance:
$$
\textbf{X}_i=[x_{i1}, x_{i2}, x_{i3},...,x_{ik}]
$$

These attributes will typically be counts (e.g., number of people in a DA), or some summary measure of the underlying data (e.g., mean commute time).

## Processes and Area Data

Imagine that data on income by household were collected as follows:

``` r
# Here, we are creating a dataframe with three columns, coordinates x and y in space to indicate the locations of households, and their income.
df <- data.frame(x = c(0.3, 0.4, 0.5, 0.6, 0.7), y = c(0.1, 0.4, 0.2, 0.5, 0.3), Income = c(30000, 30000, 100000, 100000, 100000))
```

Households are geocoded as points with coordinates `x` and `y`, whereas income is in dollars.

Plot the income as points (hover over the points to see the attributes):

``` r
# The `ggplot()` function is used to create a plot. The function `geom_point()` adds points to the plot, using the values of coordinates x and y, and coloring by Income. Higher income households appear to be on the East regions of the area.

p <- ggplot(data = df, aes(x = x, y = y, color = Income)) + 
  geom_point(shape = 17, size = 5) +
  coord_fixed()
ggplotly(p)
```

```{=html}
<div class="plotly html-widget html-fill-item" id="htmlwidget-cfbe6b653d801c385ede" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-cfbe6b653d801c385ede">{"x":{"data":[{"x":[0.29999999999999999,0.40000000000000002,0.5,0.59999999999999998,0.69999999999999996],"y":[0.10000000000000001,0.40000000000000002,0.20000000000000001,0.5,0.29999999999999999],"text":["x: 0.3<br />y: 0.1<br />Income: 3e+04","x: 0.4<br />y: 0.4<br />Income: 3e+04","x: 0.5<br />y: 0.2<br />Income: 1e+05","x: 0.6<br />y: 0.5<br />Income: 1e+05","x: 0.7<br />y: 0.3<br />Income: 1e+05"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":["rgba(19,43,67,1)","rgba(19,43,67,1)","rgba(86,177,247,1)","rgba(86,177,247,1)","rgba(86,177,247,1)"],"opacity":1,"size":18.897637795275593,"symbol":"triangle-up","line":{"width":1.8897637795275593,"color":["rgba(19,43,67,1)","rgba(19,43,67,1)","rgba(86,177,247,1)","rgba(86,177,247,1)","rgba(86,177,247,1)"]}},"hoveron":"points","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[0.30000000000000004],"y":[0.10000000000000001],"name":"10c3639fc91899524bb5ad52867283a1","type":"scatter","mode":"markers","opacity":0,"hoverinfo":"skip","showlegend":false,"marker":{"color":[0,1],"colorscale":[[0,"#132B43"],[0.0033444816053511853,"#132B44"],[0.0066889632107023185,"#132C44"],[0.010033444816053503,"#142C45"],[0.013377926421404689,"#142D45"],[0.016722408026755876,"#142D46"],[0.020066889632107007,"#142D46"],[0.023411371237458192,"#142E47"],[0.026755852842809378,"#152E47"],[0.030100334448160512,"#152F48"],[0.033444816053511697,"#152F48"],[0.03678929765886288,"#152F49"],[0.040133779264214013,"#153049"],[0.043478260869565251,"#16304A"],[0.046822742474916385,"#16304A"],[0.050167224080267518,"#16314B"],[0.053511705685618756,"#16314B"],[0.05685618729096989,"#16324C"],[0.060200668896321023,"#17324D"],[0.063545150501672268,"#17324D"],[0.066889632107023395,"#17334E"],[0.070234113712374535,"#17334E"],[0.073578595317725759,"#17344F"],[0.0769230769230769,"#18344F"],[0.080267558528428137,"#183450"],[0.083612040133779264,"#183550"],[0.086956521739130405,"#183551"],[0.090301003344481642,"#183651"],[0.093645484949832769,"#193652"],[0.09698996655518391,"#193652"],[0.10033444816053515,"#193753"],[0.10367892976588627,"#193754"],[0.10702341137123751,"#193854"],[0.11036789297658865,"#1A3855"],[0.11371237458193978,"#1A3955"],[0.11705685618729102,"#1A3956"],[0.12040133779264216,"#1A3956"],[0.12374581939799328,"#1A3A57"],[0.12709030100334454,"#1B3A57"],[0.13043478260869565,"#1B3B58"],[0.13377926421404679,"#1B3B59"],[0.13712374581939804,"#1B3B59"],[0.14046822742474915,"#1C3C5A"],[0.14381270903010029,"#1C3C5A"],[0.14715719063545152,"#1C3D5B"],[0.15050167224080266,"#1C3D5B"],[0.1538461538461538,"#1C3D5C"],[0.15719063545150502,"#1D3E5C"],[0.16053511705685616,"#1D3E5D"],[0.1638795986622073,"#1D3F5D"],[0.16722408026755853,"#1D3F5E"],[0.17056856187290967,"#1D3F5F"],[0.17391304347826092,"#1E405F"],[0.17725752508361203,"#1E4060"],[0.18060200668896328,"#1E4160"],[0.18394648829431443,"#1E4161"],[0.18729096989966554,"#1E4261"],[0.19063545150501679,"#1F4262"],[0.19397993311036793,"#1F4263"],[0.19732441471571904,"#1F4363"],[0.20066889632107029,"#1F4364"],[0.20401337792642144,"#1F4464"],[0.20735785953177255,"#204465"],[0.2107023411371238,"#204465"],[0.21404682274247494,"#204566"],[0.21739130434782605,"#204566"],[0.2207357859531773,"#214667"],[0.22408026755852842,"#214668"],[0.22742474916387956,"#214768"],[0.23076923076923081,"#214769"],[0.23411371237458192,"#214769"],[0.23745819397993306,"#22486A"],[0.24080267558528431,"#22486A"],[0.24414715719063543,"#22496B"],[0.24749163879598657,"#22496C"],[0.25083612040133779,"#224A6C"],[0.25418060200668907,"#234A6D"],[0.25752508361204007,"#234A6D"],[0.2608695652173913,"#234B6E"],[0.26421404682274258,"#234B6E"],[0.26755852842809369,"#244C6F"],[0.2709030100334448,"#244C70"],[0.27424749163879608,"#244C70"],[0.27759197324414719,"#244D71"],[0.28093645484949831,"#244D71"],[0.28428093645484959,"#254E72"],[0.2876254180602007,"#254E72"],[0.29096989966555181,"#254F73"],[0.29431438127090304,"#254F74"],[0.2976588628762542,"#254F74"],[0.30100334448160532,"#265075"],[0.30434782608695654,"#265075"],[0.30769230769230771,"#265176"],[0.31103678929765882,"#265176"],[0.31438127090301005,"#275277"],[0.31772575250836121,"#275278"],[0.32107023411371233,"#275278"],[0.32441471571906355,"#275379"],[0.32775919732441472,"#275379"],[0.33110367892976583,"#28547A"],[0.33444816053511706,"#28547B"],[0.33779264214046834,"#28557B"],[0.34113712374581934,"#28557C"],[0.34448160535117056,"#28567C"],[0.34782608695652184,"#29567D"],[0.35117056856187284,"#29567D"],[0.35451505016722407,"#29577E"],[0.35785953177257535,"#29577F"],[0.36120401337792646,"#2A587F"],[0.36454849498327757,"#2A5880"],[0.36789297658862885,"#2A5980"],[0.37123745819397996,"#2A5981"],[0.37458193979933108,"#2A5982"],[0.37792642140468236,"#2B5A82"],[0.38127090301003347,"#2B5A83"],[0.38461538461538458,"#2B5B83"],[0.38795986622073586,"#2B5B84"],[0.39130434782608697,"#2C5C85"],[0.39464882943143809,"#2C5C85"],[0.39799331103678937,"#2C5D86"],[0.40133779264214048,"#2C5D86"],[0.40468227424749159,"#2C5D87"],[0.40802675585284287,"#2D5E87"],[0.41137123745819398,"#2D5E88"],[0.4147157190635451,"#2D5F89"],[0.41806020066889638,"#2D5F89"],[0.4214046822742476,"#2E608A"],[0.4247491638795986,"#2E608A"],[0.42809364548494988,"#2E618B"],[0.4314381270903011,"#2E618C"],[0.43478260869565211,"#2E618C"],[0.43812709030100333,"#2F628D"],[0.44147157190635461,"#2F628D"],[0.44481605351170572,"#2F638E"],[0.44816053511705684,"#2F638F"],[0.45150501672240811,"#30648F"],[0.45484949832775923,"#306490"],[0.45819397993311034,"#306590"],[0.46153846153846162,"#306591"],[0.46488294314381273,"#306592"],[0.46822742474916385,"#316692"],[0.47157190635451512,"#316693"],[0.47491638795986624,"#316793"],[0.47826086956521735,"#316794"],[0.48160535117056863,"#326895"],[0.48494983277591974,"#326895"],[0.48829431438127086,"#326996"],[0.49163879598662213,"#326996"],[0.49498327759197325,"#326997"],[0.49832775919732447,"#336A98"],[0.50167224080267558,"#336A98"],[0.50501672240802675,"#336B99"],[0.50836120401337814,"#336B99"],[0.51170568561872909,"#346C9A"],[0.51505016722408015,"#346C9B"],[0.51839464882943143,"#346D9B"],[0.52173913043478259,"#346D9C"],[0.52508361204013387,"#346E9D"],[0.52842809364548515,"#356E9D"],[0.5317725752508361,"#356E9E"],[0.53511705685618738,"#356F9E"],[0.53846153846153844,"#356F9F"],[0.5418060200668896,"#3670A0"],[0.54515050167224088,"#3670A0"],[0.54849498327759216,"#3671A1"],[0.55183946488294311,"#3671A1"],[0.55518394648829439,"#3772A2"],[0.55852842809364545,"#3772A3"],[0.56187290969899661,"#3773A3"],[0.56521739130434789,"#3773A4"],[0.56856187290969917,"#3773A4"],[0.57190635451505012,"#3874A5"],[0.5752508361204014,"#3874A6"],[0.57859531772575246,"#3875A6"],[0.58193979933110362,"#3875A7"],[0.5852842809364549,"#3976A8"],[0.58862876254180607,"#3976A8"],[0.59197324414715735,"#3977A9"],[0.59531772575250841,"#3977A9"],[0.59866220735785947,"#3978AA"],[0.60200668896321063,"#3A78AB"],[0.60535117056856191,"#3A79AB"],[0.60869565217391308,"#3A79AC"],[0.61204013377926436,"#3A79AC"],[0.61538461538461542,"#3B7AAD"],[0.61872909698996648,"#3B7AAE"],[0.62207357859531764,"#3B7BAE"],[0.62541806020066892,"#3B7BAF"],[0.62876254180602009,"#3C7CB0"],[0.63210702341137137,"#3C7CB0"],[0.63545150501672243,"#3C7DB1"],[0.6387959866220736,"#3C7DB1"],[0.64214046822742465,"#3C7EB2"],[0.64548494983277593,"#3D7EB3"],[0.6488294314381271,"#3D7FB3"],[0.65217391304347838,"#3D7FB4"],[0.65551839464882944,"#3D7FB5"],[0.65886287625418061,"#3E80B5"],[0.66220735785953166,"#3E80B6"],[0.66555183946488294,"#3E81B6"],[0.66889632107023411,"#3E81B7"],[0.67224080267558539,"#3F82B8"],[0.67558528428093667,"#3F82B8"],[0.67892976588628762,"#3F83B9"],[0.68227424749163867,"#3F83BA"],[0.68561872909698995,"#4084BA"],[0.68896321070234112,"#4084BB"],[0.6923076923076924,"#4085BB"],[0.69565217391304368,"#4085BC"],[0.69899665551839463,"#4086BD"],[0.70234113712374568,"#4186BD"],[0.70568561872909696,"#4186BE"],[0.70903010033444813,"#4187BF"],[0.71237458193979941,"#4187BF"],[0.71571906354515069,"#4288C0"],[0.71906354515050164,"#4288C1"],[0.72240802675585292,"#4289C1"],[0.72575250836120397,"#4289C2"],[0.72909698996655514,"#438AC2"],[0.73244147157190642,"#438AC3"],[0.7357859531772577,"#438BC4"],[0.73913043478260865,"#438BC4"],[0.74247491638795993,"#438CC5"],[0.74581939799331098,"#448CC6"],[0.74916387959866215,"#448DC6"],[0.75250836120401343,"#448DC7"],[0.75585284280936471,"#448EC8"],[0.75919732441471588,"#458EC8"],[0.76254180602006694,"#458FC9"],[0.76588628762541799,"#458FC9"],[0.76923076923076916,"#458FCA"],[0.77257525083612044,"#4690CB"],[0.77591973244147172,"#4690CB"],[0.77926421404682289,"#4691CC"],[0.78260869565217395,"#4691CD"],[0.785953177257525,"#4792CD"],[0.78929765886287617,"#4792CE"],[0.79264214046822745,"#4793CF"],[0.79598662207357873,"#4793CF"],[0.7993311036789299,"#4894D0"],[0.80267558528428096,"#4894D0"],[0.80602006688963224,"#4895D1"],[0.80936454849498318,"#4895D2"],[0.81270903010033446,"#4896D2"],[0.81605351170568574,"#4996D3"],[0.81939799331103691,"#4997D4"],[0.82274247491638797,"#4997D4"],[0.82608695652173925,"#4998D5"],[0.82943143812709019,"#4A98D6"],[0.83277591973244147,"#4A99D6"],[0.83612040133779275,"#4A99D7"],[0.83946488294314392,"#4A9AD8"],[0.8428093645484952,"#4B9AD8"],[0.84615384615384626,"#4B9BD9"],[0.8494983277591972,"#4B9BDA"],[0.85284280936454848,"#4B9BDA"],[0.85618729096989976,"#4C9CDB"],[0.85953177257525093,"#4C9CDB"],[0.86287625418060221,"#4C9DDC"],[0.86622073578595316,"#4C9DDD"],[0.86956521739130421,"#4D9EDD"],[0.87290969899665549,"#4D9EDE"],[0.87625418060200666,"#4D9FDF"],[0.87959866220735794,"#4D9FDF"],[0.88294314381270922,"#4DA0E0"],[0.88628762541806017,"#4EA0E1"],[0.88963210702341144,"#4EA1E1"],[0.8929765886287625,"#4EA1E2"],[0.89632107023411367,"#4EA2E3"],[0.89966555183946495,"#4FA2E3"],[0.90301003344481623,"#4FA3E4"],[0.90635451505016718,"#4FA3E5"],[0.90969899665551845,"#4FA4E5"],[0.91304347826086951,"#50A4E6"],[0.91638795986622068,"#50A5E7"],[0.91973244147157196,"#50A5E7"],[0.92307692307692324,"#50A6E8"],[0.92642140468227441,"#51A6E8"],[0.92976588628762546,"#51A7E9"],[0.93311036789297652,"#51A7EA"],[0.93645484949832769,"#51A8EA"],[0.93979933110367897,"#52A8EB"],[0.94314381270903025,"#52A9EC"],[0.9464882943143812,"#52A9EC"],[0.94983277591973247,"#52AAED"],[0.95317725752508375,"#53AAEE"],[0.9565217391304347,"#53ABEE"],[0.95986622073578598,"#53ABEF"],[0.96321070234113726,"#53ACF0"],[0.96655518394648821,"#54ACF0"],[0.96989966555183948,"#54ADF1"],[0.97324414715719076,"#54ADF2"],[0.97658862876254171,"#54AEF2"],[0.97993311036789299,"#55AEF3"],[0.98327759197324427,"#55AFF4"],[0.98662207357859544,"#55AFF4"],[0.98996655518394649,"#55B0F5"],[0.99331103678929777,"#56B0F6"],[0.99665551839464894,"#56B1F6"],[1,"#56B1F7"]],"colorbar":{"bgcolor":"rgba(255,255,255,1)","bordercolor":"transparent","borderwidth":1.8897637795275593,"thickness":23.039999999999996,"title":"Income","titlefont":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724},"tickmode":"array","ticktext":["4e+04","6e+04","8e+04","1e+05"],"tickvals":[0.14404761904761904,0.42880952380952375,0.71357142857142852,0.99833333333333329],"tickfont":{"color":"rgba(0,0,0,1)","family":"","size":11.68949771689498},"ticklen":2,"len":0.5}},"xaxis":"x","yaxis":"y","frame":null}],"layout":{"margin":{"t":26.228310502283104,"r":7.3059360730593621,"b":40.182648401826491,"l":43.105022831050235},"plot_bgcolor":"rgba(235,235,235,1)","paper_bgcolor":"rgba(255,255,255,1)","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[0.27999999999999997,0.71999999999999997],"tickmode":"array","ticktext":["0.3","0.4","0.5","0.6","0.7"],"tickvals":[0.30000000000000004,0.40000000000000002,0.5,0.60000000000000009,0.70000000000000007],"categoryorder":"array","categoryarray":["0.3","0.4","0.5","0.6","0.7"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.6529680365296811,"tickwidth":0.66417600664176002,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.68949771689498},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(255,255,255,1)","gridwidth":0.66417600664176002,"zeroline":false,"anchor":"y","title":{"text":"x","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}},"scaleanchor":"y","scaleratio":1,"hoverformat":".2f"},"yaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[0.080000000000000002,0.52000000000000002],"tickmode":"array","ticktext":["0.1","0.2","0.3","0.4","0.5"],"tickvals":[0.10000000000000001,0.20000000000000001,0.30000000000000004,0.40000000000000002,0.5],"categoryorder":"array","categoryarray":["0.1","0.2","0.3","0.4","0.5"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.6529680365296811,"tickwidth":0.66417600664176002,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.68949771689498},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(255,255,255,1)","gridwidth":0.66417600664176002,"zeroline":false,"anchor":"x","title":{"text":"y","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}},"scaleanchor":"x","scaleratio":1,"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":null,"line":{"color":null,"width":0,"linetype":[]},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":false,"legend":{"bgcolor":"rgba(255,255,255,1)","bordercolor":"transparent","borderwidth":1.8897637795275593,"font":{"color":"rgba(0,0,0,1)","family":"","size":11.68949771689498},"title":{"text":"Income","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}}},"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"source":"A","attrs":{"31c0b3453afa4":{"x":{},"y":{},"colour":{},"type":"scatter"}},"cur_data":"31c0b3453afa4","visdat":{"31c0b3453afa4":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.20000000000000001,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>
```

The underlying process is one of income sorting, with lower incomes to the west, and higher incomes to the east. This could be due to a geographical feature of the landscape (for instance, an escarpment), or the distribution of the housing stock (with a neighborhood that has more expensive houses). These are examples of a variable that responds to a common environmental factor. As an alternative, people may display a preference towards being near others that are similar to them (this is called homophily). When this happens, the variable responds to itself in space.

The quality of similarity or dissimilarity between neighboring observations of the same variable in space is called _spatial autocorrelation_. You will learn more about this later on.

Another reason why variables reported for areas could display similarities in space is as an consequence of the zoning system.

Suppose for a moment that the data above can only be reported at the zonal level, perhaps because of privacy and confidentiality concerns. Thanks to the great talent of the designers of the zoning system (or a felicitous coincidence!), the zoning system is such that it is consistent with the underlying process of sorting. The zones, therefore, are as follows:

``` r
# Here, we create a new dataframe with the coordinates necessary to define two zones. The zones are rectangles, so we need to define four corners for each. "Zone_ID" only has 2 values because there are only two zones in the analysis. 

zones1 <- data.frame(x1=c(0.2, 0.45), x2=c(0.45, 0.80), y1=c(0.0, 0.0), y2=c(0.6, 0.6), Zone_ID = c('1','2'))
```

If you add these zones to the plot:

``` r
# Similar to the plot above, but adding the zones with `geom_rect()` for plotting rectangles.
p <- ggplot() + 
  geom_rect(data = zones1, mapping = aes(xmin = x1, xmax = x2, ymin = y1, ymax = y2, fill = Zone_ID), alpha = 0.3) + 
  geom_point(data = df, aes(x = x, y = y, color = Income), shape = 17, size = 5) +
  coord_fixed()
ggplotly(p)
```

```{=html}
<div class="plotly html-widget html-fill-item" id="htmlwidget-6d99b4338932d618564c" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-6d99b4338932d618564c">{"x":{"data":[{"x":[0.20000000000000001,0.20000000000000001,0.45000000000000001,0.45000000000000001,0.20000000000000001],"y":[0,0.59999999999999998,0.59999999999999998,0,0],"text":"Zone_ID: 1","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"transparent","dash":"solid"},"fill":"toself","fillcolor":"rgba(248,118,109,0.3)","hoveron":"fills","name":"1","legendgroup":"1","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[0.45000000000000001,0.45000000000000001,0.80000000000000004,0.80000000000000004,0.45000000000000001],"y":[0,0.59999999999999998,0.59999999999999998,0,0],"text":"Zone_ID: 2","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"transparent","dash":"solid"},"fill":"toself","fillcolor":"rgba(0,191,196,0.3)","hoveron":"fills","name":"2","legendgroup":"2","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[0.29999999999999999,0.40000000000000002,0.5,0.59999999999999998,0.69999999999999996],"y":[0.10000000000000001,0.40000000000000002,0.20000000000000001,0.5,0.29999999999999999],"text":["x: 0.3<br />y: 0.1<br />Income: 3e+04","x: 0.4<br />y: 0.4<br />Income: 3e+04","x: 0.5<br />y: 0.2<br />Income: 1e+05","x: 0.6<br />y: 0.5<br />Income: 1e+05","x: 0.7<br />y: 0.3<br />Income: 1e+05"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":["rgba(19,43,67,1)","rgba(19,43,67,1)","rgba(86,177,247,1)","rgba(86,177,247,1)","rgba(86,177,247,1)"],"opacity":1,"size":18.897637795275593,"symbol":"triangle-up","line":{"width":1.8897637795275593,"color":["rgba(19,43,67,1)","rgba(19,43,67,1)","rgba(86,177,247,1)","rgba(86,177,247,1)","rgba(86,177,247,1)"]}},"hoveron":"points","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[0.20000000000000001],"y":[0],"name":"10c3639fc91899524bb5ad52867283a1","type":"scatter","mode":"markers","opacity":0,"hoverinfo":"skip","showlegend":false,"marker":{"color":[0,1],"colorscale":[[0,"#132B43"],[0.0033444816053511853,"#132B44"],[0.0066889632107023185,"#132C44"],[0.010033444816053503,"#142C45"],[0.013377926421404689,"#142D45"],[0.016722408026755876,"#142D46"],[0.020066889632107007,"#142D46"],[0.023411371237458192,"#142E47"],[0.026755852842809378,"#152E47"],[0.030100334448160512,"#152F48"],[0.033444816053511697,"#152F48"],[0.03678929765886288,"#152F49"],[0.040133779264214013,"#153049"],[0.043478260869565251,"#16304A"],[0.046822742474916385,"#16304A"],[0.050167224080267518,"#16314B"],[0.053511705685618756,"#16314B"],[0.05685618729096989,"#16324C"],[0.060200668896321023,"#17324D"],[0.063545150501672268,"#17324D"],[0.066889632107023395,"#17334E"],[0.070234113712374535,"#17334E"],[0.073578595317725759,"#17344F"],[0.0769230769230769,"#18344F"],[0.080267558528428137,"#183450"],[0.083612040133779264,"#183550"],[0.086956521739130405,"#183551"],[0.090301003344481642,"#183651"],[0.093645484949832769,"#193652"],[0.09698996655518391,"#193652"],[0.10033444816053515,"#193753"],[0.10367892976588627,"#193754"],[0.10702341137123751,"#193854"],[0.11036789297658865,"#1A3855"],[0.11371237458193978,"#1A3955"],[0.11705685618729102,"#1A3956"],[0.12040133779264216,"#1A3956"],[0.12374581939799328,"#1A3A57"],[0.12709030100334454,"#1B3A57"],[0.13043478260869565,"#1B3B58"],[0.13377926421404679,"#1B3B59"],[0.13712374581939804,"#1B3B59"],[0.14046822742474915,"#1C3C5A"],[0.14381270903010029,"#1C3C5A"],[0.14715719063545152,"#1C3D5B"],[0.15050167224080266,"#1C3D5B"],[0.1538461538461538,"#1C3D5C"],[0.15719063545150502,"#1D3E5C"],[0.16053511705685616,"#1D3E5D"],[0.1638795986622073,"#1D3F5D"],[0.16722408026755853,"#1D3F5E"],[0.17056856187290967,"#1D3F5F"],[0.17391304347826092,"#1E405F"],[0.17725752508361203,"#1E4060"],[0.18060200668896328,"#1E4160"],[0.18394648829431443,"#1E4161"],[0.18729096989966554,"#1E4261"],[0.19063545150501679,"#1F4262"],[0.19397993311036793,"#1F4263"],[0.19732441471571904,"#1F4363"],[0.20066889632107029,"#1F4364"],[0.20401337792642144,"#1F4464"],[0.20735785953177255,"#204465"],[0.2107023411371238,"#204465"],[0.21404682274247494,"#204566"],[0.21739130434782605,"#204566"],[0.2207357859531773,"#214667"],[0.22408026755852842,"#214668"],[0.22742474916387956,"#214768"],[0.23076923076923081,"#214769"],[0.23411371237458192,"#214769"],[0.23745819397993306,"#22486A"],[0.24080267558528431,"#22486A"],[0.24414715719063543,"#22496B"],[0.24749163879598657,"#22496C"],[0.25083612040133779,"#224A6C"],[0.25418060200668907,"#234A6D"],[0.25752508361204007,"#234A6D"],[0.2608695652173913,"#234B6E"],[0.26421404682274258,"#234B6E"],[0.26755852842809369,"#244C6F"],[0.2709030100334448,"#244C70"],[0.27424749163879608,"#244C70"],[0.27759197324414719,"#244D71"],[0.28093645484949831,"#244D71"],[0.28428093645484959,"#254E72"],[0.2876254180602007,"#254E72"],[0.29096989966555181,"#254F73"],[0.29431438127090304,"#254F74"],[0.2976588628762542,"#254F74"],[0.30100334448160532,"#265075"],[0.30434782608695654,"#265075"],[0.30769230769230771,"#265176"],[0.31103678929765882,"#265176"],[0.31438127090301005,"#275277"],[0.31772575250836121,"#275278"],[0.32107023411371233,"#275278"],[0.32441471571906355,"#275379"],[0.32775919732441472,"#275379"],[0.33110367892976583,"#28547A"],[0.33444816053511706,"#28547B"],[0.33779264214046834,"#28557B"],[0.34113712374581934,"#28557C"],[0.34448160535117056,"#28567C"],[0.34782608695652184,"#29567D"],[0.35117056856187284,"#29567D"],[0.35451505016722407,"#29577E"],[0.35785953177257535,"#29577F"],[0.36120401337792646,"#2A587F"],[0.36454849498327757,"#2A5880"],[0.36789297658862885,"#2A5980"],[0.37123745819397996,"#2A5981"],[0.37458193979933108,"#2A5982"],[0.37792642140468236,"#2B5A82"],[0.38127090301003347,"#2B5A83"],[0.38461538461538458,"#2B5B83"],[0.38795986622073586,"#2B5B84"],[0.39130434782608697,"#2C5C85"],[0.39464882943143809,"#2C5C85"],[0.39799331103678937,"#2C5D86"],[0.40133779264214048,"#2C5D86"],[0.40468227424749159,"#2C5D87"],[0.40802675585284287,"#2D5E87"],[0.41137123745819398,"#2D5E88"],[0.4147157190635451,"#2D5F89"],[0.41806020066889638,"#2D5F89"],[0.4214046822742476,"#2E608A"],[0.4247491638795986,"#2E608A"],[0.42809364548494988,"#2E618B"],[0.4314381270903011,"#2E618C"],[0.43478260869565211,"#2E618C"],[0.43812709030100333,"#2F628D"],[0.44147157190635461,"#2F628D"],[0.44481605351170572,"#2F638E"],[0.44816053511705684,"#2F638F"],[0.45150501672240811,"#30648F"],[0.45484949832775923,"#306490"],[0.45819397993311034,"#306590"],[0.46153846153846162,"#306591"],[0.46488294314381273,"#306592"],[0.46822742474916385,"#316692"],[0.47157190635451512,"#316693"],[0.47491638795986624,"#316793"],[0.47826086956521735,"#316794"],[0.48160535117056863,"#326895"],[0.48494983277591974,"#326895"],[0.48829431438127086,"#326996"],[0.49163879598662213,"#326996"],[0.49498327759197325,"#326997"],[0.49832775919732447,"#336A98"],[0.50167224080267558,"#336A98"],[0.50501672240802675,"#336B99"],[0.50836120401337814,"#336B99"],[0.51170568561872909,"#346C9A"],[0.51505016722408015,"#346C9B"],[0.51839464882943143,"#346D9B"],[0.52173913043478259,"#346D9C"],[0.52508361204013387,"#346E9D"],[0.52842809364548515,"#356E9D"],[0.5317725752508361,"#356E9E"],[0.53511705685618738,"#356F9E"],[0.53846153846153844,"#356F9F"],[0.5418060200668896,"#3670A0"],[0.54515050167224088,"#3670A0"],[0.54849498327759216,"#3671A1"],[0.55183946488294311,"#3671A1"],[0.55518394648829439,"#3772A2"],[0.55852842809364545,"#3772A3"],[0.56187290969899661,"#3773A3"],[0.56521739130434789,"#3773A4"],[0.56856187290969917,"#3773A4"],[0.57190635451505012,"#3874A5"],[0.5752508361204014,"#3874A6"],[0.57859531772575246,"#3875A6"],[0.58193979933110362,"#3875A7"],[0.5852842809364549,"#3976A8"],[0.58862876254180607,"#3976A8"],[0.59197324414715735,"#3977A9"],[0.59531772575250841,"#3977A9"],[0.59866220735785947,"#3978AA"],[0.60200668896321063,"#3A78AB"],[0.60535117056856191,"#3A79AB"],[0.60869565217391308,"#3A79AC"],[0.61204013377926436,"#3A79AC"],[0.61538461538461542,"#3B7AAD"],[0.61872909698996648,"#3B7AAE"],[0.62207357859531764,"#3B7BAE"],[0.62541806020066892,"#3B7BAF"],[0.62876254180602009,"#3C7CB0"],[0.63210702341137137,"#3C7CB0"],[0.63545150501672243,"#3C7DB1"],[0.6387959866220736,"#3C7DB1"],[0.64214046822742465,"#3C7EB2"],[0.64548494983277593,"#3D7EB3"],[0.6488294314381271,"#3D7FB3"],[0.65217391304347838,"#3D7FB4"],[0.65551839464882944,"#3D7FB5"],[0.65886287625418061,"#3E80B5"],[0.66220735785953166,"#3E80B6"],[0.66555183946488294,"#3E81B6"],[0.66889632107023411,"#3E81B7"],[0.67224080267558539,"#3F82B8"],[0.67558528428093667,"#3F82B8"],[0.67892976588628762,"#3F83B9"],[0.68227424749163867,"#3F83BA"],[0.68561872909698995,"#4084BA"],[0.68896321070234112,"#4084BB"],[0.6923076923076924,"#4085BB"],[0.69565217391304368,"#4085BC"],[0.69899665551839463,"#4086BD"],[0.70234113712374568,"#4186BD"],[0.70568561872909696,"#4186BE"],[0.70903010033444813,"#4187BF"],[0.71237458193979941,"#4187BF"],[0.71571906354515069,"#4288C0"],[0.71906354515050164,"#4288C1"],[0.72240802675585292,"#4289C1"],[0.72575250836120397,"#4289C2"],[0.72909698996655514,"#438AC2"],[0.73244147157190642,"#438AC3"],[0.7357859531772577,"#438BC4"],[0.73913043478260865,"#438BC4"],[0.74247491638795993,"#438CC5"],[0.74581939799331098,"#448CC6"],[0.74916387959866215,"#448DC6"],[0.75250836120401343,"#448DC7"],[0.75585284280936471,"#448EC8"],[0.75919732441471588,"#458EC8"],[0.76254180602006694,"#458FC9"],[0.76588628762541799,"#458FC9"],[0.76923076923076916,"#458FCA"],[0.77257525083612044,"#4690CB"],[0.77591973244147172,"#4690CB"],[0.77926421404682289,"#4691CC"],[0.78260869565217395,"#4691CD"],[0.785953177257525,"#4792CD"],[0.78929765886287617,"#4792CE"],[0.79264214046822745,"#4793CF"],[0.79598662207357873,"#4793CF"],[0.7993311036789299,"#4894D0"],[0.80267558528428096,"#4894D0"],[0.80602006688963224,"#4895D1"],[0.80936454849498318,"#4895D2"],[0.81270903010033446,"#4896D2"],[0.81605351170568574,"#4996D3"],[0.81939799331103691,"#4997D4"],[0.82274247491638797,"#4997D4"],[0.82608695652173925,"#4998D5"],[0.82943143812709019,"#4A98D6"],[0.83277591973244147,"#4A99D6"],[0.83612040133779275,"#4A99D7"],[0.83946488294314392,"#4A9AD8"],[0.8428093645484952,"#4B9AD8"],[0.84615384615384626,"#4B9BD9"],[0.8494983277591972,"#4B9BDA"],[0.85284280936454848,"#4B9BDA"],[0.85618729096989976,"#4C9CDB"],[0.85953177257525093,"#4C9CDB"],[0.86287625418060221,"#4C9DDC"],[0.86622073578595316,"#4C9DDD"],[0.86956521739130421,"#4D9EDD"],[0.87290969899665549,"#4D9EDE"],[0.87625418060200666,"#4D9FDF"],[0.87959866220735794,"#4D9FDF"],[0.88294314381270922,"#4DA0E0"],[0.88628762541806017,"#4EA0E1"],[0.88963210702341144,"#4EA1E1"],[0.8929765886287625,"#4EA1E2"],[0.89632107023411367,"#4EA2E3"],[0.89966555183946495,"#4FA2E3"],[0.90301003344481623,"#4FA3E4"],[0.90635451505016718,"#4FA3E5"],[0.90969899665551845,"#4FA4E5"],[0.91304347826086951,"#50A4E6"],[0.91638795986622068,"#50A5E7"],[0.91973244147157196,"#50A5E7"],[0.92307692307692324,"#50A6E8"],[0.92642140468227441,"#51A6E8"],[0.92976588628762546,"#51A7E9"],[0.93311036789297652,"#51A7EA"],[0.93645484949832769,"#51A8EA"],[0.93979933110367897,"#52A8EB"],[0.94314381270903025,"#52A9EC"],[0.9464882943143812,"#52A9EC"],[0.94983277591973247,"#52AAED"],[0.95317725752508375,"#53AAEE"],[0.9565217391304347,"#53ABEE"],[0.95986622073578598,"#53ABEF"],[0.96321070234113726,"#53ACF0"],[0.96655518394648821,"#54ACF0"],[0.96989966555183948,"#54ADF1"],[0.97324414715719076,"#54ADF2"],[0.97658862876254171,"#54AEF2"],[0.97993311036789299,"#55AEF3"],[0.98327759197324427,"#55AFF4"],[0.98662207357859544,"#55AFF4"],[0.98996655518394649,"#55B0F5"],[0.99331103678929777,"#56B0F6"],[0.99665551839464894,"#56B1F6"],[1,"#56B1F7"]],"colorbar":{"bgcolor":"rgba(255,255,255,1)","bordercolor":"transparent","borderwidth":1.8897637795275593,"thickness":23.039999999999996,"title":"Income","titlefont":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724},"tickmode":"array","ticktext":["4e+04","6e+04","8e+04","1e+05"],"tickvals":[0.14404761904761904,0.42880952380952375,0.71357142857142852,0.99833333333333329],"tickfont":{"color":"rgba(0,0,0,1)","family":"","size":11.68949771689498},"ticklen":2,"len":0.5,"yanchor":"top","y":1}},"xaxis":"x","yaxis":"y","frame":null}],"layout":{"margin":{"t":26.228310502283104,"r":7.3059360730593621,"b":40.182648401826491,"l":43.105022831050235},"plot_bgcolor":"rgba(235,235,235,1)","paper_bgcolor":"rgba(255,255,255,1)","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[0.17000000000000001,0.83000000000000007],"tickmode":"array","ticktext":["0.2","0.4","0.6","0.8"],"tickvals":[0.20000000000000001,0.40000000000000002,0.60000000000000009,0.80000000000000004],"categoryorder":"array","categoryarray":["0.2","0.4","0.6","0.8"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.6529680365296811,"tickwidth":0.66417600664176002,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.68949771689498},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(255,255,255,1)","gridwidth":0.66417600664176002,"zeroline":false,"anchor":"y","title":{"text":"x","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}},"scaleanchor":"y","scaleratio":1,"hoverformat":".2f"},"yaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[-0.029999999999999999,0.63],"tickmode":"array","ticktext":["0.0","0.2","0.4","0.6"],"tickvals":[0,0.20000000000000004,0.40000000000000013,0.60000000000000009],"categoryorder":"array","categoryarray":["0.0","0.2","0.4","0.6"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.6529680365296811,"tickwidth":0.66417600664176002,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.68949771689498},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(255,255,255,1)","gridwidth":0.66417600664176002,"zeroline":false,"anchor":"x","title":{"text":"y","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}},"scaleanchor":"x","scaleratio":1,"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":null,"line":{"color":null,"width":0,"linetype":[]},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":true,"legend":{"bgcolor":"rgba(255,255,255,1)","bordercolor":"transparent","borderwidth":1.8897637795275593,"font":{"color":"rgba(0,0,0,1)","family":"","size":11.68949771689498},"y":0.5,"yanchor":"top","title":{"text":"Income<br />Zone_ID","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}}},"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"source":"A","attrs":{"31c0b7a1ddcfc":{"xmin":{},"xmax":{},"ymin":{},"ymax":{},"fill":{},"type":"scatter"},"31c0bad6d28":{"x":{},"y":{},"colour":{}}},"cur_data":"31c0b7a1ddcfc","visdat":{"31c0b7a1ddcfc":["function (y) ","x"],"31c0bad6d28":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.20000000000000001,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>
```

What is the mean income in zone 1? What is the mean income in zone 2? Not only are the summary measures of income highly representative of the observations they describe, the two zones are also highly distinct.

Imagine now that for whatever reason (lack of prior knowledge of the process, convenience for data collection, etc.) the zones instead are as follows:

``` r
# Note how the values have changed for x1 and x2. This reveals that the zones have shifted and are no longer the same as the plot above. 

zones2 <- data.frame(x1=c(0.2, 0.55), x2=c(0.55, 0.80), y1=c(0.0, 0.0), y2=c(0.6, 0.6), Zone_ID = c('1','2'))
```

If you plot these zones:

``` r
p <- ggplot() + 
  geom_rect(data = zones2, mapping = aes(xmin = x1, xmax = x2, ymin = y1, ymax = y2, fill = Zone_ID), alpha = 0.3) + 
  geom_point(data = df, aes(x = x, y = y, color = Income), shape = 17, size = 5) +
  coord_fixed()
ggplotly(p)
```

```{=html}
<div class="plotly html-widget html-fill-item" id="htmlwidget-0d095a06ba8569b967f3" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-0d095a06ba8569b967f3">{"x":{"data":[{"x":[0.20000000000000001,0.20000000000000001,0.55000000000000004,0.55000000000000004,0.20000000000000001],"y":[0,0.59999999999999998,0.59999999999999998,0,0],"text":"Zone_ID: 1","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"transparent","dash":"solid"},"fill":"toself","fillcolor":"rgba(248,118,109,0.3)","hoveron":"fills","name":"1","legendgroup":"1","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[0.55000000000000004,0.55000000000000004,0.80000000000000004,0.80000000000000004,0.55000000000000004],"y":[0,0.59999999999999998,0.59999999999999998,0,0],"text":"Zone_ID: 2","type":"scatter","mode":"lines","line":{"width":1.8897637795275593,"color":"transparent","dash":"solid"},"fill":"toself","fillcolor":"rgba(0,191,196,0.3)","hoveron":"fills","name":"2","legendgroup":"2","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[0.29999999999999999,0.40000000000000002,0.5,0.59999999999999998,0.69999999999999996],"y":[0.10000000000000001,0.40000000000000002,0.20000000000000001,0.5,0.29999999999999999],"text":["x: 0.3<br />y: 0.1<br />Income: 3e+04","x: 0.4<br />y: 0.4<br />Income: 3e+04","x: 0.5<br />y: 0.2<br />Income: 1e+05","x: 0.6<br />y: 0.5<br />Income: 1e+05","x: 0.7<br />y: 0.3<br />Income: 1e+05"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":["rgba(19,43,67,1)","rgba(19,43,67,1)","rgba(86,177,247,1)","rgba(86,177,247,1)","rgba(86,177,247,1)"],"opacity":1,"size":18.897637795275593,"symbol":"triangle-up","line":{"width":1.8897637795275593,"color":["rgba(19,43,67,1)","rgba(19,43,67,1)","rgba(86,177,247,1)","rgba(86,177,247,1)","rgba(86,177,247,1)"]}},"hoveron":"points","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[0.20000000000000001],"y":[0],"name":"10c3639fc91899524bb5ad52867283a1","type":"scatter","mode":"markers","opacity":0,"hoverinfo":"skip","showlegend":false,"marker":{"color":[0,1],"colorscale":[[0,"#132B43"],[0.0033444816053511853,"#132B44"],[0.0066889632107023185,"#132C44"],[0.010033444816053503,"#142C45"],[0.013377926421404689,"#142D45"],[0.016722408026755876,"#142D46"],[0.020066889632107007,"#142D46"],[0.023411371237458192,"#142E47"],[0.026755852842809378,"#152E47"],[0.030100334448160512,"#152F48"],[0.033444816053511697,"#152F48"],[0.03678929765886288,"#152F49"],[0.040133779264214013,"#153049"],[0.043478260869565251,"#16304A"],[0.046822742474916385,"#16304A"],[0.050167224080267518,"#16314B"],[0.053511705685618756,"#16314B"],[0.05685618729096989,"#16324C"],[0.060200668896321023,"#17324D"],[0.063545150501672268,"#17324D"],[0.066889632107023395,"#17334E"],[0.070234113712374535,"#17334E"],[0.073578595317725759,"#17344F"],[0.0769230769230769,"#18344F"],[0.080267558528428137,"#183450"],[0.083612040133779264,"#183550"],[0.086956521739130405,"#183551"],[0.090301003344481642,"#183651"],[0.093645484949832769,"#193652"],[0.09698996655518391,"#193652"],[0.10033444816053515,"#193753"],[0.10367892976588627,"#193754"],[0.10702341137123751,"#193854"],[0.11036789297658865,"#1A3855"],[0.11371237458193978,"#1A3955"],[0.11705685618729102,"#1A3956"],[0.12040133779264216,"#1A3956"],[0.12374581939799328,"#1A3A57"],[0.12709030100334454,"#1B3A57"],[0.13043478260869565,"#1B3B58"],[0.13377926421404679,"#1B3B59"],[0.13712374581939804,"#1B3B59"],[0.14046822742474915,"#1C3C5A"],[0.14381270903010029,"#1C3C5A"],[0.14715719063545152,"#1C3D5B"],[0.15050167224080266,"#1C3D5B"],[0.1538461538461538,"#1C3D5C"],[0.15719063545150502,"#1D3E5C"],[0.16053511705685616,"#1D3E5D"],[0.1638795986622073,"#1D3F5D"],[0.16722408026755853,"#1D3F5E"],[0.17056856187290967,"#1D3F5F"],[0.17391304347826092,"#1E405F"],[0.17725752508361203,"#1E4060"],[0.18060200668896328,"#1E4160"],[0.18394648829431443,"#1E4161"],[0.18729096989966554,"#1E4261"],[0.19063545150501679,"#1F4262"],[0.19397993311036793,"#1F4263"],[0.19732441471571904,"#1F4363"],[0.20066889632107029,"#1F4364"],[0.20401337792642144,"#1F4464"],[0.20735785953177255,"#204465"],[0.2107023411371238,"#204465"],[0.21404682274247494,"#204566"],[0.21739130434782605,"#204566"],[0.2207357859531773,"#214667"],[0.22408026755852842,"#214668"],[0.22742474916387956,"#214768"],[0.23076923076923081,"#214769"],[0.23411371237458192,"#214769"],[0.23745819397993306,"#22486A"],[0.24080267558528431,"#22486A"],[0.24414715719063543,"#22496B"],[0.24749163879598657,"#22496C"],[0.25083612040133779,"#224A6C"],[0.25418060200668907,"#234A6D"],[0.25752508361204007,"#234A6D"],[0.2608695652173913,"#234B6E"],[0.26421404682274258,"#234B6E"],[0.26755852842809369,"#244C6F"],[0.2709030100334448,"#244C70"],[0.27424749163879608,"#244C70"],[0.27759197324414719,"#244D71"],[0.28093645484949831,"#244D71"],[0.28428093645484959,"#254E72"],[0.2876254180602007,"#254E72"],[0.29096989966555181,"#254F73"],[0.29431438127090304,"#254F74"],[0.2976588628762542,"#254F74"],[0.30100334448160532,"#265075"],[0.30434782608695654,"#265075"],[0.30769230769230771,"#265176"],[0.31103678929765882,"#265176"],[0.31438127090301005,"#275277"],[0.31772575250836121,"#275278"],[0.32107023411371233,"#275278"],[0.32441471571906355,"#275379"],[0.32775919732441472,"#275379"],[0.33110367892976583,"#28547A"],[0.33444816053511706,"#28547B"],[0.33779264214046834,"#28557B"],[0.34113712374581934,"#28557C"],[0.34448160535117056,"#28567C"],[0.34782608695652184,"#29567D"],[0.35117056856187284,"#29567D"],[0.35451505016722407,"#29577E"],[0.35785953177257535,"#29577F"],[0.36120401337792646,"#2A587F"],[0.36454849498327757,"#2A5880"],[0.36789297658862885,"#2A5980"],[0.37123745819397996,"#2A5981"],[0.37458193979933108,"#2A5982"],[0.37792642140468236,"#2B5A82"],[0.38127090301003347,"#2B5A83"],[0.38461538461538458,"#2B5B83"],[0.38795986622073586,"#2B5B84"],[0.39130434782608697,"#2C5C85"],[0.39464882943143809,"#2C5C85"],[0.39799331103678937,"#2C5D86"],[0.40133779264214048,"#2C5D86"],[0.40468227424749159,"#2C5D87"],[0.40802675585284287,"#2D5E87"],[0.41137123745819398,"#2D5E88"],[0.4147157190635451,"#2D5F89"],[0.41806020066889638,"#2D5F89"],[0.4214046822742476,"#2E608A"],[0.4247491638795986,"#2E608A"],[0.42809364548494988,"#2E618B"],[0.4314381270903011,"#2E618C"],[0.43478260869565211,"#2E618C"],[0.43812709030100333,"#2F628D"],[0.44147157190635461,"#2F628D"],[0.44481605351170572,"#2F638E"],[0.44816053511705684,"#2F638F"],[0.45150501672240811,"#30648F"],[0.45484949832775923,"#306490"],[0.45819397993311034,"#306590"],[0.46153846153846162,"#306591"],[0.46488294314381273,"#306592"],[0.46822742474916385,"#316692"],[0.47157190635451512,"#316693"],[0.47491638795986624,"#316793"],[0.47826086956521735,"#316794"],[0.48160535117056863,"#326895"],[0.48494983277591974,"#326895"],[0.48829431438127086,"#326996"],[0.49163879598662213,"#326996"],[0.49498327759197325,"#326997"],[0.49832775919732447,"#336A98"],[0.50167224080267558,"#336A98"],[0.50501672240802675,"#336B99"],[0.50836120401337814,"#336B99"],[0.51170568561872909,"#346C9A"],[0.51505016722408015,"#346C9B"],[0.51839464882943143,"#346D9B"],[0.52173913043478259,"#346D9C"],[0.52508361204013387,"#346E9D"],[0.52842809364548515,"#356E9D"],[0.5317725752508361,"#356E9E"],[0.53511705685618738,"#356F9E"],[0.53846153846153844,"#356F9F"],[0.5418060200668896,"#3670A0"],[0.54515050167224088,"#3670A0"],[0.54849498327759216,"#3671A1"],[0.55183946488294311,"#3671A1"],[0.55518394648829439,"#3772A2"],[0.55852842809364545,"#3772A3"],[0.56187290969899661,"#3773A3"],[0.56521739130434789,"#3773A4"],[0.56856187290969917,"#3773A4"],[0.57190635451505012,"#3874A5"],[0.5752508361204014,"#3874A6"],[0.57859531772575246,"#3875A6"],[0.58193979933110362,"#3875A7"],[0.5852842809364549,"#3976A8"],[0.58862876254180607,"#3976A8"],[0.59197324414715735,"#3977A9"],[0.59531772575250841,"#3977A9"],[0.59866220735785947,"#3978AA"],[0.60200668896321063,"#3A78AB"],[0.60535117056856191,"#3A79AB"],[0.60869565217391308,"#3A79AC"],[0.61204013377926436,"#3A79AC"],[0.61538461538461542,"#3B7AAD"],[0.61872909698996648,"#3B7AAE"],[0.62207357859531764,"#3B7BAE"],[0.62541806020066892,"#3B7BAF"],[0.62876254180602009,"#3C7CB0"],[0.63210702341137137,"#3C7CB0"],[0.63545150501672243,"#3C7DB1"],[0.6387959866220736,"#3C7DB1"],[0.64214046822742465,"#3C7EB2"],[0.64548494983277593,"#3D7EB3"],[0.6488294314381271,"#3D7FB3"],[0.65217391304347838,"#3D7FB4"],[0.65551839464882944,"#3D7FB5"],[0.65886287625418061,"#3E80B5"],[0.66220735785953166,"#3E80B6"],[0.66555183946488294,"#3E81B6"],[0.66889632107023411,"#3E81B7"],[0.67224080267558539,"#3F82B8"],[0.67558528428093667,"#3F82B8"],[0.67892976588628762,"#3F83B9"],[0.68227424749163867,"#3F83BA"],[0.68561872909698995,"#4084BA"],[0.68896321070234112,"#4084BB"],[0.6923076923076924,"#4085BB"],[0.69565217391304368,"#4085BC"],[0.69899665551839463,"#4086BD"],[0.70234113712374568,"#4186BD"],[0.70568561872909696,"#4186BE"],[0.70903010033444813,"#4187BF"],[0.71237458193979941,"#4187BF"],[0.71571906354515069,"#4288C0"],[0.71906354515050164,"#4288C1"],[0.72240802675585292,"#4289C1"],[0.72575250836120397,"#4289C2"],[0.72909698996655514,"#438AC2"],[0.73244147157190642,"#438AC3"],[0.7357859531772577,"#438BC4"],[0.73913043478260865,"#438BC4"],[0.74247491638795993,"#438CC5"],[0.74581939799331098,"#448CC6"],[0.74916387959866215,"#448DC6"],[0.75250836120401343,"#448DC7"],[0.75585284280936471,"#448EC8"],[0.75919732441471588,"#458EC8"],[0.76254180602006694,"#458FC9"],[0.76588628762541799,"#458FC9"],[0.76923076923076916,"#458FCA"],[0.77257525083612044,"#4690CB"],[0.77591973244147172,"#4690CB"],[0.77926421404682289,"#4691CC"],[0.78260869565217395,"#4691CD"],[0.785953177257525,"#4792CD"],[0.78929765886287617,"#4792CE"],[0.79264214046822745,"#4793CF"],[0.79598662207357873,"#4793CF"],[0.7993311036789299,"#4894D0"],[0.80267558528428096,"#4894D0"],[0.80602006688963224,"#4895D1"],[0.80936454849498318,"#4895D2"],[0.81270903010033446,"#4896D2"],[0.81605351170568574,"#4996D3"],[0.81939799331103691,"#4997D4"],[0.82274247491638797,"#4997D4"],[0.82608695652173925,"#4998D5"],[0.82943143812709019,"#4A98D6"],[0.83277591973244147,"#4A99D6"],[0.83612040133779275,"#4A99D7"],[0.83946488294314392,"#4A9AD8"],[0.8428093645484952,"#4B9AD8"],[0.84615384615384626,"#4B9BD9"],[0.8494983277591972,"#4B9BDA"],[0.85284280936454848,"#4B9BDA"],[0.85618729096989976,"#4C9CDB"],[0.85953177257525093,"#4C9CDB"],[0.86287625418060221,"#4C9DDC"],[0.86622073578595316,"#4C9DDD"],[0.86956521739130421,"#4D9EDD"],[0.87290969899665549,"#4D9EDE"],[0.87625418060200666,"#4D9FDF"],[0.87959866220735794,"#4D9FDF"],[0.88294314381270922,"#4DA0E0"],[0.88628762541806017,"#4EA0E1"],[0.88963210702341144,"#4EA1E1"],[0.8929765886287625,"#4EA1E2"],[0.89632107023411367,"#4EA2E3"],[0.89966555183946495,"#4FA2E3"],[0.90301003344481623,"#4FA3E4"],[0.90635451505016718,"#4FA3E5"],[0.90969899665551845,"#4FA4E5"],[0.91304347826086951,"#50A4E6"],[0.91638795986622068,"#50A5E7"],[0.91973244147157196,"#50A5E7"],[0.92307692307692324,"#50A6E8"],[0.92642140468227441,"#51A6E8"],[0.92976588628762546,"#51A7E9"],[0.93311036789297652,"#51A7EA"],[0.93645484949832769,"#51A8EA"],[0.93979933110367897,"#52A8EB"],[0.94314381270903025,"#52A9EC"],[0.9464882943143812,"#52A9EC"],[0.94983277591973247,"#52AAED"],[0.95317725752508375,"#53AAEE"],[0.9565217391304347,"#53ABEE"],[0.95986622073578598,"#53ABEF"],[0.96321070234113726,"#53ACF0"],[0.96655518394648821,"#54ACF0"],[0.96989966555183948,"#54ADF1"],[0.97324414715719076,"#54ADF2"],[0.97658862876254171,"#54AEF2"],[0.97993311036789299,"#55AEF3"],[0.98327759197324427,"#55AFF4"],[0.98662207357859544,"#55AFF4"],[0.98996655518394649,"#55B0F5"],[0.99331103678929777,"#56B0F6"],[0.99665551839464894,"#56B1F6"],[1,"#56B1F7"]],"colorbar":{"bgcolor":"rgba(255,255,255,1)","bordercolor":"transparent","borderwidth":1.8897637795275593,"thickness":23.039999999999996,"title":"Income","titlefont":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724},"tickmode":"array","ticktext":["4e+04","6e+04","8e+04","1e+05"],"tickvals":[0.14404761904761904,0.42880952380952375,0.71357142857142852,0.99833333333333329],"tickfont":{"color":"rgba(0,0,0,1)","family":"","size":11.68949771689498},"ticklen":2,"len":0.5,"yanchor":"top","y":1}},"xaxis":"x","yaxis":"y","frame":null}],"layout":{"margin":{"t":26.228310502283104,"r":7.3059360730593621,"b":40.182648401826491,"l":43.105022831050235},"plot_bgcolor":"rgba(235,235,235,1)","paper_bgcolor":"rgba(255,255,255,1)","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[0.17000000000000001,0.83000000000000007],"tickmode":"array","ticktext":["0.2","0.4","0.6","0.8"],"tickvals":[0.20000000000000001,0.40000000000000002,0.60000000000000009,0.80000000000000004],"categoryorder":"array","categoryarray":["0.2","0.4","0.6","0.8"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.6529680365296811,"tickwidth":0.66417600664176002,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.68949771689498},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(255,255,255,1)","gridwidth":0.66417600664176002,"zeroline":false,"anchor":"y","title":{"text":"x","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}},"scaleanchor":"y","scaleratio":1,"hoverformat":".2f"},"yaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[-0.029999999999999999,0.63],"tickmode":"array","ticktext":["0.0","0.2","0.4","0.6"],"tickvals":[0,0.20000000000000004,0.40000000000000013,0.60000000000000009],"categoryorder":"array","categoryarray":["0.0","0.2","0.4","0.6"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.6529680365296811,"tickwidth":0.66417600664176002,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.68949771689498},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(255,255,255,1)","gridwidth":0.66417600664176002,"zeroline":false,"anchor":"x","title":{"text":"y","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}},"scaleanchor":"x","scaleratio":1,"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":null,"line":{"color":null,"width":0,"linetype":[]},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":true,"legend":{"bgcolor":"rgba(255,255,255,1)","bordercolor":"transparent","borderwidth":1.8897637795275593,"font":{"color":"rgba(0,0,0,1)","family":"","size":11.68949771689498},"y":0.5,"yanchor":"top","title":{"text":"Income<br />Zone_ID","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}}},"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"source":"A","attrs":{"31c0b346bfe28":{"xmin":{},"xmax":{},"ymin":{},"ymax":{},"fill":{},"type":"scatter"},"31c0b19ca9495":{"x":{},"y":{},"colour":{}}},"cur_data":"31c0b346bfe28","visdat":{"31c0b346bfe28":["function (y) ","x"],"31c0b19ca9495":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.20000000000000001,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>
```

What is now the mean income of zone 1? What is the mean income of zone 2? The observations have not changed, and the generating spatial process remains the same. However, as you can see, the summary measures for the two zones are more similar in this case than they were when the zones more closely captured the underlying process.

## Visualizing Area Data: Choropleth Maps

The very first step when working with spatial area data, perhaps, is to visualize the data.

Commonly, area data are visualized by means of choropleth maps. A choropleth map is a map of the polygons that form the areas in the region, each colored in a way to represent the value of an underlying variable. 

Lets use `ggplot2` to create a choropleth map of population in Hamilton. Notice that the fill color for the polygons is given by cutting the values of `POPULATION` in five equal segments. In other words, the colors represent zones in the bottom 20% of population, zones in the next 20%, and so on, so that the darkest zones are those with populations so large as to be in the top 20% of the population distribution:

``` r
# Geographical information can also be plotted using `ggplot2` when it is in the form of simple features or `sf`. Here, we create a plot with function `ggplot()`. We also have available the census tracts for Hamilton in an `sf` dataframe. To plot the distribution of the population in five equal segments (or quintiles), we apply the function `cut_number()` to the variable `POPULATION` from the `Hamilton_CT` census tract dataframe. The aesthetic value for `fill` will color the zones according to the population quintiles.  

ggplot(Hamilton_CT) + 
  geom_sf(aes(fill = cut_number(POPULATION, 5)), color = NA, size = 0.1) +
  scale_fill_brewer(palette = "YlOrRd") +
  coord_sf() +
  labs(fill = "Population")
```

<img src="19-Area-Data-I_files/figure-html/unnamed-chunk-10-1.png" width="672" />

Inspect the map above. Would you say that the distribution of population is random, or not random? If not random, what do you think might be an underlying process for the distribution of population?

Often, creating a choropleth map using the absolute value of a variable can be somewhat misleading. As illustrated by the map of population by census tract in Hamilton, the zones with the largest population are also often large zones. Many process are confounded by the size of the zones: quite simply, in larger areas often there is more of, well, almost anything, compared with smaller areas. For this reason, it is often more informative when creating a choropleth map to use a variable that is a rate. Rates are quantities that are measured with respect to something. For instance population measured by area, or population density, is a rate:

``` r
# Note how the `cut_number()` is applied to population density rather than population like the figure above. This gives a more different, and perhaps more informative, of the distribution of population, by measuring population against area.

pop_den.map <- ggplot(Hamilton_CT) + 
  geom_sf(aes(fill = cut_number(POP_DENSITY, 5)), color = "white", size = 0.1) +
  scale_fill_brewer(palette = "YlOrRd") +
  labs(fill = "Pop Density")
pop_den.map
```

<img src="19-Area-Data-I_files/figure-html/unnamed-chunk-11-1.png" width="672" />

It can be seen now that the population density is higher in the more central parts of Hamilton, Burlington, Dundas, etc. Does the map look random? If not, what might be an underlying process that explains the variations in population density in a city like Hamilton?

Other times, it is appropriate to standardize instead of by area, by what might be called the _population at risk_. For instance, imagine that we wanted to explore the distribution of the population of older adults (say, 65 and older). In this case, if instead of normalizing by area, we used the total population instead, would remove the "size" effect, giving a rate:

``` r
#The "HAMILTON_CT" dataframe portions ages by category. For this choropleth map, we sum all age categories over 65, and then divide by total population. This measures the population of older adults against total population, to give a proportion (the rate out of a total). 

ggplot(Hamilton_CT) + 
  geom_sf(aes(fill = cut_number((AGE_65_TO_69 +
                                 AGE_70_TO_74 +
                                 AGE_75_TO_79 +
                                 AGE_80_TO_84 +
                                 AGE_MORE_85) / POPULATION, 5)),
          color = NA, 
          size = 0.1) +
  scale_fill_brewer(palette = "YlOrRd") +
  labs(fill = "Prop Age 65+")
```

<img src="19-Area-Data-I_files/figure-html/unnamed-chunk-12-1.png" width="672" />

Do you notice a pattern in the distribution of seniors in the Hamilton, CMA?

There are a few things to keep in mind when creating choropleth maps.

First, what classification scheme to use, with how many classes, and what colors? 

The examples above were all created using a classification scheme based on the _quintiles_ of the distribution. As noted above, these are obtained by dividing the sample into 5 equal parts to give bottom 20%, etc., of observations. The quintiles are a particular form of a statistical summary measure known as _quantiles_. Another example of a quantile is the _median_, which is the value obtained when the sample is divided in two equal sized parts. Other classification schemes may include the mean, standard deviations, and so on. Essentially, a classification scheme defines a way to divide the sample for representation in a choropleth map.

In terms of how many classes to use, often there is little point in using more than six or seven classes, because the human eye cannot distinguish color differences at a much higher resolution.

The colors are a matter of style and preference, but there are coloring schemes that are colorblind safe (see [here](http://colorbrewer2.org/#type=sequential&scheme=BuGn&n=3)). Also, for communication purposes, there are conventions that assign values or meanings to colors. Maps showing results of elections often use the colors of political parties: this is such a widespread convention that it would be thoroughly confusing if the colors were reversed, more so than if just the colors were exchanged for others. Red is often associated with heat, concentration, or sometimes bad, whereas green is associated with good. [Here is an interesting discussion of use of colors in visualization](https://blog.datawrapper.de/colors/).

Secondly, when the zoning system is irregular (as opposed to, say, a raster, which is composed of pixels, regular tiles of consistent size), large zones can easily become dominant. In effect, much detail in the maps above is lost for small zones, whereas large zones, especially if similarly colored, may mislead the eye as to their relative frequency.

Another mapping technique, the cartogram, is meant to reduce the issues with small-large zones.

## Visualizing Area Data: Cartograms

A cartogram is a map where the size of the zones is adjusted so that instead of being the surface area, it is proportional to some other variable of interest.

We will illustrate the idea behind the cartogram here.

In the maps that we created above, the zones are faithful to their geographical properties (subject to distortions due to geographical projection). Unfortunately, this feature of the maps obscured the relevance of some of the smaller zones. A cartogram can be weighted by another variable, say for instance, the population. In this way, the size of the zones will depend on the total population.

Cartograms are implemented in `R` in the package `cartogram`.

``` r
# The function `cartogram_cont()` constructs a continuous area cartogram. Here, a cartogram is created for census tracts of the city of Hamilton, but the size of the zones will be weighted by the variable `POPULATION`.
CT_pop_cartogram <- cartogram_cont(Hamilton_CT, weight = "POPULATION")
```

Plotting the cartogram:

``` r
#We are using "ggplot" to create a cartogram for populations by census tact in Hamilton. Census tracts with a larger value are distorted to visually represent their population size. The number "5" after calling the population variable states that there will be 5 categories dividing population quantities.
ggplot(CT_pop_cartogram) + 
  geom_sf(aes(fill = cut_number(POPULATION, 5)), color = "white", size = 0.1) +
  scale_fill_brewer(palette = "YlOrRd") +
  labs(fill = "Population")
```

<img src="19-Area-Data-I_files/figure-html/unnamed-chunk-14-1.png" width="672" />

Notice how the size of the zones has been adjusted.

The cartogram can be combined with coloring schemes, as in choropleth maps:

``` r
CT_popden_cartogram <- cartogram_cont(Hamilton_CT, weight = "POP_DENSITY")
```

Plot the cartogram:

``` r
pop_den.cartogram <- ggplot(CT_popden_cartogram) + 
  geom_sf(aes(fill = cut_number(POP_DENSITY, 5)),color = "white", size = 0.1) +
  scale_fill_brewer(palette = "YlOrRd") +
  labs(fill = "Pop Density")
pop_den.cartogram
```

<img src="19-Area-Data-I_files/figure-html/unnamed-chunk-16-1.png" width="672" />

By combining a cartogram with choropleth mapping, it becomes easier to appreciate the way high population density is concentrated in the central parts of Hamilton, Burlington, etc.

``` r
grid.arrange(pop_den.map, pop_den.cartogram, nrow = 2)
```

<img src="19-Area-Data-I_files/figure-html/unnamed-chunk-17-1.png" width="672" />

This concludes this chapter.

