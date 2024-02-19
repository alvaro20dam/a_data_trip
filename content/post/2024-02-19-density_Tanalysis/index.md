---
title: "Density plot"
author: "Alvaro Gonzalez"
date: "2024-02-19"
output: html_document
---


## Life Expectancy Analysis t-test with R

Life expectancy is a measure that reflects the quality of life of a population. However, there are large differences between countries and regions of the world in this indicator. What factors explain these disparities? How can we compare the life expectancy of different groups statistically? In this paper, we will use the t-test method to analyze a worldwide life expectancy database[^1^][1].

The t-test is a test that allows us to determine if there is a significant difference between the means of two independent samples. With this tool, we will be able to answer questions such as: Is there a significant difference between the life expectancy of men and women? Which regions have the highest and lowest life expectancy? How has life expectancy evolved in recent decades? These and other questions will be addressed in this work, which aims to be a work of Statistics Art.

To perform a t-test, the following steps must be followed:

- Define the null hypothesis (Ho) and the alternative hypothesis (Ha) before collecting the data. The null hypothesis usually states that there is no difference between the means of the two groups, while the alternative asserts the opposite.
- Decide the level of significance (alpha or α). This is the risk you want to take of rejecting the null hypothesis when it is true. A value of 0.05 or 0.01 is usually chosen.
- Calculate the t-statistic. This is a measure that indicates how far the means of the two groups deviate from the expected value under the null hypothesis. The formula for calculating the t varies depending on the type of t-test used (single sample, two independent samples, or two related samples).
- Calculate degrees of freedom. These are the number of values that can vary freely in the calculation of t. The formula for calculating degrees of freedom also depends on the type of t-test used.
- Set the critical value. This is the t-value needed to reject the null hypothesis at the chosen level of significance. It can be obtained by consulting a distribution table t or by using an online calculator.

The mathematical formula for calculating the values of Student's t-table is as follows:

To calculate the values of the Student's t-table, you can use the formula for the probability density function of the t-distribution of Student[1], which is:

`\(f(t) = \frac{\Gamma(\frac{v + 1}2)}{\sqrt{v\pi} \Gamma(\frac{v}2)}{(1 + \frac{t^2}v)^-\frac{v+1}2}\)`

Where:

- `\(\Gamma\)` is the gamma function, which is a generalization of the factorial for real and complex numbers.
- `\(\pi\)` is the mathematical constant which is approximately 3.1416.

The above formula is quite complicated to work with, so you usually use a table of values or an online calculator[2][3] to find the values of Student's t. These values depend on the level of significance and the degrees of freedom chosen.

### First Load packages and data



```r
library(tidyverse)
library(patchwork)
library(gapminder)
data()
head(gapminder)
```

```
## # A tibble: 6 × 6
##   country     continent  year lifeExp      pop gdpPercap
##   <fct>       <fct>     <int>   <dbl>    <int>     <dbl>
## 1 Afghanistan Asia       1952    28.8  8425333      779.
## 2 Afghanistan Asia       1957    30.3  9240934      821.
## 3 Afghanistan Asia       1962    32.0 10267083      853.
## 4 Afghanistan Asia       1967    34.0 11537966      836.
## 5 Afghanistan Asia       1972    36.1 13079460      740.
## 6 Afghanistan Asia       1977    38.4 14880372      786.
```


```r
str(gapminder)
```

```
## tibble [1,704 × 6] (S3: tbl_df/tbl/data.frame)
##  $ country  : Factor w/ 142 levels "Afghanistan",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ continent: Factor w/ 5 levels "Africa","Americas",..: 3 3 3 3 3 3 3 3 3 3 ...
##  $ year     : int [1:1704] 1952 1957 1962 1967 1972 1977 1982 1987 1992 1997 ...
##  $ lifeExp  : num [1:1704] 28.8 30.3 32 34 36.1 ...
##  $ pop      : int [1:1704] 8425333 9240934 10267083 11537966 13079460 14880372 12881816 13867957 16317921 22227415 ...
##  $ gdpPercap: num [1:1704] 779 821 853 836 740 ...
```

## Including Plots

You can also embed plots, for example:

<img src="{{< blogdown/postref >}}index_files/figure-html/pressure-1.png" width="672" />

Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.
