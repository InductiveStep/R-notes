--- 
title: "R notes"
author: "Andi Fugard"
date: "2020-11-13"
site: bookdown::bookdown_site
documentclass: book
bibliography:
- book.bib
- packages.bib
biblio-style: apalike
link-citations: yes
description: These are some notes on using R
---
--- 
title: "R notes"
author: "Andi Fugard"
date: "2020-11-13"
site: bookdown::bookdown_site
documentclass: book
bibliography:
- book.bib
- packages.bib
biblio-style: apalike
link-citations: yes
description: These are some notes on using R
---





# Introduction

These are under development...



<div class='solution'><button>Solution</button>

Hello there.

Wow.

</div>


<!--chapter:end:index.Rmd-->


# Starting R

Placeholder


## Arithmetic {.tabset}
### Activities
### Answers
## Variables {.tabset}
### Activity
### Answer
## A note on variable names
## Vectors {.tabset}
### Activity
### Answer
## Functions {.tabset}
### Activity
### Answer
## More functions {.tabset}
### Activity
### Answer
## Data frames {.tabset}
### Activity
### Answer
## Loading data frames from a file {.tabset}
### Activity
### Answer
## Packages  {.tabset}
### Activity
### Answer
## The end!

<!--chapter:end:001-starting_r.Rmd-->


# Visualising data in the tidyverse

Placeholder


## Getting setup
## An interlude on functions
## A scatterplot in ggplot {.tabset}
### Warm-up activity
### Answer
## Another aesthetic: colour
## Another geom: jitter {.tabset}
### Activity to develop your help-searching skill!
### Answer
## Aggregating/summarising data by group {.tabset}
### Activity
### Answer
## Pipes
## Plot the mean life expectancy by continent {.tabset}
### Actvity
### Answer
## Yet another geom: line {.tabset}
### Activity
### Answer
## Filtering data along the pipeline {.tabset}
### Activity
### Answer
## Other handy tools: select, slice, bind, and arrange
## Filtering for members of a vector {.tabset}
## Final challenge {.tabset}
### Activity
### Answer

<!--chapter:end:002-visualising-data.Rmd-->


# Linear regression

Placeholder


## Before we begin
## The dataset
## Interlude on methodology
## Descriptives {.tabset}
### Activity
### Answer
## Prep to understand the simplest regression model {.tabset}
### Activity
### Answer
## The simplest regression model: intercept only model {.tabset}
### Activity
### Answer
## Adding a slope to the regression model {.tabset}
### Activity
### Answer
## Intermezzo on causality
## Residuals {.tabset}
### Activity
### Answer
## Comparing models
## Interlude on what "*F*(1,100) = 260.8" means
## Regression with two or more predictors {.tabset}
### Activity
### Answer
## Interpreting regression models with two or more predictors
## Optional: that pesky negative intercept  {.tabset}
### Activity
### Answer
## Finally: confidence intervals
## Very optional extras
### More on making functions
### Another way to make scatterplots: GGally

<!--chapter:end:003-regression.Rmd-->


# Linear regression diagnostics

Placeholder


## Before we begin
## The dataset
## Fit a regression model {.tabset}
### Activity
### Answer
## Checking for normally distributed residuals
### Base R histogram
### Quantile-comparison plot
#### Illustration using simulated data
### Statistical test of normality {.tabset}
#### Activity
#### Answer
## Checking constant residual variance {.tabset}
### Activity
### Answer
## Checking for relationships between residuals and predicted outcome or predictors
## Checking linearity
### What should be linear in a linear model?
### Checking for linearity {.tabset}
#### Activity
#### Answer
## Checking influence: leave-one-out analyses
### Residual outliers
### Cook's distance
### DFBETA and (close sibling) DFBETAS
### View them all
### So, er, what should we do with "potentially influential" observations...?
## Checking the variance inflation factors (VIFs) {.tabset}
### Activity
### Answer
## The challenge {.tabset}
### Activity
### Answer

