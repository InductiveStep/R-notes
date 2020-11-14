# Linear regression

By the end of this tutorial you will:

* Know how fit and compare regression models using R (three commands: `lm`, `summary`, `anova`)
* Understand how to interpret the output from these commands
* Have explored some more examples of tidyverse analyses

**Note:** Next week we will explore how to check model assumptions, which may or may not hold for the models we fit.


## Before we begin

Download the dataset from Moodle, create an R Markdown file, and ensure they are both saved in a folder you will find again. I recommend writing down any questions you have as you go, perhaps in your Markdown file, so that we can discuss them in the live session or on Moodle.


## The dataset

This week we will use a dataset on occupational prestige from Canada, 1971, which is used by Fox and Weisberg (2019). Each row in the dataset describes an occupation, and aggregated data about that occupation.

| Variable name | Description |
|---------------|-------------|
| occ       | Occupation |
| education | Average years of education for people in job |
| income    | Average income in dollars |
| women     | Percentage of women in occupation |
| prestige  | A Pineo-Porter prestige score for the occupation with a possible range of 0-100 (higher = more prestigious)  |
| type      | "bc" = blue collar <br> "wc" = white collar <br> "prof" = professional, managerial, or technical |

I'm going to read it in as `dat` but feel free to choose any name you desire.


```r
dat <- read.csv("prestige.csv")
```

Here are the top 5 rows:


```r
head(dat, 5)
```

```
##                   occ education income women prestige type
## 1  gov administrators     13.11  12351 11.16     68.8 prof
## 2    general managers     12.26  25879  4.02     69.1 prof
## 3         accountants     12.77   9271 15.70     63.4 prof
## 4 purchasing officers     11.42   8865  9.11     56.8 prof
## 5            chemists     14.62   8403 11.68     73.5 prof
```

Spend some time checking that you understand each row.

Our challenge for this tutorial is to work out what predicts the prestige of occupations.


## Interlude on methodology

The objective of this tutorial is simply to understand linear regression; however, I think it's worth commenting briefly on methodology.

It is important to develop research questions and (if possible, directional) statistical hypotheses *before* obtaining data, based on prior evidence and theory.

These days, analysis plans are often published before any data are obtained. This is to avoid "data dredging" (also known as "p-hacking") which makes it likely to find results by "capitalising on chance" that have no hope of replicating or generalising beyond the sample. Looking at graphs of data counts as data dredging; you can't create hypotheses from hindsight. See this page from the [Center for Open Science](https://www.cos.io/initiatives/prereg) for more info.



## Descriptives {.tabset}

I'll include `tidyverse` for `ggplot`, pipes, etc., as they will come in handy later:


```r
library(tidyverse)
```


The base R `summary` command is a quick way to obtain a summary of all variables; give it the data frame you wish to summarise as follows:


```r
summary(dat)
```

```
##      occ              education          income          women       
##  Length:102         Min.   : 6.380   Min.   :  611   Min.   : 0.000  
##  Class :character   1st Qu.: 8.445   1st Qu.: 4106   1st Qu.: 3.592  
##  Mode  :character   Median :10.540   Median : 5930   Median :13.600  
##                     Mean   :10.738   Mean   : 6798   Mean   :28.979  
##                     3rd Qu.:12.648   3rd Qu.: 8187   3rd Qu.:52.203  
##                     Max.   :15.970   Max.   :25879   Max.   :97.510  
##     prestige         type          
##  Min.   :14.80   Length:102        
##  1st Qu.:35.23   Class :character  
##  Median :43.60   Mode  :character  
##  Mean   :46.83                     
##  3rd Qu.:59.27                     
##  Max.   :87.20
```

There are a variety of ways to create what is often known as "Table 1", so called because it is usually the first table in quantitative journal articles.

Here is one, in the `tableone` package:


```r
library(tableone)

dat %>%
  select(-occ) %>%
  CreateTableOne(data = .)
```

```
##                        
##                         Overall          
##   n                         102          
##   education (mean (SD))   10.74 (2.73)   
##   income (mean (SD))    6797.90 (4245.92)
##   women (mean (SD))       28.98 (31.72)  
##   prestige (mean (SD))    46.83 (17.20)  
##   type (%)                               
##      bc                      44 (44.9)   
##      prof                    31 (31.6)   
##      wc                      23 (23.5)
```

The `select` line says to remove the `occ` (occupation) variable (can you see why I did that?). The `data = .` option is there because `CreateTableOne` doesn't understand `%>%` plumbing. The `.` represents the output from the previous line so connects the information flow correctly.

You will also usually want to create scatterplots of relationships between continuous variables, similar to what we did with the Gapminder dataset. Which leads onto the following activity.


### Activity

Create the following scatterplots:

(a) prestige against education
(b) prestige against income

Describe the relationship you see.

### Answer

**(a) prestige against education**


```r
dat %>%
  ggplot(aes(x = education, y = prestige)) +
  geom_point() +
  labs(x = "Average years of education",
       y = "Pineo-Porter prestige")
```

<div class="figure" style="text-align: center">
<img src="003-regression_files/figure-html/unnamed-chunk-6-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-6)**CAPTION THIS FIGURE!!**</p>
</div>

There appears to be a linear association between education and prestige; more education is associated with higher prestige.


**(b) prestige against income**


```r
dat %>%
  ggplot(aes(x = income, y = prestige)) +
  geom_point() +
  labs(x = "Average income (dollars)",
       y = "Pineo-Porter prestige")
```

<div class="figure" style="text-align: center">
<img src="003-regression_files/figure-html/unnamed-chunk-7-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-7)**CAPTION THIS FIGURE!!**</p>
</div>

