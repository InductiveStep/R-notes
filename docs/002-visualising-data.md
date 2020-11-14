
# Visualising data in the tidyverse

By the end of this tutorial you will:

* Have explored a key example visualisation in depth, using packages in the tidyverse.
* Understand what happens when you tweak this in various ways.
* Know where to look for ideas and code for other visualisations.


## Getting setup

You will need to create a new *R* or Markdown (*Rmd*) file (depending on your preference - I recommend Markdown) and save it somewhere sensible where you can find it again in a few months time.

We will be using the [Gapminder dataset](https://github.com/jennybc/gapminder) dataset from last time.


```r
gap <- read.csv("gapminder.csv")
```

Previously we used the "base" plot function:


```r
plot(lifeExp ~ year,
     data = gap,
     xlab = "Year",
     ylab = "Life expectancy at birth")
```

<div class="figure" style="text-align: center">
<img src="002-visualising-data_files/figure-html/unnamed-chunk-2-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-2)**CAPTION THIS FIGURE!!**</p>
</div>

We can do better than this.

A collection of packages called the [tidyverse](https://www.tidyverse.org/) has become an industry standard in R (though see also [an alternate view](https://github.com/matloff/TidyverseSkeptic/blob/master/READMEFull.md)).

This command will include tidyverse and make a bit of noise as it arrives...


```r
library(tidyverse)
```

If that didn't work, there are two things you can do. You could try saving your R/Rmd file. This may prompt RStudio to notice that the package isn't installed and ask you if you want to install it.

Alternatively, just use the `install.packages` command as per last week:


```r
install.packages("tidyverse")
```

To see if that worked, run this:


```r
ggplot(data = gap,
       mapping = aes(x = year, y = lifeExp)) +
  geom_point() +
  labs(x = "Year",
       y = "Life expectancy at birth")
```

<div class="figure" style="text-align: center">
<img src="002-visualising-data_files/figure-html/unnamed-chunk-5-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-5)**CAPTION THIS FIGURE!!**</p>
</div>

Ta-da: a graph! This used `ggplot`, which is part of the `ggplot2` package which, in turn, is part of `tidyverse`.

The rest of this tutorial will explore how to develop this into a more useful visualisation.


## An interlude on functions

Previously, I described R *functions* as magical computational machines which take inputs and transform them in some way, giving an output.

Above, we have seen that the output of a function can be a picture. It can also be a vibration (that's how sounds are made) or anything else that can be plugged into a computer. It might be a humble number, like a mean.

Sometimes I'll call functions "commands" and sometimes I'll call the inputs "options" or "parameters" or "arguments". Hopefully it will be clear from the context what I mean. If not, even after scratching your head, then do ask!


## A scatterplot in ggplot {.tabset}

Let's build the previous example step-by-step.


```r
ggplot(data = gap,
       mapping = aes(x = year, y = lifeExp))
```

<div class="figure" style="text-align: center">
<img src="002-visualising-data_files/figure-html/unnamed-chunk-6-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-6)**CAPTION THIS FIGURE!!**</p>
</div>

This first part tells `ggplot` what data to use and an aesthetic mapping. *Aesthetics* in tidyverse are properties of the objects in your plot and the mapping tells `ggplot` how those objects relate to your data. Two basic properties are *x* and *y* locations on a plot. Here they have been mapped to year and life expectancy, respectively.

When you run that code, you will see that nothing was actually done with the mappings. The next stage is to add a *geom* -- a geometric object -- for each row of data. That's where the point geom, `geom_point`, comes in:


```r
ggplot(data = gap,
       mapping = aes(x = year, y = lifeExp)) +
  geom_point()
```

<div class="figure" style="text-align: center">
<img src="002-visualising-data_files/figure-html/unnamed-chunk-7-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-7)**CAPTION THIS FIGURE!!**</p>
</div>

Note how the "+" symbol is used here to mean adding elements to a plot. The meaning of "+" depends on context.

I could also have made this plot by giving a name to the first part:


```r
the_basic_plot <- ggplot(data = gap,
                  mapping = aes(x = year, y = lifeExp))
```

Then added this to the geom:


```r
plot_with_stuff_on <- the_basic_plot + geom_point()
```

The plot hasn't displayed yet, though.


### Warm-up activity

(a) What do you need to do to get the `plot_with_stuff_on` plot to display?

(b) How could you change the axis labels on `plot_with_stuff_on`? (Look up for a clue!)


### Answer

(a) What do you need to do to get the plot to display?


```r
plot_with_stuff_on
```

<div class="figure" style="text-align: center">
<img src="002-visualising-data_files/figure-html/unnamed-chunk-10-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-10)**CAPTION THIS FIGURE!!**</p>
</div>

(b) How could you change the axis labels on `plot_with_stuff_on`?

Either:


```r
plot_with_stuff_on + 
  labs(x = "Year",
       y = "Life expectancy at birth")
```

<div class="figure" style="text-align: center">
<img src="002-visualising-data_files/figure-html/unnamed-chunk-11-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-11)**CAPTION THIS FIGURE!!**</p>
</div>

Or:


```r
final_plot <- plot_with_stuff_on + 
                labs(x = "Year",
                     y = "Life expectancy at birth")
final_plot
```

## Another aesthetic: colour

This is a simple change, but begins to highlight patterns in the data. Here I have just copied and pasted a chunk from above and added the mapping `colour = continent`.


```r
ggplot(data = gap,
       mapping = aes(x = year,
                     y = lifeExp,
                     colour = continent)) +
  geom_point() +
  labs(x = "Year",
       y = "Life expectancy at birth")
```

<div class="figure" style="text-align: center">
<img src="002-visualising-data_files/figure-html/unnamed-chunk-13-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-13)**CAPTION THIS FIGURE!!**</p>
</div>

Can you spot any patterns in the graph?

A legend has appeared at the right hand side explaining what the colours represent.

By default the legend title is the same as the variable name. In this case it's "continent" which is clear, but sometimes it will be something like "group_2_id" which is less pleasing on the eye (and I cringe when I see something like this in a journal article).

The legend title is easy to change by adding another option to `labs`:


```r
ggplot(data = gap,
       mapping = aes(x = year,
                     y = lifeExp,
                     colour = continent)) +
  geom_point() +
  labs(x = "Year",
       y = "Life expectancy at birth",
       colour = "Continent")
```

<div class="figure" style="text-align: center">
<img src="002-visualising-data_files/figure-html/unnamed-chunk-14-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-14)**CAPTION THIS FIGURE!!**</p>
</div>


## Another geom: jitter {.tabset}

Making graphs often involves playing around with different ways of showing the information. Here's the *jitter* geom, which is the same as the point geom but with "a small amount of random variation to the location of each point" (see `?geom_jitter`).


```r
ggplot(data = gap,
       mapping = aes(x = year,
                     y = lifeExp,
                     colour = continent)) +
  geom_jitter() +
  labs(x = "Year",
       y = "Life expectancy at birth",
       colour = "Continent")
```

<div class="figure" style="text-align: center">
<img src="002-visualising-data_files/figure-html/unnamed-chunk-15-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-15)**CAPTION THIS FIGURE!!**</p>
</div>

### Activity to develop your help-searching skill!

How can you vary the amount of jitter?

Tip: you might find the help useful:


```r
?geom_jitter
```

Alternatively, try Google.

If that doesn't deliver anything useful, try [this reference link](https://ggplot2.tidyverse.org/reference/geom_jitter.html#examples).


### Answer

There are two options, `width` and `height`, which specify how wide the jitteriness is. Set these to zero, and the plot is indistinguishable from the point geom:


```r
ggplot(data = gap,
       mapping = aes(x = year,
                     y = lifeExp,
                     colour = continent)) +
  geom_jitter(width = 0, height = 0) +
  labs(x = "Year",
       y = "Life expectancy at birth",
       color = "Continent")
```

<div class="figure" style="text-align: center">
<img src="002-visualising-data_files/figure-html/unnamed-chunk-17-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-17)**CAPTION THIS FIGURE!!**</p>
</div>

Here's a little jitter added only to the width:


```r
ggplot(data = gap,
       mapping = aes(x = year, y = lifeExp, colour = continent)) +
  geom_jitter(width = 1, height = 0) +
  labs(x = "Year",
       y = "Life expectancy at birth",
       color = "Continent")
```

<div class="figure" style="text-align: center">
<img src="002-visualising-data_files/figure-html/unnamed-chunk-18-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-18)**CAPTION THIS FIGURE!!**</p>
</div>


## Aggregating/summarising data by group {.tabset}

Last time, we saw how to calculate the mean of a variable. Here's the mean of life expectancy, across all countries and years:


```r
mean(gap$lifeExp)
```

```
## [1] 59.47444
```

I don't know what to make of that!

Typically we want to calculate means by group rather than for a whole variable. This is known as *aggregating* or *summarising* by group. For instance, looking at the plots above it seems that there will be a mean difference in life expectancy between continents, and it would be interesting to see that.

For this, we will use `dplyr` (pronounced "DEE-ply-er"). It's part of `tidyverse` so already included, but it's useful to know the name of this specific part for when you are searching for help.

I'm going to work through an example in excruciating detail, but it will be worth it I promise.

The punchline is that to calculate mean life expectancy by year and continent, you do this:


```r
mean_life_exp_gap <- gap %>%
  group_by(year, continent) %>%
  summarise(mean_life_exp = mean(lifeExp))
```

```
## `summarise()` regrouping output by 'year' (override with `.groups` argument)
```

(Have a look and see.)


Here's a longer worked example.

**Step 1.** Use `group_by` to tell R what variables you want to group the data by. The first parameter of `group_by` is the dataset you want to group. The remaining parameters are the variables in that dataset to group by:


```r
grouped_gap <- group_by(gap, year, continent)
```

So this says, group the `gap` data frame by `year` and `continent`.

This new variable, `grouped_gap` is a *grouped data frame*. It has all the same information as before, plus a little note (semi-hidden) to say that analyses on this should be grouped.

Here's how to peek at this note:


```r
group_vars(grouped_gap)
```

```
## [1] "year"      "continent"
```

**Step 2.** Use `summarise` on this grouped data frame to calculate what you want. The first argument of summarise is the data frame (grouped or otherwise) followed by new variable names and what you want them to contain.


```r
summarised_grouped_gap <- summarise(grouped_gap,
                                    mean_life_exp = mean(lifeExp))
```

```
## `summarise()` regrouping output by 'year' (override with `.groups` argument)
```

Let's have a look at the top 10 rows:


```r
head(summarised_grouped_gap, 10)
```

```
## # A tibble: 10 x 3
## # Groups:   year [2]
##     year continent mean_life_exp
##    <int> <chr>             <dbl>
##  1  1952 Africa             39.1
##  2  1952 Americas           53.3
##  3  1952 Asia               46.3
##  4  1952 Europe             64.4
##  5  1952 Oceania            69.3
##  6  1957 Africa             41.3
##  7  1957 Americas           56.0
##  8  1957 Asia               49.3
##  9  1957 Europe             66.7
## 10  1957 Oceania            70.3
```

It worked! We could now use this in `ggplot` (and shall do so below).


### Activity

Do the same again but this time calculate means only by year, averaging across continents.

### Answer


```r
grouped_gap_year <- group_by(gap, year)
summarised_grouped_year <- summarise(grouped_gap_year,
                                     mean_life_exp = mean(lifeExp))
```

```
## `summarise()` ungrouping output (override with `.groups` argument)
```

```r
summarised_grouped_year
```

```
## # A tibble: 12 x 2
##     year mean_life_exp
##    <int>         <dbl>
##  1  1952          49.1
##  2  1957          51.5
##  3  1962          53.6
##  4  1967          55.7
##  5  1972          57.6
##  6  1977          59.6
##  7  1982          61.5
##  8  1987          63.2
##  9  1992          64.2
## 10  1997          65.0
## 11  2002          65.7
## 12  2007          67.0
```


## Pipes

R analyses often feel like making information flow along a pipe, transforming it in various ways as it goes. Maybe reshaping it, selecting some variables, filtering, grouping, calculating. Finally, out flows an answer.

This leads to another member of the tidyverse family, `magrittr`, named after René Magritte because of his [1929 painting](https://en.wikipedia.org/wiki/The_Treachery_of_Images) showing a pipe and a caption "Ceci n'est pas une pipe" ("This is not a pipe").

![](MagrittePipe.jpg)


You may have noticed that both `group_by` and `summarise` had a data frame as their first argument. They also both outputted a data frame. 

The *forward pipe operator*, `%>%`, allows you to pass the data frame along your information flow, without having to save results in interim variables.

You start with the name of the input data frame and then pipe it into the first function. For example, here is how to group the data:


```r
gap %>%
  group_by(year, continent)
```

As before you can then save the result:


```r
grouped <- gap %>%
  group_by(year, continent)
```

To flow this onto summarise, just add another pipe like so:


```r
grouped <- gap %>%
  group_by(year, continent) %>%
  summarise(mean_life_exp = mean(lifeExp))
```

```
## `summarise()` regrouping output by 'year' (override with `.groups` argument)
```

The `%>%` is purely designed to make the flow of information easier to see and hopefully also easier to design.


## Plot the mean life expectancy by continent {.tabset}

By here you hopefully get the gist of how to use pipes to group data frames and summarise them. There will be further opportunities to practice this skill.

Here's an aggregated data frame with mean life expectancy by year and continent:


```r
mean_life_exp_gap <- gap %>%
  group_by(year, continent) %>%
  summarise(mean_life_exp = mean(lifeExp))
```

```
## `summarise()` regrouping output by 'year' (override with `.groups` argument)
```

You can view this to check the information is as you expect:


```r
View(mean_life_exp_gap)
```

Here are the variable names, for ease of reference.


```r
names(mean_life_exp_gap)
```

```
## [1] "year"          "continent"     "mean_life_exp"
```


### Actvity

Now your challenge is to plot the mean life expectancy by year, with colour showing the continent.

You could try adapting an example from above to help you.

### Answer


```r
ggplot(mean_life_exp_gap, aes(x = year,
                              y = mean_life_exp,
                              colour = continent)) +
  geom_point()
```

<div class="figure" style="text-align: center">
<img src="002-visualising-data_files/figure-html/unnamed-chunk-32-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-32)**CAPTION THIS FIGURE!!**</p>
</div>


## Yet another geom: line {.tabset}

Instead of plotting points for each year, you may wish to join the data with lines. Here's how -- just use `geom_line`:


```r
ggplot(mean_life_exp_gap, aes(x = year,
                              y = mean_life_exp,
                              colour = continent)) +
  geom_line() +
  labs(x = "Year",
     y = "Life expectancy at birth",
     colour = "Continent")
```

<div class="figure" style="text-align: center">
<img src="002-visualising-data_files/figure-html/unnamed-chunk-33-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-33)**CAPTION THIS FIGURE!!**</p>
</div>


### Activity

How could you add points back to the lines?

### Answer

Simply use "+" again:


```r
ggplot(mean_life_exp_gap, aes(x = year,
                              y = mean_life_exp,
                              colour = continent)) +
  geom_point() +
  geom_line()
```

<div class="figure" style="text-align: center">
<img src="002-visualising-data_files/figure-html/unnamed-chunk-34-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-34)**CAPTION THIS FIGURE!!**</p>
</div>

I've been a bit lazy here and haven't bothered changing the axis labels and legend title. That is fine when playing around with different visualisations and learning. Just remember to tidy it all up before adding to a written report!


## Filtering data along the pipeline {.tabset}

Analysing by continent clearly doesn't do the data justice: in the jittered points we saw there was loads of variation within continent.

The mean plots highlighted that improvement in life expectancy in Africa stalled around 1990. I wonder if this was the same for all countries therein?

The next tidyverse function we will explore to help us is called `filter`. (See the help for lots of examples using a Star Wars dataset.)

Here is how to filter the data so we only have rows for Africa:


```r
gap %>% 
  filter(continent == "Africa")
```

```
##                      country continent year lifeExp       pop  gdpPercap
## 1                    Algeria    Africa 1952  43.077   9279525  2449.0082
## 2                    Algeria    Africa 1957  45.685  10270856  3013.9760
## 3                    Algeria    Africa 1962  48.303  11000948  2550.8169
## 4                    Algeria    Africa 1967  51.407  12760499  3246.9918
## 5                    Algeria    Africa 1972  54.518  14760787  4182.6638
## 6                    Algeria    Africa 1977  58.014  17152804  4910.4168
## 7                    Algeria    Africa 1982  61.368  20033753  5745.1602
## 8                    Algeria    Africa 1987  65.799  23254956  5681.3585
## 9                    Algeria    Africa 1992  67.744  26298373  5023.2166
## 10                   Algeria    Africa 1997  69.152  29072015  4797.2951
## 11                   Algeria    Africa 2002  70.994  31287142  5288.0404
## 12                   Algeria    Africa 2007  72.301  33333216  6223.3675
## 13                    Angola    Africa 1952  30.015   4232095  3520.6103
## 14                    Angola    Africa 1957  31.999   4561361  3827.9405
## 15                    Angola    Africa 1962  34.000   4826015  4269.2767
## 16                    Angola    Africa 1967  35.985   5247469  5522.7764
## 17                    Angola    Africa 1972  37.928   5894858  5473.2880
## 18                    Angola    Africa 1977  39.483   6162675  3008.6474
## 19                    Angola    Africa 1982  39.942   7016384  2756.9537
## 20                    Angola    Africa 1987  39.906   7874230  2430.2083
## 21                    Angola    Africa 1992  40.647   8735988  2627.8457
## 22                    Angola    Africa 1997  40.963   9875024  2277.1409
## 23                    Angola    Africa 2002  41.003  10866106  2773.2873
## 24                    Angola    Africa 2007  42.731  12420476  4797.2313
## 25                     Benin    Africa 1952  38.223   1738315  1062.7522
## 26                     Benin    Africa 1957  40.358   1925173   959.6011
## 27                     Benin    Africa 1962  42.618   2151895   949.4991
## 28                     Benin    Africa 1967  44.885   2427334  1035.8314
## 29                     Benin    Africa 1972  47.014   2761407  1085.7969
## 30                     Benin    Africa 1977  49.190   3168267  1029.1613
## 31                     Benin    Africa 1982  50.904   3641603  1277.8976
## 32                     Benin    Africa 1987  52.337   4243788  1225.8560
## 33                     Benin    Africa 1992  53.919   4981671  1191.2077
## 34                     Benin    Africa 1997  54.777   6066080  1232.9753
## 35                     Benin    Africa 2002  54.406   7026113  1372.8779
## 36                     Benin    Africa 2007  56.728   8078314  1441.2849
## 37                  Botswana    Africa 1952  47.622    442308   851.2411
## 38                  Botswana    Africa 1957  49.618    474639   918.2325
## 39                  Botswana    Africa 1962  51.520    512764   983.6540
## 40                  Botswana    Africa 1967  53.298    553541  1214.7093
## 41                  Botswana    Africa 1972  56.024    619351  2263.6111
## 42                  Botswana    Africa 1977  59.319    781472  3214.8578
## 43                  Botswana    Africa 1982  61.484    970347  4551.1421
## 44                  Botswana    Africa 1987  63.622   1151184  6205.8839
## 45                  Botswana    Africa 1992  62.745   1342614  7954.1116
## 46                  Botswana    Africa 1997  52.556   1536536  8647.1423
## 47                  Botswana    Africa 2002  46.634   1630347 11003.6051
## 48                  Botswana    Africa 2007  50.728   1639131 12569.8518
## 49              Burkina Faso    Africa 1952  31.975   4469979   543.2552
## 50              Burkina Faso    Africa 1957  34.906   4713416   617.1835
## 51              Burkina Faso    Africa 1962  37.814   4919632   722.5120
## 52              Burkina Faso    Africa 1967  40.697   5127935   794.8266
## 53              Burkina Faso    Africa 1972  43.591   5433886   854.7360
## 54              Burkina Faso    Africa 1977  46.137   5889574   743.3870
## 55              Burkina Faso    Africa 1982  48.122   6634596   807.1986
## 56              Burkina Faso    Africa 1987  49.557   7586551   912.0631
## 57              Burkina Faso    Africa 1992  50.260   8878303   931.7528
## 58              Burkina Faso    Africa 1997  50.324  10352843   946.2950
## 59              Burkina Faso    Africa 2002  50.650  12251209  1037.6452
## 60              Burkina Faso    Africa 2007  52.295  14326203  1217.0330
## 61                   Burundi    Africa 1952  39.031   2445618   339.2965
## 62                   Burundi    Africa 1957  40.533   2667518   379.5646
## 63                   Burundi    Africa 1962  42.045   2961915   355.2032
## 64                   Burundi    Africa 1967  43.548   3330989   412.9775
## 65                   Burundi    Africa 1972  44.057   3529983   464.0995
## 66                   Burundi    Africa 1977  45.910   3834415   556.1033
## 67                   Burundi    Africa 1982  47.471   4580410   559.6032
## 68                   Burundi    Africa 1987  48.211   5126023   621.8188
## 69                   Burundi    Africa 1992  44.736   5809236   631.6999
## 70                   Burundi    Africa 1997  45.326   6121610   463.1151
## 71                   Burundi    Africa 2002  47.360   7021078   446.4035
## 72                   Burundi    Africa 2007  49.580   8390505   430.0707
## 73                  Cameroon    Africa 1952  38.523   5009067  1172.6677
## 74                  Cameroon    Africa 1957  40.428   5359923  1313.0481
## 75                  Cameroon    Africa 1962  42.643   5793633  1399.6074
## 76                  Cameroon    Africa 1967  44.799   6335506  1508.4531
## 77                  Cameroon    Africa 1972  47.049   7021028  1684.1465
## 78                  Cameroon    Africa 1977  49.355   7959865  1783.4329
## 79                  Cameroon    Africa 1982  52.961   9250831  2367.9833
## 80                  Cameroon    Africa 1987  54.985  10780667  2602.6642
## 81                  Cameroon    Africa 1992  54.314  12467171  1793.1633
## 82                  Cameroon    Africa 1997  52.199  14195809  1694.3375
## 83                  Cameroon    Africa 2002  49.856  15929988  1934.0114
## 84                  Cameroon    Africa 2007  50.430  17696293  2042.0952
## 85  Central African Republic    Africa 1952  35.463   1291695  1071.3107
## 86  Central African Republic    Africa 1957  37.464   1392284  1190.8443
## 87  Central African Republic    Africa 1962  39.475   1523478  1193.0688
## 88  Central African Republic    Africa 1967  41.478   1733638  1136.0566
## 89  Central African Republic    Africa 1972  43.457   1927260  1070.0133
## 90  Central African Republic    Africa 1977  46.775   2167533  1109.3743
## 91  Central African Republic    Africa 1982  48.295   2476971   956.7530
## 92  Central African Republic    Africa 1987  50.485   2840009   844.8764
## 93  Central African Republic    Africa 1992  49.396   3265124   747.9055
## 94  Central African Republic    Africa 1997  46.066   3696513   740.5063
## 95  Central African Republic    Africa 2002  43.308   4048013   738.6906
## 96  Central African Republic    Africa 2007  44.741   4369038   706.0165
## 97                      Chad    Africa 1952  38.092   2682462  1178.6659
## 98                      Chad    Africa 1957  39.881   2894855  1308.4956
## 99                      Chad    Africa 1962  41.716   3150417  1389.8176
## 100                     Chad    Africa 1967  43.601   3495967  1196.8106
## 101                     Chad    Africa 1972  45.569   3899068  1104.1040
## 102                     Chad    Africa 1977  47.383   4388260  1133.9850
## 103                     Chad    Africa 1982  49.517   4875118   797.9081
## 104                     Chad    Africa 1987  51.051   5498955   952.3861
## 105                     Chad    Africa 1992  51.724   6429417  1058.0643
## 106                     Chad    Africa 1997  51.573   7562011  1004.9614
## 107                     Chad    Africa 2002  50.525   8835739  1156.1819
## 108                     Chad    Africa 2007  50.651  10238807  1704.0637
## 109                  Comoros    Africa 1952  40.715    153936  1102.9909
## 110                  Comoros    Africa 1957  42.460    170928  1211.1485
## 111                  Comoros    Africa 1962  44.467    191689  1406.6483
## 112                  Comoros    Africa 1967  46.472    217378  1876.0296
## 113                  Comoros    Africa 1972  48.944    250027  1937.5777
## 114                  Comoros    Africa 1977  50.939    304739  1172.6030
## 115                  Comoros    Africa 1982  52.933    348643  1267.1001
## 116                  Comoros    Africa 1987  54.926    395114  1315.9808
## 117                  Comoros    Africa 1992  57.939    454429  1246.9074
## 118                  Comoros    Africa 1997  60.660    527982  1173.6182
## 119                  Comoros    Africa 2002  62.974    614382  1075.8116
## 120                  Comoros    Africa 2007  65.152    710960   986.1479
## 121         Congo, Dem. Rep.    Africa 1952  39.143  14100005   780.5423
## 122         Congo, Dem. Rep.    Africa 1957  40.652  15577932   905.8602
## 123         Congo, Dem. Rep.    Africa 1962  42.122  17486434   896.3146
## 124         Congo, Dem. Rep.    Africa 1967  44.056  19941073   861.5932
## 125         Congo, Dem. Rep.    Africa 1972  45.989  23007669   904.8961
## 126         Congo, Dem. Rep.    Africa 1977  47.804  26480870   795.7573
## 127         Congo, Dem. Rep.    Africa 1982  47.784  30646495   673.7478
## 128         Congo, Dem. Rep.    Africa 1987  47.412  35481645   672.7748
## 129         Congo, Dem. Rep.    Africa 1992  45.548  41672143   457.7192
## 130         Congo, Dem. Rep.    Africa 1997  42.587  47798986   312.1884
## 131         Congo, Dem. Rep.    Africa 2002  44.966  55379852   241.1659
## 132         Congo, Dem. Rep.    Africa 2007  46.462  64606759   277.5519
## 133              Congo, Rep.    Africa 1952  42.111    854885  2125.6214
## 134              Congo, Rep.    Africa 1957  45.053    940458  2315.0566
## 135              Congo, Rep.    Africa 1962  48.435   1047924  2464.7832
## 136              Congo, Rep.    Africa 1967  52.040   1179760  2677.9396
## 137              Congo, Rep.    Africa 1972  54.907   1340458  3213.1527
## 138              Congo, Rep.    Africa 1977  55.625   1536769  3259.1790
## 139              Congo, Rep.    Africa 1982  56.695   1774735  4879.5075
## 140              Congo, Rep.    Africa 1987  57.470   2064095  4201.1949
## 141              Congo, Rep.    Africa 1992  56.433   2409073  4016.2395
## 142              Congo, Rep.    Africa 1997  52.962   2800947  3484.1644
## 143              Congo, Rep.    Africa 2002  52.970   3328795  3484.0620
## 144              Congo, Rep.    Africa 2007  55.322   3800610  3632.5578
## 145            Cote d'Ivoire    Africa 1952  40.477   2977019  1388.5947
## 146            Cote d'Ivoire    Africa 1957  42.469   3300000  1500.8959
## 147            Cote d'Ivoire    Africa 1962  44.930   3832408  1728.8694
## 148            Cote d'Ivoire    Africa 1967  47.350   4744870  2052.0505
## 149            Cote d'Ivoire    Africa 1972  49.801   6071696  2378.2011
## 150            Cote d'Ivoire    Africa 1977  52.374   7459574  2517.7365
## 151            Cote d'Ivoire    Africa 1982  53.983   9025951  2602.7102
## 152            Cote d'Ivoire    Africa 1987  54.655  10761098  2156.9561
## 153            Cote d'Ivoire    Africa 1992  52.044  12772596  1648.0738
## 154            Cote d'Ivoire    Africa 1997  47.991  14625967  1786.2654
## 155            Cote d'Ivoire    Africa 2002  46.832  16252726  1648.8008
## 156            Cote d'Ivoire    Africa 2007  48.328  18013409  1544.7501
## 157                 Djibouti    Africa 1952  34.812     63149  2669.5295
## 158                 Djibouti    Africa 1957  37.328     71851  2864.9691
## 159                 Djibouti    Africa 1962  39.693     89898  3020.9893
## 160                 Djibouti    Africa 1967  42.074    127617  3020.0505
## 161                 Djibouti    Africa 1972  44.366    178848  3694.2124
## 162                 Djibouti    Africa 1977  46.519    228694  3081.7610
## 163                 Djibouti    Africa 1982  48.812    305991  2879.4681
## 164                 Djibouti    Africa 1987  50.040    311025  2880.1026
## 165                 Djibouti    Africa 1992  51.604    384156  2377.1562
## 166                 Djibouti    Africa 1997  53.157    417908  1895.0170
## 167                 Djibouti    Africa 2002  53.373    447416  1908.2609
## 168                 Djibouti    Africa 2007  54.791    496374  2082.4816
## 169                    Egypt    Africa 1952  41.893  22223309  1418.8224
## 170                    Egypt    Africa 1957  44.444  25009741  1458.9153
## 171                    Egypt    Africa 1962  46.992  28173309  1693.3359
## 172                    Egypt    Africa 1967  49.293  31681188  1814.8807
## 173                    Egypt    Africa 1972  51.137  34807417  2024.0081
## 174                    Egypt    Africa 1977  53.319  38783863  2785.4936
## 175                    Egypt    Africa 1982  56.006  45681811  3503.7296
## 176                    Egypt    Africa 1987  59.797  52799062  3885.4607
## 177                    Egypt    Africa 1992  63.674  59402198  3794.7552
## 178                    Egypt    Africa 1997  67.217  66134291  4173.1818
## 179                    Egypt    Africa 2002  69.806  73312559  4754.6044
## 180                    Egypt    Africa 2007  71.338  80264543  5581.1810
## 181        Equatorial Guinea    Africa 1952  34.482    216964   375.6431
## 182        Equatorial Guinea    Africa 1957  35.983    232922   426.0964
## 183        Equatorial Guinea    Africa 1962  37.485    249220   582.8420
## 184        Equatorial Guinea    Africa 1967  38.987    259864   915.5960
## 185        Equatorial Guinea    Africa 1972  40.516    277603   672.4123
## 186        Equatorial Guinea    Africa 1977  42.024    192675   958.5668
## 187        Equatorial Guinea    Africa 1982  43.662    285483   927.8253
## 188        Equatorial Guinea    Africa 1987  45.664    341244   966.8968
## 189        Equatorial Guinea    Africa 1992  47.545    387838  1132.0550
## 190        Equatorial Guinea    Africa 1997  48.245    439971  2814.4808
## 191        Equatorial Guinea    Africa 2002  49.348    495627  7703.4959
## 192        Equatorial Guinea    Africa 2007  51.579    551201 12154.0897
## 193                  Eritrea    Africa 1952  35.928   1438760   328.9406
## 194                  Eritrea    Africa 1957  38.047   1542611   344.1619
## 195                  Eritrea    Africa 1962  40.158   1666618   380.9958
## 196                  Eritrea    Africa 1967  42.189   1820319   468.7950
## 197                  Eritrea    Africa 1972  44.142   2260187   514.3242
## 198                  Eritrea    Africa 1977  44.535   2512642   505.7538
## 199                  Eritrea    Africa 1982  43.890   2637297   524.8758
## 200                  Eritrea    Africa 1987  46.453   2915959   521.1341
## 201                  Eritrea    Africa 1992  49.991   3668440   582.8585
## 202                  Eritrea    Africa 1997  53.378   4058319   913.4708
## 203                  Eritrea    Africa 2002  55.240   4414865   765.3500
## 204                  Eritrea    Africa 2007  58.040   4906585   641.3695
## 205                 Ethiopia    Africa 1952  34.078  20860941   362.1463
## 206                 Ethiopia    Africa 1957  36.667  22815614   378.9042
## 207                 Ethiopia    Africa 1962  40.059  25145372   419.4564
## 208                 Ethiopia    Africa 1967  42.115  27860297   516.1186
## 209                 Ethiopia    Africa 1972  43.515  30770372   566.2439
## 210                 Ethiopia    Africa 1977  44.510  34617799   556.8084
## 211                 Ethiopia    Africa 1982  44.916  38111756   577.8607
## 212                 Ethiopia    Africa 1987  46.684  42999530   573.7413
## 213                 Ethiopia    Africa 1992  48.091  52088559   421.3535
## 214                 Ethiopia    Africa 1997  49.402  59861301   515.8894
## 215                 Ethiopia    Africa 2002  50.725  67946797   530.0535
## 216                 Ethiopia    Africa 2007  52.947  76511887   690.8056
## 217                    Gabon    Africa 1952  37.003    420702  4293.4765
## 218                    Gabon    Africa 1957  38.999    434904  4976.1981
## 219                    Gabon    Africa 1962  40.489    455661  6631.4592
## 220                    Gabon    Africa 1967  44.598    489004  8358.7620
## 221                    Gabon    Africa 1972  48.690    537977 11401.9484
## 222                    Gabon    Africa 1977  52.790    706367 21745.5733
## 223                    Gabon    Africa 1982  56.564    753874 15113.3619
## 224                    Gabon    Africa 1987  60.190    880397 11864.4084
## 225                    Gabon    Africa 1992  61.366    985739 13522.1575
## 226                    Gabon    Africa 1997  60.461   1126189 14722.8419
## 227                    Gabon    Africa 2002  56.761   1299304 12521.7139
## 228                    Gabon    Africa 2007  56.735   1454867 13206.4845
## 229                   Gambia    Africa 1952  30.000    284320   485.2307
## 230                   Gambia    Africa 1957  32.065    323150   520.9267
## 231                   Gambia    Africa 1962  33.896    374020   599.6503
## 232                   Gambia    Africa 1967  35.857    439593   734.7829
## 233                   Gambia    Africa 1972  38.308    517101   756.0868
## 234                   Gambia    Africa 1977  41.842    608274   884.7553
## 235                   Gambia    Africa 1982  45.580    715523   835.8096
## 236                   Gambia    Africa 1987  49.265    848406   611.6589
## 237                   Gambia    Africa 1992  52.644   1025384   665.6244
## 238                   Gambia    Africa 1997  55.861   1235767   653.7302
## 239                   Gambia    Africa 2002  58.041   1457766   660.5856
## 240                   Gambia    Africa 2007  59.448   1688359   752.7497
## 241                    Ghana    Africa 1952  43.149   5581001   911.2989
## 242                    Ghana    Africa 1957  44.779   6391288  1043.5615
## 243                    Ghana    Africa 1962  46.452   7355248  1190.0411
## 244                    Ghana    Africa 1967  48.072   8490213  1125.6972
## 245                    Ghana    Africa 1972  49.875   9354120  1178.2237
## 246                    Ghana    Africa 1977  51.756  10538093   993.2240
## 247                    Ghana    Africa 1982  53.744  11400338   876.0326
## 248                    Ghana    Africa 1987  55.729  14168101   847.0061
## 249                    Ghana    Africa 1992  57.501  16278738   925.0602
## 250                    Ghana    Africa 1997  58.556  18418288  1005.2458
## 251                    Ghana    Africa 2002  58.453  20550751  1111.9846
## 252                    Ghana    Africa 2007  60.022  22873338  1327.6089
## 253                   Guinea    Africa 1952  33.609   2664249   510.1965
## 254                   Guinea    Africa 1957  34.558   2876726   576.2670
## 255                   Guinea    Africa 1962  35.753   3140003   686.3737
## 256                   Guinea    Africa 1967  37.197   3451418   708.7595
## 257                   Guinea    Africa 1972  38.842   3811387   741.6662
## 258                   Guinea    Africa 1977  40.762   4227026   874.6859
## 259                   Guinea    Africa 1982  42.891   4710497   857.2504
## 260                   Guinea    Africa 1987  45.552   5650262   805.5725
## 261                   Guinea    Africa 1992  48.576   6990574   794.3484
## 262                   Guinea    Africa 1997  51.455   8048834   869.4498
## 263                   Guinea    Africa 2002  53.676   8807818   945.5836
## 264                   Guinea    Africa 2007  56.007   9947814   942.6542
## 265            Guinea-Bissau    Africa 1952  32.500    580653   299.8503
## 266            Guinea-Bissau    Africa 1957  33.489    601095   431.7905
## 267            Guinea-Bissau    Africa 1962  34.488    627820   522.0344
## 268            Guinea-Bissau    Africa 1967  35.492    601287   715.5806
## 269            Guinea-Bissau    Africa 1972  36.486    625361   820.2246
## 270            Guinea-Bissau    Africa 1977  37.465    745228   764.7260
## 271            Guinea-Bissau    Africa 1982  39.327    825987   838.1240
## 272            Guinea-Bissau    Africa 1987  41.245    927524   736.4154
## 273            Guinea-Bissau    Africa 1992  43.266   1050938   745.5399
## 274            Guinea-Bissau    Africa 1997  44.873   1193708   796.6645
## 275            Guinea-Bissau    Africa 2002  45.504   1332459   575.7047
## 276            Guinea-Bissau    Africa 2007  46.388   1472041   579.2317
## 277                    Kenya    Africa 1952  42.270   6464046   853.5409
## 278                    Kenya    Africa 1957  44.686   7454779   944.4383
## 279                    Kenya    Africa 1962  47.949   8678557   896.9664
## 280                    Kenya    Africa 1967  50.654  10191512  1056.7365
## 281                    Kenya    Africa 1972  53.559  12044785  1222.3600
## 282                    Kenya    Africa 1977  56.155  14500404  1267.6132
## 283                    Kenya    Africa 1982  58.766  17661452  1348.2258
## 284                    Kenya    Africa 1987  59.339  21198082  1361.9369
## 285                    Kenya    Africa 1992  59.285  25020539  1341.9217
## 286                    Kenya    Africa 1997  54.407  28263827  1360.4850
## 287                    Kenya    Africa 2002  50.992  31386842  1287.5147
## 288                    Kenya    Africa 2007  54.110  35610177  1463.2493
## 289                  Lesotho    Africa 1952  42.138    748747   298.8462
## 290                  Lesotho    Africa 1957  45.047    813338   335.9971
## 291                  Lesotho    Africa 1962  47.747    893143   411.8006
## 292                  Lesotho    Africa 1967  48.492    996380   498.6390
## 293                  Lesotho    Africa 1972  49.767   1116779   496.5816
## 294                  Lesotho    Africa 1977  52.208   1251524   745.3695
## 295                  Lesotho    Africa 1982  55.078   1411807   797.2631
## 296                  Lesotho    Africa 1987  57.180   1599200   773.9932
## 297                  Lesotho    Africa 1992  59.685   1803195   977.4863
## 298                  Lesotho    Africa 1997  55.558   1982823  1186.1480
## 299                  Lesotho    Africa 2002  44.593   2046772  1275.1846
## 300                  Lesotho    Africa 2007  42.592   2012649  1569.3314
## 301                  Liberia    Africa 1952  38.480    863308   575.5730
## 302                  Liberia    Africa 1957  39.486    975950   620.9700
## 303                  Liberia    Africa 1962  40.502   1112796   634.1952
## 304                  Liberia    Africa 1967  41.536   1279406   713.6036
## 305                  Liberia    Africa 1972  42.614   1482628   803.0055
## 306                  Liberia    Africa 1977  43.764   1703617   640.3224
## 307                  Liberia    Africa 1982  44.852   1956875   572.1996
## 308                  Liberia    Africa 1987  46.027   2269414   506.1139
## 309                  Liberia    Africa 1992  40.802   1912974   636.6229
## 310                  Liberia    Africa 1997  42.221   2200725   609.1740
## 311                  Liberia    Africa 2002  43.753   2814651   531.4824
## 312                  Liberia    Africa 2007  45.678   3193942   414.5073
## 313                    Libya    Africa 1952  42.723   1019729  2387.5481
## 314                    Libya    Africa 1957  45.289   1201578  3448.2844
## 315                    Libya    Africa 1962  47.808   1441863  6757.0308
## 316                    Libya    Africa 1967  50.227   1759224 18772.7517
## 317                    Libya    Africa 1972  52.773   2183877 21011.4972
## 318                    Libya    Africa 1977  57.442   2721783 21951.2118
## 319                    Libya    Africa 1982  62.155   3344074 17364.2754
## 320                    Libya    Africa 1987  66.234   3799845 11770.5898
## 321                    Libya    Africa 1992  68.755   4364501  9640.1385
## 322                    Libya    Africa 1997  71.555   4759670  9467.4461
## 323                    Libya    Africa 2002  72.737   5368585  9534.6775
## 324                    Libya    Africa 2007  73.952   6036914 12057.4993
## 325               Madagascar    Africa 1952  36.681   4762912  1443.0117
## 326               Madagascar    Africa 1957  38.865   5181679  1589.2027
## 327               Madagascar    Africa 1962  40.848   5703324  1643.3871
## 328               Madagascar    Africa 1967  42.881   6334556  1634.0473
## 329               Madagascar    Africa 1972  44.851   7082430  1748.5630
## 330               Madagascar    Africa 1977  46.881   8007166  1544.2286
## 331               Madagascar    Africa 1982  48.969   9171477  1302.8787
## 332               Madagascar    Africa 1987  49.350  10568642  1155.4419
## 333               Madagascar    Africa 1992  52.214  12210395  1040.6762
## 334               Madagascar    Africa 1997  54.978  14165114   986.2959
## 335               Madagascar    Africa 2002  57.286  16473477   894.6371
## 336               Madagascar    Africa 2007  59.443  19167654  1044.7701
## 337                   Malawi    Africa 1952  36.256   2917802   369.1651
## 338                   Malawi    Africa 1957  37.207   3221238   416.3698
## 339                   Malawi    Africa 1962  38.410   3628608   427.9011
## 340                   Malawi    Africa 1967  39.487   4147252   495.5148
## 341                   Malawi    Africa 1972  41.766   4730997   584.6220
## 342                   Malawi    Africa 1977  43.767   5637246   663.2237
## 343                   Malawi    Africa 1982  45.642   6502825   632.8039
## 344                   Malawi    Africa 1987  47.457   7824747   635.5174
## 345                   Malawi    Africa 1992  49.420  10014249   563.2000
## 346                   Malawi    Africa 1997  47.495  10419991   692.2758
## 347                   Malawi    Africa 2002  45.009  11824495   665.4231
## 348                   Malawi    Africa 2007  48.303  13327079   759.3499
## 349                     Mali    Africa 1952  33.685   3838168   452.3370
## 350                     Mali    Africa 1957  35.307   4241884   490.3822
## 351                     Mali    Africa 1962  36.936   4690372   496.1743
## 352                     Mali    Africa 1967  38.487   5212416   545.0099
## 353                     Mali    Africa 1972  39.977   5828158   581.3689
## 354                     Mali    Africa 1977  41.714   6491649   686.3953
## 355                     Mali    Africa 1982  43.916   6998256   618.0141
## 356                     Mali    Africa 1987  46.364   7634008   684.1716
## 357                     Mali    Africa 1992  48.388   8416215   739.0144
## 358                     Mali    Africa 1997  49.903   9384984   790.2580
## 359                     Mali    Africa 2002  51.818  10580176   951.4098
## 360                     Mali    Africa 2007  54.467  12031795  1042.5816
## 361               Mauritania    Africa 1952  40.543   1022556   743.1159
## 362               Mauritania    Africa 1957  42.338   1076852   846.1203
## 363               Mauritania    Africa 1962  44.248   1146757  1055.8960
## 364               Mauritania    Africa 1967  46.289   1230542  1421.1452
## 365               Mauritania    Africa 1972  48.437   1332786  1586.8518
## 366               Mauritania    Africa 1977  50.852   1456688  1497.4922
## 367               Mauritania    Africa 1982  53.599   1622136  1481.1502
## 368               Mauritania    Africa 1987  56.145   1841240  1421.6036
## 369               Mauritania    Africa 1992  58.333   2119465  1361.3698
## 370               Mauritania    Africa 1997  60.430   2444741  1483.1361
## 371               Mauritania    Africa 2002  62.247   2828858  1579.0195
## 372               Mauritania    Africa 2007  64.164   3270065  1803.1515
## 373                Mauritius    Africa 1952  50.986    516556  1967.9557
## 374                Mauritius    Africa 1957  58.089    609816  2034.0380
## 375                Mauritius    Africa 1962  60.246    701016  2529.0675
## 376                Mauritius    Africa 1967  61.557    789309  2475.3876
## 377                Mauritius    Africa 1972  62.944    851334  2575.4842
## 378                Mauritius    Africa 1977  64.930    913025  3710.9830
## 379                Mauritius    Africa 1982  66.711    992040  3688.0377
## 380                Mauritius    Africa 1987  68.740   1042663  4783.5869
## 381                Mauritius    Africa 1992  69.745   1096202  6058.2538
## 382                Mauritius    Africa 1997  70.736   1149818  7425.7053
## 383                Mauritius    Africa 2002  71.954   1200206  9021.8159
## 384                Mauritius    Africa 2007  72.801   1250882 10956.9911
## 385                  Morocco    Africa 1952  42.873   9939217  1688.2036
## 386                  Morocco    Africa 1957  45.423  11406350  1642.0023
## 387                  Morocco    Africa 1962  47.924  13056604  1566.3535
## 388                  Morocco    Africa 1967  50.335  14770296  1711.0448
## 389                  Morocco    Africa 1972  52.862  16660670  1930.1950
## 390                  Morocco    Africa 1977  55.730  18396941  2370.6200
## 391                  Morocco    Africa 1982  59.650  20198730  2702.6204
## 392                  Morocco    Africa 1987  62.677  22987397  2755.0470
## 393                  Morocco    Africa 1992  65.393  25798239  2948.0473
## 394                  Morocco    Africa 1997  67.660  28529501  2982.1019
## 395                  Morocco    Africa 2002  69.615  31167783  3258.4956
## 396                  Morocco    Africa 2007  71.164  33757175  3820.1752
## 397               Mozambique    Africa 1952  31.286   6446316   468.5260
## 398               Mozambique    Africa 1957  33.779   7038035   495.5868
## 399               Mozambique    Africa 1962  36.161   7788944   556.6864
## 400               Mozambique    Africa 1967  38.113   8680909   566.6692
## 401               Mozambique    Africa 1972  40.328   9809596   724.9178
## 402               Mozambique    Africa 1977  42.495  11127868   502.3197
## 403               Mozambique    Africa 1982  42.795  12587223   462.2114
## 404               Mozambique    Africa 1987  42.861  12891952   389.8762
## 405               Mozambique    Africa 1992  44.284  13160731   410.8968
## 406               Mozambique    Africa 1997  46.344  16603334   472.3461
## 407               Mozambique    Africa 2002  44.026  18473780   633.6179
## 408               Mozambique    Africa 2007  42.082  19951656   823.6856
## 409                  Namibia    Africa 1952  41.725    485831  2423.7804
## 410                  Namibia    Africa 1957  45.226    548080  2621.4481
## 411                  Namibia    Africa 1962  48.386    621392  3173.2156
## 412                  Namibia    Africa 1967  51.159    706640  3793.6948
## 413                  Namibia    Africa 1972  53.867    821782  3746.0809
## 414                  Namibia    Africa 1977  56.437    977026  3876.4860
## 415                  Namibia    Africa 1982  58.968   1099010  4191.1005
## 416                  Namibia    Africa 1987  60.835   1278184  3693.7313
## 417                  Namibia    Africa 1992  61.999   1554253  3804.5380
## 418                  Namibia    Africa 1997  58.909   1774766  3899.5243
## 419                  Namibia    Africa 2002  51.479   1972153  4072.3248
## 420                  Namibia    Africa 2007  52.906   2055080  4811.0604
## 421                    Niger    Africa 1952  37.444   3379468   761.8794
## 422                    Niger    Africa 1957  38.598   3692184   835.5234
## 423                    Niger    Africa 1962  39.487   4076008   997.7661
## 424                    Niger    Africa 1967  40.118   4534062  1054.3849
## 425                    Niger    Africa 1972  40.546   5060262   954.2092
## 426                    Niger    Africa 1977  41.291   5682086   808.8971
## 427                    Niger    Africa 1982  42.598   6437188   909.7221
## 428                    Niger    Africa 1987  44.555   7332638   668.3000
## 429                    Niger    Africa 1992  47.391   8392818   581.1827
## 430                    Niger    Africa 1997  51.313   9666252   580.3052
## 431                    Niger    Africa 2002  54.496  11140655   601.0745
## 432                    Niger    Africa 2007  56.867  12894865   619.6769
## 433                  Nigeria    Africa 1952  36.324  33119096  1077.2819
## 434                  Nigeria    Africa 1957  37.802  37173340  1100.5926
## 435                  Nigeria    Africa 1962  39.360  41871351  1150.9275
## 436                  Nigeria    Africa 1967  41.040  47287752  1014.5141
## 437                  Nigeria    Africa 1972  42.821  53740085  1698.3888
## 438                  Nigeria    Africa 1977  44.514  62209173  1981.9518
## 439                  Nigeria    Africa 1982  45.826  73039376  1576.9738
## 440                  Nigeria    Africa 1987  46.886  81551520  1385.0296
## 441                  Nigeria    Africa 1992  47.472  93364244  1619.8482
## 442                  Nigeria    Africa 1997  47.464 106207839  1624.9413
## 443                  Nigeria    Africa 2002  46.608 119901274  1615.2864
## 444                  Nigeria    Africa 2007  46.859 135031164  2013.9773
## 445                  Reunion    Africa 1952  52.724    257700  2718.8853
## 446                  Reunion    Africa 1957  55.090    308700  2769.4518
## 447                  Reunion    Africa 1962  57.666    358900  3173.7233
## 448                  Reunion    Africa 1967  60.542    414024  4021.1757
## 449                  Reunion    Africa 1972  64.274    461633  5047.6586
## 450                  Reunion    Africa 1977  67.064    492095  4319.8041
## 451                  Reunion    Africa 1982  69.885    517810  5267.2194
## 452                  Reunion    Africa 1987  71.913    562035  5303.3775
## 453                  Reunion    Africa 1992  73.615    622191  6101.2558
## 454                  Reunion    Africa 1997  74.772    684810  6071.9414
## 455                  Reunion    Africa 2002  75.744    743981  6316.1652
## 456                  Reunion    Africa 2007  76.442    798094  7670.1226
## 457                   Rwanda    Africa 1952  40.000   2534927   493.3239
## 458                   Rwanda    Africa 1957  41.500   2822082   540.2894
## 459                   Rwanda    Africa 1962  43.000   3051242   597.4731
## 460                   Rwanda    Africa 1967  44.100   3451079   510.9637
## 461                   Rwanda    Africa 1972  44.600   3992121   590.5807
## 462                   Rwanda    Africa 1977  45.000   4657072   670.0806
## 463                   Rwanda    Africa 1982  46.218   5507565   881.5706
## 464                   Rwanda    Africa 1987  44.020   6349365   847.9912
## 465                   Rwanda    Africa 1992  23.599   7290203   737.0686
## 466                   Rwanda    Africa 1997  36.087   7212583   589.9445
## 467                   Rwanda    Africa 2002  43.413   7852401   785.6538
## 468                   Rwanda    Africa 2007  46.242   8860588   863.0885
## 469    Sao Tome and Principe    Africa 1952  46.471     60011   879.5836
## 470    Sao Tome and Principe    Africa 1957  48.945     61325   860.7369
## 471    Sao Tome and Principe    Africa 1962  51.893     65345  1071.5511
## 472    Sao Tome and Principe    Africa 1967  54.425     70787  1384.8406
## 473    Sao Tome and Principe    Africa 1972  56.480     76595  1532.9853
## 474    Sao Tome and Principe    Africa 1977  58.550     86796  1737.5617
## 475    Sao Tome and Principe    Africa 1982  60.351     98593  1890.2181
## 476    Sao Tome and Principe    Africa 1987  61.728    110812  1516.5255
## 477    Sao Tome and Principe    Africa 1992  62.742    125911  1428.7778
## 478    Sao Tome and Principe    Africa 1997  63.306    145608  1339.0760
## 479    Sao Tome and Principe    Africa 2002  64.337    170372  1353.0924
## 480    Sao Tome and Principe    Africa 2007  65.528    199579  1598.4351
## 481                  Senegal    Africa 1952  37.278   2755589  1450.3570
## 482                  Senegal    Africa 1957  39.329   3054547  1567.6530
## 483                  Senegal    Africa 1962  41.454   3430243  1654.9887
## 484                  Senegal    Africa 1967  43.563   3965841  1612.4046
## 485                  Senegal    Africa 1972  45.815   4588696  1597.7121
## 486                  Senegal    Africa 1977  48.879   5260855  1561.7691
## 487                  Senegal    Africa 1982  52.379   6147783  1518.4800
## 488                  Senegal    Africa 1987  55.769   7171347  1441.7207
## 489                  Senegal    Africa 1992  58.196   8307920  1367.8994
## 490                  Senegal    Africa 1997  60.187   9535314  1392.3683
## 491                  Senegal    Africa 2002  61.600  10870037  1519.6353
## 492                  Senegal    Africa 2007  63.062  12267493  1712.4721
## 493             Sierra Leone    Africa 1952  30.331   2143249   879.7877
## 494             Sierra Leone    Africa 1957  31.570   2295678  1004.4844
## 495             Sierra Leone    Africa 1962  32.767   2467895  1116.6399
## 496             Sierra Leone    Africa 1967  34.113   2662190  1206.0435
## 497             Sierra Leone    Africa 1972  35.400   2879013  1353.7598
## 498             Sierra Leone    Africa 1977  36.788   3140897  1348.2852
## 499             Sierra Leone    Africa 1982  38.445   3464522  1465.0108
## 500             Sierra Leone    Africa 1987  40.006   3868905  1294.4478
## 501             Sierra Leone    Africa 1992  38.333   4260884  1068.6963
## 502             Sierra Leone    Africa 1997  39.897   4578212   574.6482
## 503             Sierra Leone    Africa 2002  41.012   5359092   699.4897
## 504             Sierra Leone    Africa 2007  42.568   6144562   862.5408
## 505                  Somalia    Africa 1952  32.978   2526994  1135.7498
## 506                  Somalia    Africa 1957  34.977   2780415  1258.1474
## 507                  Somalia    Africa 1962  36.981   3080153  1369.4883
## 508                  Somalia    Africa 1967  38.977   3428839  1284.7332
## 509                  Somalia    Africa 1972  40.973   3840161  1254.5761
## 510                  Somalia    Africa 1977  41.974   4353666  1450.9925
## 511                  Somalia    Africa 1982  42.955   5828892  1176.8070
## 512                  Somalia    Africa 1987  44.501   6921858  1093.2450
## 513                  Somalia    Africa 1992  39.658   6099799   926.9603
## 514                  Somalia    Africa 1997  43.795   6633514   930.5964
## 515                  Somalia    Africa 2002  45.936   7753310   882.0818
## 516                  Somalia    Africa 2007  48.159   9118773   926.1411
## 517             South Africa    Africa 1952  45.009  14264935  4725.2955
## 518             South Africa    Africa 1957  47.985  16151549  5487.1042
## 519             South Africa    Africa 1962  49.951  18356657  5768.7297
## 520             South Africa    Africa 1967  51.927  20997321  7114.4780
## 521             South Africa    Africa 1972  53.696  23935810  7765.9626
## 522             South Africa    Africa 1977  55.527  27129932  8028.6514
## 523             South Africa    Africa 1982  58.161  31140029  8568.2662
## 524             South Africa    Africa 1987  60.834  35933379  7825.8234
## 525             South Africa    Africa 1992  61.888  39964159  7225.0693
## 526             South Africa    Africa 1997  60.236  42835005  7479.1882
## 527             South Africa    Africa 2002  53.365  44433622  7710.9464
## 528             South Africa    Africa 2007  49.339  43997828  9269.6578
## 529                    Sudan    Africa 1952  38.635   8504667  1615.9911
## 530                    Sudan    Africa 1957  39.624   9753392  1770.3371
## 531                    Sudan    Africa 1962  40.870  11183227  1959.5938
## 532                    Sudan    Africa 1967  42.858  12716129  1687.9976
## 533                    Sudan    Africa 1972  45.083  14597019  1659.6528
## 534                    Sudan    Africa 1977  47.800  17104986  2202.9884
## 535                    Sudan    Africa 1982  50.338  20367053  1895.5441
## 536                    Sudan    Africa 1987  51.744  24725960  1507.8192
## 537                    Sudan    Africa 1992  53.556  28227588  1492.1970
## 538                    Sudan    Africa 1997  55.373  32160729  1632.2108
## 539                    Sudan    Africa 2002  56.369  37090298  1993.3983
## 540                    Sudan    Africa 2007  58.556  42292929  2602.3950
## 541                Swaziland    Africa 1952  41.407    290243  1148.3766
## 542                Swaziland    Africa 1957  43.424    326741  1244.7084
## 543                Swaziland    Africa 1962  44.992    370006  1856.1821
## 544                Swaziland    Africa 1967  46.633    420690  2613.1017
## 545                Swaziland    Africa 1972  49.552    480105  3364.8366
## 546                Swaziland    Africa 1977  52.537    551425  3781.4106
## 547                Swaziland    Africa 1982  55.561    649901  3895.3840
## 548                Swaziland    Africa 1987  57.678    779348  3984.8398
## 549                Swaziland    Africa 1992  58.474    962344  3553.0224
## 550                Swaziland    Africa 1997  54.289   1054486  3876.7685
## 551                Swaziland    Africa 2002  43.869   1130269  4128.1169
## 552                Swaziland    Africa 2007  39.613   1133066  4513.4806
## 553                 Tanzania    Africa 1952  41.215   8322925   716.6501
## 554                 Tanzania    Africa 1957  42.974   9452826   698.5356
## 555                 Tanzania    Africa 1962  44.246  10863958   722.0038
## 556                 Tanzania    Africa 1967  45.757  12607312   848.2187
## 557                 Tanzania    Africa 1972  47.620  14706593   915.9851
## 558                 Tanzania    Africa 1977  49.919  17129565   962.4923
## 559                 Tanzania    Africa 1982  50.608  19844382   874.2426
## 560                 Tanzania    Africa 1987  51.535  23040630   831.8221
## 561                 Tanzania    Africa 1992  50.440  26605473   825.6825
## 562                 Tanzania    Africa 1997  48.466  30686889   789.1862
## 563                 Tanzania    Africa 2002  49.651  34593779   899.0742
## 564                 Tanzania    Africa 2007  52.517  38139640  1107.4822
## 565                     Togo    Africa 1952  38.596   1219113   859.8087
## 566                     Togo    Africa 1957  41.208   1357445   925.9083
## 567                     Togo    Africa 1962  43.922   1528098  1067.5348
## 568                     Togo    Africa 1967  46.769   1735550  1477.5968
## 569                     Togo    Africa 1972  49.759   2056351  1649.6602
## 570                     Togo    Africa 1977  52.887   2308582  1532.7770
## 571                     Togo    Africa 1982  55.471   2644765  1344.5780
## 572                     Togo    Africa 1987  56.941   3154264  1202.2014
## 573                     Togo    Africa 1992  58.061   3747553  1034.2989
## 574                     Togo    Africa 1997  58.390   4320890   982.2869
## 575                     Togo    Africa 2002  57.561   4977378   886.2206
## 576                     Togo    Africa 2007  58.420   5701579   882.9699
## 577                  Tunisia    Africa 1952  44.600   3647735  1468.4756
## 578                  Tunisia    Africa 1957  47.100   3950849  1395.2325
## 579                  Tunisia    Africa 1962  49.579   4286552  1660.3032
## 580                  Tunisia    Africa 1967  52.053   4786986  1932.3602
## 581                  Tunisia    Africa 1972  55.602   5303507  2753.2860
## 582                  Tunisia    Africa 1977  59.837   6005061  3120.8768
## 583                  Tunisia    Africa 1982  64.048   6734098  3560.2332
## 584                  Tunisia    Africa 1987  66.894   7724976  3810.4193
## 585                  Tunisia    Africa 1992  70.001   8523077  4332.7202
## 586                  Tunisia    Africa 1997  71.973   9231669  4876.7986
## 587                  Tunisia    Africa 2002  73.042   9770575  5722.8957
## 588                  Tunisia    Africa 2007  73.923  10276158  7092.9230
## 589                   Uganda    Africa 1952  39.978   5824797   734.7535
## 590                   Uganda    Africa 1957  42.571   6675501   774.3711
## 591                   Uganda    Africa 1962  45.344   7688797   767.2717
## 592                   Uganda    Africa 1967  48.051   8900294   908.9185
## 593                   Uganda    Africa 1972  51.016  10190285   950.7359
## 594                   Uganda    Africa 1977  50.350  11457758   843.7331
## 595                   Uganda    Africa 1982  49.849  12939400   682.2662
## 596                   Uganda    Africa 1987  51.509  15283050   617.7244
## 597                   Uganda    Africa 1992  48.825  18252190   644.1708
## 598                   Uganda    Africa 1997  44.578  21210254   816.5591
## 599                   Uganda    Africa 2002  47.813  24739869   927.7210
## 600                   Uganda    Africa 2007  51.542  29170398  1056.3801
## 601                   Zambia    Africa 1952  42.038   2672000  1147.3888
## 602                   Zambia    Africa 1957  44.077   3016000  1311.9568
## 603                   Zambia    Africa 1962  46.023   3421000  1452.7258
## 604                   Zambia    Africa 1967  47.768   3900000  1777.0773
## 605                   Zambia    Africa 1972  50.107   4506497  1773.4983
## 606                   Zambia    Africa 1977  51.386   5216550  1588.6883
## 607                   Zambia    Africa 1982  51.821   6100407  1408.6786
## 608                   Zambia    Africa 1987  50.821   7272406  1213.3151
## 609                   Zambia    Africa 1992  46.100   8381163  1210.8846
## 610                   Zambia    Africa 1997  40.238   9417789  1071.3538
## 611                   Zambia    Africa 2002  39.193  10595811  1071.6139
## 612                   Zambia    Africa 2007  42.384  11746035  1271.2116
## 613                 Zimbabwe    Africa 1952  48.451   3080907   406.8841
## 614                 Zimbabwe    Africa 1957  50.469   3646340   518.7643
## 615                 Zimbabwe    Africa 1962  52.358   4277736   527.2722
## 616                 Zimbabwe    Africa 1967  53.995   4995432   569.7951
## 617                 Zimbabwe    Africa 1972  55.635   5861135   799.3622
## 618                 Zimbabwe    Africa 1977  57.674   6642107   685.5877
## 619                 Zimbabwe    Africa 1982  60.363   7636524   788.8550
## 620                 Zimbabwe    Africa 1987  62.351   9216418   706.1573
## 621                 Zimbabwe    Africa 1992  60.377  10704340   693.4208
## 622                 Zimbabwe    Africa 1997  46.809  11404948   792.4500
## 623                 Zimbabwe    Africa 2002  39.989  11926563   672.0386
## 624                 Zimbabwe    Africa 2007  43.487  12311143   469.7093
```

Note the double equals, `==`, not to be confused with `=` which is used to set inputs (also known as arguments). To see how it works, compare:


```r
11 + 3 == 14
```

```
## [1] TRUE
```

And:


```r
11 + 3 == 2
```

```
## [1] FALSE
```

Now I'm going to try piping this filtered data frame directly into `ggplot`, without saving it. This should work because `ggplot`'s first argument is the data frame.


```r
gap %>% 
  filter(continent == "Africa") %>%
  ggplot(aes(x = year, y = lifeExp, colour = country)) +
    geom_point() +
    geom_line()
```

<div class="figure" style="text-align: center">
<img src="002-visualising-data_files/figure-html/unnamed-chunk-38-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-38)**CAPTION THIS FIGURE!!**</p>
</div>

Well... it did... but the plot is very busy and I'm not sure I could distinguish between all those colours!

Let's try again without the legend to see what's going on.

At this point you may wonder, "How on earth will I be able to remember all these commands?" I will share a trick.

![](google_hide_legend.png)

Attempt 2:


```r
gap %>% 
  filter(continent == "Africa") %>%
  ggplot(aes(x = year, y = lifeExp, colour = country)) +
    geom_point() +
    geom_line() +
    theme(legend.position = "none")
```

<div class="figure" style="text-align: center">
<img src="002-visualising-data_files/figure-html/unnamed-chunk-39-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-39)**CAPTION THIS FIGURE!!**</p>
</div>

### Activity

One of the countries' life expectancies dropped below 25. Can you work out which one it was by using `filter`?

Tip: `==` was equals. You can use `<` for less than.


```r
2 < 3
```

```
## [1] TRUE
```


### Answer


```r
gap %>%
  filter(lifeExp < 25)
```

```
##   country continent year lifeExp     pop gdpPercap
## 1  Rwanda    Africa 1992  23.599 7290203  737.0686
```

So the answer is Rwanda.


## Other handy tools: select, slice, bind, and arrange

Often you will have datasets with a huge number of variables and will want to select a few of those to make the tables easier to read. The command for that is `select`; give it the names of the variables you want.

Another useful function is `arrange` which sorts a data frames by the variable(s) you provide.

Here is an example illustrating both. I have also added the operator `&` for "and".


```r
gap %>%
  filter(year == 2007 &
           continent == "Africa") %>%
  arrange(lifeExp) %>%
  select(country, lifeExp)
```

```
##                     country lifeExp
## 1                 Swaziland  39.613
## 2                Mozambique  42.082
## 3                    Zambia  42.384
## 4              Sierra Leone  42.568
## 5                   Lesotho  42.592
## 6                    Angola  42.731
## 7                  Zimbabwe  43.487
## 8  Central African Republic  44.741
## 9                   Liberia  45.678
## 10                   Rwanda  46.242
## 11            Guinea-Bissau  46.388
## 12         Congo, Dem. Rep.  46.462
## 13                  Nigeria  46.859
## 14                  Somalia  48.159
## 15                   Malawi  48.303
## 16            Cote d'Ivoire  48.328
## 17             South Africa  49.339
## 18                  Burundi  49.580
## 19                 Cameroon  50.430
## 20                     Chad  50.651
## 21                 Botswana  50.728
## 22                   Uganda  51.542
## 23        Equatorial Guinea  51.579
## 24             Burkina Faso  52.295
## 25                 Tanzania  52.517
## 26                  Namibia  52.906
## 27                 Ethiopia  52.947
## 28                    Kenya  54.110
## 29                     Mali  54.467
## 30                 Djibouti  54.791
## 31              Congo, Rep.  55.322
## 32                   Guinea  56.007
## 33                    Benin  56.728
## 34                    Gabon  56.735
## 35                    Niger  56.867
## 36                  Eritrea  58.040
## 37                     Togo  58.420
## 38                    Sudan  58.556
## 39               Madagascar  59.443
## 40                   Gambia  59.448
## 41                    Ghana  60.022
## 42                  Senegal  63.062
## 43               Mauritania  64.164
## 44                  Comoros  65.152
## 45    Sao Tome and Principe  65.528
## 46                  Morocco  71.164
## 47                    Egypt  71.338
## 48                  Algeria  72.301
## 49                Mauritius  72.801
## 50                  Tunisia  73.923
## 51                    Libya  73.952
## 52                  Reunion  76.442
```

This filters `gap` to data from 2007 *and* Africa, sorts it by life expectancy, and then selects the country and life expectancy variables.

The `slice` family of functions can be used to zoom into to the top or bottom slices of rows, or a random sample.

Here's an example. First save the previous chunk results above in `africa2007`:


```r
africa2007 <- gap %>%
  filter(year == 2007 &
           continent == "Africa") %>%
  arrange(lifeExp) %>%
  select(country, lifeExp)
```

The following R code saves the "head" of the dataset, which has the lowest life expectancies. The `n` is 3, so three rows are returned. Note the single `=` here: it's an parameter *setting* `n` to 3 rather than an equality `==` *checking* whether `n` is 3.


```r
africa2007min <- africa2007 %>%
  slice_head(n = 3)
africa2007min
```

```
##      country lifeExp
## 1  Swaziland  39.613
## 2 Mozambique  42.082
## 3     Zambia  42.384
```

Do this again for the tail, i.e., the bottom of the dataset, which is actually the highest values for life expectancy.


```r
africa2007max <- africa2007 %>%
  slice_tail(n = 3)
africa2007max
```

```
##   country lifeExp
## 1 Tunisia  73.923
## 2   Libya  73.952
## 3 Reunion  76.442
```


We can bind the two data frames together again using `bind_rows`:


```r
top_and_bottom <- bind_rows(africa2007min, africa2007max)
top_and_bottom
```

```
##      country lifeExp
## 1  Swaziland  39.613
## 2 Mozambique  42.082
## 3     Zambia  42.384
## 4    Tunisia  73.923
## 5      Libya  73.952
## 6    Reunion  76.442
```



## Filtering for members of a vector {.tabset}

The `top_and_bottom` data frame has the names of countries with the top and bottom three life expectancies.


```r
top_and_bottom$country
```

```
## [1] "Swaziland"  "Mozambique" "Zambia"     "Tunisia"    "Libya"     
## [6] "Reunion"
```
Next we are going to filter the data set to only these countries, using the `%in%` operator which returns true if a value is in the vector you provide and false otherwise.

Here are two examples:


```r
"Libya" %in% top_and_bottom$country
```

```
## [1] TRUE
```

```r
"Uganda" %in% top_and_bottom$country
```

```
## [1] FALSE
```


```r
gap %>%
  filter(country %in% top_and_bottom$country) %>%
  ggplot(aes(x = year,
             y = lifeExp,
             colour = country)) +
  geom_line()
```

<div class="figure" style="text-align: center">
<img src="002-visualising-data_files/figure-html/unnamed-chunk-50-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-50)**CAPTION THIS FIGURE!!**</p>
</div>

We can add `Rwanda` back in by using the `c` operator. Here's an example to show how it works:


```r
some_numbers <- c(1,2,3)
c(some_numbers,4)
```

```
## [1] 1 2 3 4
```

Back to the graph. Below I have also enlarged the `size` of the lines to make the colours easier to distinguish.


```r
gap %>%
  filter(country %in% c(top_and_bottom$country, "Rwanda")) %>%
  ggplot(aes(x = year,
             y = lifeExp,
             colour = country)) +
  geom_line(size = 1) + 
  labs(x = "Year",
       y = "Mean life expectancy (years)",
       colour = "Country")
```

<div class="figure" style="text-align: center">
<img src="002-visualising-data_files/figure-html/unnamed-chunk-52-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-52)**CAPTION THIS FIGURE!!**</p>
</div>

You might now consider a qualitative analysis of these countries (or lookup Wikipedia, for the purposes of a weekly R ехеrcise) to conjecture why there are these differences.


## Final challenge {.tabset}

### Activity

Plot life expectancy against GDP per capita for all countries in the dataset at the most recent time point. Colour the points by continent.


### Answer

Here's how I did it.

First, check the variable names:


```r
names(gap)
```

```
## [1] "country"   "continent" "year"      "lifeExp"   "pop"       "gdpPercap"
```

So we want `lifeExp` and `gdpPercap`.

The most recent year is:


```r
max(gap$year)
```

```
## [1] 2007
```

(You could also find that by looking at the data frame using `View`.)

Now make filter and make the graph in one go:


```r
gap %>%
  filter(year == 2007) %>%
  ggplot(aes(x = gdpPercap, y = lifeExp, colour = continent)) +
  geom_point() +
  labs(y = "Mean life expectancy (years)",
       x = "GDP per capita (US$, inflation-adjusted)",
       colour = "Continent",
       title = "Life expectancy and GDP per capita in 2007")
```

<div class="figure" style="text-align: center">
<img src="002-visualising-data_files/figure-html/unnamed-chunk-55-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-55)**CAPTION THIS FIGURE!!**</p>
</div>