<!--chapter:end:004-regression-dx.Rmd-->


# Categorical predictors and interactions

Placeholder


## Before we begin
## The dataset
## Factors
## Visualising the data
## The punchline: occupation type does predict prestige
## Understanding factors in regression models
### How are categorical variables encoded? {.tabset}
#### Activity
#### Answer
### How are binary (two-level) categorical predictors encoded?
### Categorical predictors with 3 or more levels
## Interpreting the coefficients {.tabset}
### Activity
### Answer
## Checking all combinations
## The intercept is not always the mean of the comparison group
## Recap
## Challenge {.tabset}
### Activity
### Answers
## Brief introduction to interactions
### What is an interaction?
###  How to test for interactions in R
### Understanding interactions

<!--chapter:end:005-categoricals-and-interactions.Rmd-->


# Logistic regression

Placeholder


## Setup
## The dataset
## Warmup activity {.tabset}
### Activity
### Answer
## The punchline
## Intermezzo: parametric versus nonparametric
## What is a generalised linear model? {#glms}
## What is the log function again...?
### The arithmetic {.tabset #log-arithmetic}
#### Activity
#### Answer
### Why log?
## Intercept-only models again
## Odds and log odds
## Back to that intercept
## Interpreting model slopes {#interpret-slopes}
### Interpret on the log-odds scale {.tabset}
#### Activity
#### Answer
### Interpret using the "divide-by-4" approximation  {.tabset}
#### Activity
#### Answer
### Interpret using odds
#### Some arithmetic 
#### What does this mean? (Or: so what?) {#exp-meaning}
### Interpret using predicted probabilities
#### Using a customs predictions table
#### Use ggeffects {#easierpredictions}
## Diagnostics
### Check the residual distribution
### Check that the residual mean is constant
### Linearity of predictors
### Influence
### Multicolinearity
## A challenge {.tabset}
### Activity
### (An) Answer

<!--chapter:end:006-logistic-regression.Rmd-->

# References

Belsey, D. A., Kuh, E., & Welsch, R. E. (1980). Regression Diagnostics: Identifying Influential Data and Sources of Collinearity. Hoboken, NJ: John Wiley & Sons, Inc.

Bollen, K. A., & Jackman, R. W. (1985). Regression Diagnostics: An Expository Treatment of Outliers and Influential Cases. *Sociological Methods & Research*, *13*(4), 510–542.

Chang, W. (2020). [R Graphics Cookbook](https://r-graphics.org/) (2nd ed.). Sebastopol, CA: O’Reilly.

Fox, J. & Weisberg, S. (2019). *An R Companion to Applied Regression*, Third Edition, Sage.

Gelman, A., & Hill, J. (2007). *Data Analysis Using Regression and Multilevel/Hierarchical Models*. Cambridge: Cambridge University Press.

Healy, K. (2019). [Data Visualization: A Practical Introduction](https://socviz.co/). Princeton University Press.

Lüdecke D (2018). [ggeffects: Tidy Data Frames of Marginal Effects from Regression Models](https://joss.theoj.org/papers/10.21105/joss.00772). Journal of Open Source Software, 3(26), 772. doi: 10.21105/joss.00772

Mroz, T. A. (1987). The sensitivity of an empirical model of married women's hours of work to economic and statistical assumptions. *Econometrica*, *55*, 765–799.

Noether, G. E. (1984). Nonparametrics: The Early Years-Impressions and Recollections. *American Statistician*, *38*, 173-178]

Wickham, H., & Grolemund, G. (2017). [R for Data Science: Import, Tidy, Transform, Visualize, and Model Data](http://r4ds.had.co.nz/). Sebastopol, CA: O’Reilly.

Wolfowitz, J. (1942). Additive Partition Functions and a Class of Statistical Hypotheses. *Annals of Mathematical Statistics*, *13*, 247-279

<!--chapter:end:999-references.Rmd-->