From up to around $10000, there seems to be a linear correlation between income and prestige, after which the relationship flattens out, i.e., more income does not lead to higher prestige. (At least, that's what I see!)






## Prep to understand the simplest regression model {.tabset}


### Activity

The simplest regression model we will explore in a moment just models the mean of the outcome variable. So that we can see how it works, first calculate the mean and SD of prestige.


### Answer


```r
mean(dat$prestige)
```

```
## [1] 46.83333
```

```r
sd(dat$prestige)
```

```
## [1] 17.20449
```



## The simplest regression model: intercept only model {.tabset}

The command for fitting a regression model is called `lm`, which is short for *linear model*. It wants a formula, describing the model to be fitted, and the name of a data frame containing your data.

Here is how to fit the intercept-only model:


```r
mod0 <- lm(formula = prestige ~ 1, data = dat)
```


The left-hand side of `~` (tilde) is the *outcome* or *response* variable we want to explain/predict. The right-hand side lists predictors. Here `1` denotes the intercept.

Since the first two parameters of `lm` are formula and data (check `?lm`), this can be abbreviated to:


```r
mod0 <- lm(prestige ~ 1, dat)
```

As ever, since we have saved the result in a variable, nothing has visibly happened. You could have a peek at the result with:


```r
mod0
```

```
## 
## Call:
## lm(formula = prestige ~ 1, data = dat)
## 
## Coefficients:
## (Intercept)  
##       46.83
```

There is a LOT more info in the object which you can see with this structure command, `str`:


```r
str(mod0)
```

If you run this you will see why we usually prefer to use `summary` on the output; this pulls out useful info and presents it in a straightforward way:


```r
summary(mod0)
```

```
## 
## Call:
## lm(formula = prestige ~ 1, data = dat)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -32.033 -11.608  -3.233  12.442  40.367 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)   46.833      1.703   27.49   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 17.2 on 101 degrees of freedom
```

The estimate for the intercept is 46.833, the same as the mean of prestige we calculated above.

There is other information:

* The **standard error (SE)**, which provides information on the precision of the estimate: the smaller the SE, the more precise the estimate of the population mean.
* The **t-value**, which is equal to the estimate divided by the SE. This is used to test whether the estimate is different to zero, which isn't particularly informative for the intercept; it's more so when we get to slopes in a moment.
* The **p-value** is calculated from the distribution of *t* under the assumption that the population estimate is 0 and helps us interpret the *t*. R presents it here as "<2e-16". This says that *p* is less than 0.0000000000000002, or $2 \times 10^{-16}$. So, for what it's worth, and using the usual not-entirely-correct applied stats vernacular: the intercept, here mean prestige, is statistically significantly greater than zero.
* We will get to the **residual standard error** later, but for now observe that it is the same as the SD of prestige.


```r
sd(dat$prestige)
```

```
## [1] 17.20449
```

Okay, so you have calculated the mean and SD of a variable in an absurdly convolved way, alongside a statistical test that probably isn't any use. 
Why have I done this to you?

Two reasons:

1. It is useful to see what models do for the simplest possible specification -- increasingly useful as the models become more complicated.
2. The intercept-only model can be compared with more complex models, i.e., models with more predictors, to see if adding predictors actually explains more variance.



### Activity

Try fitting the intercept-only model again for the education variable. Compare the result with its mean and SD.


### Answer

The mean is:


```r
mean(dat$education)
```

```
## [1] 10.73804
```

The SD:


```r
sd(dat$education)
```

```
## [1] 2.728444
```

Fit the model:


```r
lm(education ~ 1, data = dat) %>%
  summary()
```

```
## 
## Call:
## lm(formula = education ~ 1, data = dat)
## 
## Residuals:
##    Min     1Q Median     3Q    Max 
## -4.358 -2.293 -0.198  1.909  5.232 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  10.7380     0.2702   39.75   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 2.728 on 101 degrees of freedom
```

(Here I have used a pipe -- again do whatever makes most sense!)



## Adding a slope to the regression model {.tabset}

So that was a silly model.

Next we are going to add years of education as a predictor to see if it explains any of the variation in prestige. Before doing so, here is a picture of the relationship from earlier.



```r
plot_ed_prestige <- ggplot(dat, aes(x = education,
                                    y = prestige)) +
                      geom_point()
plot_ed_prestige
```

<div class="figure" style="text-align: center">
<img src="003-regression_files/figure-html/unnamed-chunk-19-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-19)**CAPTION THIS FIGURE!!**</p>
</div>

I am confident that there is a positive relationship between education and prestige, using the [interocular trauma test](https://www.johndcook.com/blog/2009/08/31/the-iot-test/), but it is instructive to see the intercept-only model prediction overlaid on this graph.

To extract the coefficients from a model, we use `coef`:


```r
coef(mod0)
```

```
## (Intercept) 
##    46.83333
```

In this case, there only is one estimate, so this is equivalent to:


```r
coef(mod0)[1]
```

```
## (Intercept) 
##    46.83333
```

But usually there will be more than one coefficient in a model.

Since we saved the earlier plot, it is easy to add on a horizontal line for the intercept-only model:


```r
plot_ed_prestige +
  geom_hline(yintercept = coef(mod0)[1])
```

<div class="figure" style="text-align: center">
<img src="003-regression_files/figure-html/unnamed-chunk-22-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-22)**CAPTION THIS FIGURE!!**</p>
</div>

Now let's fit the surely better model:


```r
mod1 <- lm(prestige ~ 1 + education, data = dat)
summary(mod1)
```

```
## 
## Call:
## lm(formula = prestige ~ 1 + education, data = dat)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -26.0397  -6.5228   0.6611   6.7430  18.1636 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  -10.732      3.677  -2.919  0.00434 ** 
## education      5.361      0.332  16.148  < 2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 9.103 on 100 degrees of freedom
## Multiple R-squared:  0.7228,	Adjusted R-squared:   0.72 
## F-statistic: 260.8 on 1 and 100 DF,  p-value: < 2.2e-16
```

(Note: `prestige ~ 1 + education` is equivalent to `prestige ~ education`; R puts the intercept in by default. Try both to see!)

We can plot the the predicted mean prestige for each year of education using the model's coefficients...


```r
coef(mod1)
```

```
## (Intercept)   education 
##  -10.731982    5.360878
```

... with `geom_abline` (pronounced "A B line", named after a base R function `abline` where *a* is the intercept and *b* is the slope) like so:


```r
plot_ed_prestige +
  geom_abline(intercept = coef(mod1)[1],
              slope     = coef(mod1)[2])
```

<div class="figure" style="text-align: center">
<img src="003-regression_files/figure-html/unnamed-chunk-25-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-25)**CAPTION THIS FIGURE!!**</p>
</div>



The **intercept** is -10.732. This is clearly no longer the mean of prestige (scroll up and you will see that the measure is defined as a number between 0-100); rather, **the intercept is the predicted mean of the outcome variable where other predictors are zero** so it depends on what other predictors are present in the model.

Here's another picture, stretched to show where the intercept is:


```{.r .fold-hide}
plot_ed_prestige +
  geom_abline(intercept = coef(mod1)[1],
              slope     = coef(mod1)[2]) +
  xlim(0,   NA) + 
  ylim(-20, NA) +
  geom_vline(xintercept = 0, colour = "purple") +
  annotate(geom = "curve",
           x = 2, y = -12, xend = .05, yend = -12, 
           curvature = -.5, arrow = arrow(), colour = "purple") +
  annotate("text", x = 2.05, y = -12,
           label = "The intercept is over here",
           colour = "purple",
           hjust = "left")
```

<div class="figure" style="text-align: center">
<img src="003-regression_files/figure-html/unnamed-chunk-26-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-26)**CAPTION THIS FIGURE!!**</p>
</div>

It is not uncommon for the intercept to be substantively nonsense, but statistically necessary to ensure accurate slopes. We will see later how to make the intercept more interpretable, without changing the slope estimates.

The **slope for education** is 5.361. This means that for every unit increase in education, the prestige score increases by 5.361. The units for education are years, so we should say something like: every year increase in education is associated with 5.4 more prestige points. Is this a big effect do you reckon?

As before, the *t*'s and *p*'s are present. Now the p-value for education is actually useful. The value is `< 2e-16`, i.e., less than $2 \times 10^{-16}$. This means the sample-estimate of the slope we got would be very unlikely if the population slope were zero. Or, in the usual applied stats vernacular: there is a statistically significant relationship between education and prestige. (Keep a look out for how results are summarised in the literature you are exploring -- again I urge you to look at journal articles in your field to get ideas for write-up aesthetics.)

The $R^2$ is also useful. The `summary` command presents $R^2$ as a proportion; it represent the proportion of the variance in the outcome variable explained by the predictors. Generally we use the adjusted $R^2$ because this adjusts for the number of predictors in the model and reduces bias in the estimate. If $R^2$ were 0 then that would mean that the predictors don't explain any variance in the outcome. If it were 1, that would mean that the predictors explain all the variance -- statistically, knowing the outcome variable does not add any further information.



### Activity

Use a regression model with prestige as the outcome variable and average income as a predictor. Describe the relationship according to the model and whether it is statistically significant.

It will help to check above to see what units the income is measured in.


### Answer


```r
mod_income <- lm(prestige ~ income, data = dat)
summary(mod_income)
```

```
## 
## Call:
## lm(formula = prestige ~ income, data = dat)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -33.007  -8.378  -2.378   8.432  32.084 
## 
## Coefficients:
##              Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 2.714e+01  2.268e+00   11.97   <2e-16 ***
## income      2.897e-03  2.833e-04   10.22   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 12.09 on 100 degrees of freedom
## Multiple R-squared:  0.5111,	Adjusted R-squared:  0.5062 
## F-statistic: 104.5 on 1 and 100 DF,  p-value: < 2.2e-16
```

The slope for income is "statistically significant" at the traditional 5% level, with a very small $p$. But what did you make of that slope estimate, `2.897e-03`? This is equivalent to $2.897 \times 10^{-3}$ or about 0.0029. So for every unit increase in income, the prestige score goes up by 0.0029 on a scale from 0-100. The income units here are the Canadian dollar. We probably don't expect much of an increase in prestige for one extra dollar income!

Here's the coefficient:


```r
coef(mod_income)[2]
```

```
##      income 
## 0.002896799
```

We might expect 1000 dollars more salary to have more of an impact:



```r
coef(mod_income)[2] * 1000
```

```
##   income 
## 2.896799
```

So $1000 more salary is associated with about 2.9 more prestige points.

Another way to do this is as follows.

First transform the salary so it is in thousands of dollars rather than single dollars. I'll `mutate` the data frame to add a new variable called `income_1000s`:


```r
dat <- dat %>%
  mutate(income_1000s = income/1000)
```


Now fit the model again using this new variable as the predictor.


```r
mod_income1000s <- lm(prestige ~ income_1000s, data = dat)
summary(mod_income1000s)
```

```
## 
## Call:
## lm(formula = prestige ~ income_1000s, data = dat)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -33.007  -8.378  -2.378   8.432  32.084 
## 
## Coefficients:
##              Estimate Std. Error t value Pr(>|t|)    
## (Intercept)   27.1412     2.2677   11.97   <2e-16 ***
## income_1000s   2.8968     0.2833   10.22   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 12.09 on 100 degrees of freedom
## Multiple R-squared:  0.5111,	Adjusted R-squared:  0.5062 
## F-statistic: 104.5 on 1 and 100 DF,  p-value: < 2.2e-16
```

Note how the *t* and *p* haven't changed; however, now the estimate for income is easier to interpret.


## Intermezzo on causality

So far we have just been merrily fitting regression models and I've tried to be agnostic about causation. However, above I slipped a bit and talked about the "impact" of income on an occupation's prestige. When you have a moment, it might be worth sketching what you think *causes* an occupation to have more prestige. This is not something a statistical model alone can tell us; however, with a theory and statistical analysis you can say something about causation, so long as you articulate what your theory is and are clear about your assumptions. What you say about causation is relative to those assumptions.



## Residuals {.tabset}

Residuals are important for understanding regression fits. They are calculated for each data point and a given model as the difference between the actual value of the outcome variable and the model prediction. There are pictures coming up which will illustrate.

R gives us model predictions using the `predict` command and also automatically calculates residuals using `resid`.

Let's first do it for the intercept-only model.

The code below says:

* select the variables prestige and education (this is to keep things neat and tidy)
* add variables `predict0` and `resid0` which consist of the predictions and residuals, respectively, for `mod0`
* save it all in `dat_for_resids`


```r
dat_for_resids <- dat %>%
  select(prestige, education) %>%
  mutate(predict0  = predict(mod0),
         residual0 = resid(mod0))
dat_for_resids
```

```
##     prestige education predict0   residual0
## 1       68.8     13.11 46.83333  21.9666667
## 2       69.1     12.26 46.83333  22.2666667
## 3       63.4     12.77 46.83333  16.5666667
## 4       56.8     11.42 46.83333   9.9666667
## 5       73.5     14.62 46.83333  26.6666667
## 6       77.6     15.64 46.83333  30.7666667
## 7       72.6     15.09 46.83333  25.7666667
## 8       78.1     15.44 46.83333  31.2666667
## 9       73.1     14.52 46.83333  26.2666667
## 10      68.8     14.64 46.83333  21.9666667
## 11      62.0     12.39 46.83333  15.1666667
## 12      60.0     12.30 46.83333  13.1666667
## 13      53.8     13.83 46.83333   6.9666667
## 14      62.2     14.44 46.83333  15.3666667
## 15      74.9     14.36 46.83333  28.0666667
## 16      55.1     14.21 46.83333   8.2666667
## 17      82.3     15.77 46.83333  35.4666667
## 18      58.1     14.15 46.83333  11.2666667
## 19      58.3     15.22 46.83333  11.4666667
## 20      72.8     14.50 46.83333  25.9666667
## 21      84.6     15.97 46.83333  37.7666667
## 22      59.6     13.62 46.83333  12.7666667
## 23      66.1     15.08 46.83333  19.2666667
## 24      87.2     15.96 46.83333  40.3666667
## 25      66.7     15.94 46.83333  19.8666667
## 26      68.4     14.71 46.83333  21.5666667
## 27      64.7     12.46 46.83333  17.8666667
## 28      34.9      9.45 46.83333 -11.9333333
## 29      72.1     13.62 46.83333  25.2666667
## 30      69.3     15.21 46.83333  22.4666667
## 31      67.5     12.79 46.83333  20.6666667
## 32      57.2     11.09 46.83333  10.3666667
## 33      57.6     12.71 46.83333  10.7666667
## 34      54.1     11.44 46.83333   7.2666667
## 35      46.0     11.59 46.83333  -0.8333333
## 36      41.9     11.49 46.83333  -4.9333333
## 37      49.4     11.32 46.83333   2.5666667
## 38      42.3     10.64 46.83333  -4.5333333
## 39      47.7     11.36 46.83333   0.8666667
## 40      30.9      9.17 46.83333 -15.9333333
## 41      32.7     12.09 46.83333 -14.1333333
## 42      38.7     11.04 46.83333  -8.1333333
## 43      36.1      9.22 46.83333 -10.7333333
## 44      37.2     10.07 46.83333  -9.6333333
## 45      38.1     10.51 46.83333  -8.7333333
## 46      29.4     11.20 46.83333 -17.4333333
## 47      51.1     11.13 46.83333   4.2666667
## 48      35.7     11.43 46.83333 -11.1333333
## 49      35.6     11.00 46.83333 -11.2333333
## 50      41.5      9.84 46.83333  -5.3333333
## 51      40.2     11.13 46.83333  -6.6333333
## 52      26.5     10.05 46.83333 -20.3333333
## 53      14.8      9.62 46.83333 -32.0333333
## 54      23.3      9.93 46.83333 -23.5333333
## 55      47.3     11.60 46.83333   0.4666667
## 56      47.1     11.09 46.83333   0.2666667
## 57      51.1     11.03 46.83333   4.2666667
## 58      43.5      9.47 46.83333  -3.3333333
## 59      51.6     10.93 46.83333   4.7666667
## 60      29.7      7.74 46.83333 -17.1333333
## 61      20.2      8.50 46.83333 -26.6333333
## 62      54.9     10.57 46.83333   8.0666667
## 63      25.9      9.46 46.83333 -20.9333333
## 64      20.8      7.33 46.83333 -26.0333333
## 65      17.3      7.11 46.83333 -29.5333333
## 66      20.1      7.58 46.83333 -26.7333333
## 67      44.1      6.84 46.83333  -2.7333333
## 68      21.5      8.60 46.83333 -25.3333333
## 69      35.3      8.88 46.83333 -11.5333333
## 70      38.9      7.54 46.83333  -7.9333333
## 71      25.2      7.64 46.83333 -21.6333333
## 72      34.8      7.64 46.83333 -12.0333333
## 73      23.2      7.42 46.83333 -23.6333333
## 74      33.3      6.69 46.83333 -13.5333333
## 75      28.8      6.74 46.83333 -18.0333333
## 76      42.5     10.09 46.83333  -4.3333333
## 77      44.2      8.81 46.83333  -2.6333333
## 78      35.9      8.40 46.83333 -10.9333333
## 79      41.8      7.92 46.83333  -5.0333333
## 80      35.9      8.43 46.83333 -10.9333333
## 81      43.7      8.78 46.83333  -3.1333333
## 82      50.8      8.76 46.83333   3.9666667
## 83      37.2     10.29 46.83333  -9.6333333
## 84      28.2      6.38 46.83333 -18.6333333
## 85      38.1      8.10 46.83333  -8.7333333
## 86      50.3     10.10 46.83333   3.4666667
## 87      27.3      6.67 46.83333 -19.5333333
## 88      40.9      9.05 46.83333  -5.9333333
## 89      50.2      9.93 46.83333   3.3666667
## 90      51.1      8.24 46.83333   4.2666667
## 91      38.9      6.92 46.83333  -7.9333333
## 92      36.2      6.60 46.83333 -10.6333333
## 93      29.9      7.81 46.83333 -16.9333333
## 94      42.9      8.33 46.83333  -3.9333333
## 95      26.5      7.52 46.83333 -20.3333333
## 96      66.1     12.27 46.83333  19.2666667
## 97      48.9      8.49 46.83333   2.0666667
## 98      35.9      7.58 46.83333 -10.9333333
## 99      25.1      7.93 46.83333 -21.7333333
## 100     26.1      8.37 46.83333 -20.7333333
## 101     42.2     10.00 46.83333  -4.6333333
## 102     35.2      8.55 46.83333 -11.6333333
```

Since it's the intercept-only model, the prediction is always the mean; that's why `predict0` says 46.83333, 46.83333, 46.83333, 46.83333, 46.83333...

Look at the first residual, 21.97. That is calculated as the actual value of prestige minus the model-predicted value: $68.8 - 46.83$.

Here is a picture showing the residuals; hopefully this highlights that they just measure how far each data point is from the model prediction:


```{.r .fold-hide}
dat_for_resids %>%
  ggplot(aes(x = education, y = prestige)) +
  geom_segment(aes(xend = education, yend = predict0)) +
  geom_point() +
  geom_hline(yintercept = coef(mod0))
```

<div class="figure" style="text-align: center">
<img src="003-regression_files/figure-html/unnamed-chunk-33-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-33)**CAPTION THIS FIGURE!!**</p>
</div>

Now let's calculate predictions and residuals again for the more sensible model, `mod1`.


```r
dat_for_resids <- dat_for_resids  %>%
  mutate(predict1  = predict(mod1),
         residual1 = resid(mod1))
dat_for_resids %>%
  select(prestige, predict1, residual1, residual0)
```

```
##     prestige predict1    residual1   residual0
## 1       68.8 59.54913   9.25087492  21.9666667
## 2       69.1 54.99238  14.10762099  22.2666667
## 3       63.4 57.72643   5.67357335  16.5666667
## 4       56.8 50.48924   6.31075828   9.9666667
## 5       73.5 67.64405   5.85594955  26.6666667
## 6       77.6 73.11215   4.48785426  30.7666667
## 7       72.6 70.16366   2.43633701  25.7666667
## 8       78.1 72.03997   6.06002981  31.2666667
## 9       73.1 67.10796   5.99203732  26.2666667
## 10      68.8 67.75127   1.04873199  21.9666667
## 11      62.0 55.68929   6.31070689  15.1666667
## 12      60.0 55.20681   4.79318588  13.1666667
## 13      53.8 63.40896  -9.60895705   6.9666667
## 14      62.2 66.67909  -4.47909246  15.3666667
## 15      74.9 66.25022   8.64977776  28.0666667
## 16      55.1 65.44609 -10.34609058   8.2666667
## 17      82.3 73.80906   8.49094016  35.4666667
## 18      58.1 65.12444  -7.02443792  11.2666667
## 19      58.3 70.86058 -12.56057709  11.4666667
## 20      72.8 67.00075   5.79925487  25.9666667
## 21      84.6 74.88124   9.71876461  37.7666667
## 22      59.6 62.28317  -2.68317272  12.7666667
## 23      66.1 70.11005  -4.01005421  19.2666667
## 24      87.2 74.82763  12.37237339  40.3666667
## 25      66.7 74.72041  -8.02040906  19.8666667
## 26      68.4 68.12653   0.27347055  21.5666667
## 27      64.7 56.06455   8.63544545  17.8666667
## 28      34.9 39.92831  -5.02831259 -11.9333333
## 29      72.1 62.28317   9.81682728  25.2666667
## 30      69.3 70.80697  -1.50696831  22.4666667
## 31      67.5 57.83364   9.66635579  20.6666667
## 32      57.2 48.72015   8.47984794  10.3666667
## 33      57.6 57.40477   0.19522601  10.7666667
## 34      54.1 50.59646   3.50354073   7.2666667
## 35      46.0 51.40059  -5.40059093  -0.8333333
## 36      41.9 50.86450  -8.96450316  -4.9333333
## 37      49.4 49.95315  -0.55315394   2.5666667
## 38      42.3 46.30776  -4.00775709  -4.5333333
## 39      47.7 50.16759  -2.46758905   0.8666667
## 40      30.9 38.42727  -7.52726682 -15.9333333
## 41      32.7 54.08103 -21.38102979 -14.1333333
## 42      38.7 48.45211  -9.75210818  -8.1333333
## 43      36.1 38.69531  -2.59531071 -10.7333333
## 44      37.2 43.25206  -6.05205678  -9.6333333
## 45      38.1 45.61084  -7.51084298  -8.7333333
## 46      29.4 49.30985 -19.90984861 -17.4333333
## 47      51.1 48.93459   2.16541283   4.2666667
## 48      35.7 50.54285 -14.84285049 -11.1333333
## 49      35.6 48.23767 -12.63767307 -11.2333333
## 50      41.5 42.01905  -0.51905490  -5.3333333
## 51      40.2 48.93459  -8.73458717  -6.6333333
## 52      26.5 43.14484 -16.64483922 -20.3333333
## 53      14.8 40.83966 -26.03966180 -32.0333333
## 54      23.3 42.50153 -19.20153390 -23.5333333
## 55      47.3 51.45420  -4.15419971   0.4666667
## 56      47.1 48.72015  -1.62015206   0.2666667
## 57      51.1 48.39850   2.70150060   4.2666667
## 58      43.5 40.03553   3.46446986  -3.3333333
## 59      51.6 47.86241   3.73758837   4.7666667
## 60      29.7 30.76121  -1.06121167 -17.1333333
## 61      20.2 34.83548 -14.63547874 -26.6333333
## 62      54.9 45.93250   8.96750436   8.0666667
## 63      25.9 39.98192 -14.08192136 -20.9333333
## 64      20.8 28.56325  -7.76325180 -26.0333333
## 65      17.3 27.38386 -10.08385870 -29.5333333
## 66      20.1 29.90347  -9.80347123 -26.7333333
## 67      44.1 25.93642  18.16357829  -2.7333333
## 68      21.5 35.37157 -13.87156651 -25.3333333
## 69      35.3 36.87261  -1.57261228 -11.5333333
## 70      38.9 29.68904   9.21096388  -7.9333333
## 71      25.2 30.22512  -5.02512389 -21.6333333
## 72      34.8 30.22512   4.57487611 -12.0333333
## 73      23.2 29.04573  -5.84573079 -23.6333333
## 74      33.3 25.13229   8.16770995 -13.5333333
## 75      28.8 25.40033   3.39966606 -18.0333333
## 76      42.5 43.35927  -0.85927433  -4.3333333
## 77      44.2 36.49735   7.70264916  -2.6333333
## 78      35.9 34.29939   1.60060903 -10.9333333
## 79      41.8 31.72617  10.07383034  -5.0333333
## 80      35.9 34.46022   1.43978270 -10.9333333
## 81      43.7 36.33652   7.36347549  -3.1333333
## 82      50.8 36.22931  14.57069305   3.9666667
## 83      37.2 44.43145  -7.23144988  -9.6333333
## 84      28.2 23.47042   4.72958205 -18.6333333
## 85      38.1 32.69113   5.40887235  -8.7333333
## 86      50.3 43.41288   6.88711689   3.4666667
## 87      27.3 25.02507   2.27492751 -19.5333333
## 88      40.9 37.78396   3.11603851  -5.9333333
## 89      50.2 42.50153   7.69846610   3.3666667
## 90      51.1 33.44165  17.65834947   4.2666667
## 91      38.9 26.36529  12.53470807  -7.9333333
## 92      36.2 24.64981  11.55018895 -10.6333333
## 93      29.9 31.13647  -1.23647311 -16.9333333
## 94      42.9 33.92413   8.97587047  -3.9333333
## 95      26.5 29.58182  -3.08181857 -20.3333333
## 96      66.1 55.04599  11.05401221  19.2666667
## 97      48.9 34.78187  14.11813004   2.0666667
## 98      35.9 29.90347   5.99652877 -10.9333333
## 99      25.1 31.77978  -6.67977844 -21.7333333
## 100     26.1 34.13856  -8.03856464 -20.7333333
## 101     42.2 42.87680  -0.67679534  -4.6333333
## 102     35.2 35.10352   0.09647737 -11.6333333
```



```r
dat_for_resids$residual1 %>% round(2)
```

```
##      1      2      3      4      5      6      7      8      9     10     11 
##   9.25  14.11   5.67   6.31   5.86   4.49   2.44   6.06   5.99   1.05   6.31 
##     12     13     14     15     16     17     18     19     20     21     22 
##   4.79  -9.61  -4.48   8.65 -10.35   8.49  -7.02 -12.56   5.80   9.72  -2.68 
##     23     24     25     26     27     28     29     30     31     32     33 
##  -4.01  12.37  -8.02   0.27   8.64  -5.03   9.82  -1.51   9.67   8.48   0.20 
##     34     35     36     37     38     39     40     41     42     43     44 
##   3.50  -5.40  -8.96  -0.55  -4.01  -2.47  -7.53 -21.38  -9.75  -2.60  -6.05 
##     45     46     47     48     49     50     51     52     53     54     55 
##  -7.51 -19.91   2.17 -14.84 -12.64  -0.52  -8.73 -16.64 -26.04 -19.20  -4.15 
##     56     57     58     59     60     61     62     63     64     65     66 
##  -1.62   2.70   3.46   3.74  -1.06 -14.64   8.97 -14.08  -7.76 -10.08  -9.80 
##     67     68     69     70     71     72     73     74     75     76     77 
##  18.16 -13.87  -1.57   9.21  -5.03   4.57  -5.85   8.17   3.40  -0.86   7.70 
##     78     79     80     81     82     83     84     85     86     87     88 
##   1.60  10.07   1.44   7.36  14.57  -7.23   4.73   5.41   6.89   2.27   3.12 
##     89     90     91     92     93     94     95     96     97     98     99 
##   7.70  17.66  12.53  11.55  -1.24   8.98  -3.08  11.05  14.12   6.00  -6.68 
##    100    101    102 
##  -8.04  -0.68   0.10
```


Here is the plot:


```{.r .fold-hide}
dat_for_resids %>%
  ggplot(aes(x = education, y = prestige)) +
  geom_segment(aes(xend = education, yend = predict1)) +
  geom_point() +
  geom_abline(intercept = coef(mod1)[1],
              slope     = coef(mod1)[2])
```

<div class="figure" style="text-align: center">
<img src="003-regression_files/figure-html/unnamed-chunk-36-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-36)**CAPTION THIS FIGURE!!**</p>
</div>


### Activity

The second model clearly describes the data better than the intercept-only model, but can you explain why, solely in terms of the residuals?


### Answer

The residuals seem smaller. Intuitively, roughly, that's what is going on. 
Are *all* the residuals smaller? We can work out the length of the residual (i.e., the distance between model prediction and actual data), ignoring the sign, by using "absolute" function, `abs`, and then take the difference:


```r
dat_for_resids <- dat_for_resids %>%
  mutate(resid_diff = abs(residual1) - abs(residual0),
         intercept_better = resid_diff >= 0)
```

I have also added a variable called `intercept_better` if the intercept-only model has a smaller residual for that observation than the more complex model.

Have a look:


```r
View(dat_for_resids)
```

The answer is... no, not all residuals are smaller for the complex model.


```r
dat_for_resids %>%
  group_by(intercept_better) %>%
  count()
```

```
## # A tibble: 2 x 2
## # Groups:   intercept_better [2]
##   intercept_better     n
##   <lgl>            <int>
## 1 FALSE               73
## 2 TRUE                29
```

For 29 observations, the intercept-only model has smaller residuals than the model with a slope for education.

This makes sense if we look again at the graph for the intercept-only model, this time with the lines coloured by whether the intercept-only model is better. I have also added a dashed line for the model with a slope for education.


```{.r .fold-hide}
dat_for_resids %>%
  ggplot(aes(x = education, y = prestige, colour = intercept_better)) +
  geom_segment(aes(xend = education, yend = predict0)) +
  geom_point() +
  geom_hline(yintercept = coef(mod0)) +
  labs(colour = "Intercept-only better") +
  geom_abline(intercept = coef(mod1)[1],
              slope     = coef(mod1)[2],
              linetype = "dashed")
```

<div class="figure" style="text-align: center">
<img src="003-regression_files/figure-html/unnamed-chunk-40-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-40)**CAPTION THIS FIGURE!!**</p>
</div>

So it's not quite true that all the residuals are smaller in the more complex model. Most are, though.

One way to combine all the residuals is to square them first (to remove the sign, i.e., whether the residual is positive or negative),  and sum them together. This is called the **residual sum of squares** and, it turns out, is what the regression model minimises for any particular dataset.

Here's the arithmetic for the intercept-only model:


```r
sum(dat_for_resids$residual0^2)
```

```
## [1] 29895.43
```
And here's the arithmetic for the model with a predictor for education:


```r
sum(dat_for_resids$residual1^2)
```

```
## [1] 8286.99
```

So the residual sum of squares are smaller for the latter model.


## Comparing models

Here is a reminder of two of the models we fitted:


```r
mod0 <- lm(prestige ~ 1,             data = dat)
mod1 <- lm(prestige ~ 1 + education, data = dat)
```

To compare these, use `anova`, with the least complex model first:


```r
anova(mod0, mod1)
```

```
## Analysis of Variance Table
## 
## Model 1: prestige ~ 1
## Model 2: prestige ~ 1 + education
##   Res.Df   RSS Df Sum of Sq      F    Pr(>F)    
## 1    101 29895                                  
## 2    100  8287  1     21608 260.75 < 2.2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

This only works for *nested* models, where the smaller model's predictors are also included in the larger model. This is true here, since the larger model is the same as the intercept-only model with the addition of a slope for education.

The `anova` command has calculated the residual sum of squares (RSS) for us. There is also a p-value; since it is far smaller than 0.05,  we can conclude that the more complex model explains statistically significantly more variance.

We would usually write this as (something like): the model with education as a predictor explains prestige better than the intercept-only model, $F(1,100) = 260.8$, $p < .001$. (Generally when the p-value is very small, we use the less-than in this fashion.)



## Interlude on what "*F*(1,100) = 260.8" means

The 1 is the degrees of freedom from the difference in the number of parameters between the two models (`Df` in the table) and 100 is from the residual degrees of freedom (`Res.Df` in the table). There is [a formula](https://www.graphpad.com/guides/prism/7/curve-fitting/reg_howtheftestworks.htm) for calculating *F*, here 260.75, from the residual sum of squares and degrees of freedom. Here is what the arithmetic looks like for our model comparison:


```r
((29895 - 8287) / 1) / 
  (8287/100) 
```

```
## [1] 260.7457
```

The distribution of F assuming the null that there is *no difference* between the models depends on the degrees of freedom, which are given in parentheses after *F*.

If you haven't encountered this before in an introductory course, you might be a little confused at this point. Let me sketch the intuition behind this. Field, Miles, and Field (2012) provide a more leisurely explanation.

Here's what the distribution for F looks like for 1 and 100 degrees of freedom, i.e., $F(1,100)$. View it as a smoothed histogram where the area under the curve is 1, by definition.


```{.r .fold-hide}
ggplot() +
  xlim(1,10) +
  ylim(0,NA) +
  geom_function(fun = function(x) df(x,1,100)) +
  labs(x = "F(1,100)", y = "Density")
```

<div class="figure" style="text-align: center">
<img src="003-regression_files/figure-html/unnamed-chunk-46-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-46)**CAPTION THIS FIGURE!!**</p>
</div>

In other words, if you were to run loads of studies where there is no actual difference between the models, this is the distribution of F values you would obtain.

We actually got $F = 260.8$, which is way off to the right... Let's zoom out to see:


```{.r .fold-hide}
ggplot() +
  xlim(1,265) +
  ylim(0,NA) +
  geom_function(fun = function(x) df(x,1,100)) +
  labs(x = "F(1,100)", y = "Density") +
  annotate(geom = "curve",
           x = 250, y = .05, xend = 260.8, yend = .005, 
           curvature = -.1, arrow = arrow(), colour = "purple") +
  annotate("text", x = 250, y = .06,
           label = "260.8 is over here",
           colour = "purple",
           hjust = "right")
```

<div class="figure" style="text-align: center">
<img src="003-regression_files/figure-html/unnamed-chunk-47-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-47)**CAPTION THIS FIGURE!!**</p>
</div>

This means, *if* the null hypothesis of no difference between the models were really true then an F-value this or more extreme would be ridiculously unlikely (the area under the curve beyond the point, representing the probability, is tiny). So there probably is a difference between the models.

Degrees of freedom is another challenging concept, but the intuition is straightforward. Let's consider something easier than *F*, the mean.

Here's a challenge. Make up four numbers which have a mean of 0.

Here's one way to do it:


```r
mean(c(0,0,0,0))
```

```
## [1] 0
```

Another:


```r
mean(c(-3,-2,3,2))
```

```
## [1] 0
```
There are infinitely many combinations.

I can give you zero, one, or two of the four numbers and a target mean, and you can always fill in the blank(s) to get that mean. Once you have three numbers, the fourth is completely constrained.

Try 42, 7, 150. Only one number fills in the blank to end up with the correct mean. We can do some arithmetic on the formula for the mean of four values, $M$:

$$
M = \frac{x_1 + x_2 + x_3 + x_4}{4}
$$

to discover that $x_4 = 4M - (x_1 + x_2 + x_3)$.

Since the desired mean is 0, we just need to sum all the values we were given and stick a minus sign on front of it.



```r
42 + 7 + 150
```

```
## [1] 199
```


```r
mean(c(42, 7, 150, -199))
```

```
## [1] 0
```
The mean of 0, as desired. No other number than -199 would do, given the first three numbers.

This illustrates degrees of freedom. Given a mean, you have $n-1$ degrees of freedom, where $n$ is the total number of values you have. Once $n-1$ numbers are known, then the final one is constrained by the mean and those numbers.

Other statistics like *t*  and *F* have their own degrees of freedom and these turn out to be important for working out their distributions under the null hypothesis.


## Regression with two or more predictors {.tabset}

Linear regression models can include arbitrary numbers of predictors -- in social science, often a dozen or more. They become increasingly challenging to visualise in higher dimensions, so let's start with two predictors, education and income.

When you run the code below, a window will pop up with an interactive 3D plot. In this tutorial, you should able to click and drag the graph directly below.


```r
library(car)
library(rgl)
scatter3d(prestige ~ education + income, data = dat, surface = FALSE)
```


Now, let's fit a model to those data.


### Activity

We have seen how to fit an intercept-only model and to add a predictor to that.

(a) Can you work out how to model prestige as an outcome variable with predictors of education and income (using the version scaled to thousands)?

Don't worry about interpreting the slopes just yet.

(b) Is this model a better fit than the intercept-only model?


### Answer

(a) Hopefully that was straightforward; just use `+` again:


```r
mod_both <- lm(prestige ~ education + income_1000s, data = dat)
summary(mod_both)
```

```
## 
## Call:
## lm(formula = prestige ~ education + income_1000s, data = dat)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -19.4040  -5.3308   0.0154   4.9803  17.6889 
## 
## Coefficients:
##              Estimate Std. Error t value Pr(>|t|)    
## (Intercept)   -6.8478     3.2190  -2.127   0.0359 *  
## education      4.1374     0.3489  11.858  < 2e-16 ***
## income_1000s   1.3612     0.2242   6.071 2.36e-08 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 7.81 on 99 degrees of freedom
## Multiple R-squared:  0.798,	Adjusted R-squared:  0.7939 
## F-statistic: 195.6 on 2 and 99 DF,  p-value: < 2.2e-16
```

Both predictors were statistically significant. Interpretation coming below...



(b) We already have the intercept-only model as `mod0` so comparing models is done by:


```r
anova(mod0, mod_both)
```

```
## Analysis of Variance Table
## 
## Model 1: prestige ~ 1
## Model 2: prestige ~ education + income_1000s
##   Res.Df     RSS Df Sum of Sq      F    Pr(>F)    
## 1    101 29895.4                                  
## 2     99  6038.9  2     23857 195.55 < 2.2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

The model with two predictors explains more variation than the intercept-only model, $F(2,99) = 195.6, p < .001$




## Interpreting regression models with two or more predictors

Okay, so we have fitted a model now; I've called it `mod_both`. We can easily visualise this using:


```r
scatter3d(prestige ~ education + income, data = dat, surface = TRUE)
```

Again, try dragging the graph around. (By default `scatter3d` fits the same model we did.) The dataset was a cloud of data points and `lm` has fitted a plane to it, minimising the RSS.

Here are the coefficients, rounded to one decimal place; how do we interpret them...?


```r
coef(mod_both) %>%
  round(1)
```

```
##  (Intercept)    education income_1000s 
##         -6.8          4.1          1.4
```

Firstly, the intercept is useless again as it is outside the range of valid values for prestige. It represents the level of prestige for occupations with an average zero years of education and zero income. We will deal with it later, but it's not uncommon just to ignore intercepts when interpreting model fits.

The coefficient for education is 4.1. The interpretation for this is: every year extra education is associated with 4.1 more prestige points, *whilst holding income constant*.

Similarly for income: an increase in income by $1000 is associated with 1.4 more prestige points, whilst holding education constant.

This demonstrates one of the advantages of a multiple regression model; the predictors now indicate the unique contribution of a predictor *whilst holding the other predictors constant*, assuming that the model is correct.

We also now have a formula for predicting the mean level of prestige given education and income, which we could apply to other occupations not present in the data (from the same year and country...) to see how well the model works.

The formula is:

prestige = $-6.8$ + $4.1$$\times$education + $1.4$$\times$income in thousands.




## Optional: that pesky negative intercept  {.tabset}

I've complained a couple of times about the intercept. If you really want it to take on a meaningful value, one easy solution which provides the same slope estimates, is simply to shift the predictor variables so that zero represents a value actually present in the data. We can do that by mean-centring the predictors, that is, shifting them so that zero is now the mean.

There's a command called `scale` that does this for us, but it is easy to do it "by hand" too. Simply subtract the mean from each value.

I'm going to add two variables, `education_c` and `income_1000s_c`, which represent mean centred `education` and `income`, and then save the result in a new data frame called `dat_centred`.


```r
dat_centred <- dat %>%
  mutate(education_c    = education - mean(education),
         income_1000s_c = income_1000s - mean(income_1000s))
```

Let's compare the centred and uncentred variables:


```r
dat_centred %>%
  select(education, education_c, income_1000s, income_1000s_c) %>%
  CreateTableOne(data = .)
```

```
##                             
##                              Overall     
##   n                            102       
##   education (mean (SD))      10.74 (2.73)
##   education_c (mean (SD))     0.00 (2.73)
##   income_1000s (mean (SD))    6.80 (4.25)
##   income_1000s_c (mean (SD))  0.00 (4.25)
```

As you can see, they have the same SDs. The centred variables both have mean zero, as desired.

Here's a picture of all pairwise scatterplots:


```{.r .fold-hide}
library(GGally)
```

```
## Registered S3 method overwritten by 'GGally':
##   method from   
##   +.gg   ggplot2
```

```{.r .fold-hide}
dat_centred %>%
  select(prestige,
         education, education_c,
         income_1000s, income_1000s_c) %>%
  ggpairs(upper = "blank", progress = FALSE)
```

<div class="figure" style="text-align: center">
<img src="003-regression_files/figure-html/unnamed-chunk-59-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-59)**CAPTION THIS FIGURE!!**</p>
</div>

Spend some time with a cup of tea or two to persuade yourself that centring hasn't affected relationships between key variables, it has just shifted the centred ones so they have a mean of zero.




### Activity


Now fit the model again, using prestige as the outcome variable before but now the centred versions of education and income. What does the intercept represent now?

### Answer



```r
mod_centred <- lm(prestige ~ education_c + income_1000s_c,
                  data = dat_centred)
summary(mod_centred)
```

```
## 
## Call:
## lm(formula = prestige ~ education_c + income_1000s_c, data = dat_centred)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -19.4040  -5.3308   0.0154   4.9803  17.6889 
## 
## Coefficients:
##                Estimate Std. Error t value Pr(>|t|)    
## (Intercept)     46.8333     0.7733  60.561  < 2e-16 ***
## education_c      4.1374     0.3489  11.858  < 2e-16 ***
## income_1000s_c   1.3612     0.2242   6.071 2.36e-08 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 7.81 on 99 degrees of freedom
## Multiple R-squared:  0.798,	Adjusted R-squared:  0.7939 
## F-statistic: 195.6 on 2 and 99 DF,  p-value: < 2.2e-16
```


As the table below shows, the slope estimates are identical for the two models. However, now the intercept is interpretable and gives the mean prestige at mean education and mean income.



```{.r .fold-hide}
bind_cols(Variable                = names(coef(mod_both)),
          `Original coefficients` = coef(mod_both),
          `Centred coefs`         = coef(mod_centred))
```

```
## # A tibble: 3 x 3
##   Variable     `Original coefficients` `Centred coefs`
##   <chr>                          <dbl>           <dbl>
## 1 (Intercept)                    -6.85           46.8 
## 2 education                       4.14            4.14
## 3 income_1000s                    1.36            1.36
```



## Finally: confidence intervals

To obtain confidence intervals, just use the `confint` command on the model:



```r
confint(mod_centred)
```

```
##                     2.5 %    97.5 %
## (Intercept)    45.2988979 48.367769
## education_c     3.4451272  4.829762
## income_1000s_c  0.9162805  1.806051
```

There is a version in the `car` package ("car" is short for "Companion to Applied Regression" -- the textbook) which also includes the coefficient.


```r
library(car)
Confint(mod_centred)
```

```
##                 Estimate      2.5 %    97.5 %
## (Intercept)    46.833333 45.2988979 48.367769
## education_c     4.137444  3.4451272  4.829762
## income_1000s_c  1.361166  0.9162805  1.806051
```


Another way to get the same, in case handy for other scenarios where the `car` package function is not available:


```r
cbind(Estimate = coef(mod_centred),
      confint(mod_centred))
```

```
##                 Estimate      2.5 %    97.5 %
## (Intercept)    46.833333 45.2988979 48.367769
## education_c     4.137444  3.4451272  4.829762
## income_1000s_c  1.361166  0.9162805  1.806051
```




## Very optional extras

### More on making functions

Last live session, we had a brief look at how to write a function.


```r
add_one <- function(x) {
  x + 1
}

add_one(41)
```

```
## [1] 42
```

It was not very useful.

Here is an actually useful example that does the following:

1. "Pivot" the data frame longer, so that there are multiple rows per occupation -- one for each variable -- and only two columns: `variable` and `value`
2. Group by `variable`


```r
pivot_all_longer <- function(.data) {
  .data %>%
    pivot_longer(cols      = everything(),
                 names_to  = "variable",
                 values_to = "value") %>%
    group_by(variable)
}
```

Here it is in action, applied only to numeric variables:


```r
dat %>%
  select(where(is.numeric)) %>%
  pivot_all_longer
```

```
## # A tibble: 510 x 2
## # Groups:   variable [5]
##    variable        value
##    <chr>           <dbl>
##  1 education       13.1 
##  2 income       12351   
##  3 women           11.2 
##  4 prestige        68.8 
##  5 income_1000s    12.4 
##  6 education       12.3 
##  7 income       25879   
##  8 women            4.02
##  9 prestige        69.1 
## 10 income_1000s    25.9 
## # ... with 500 more rows
```

So now there are four times as many rows and two columns.

But why...?!

You can pipe this into the `summarise` command and easily summarise all numeric variables as follows:


```r
dat %>%
  select(where(is.numeric)) %>%
  pivot_all_longer() %>%
  summarise(M        = mean(value),
            Mdn      = median(value),
            SD       = sd(value),
            valid_n  = sum(!is.na(value)),
            n        = n())
```

```
## `summarise()` ungrouping output (override with `.groups` argument)
```

```
## # A tibble: 5 x 6
##   variable           M     Mdn      SD valid_n     n
##   <chr>          <dbl>   <dbl>   <dbl>   <int> <int>
## 1 education      10.7    10.5     2.73     102   102
## 2 income       6798.   5930.   4246.       102   102
## 3 income_1000s    6.80    5.93    4.25     102   102
## 4 prestige       46.8    43.6    17.2      102   102
## 5 women          29.0    13.6    31.7      102   102
```

So it's another way to create a Table 1.



### Another way to make scatterplots: GGally

Recall that `select` is part of tidyverse and selects variables. Here I am using the base R function `is.numeric` which returns `TRUE` if a variable is numeric, e.g., not a categorical variable.

The first parameter of `ggpairs` is a data frame, which the `%>%` pipe gladly provides.


```r
library(GGally)
dat %>%
  select(where(is.numeric)) %>%
  ggpairs(upper = "blank",
          progress = FALSE)
```

<div class="figure" style="text-align: center">
<img src="003-regression_files/figure-html/unnamed-chunk-69-1.png" alt="**CAPTION THIS FIGURE!!**" width="100%" />
<p class="caption">(\#fig:unnamed-chunk-69)**CAPTION THIS FIGURE!!**</p>
</div>

The wiggly curves on the diagonals are smoothed histograms, also known as density plots.



