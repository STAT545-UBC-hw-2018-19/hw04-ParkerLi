hw04\_YongzhengParkerLi
================
Yongzheng Parker Li
10/8/2018

This file is the Rmd file of *Homework 04* of the course STAT545A, taught by [Vincenzo Coia](https://github.com/vincenzocoia) at the University of British Columbia (UBC). The detailed requirements of this assignment could be found [here](http://stat545.com/Classroom/assignments/hw04/hw04.html). The STAT545A course page is [here](http://stat545.com/Classroom/). My own participation repository is [here](https://github.com/ParkerLi/STAT545_participation). The goal of this assignment is to solidify my data wrangling skills by working some realistic problems in the grey area between data aggregation and data reshasping.

Bring Data In
=============

This section load all the necessary packages. To disable all the conflicts/warning and other messges, I utilized the <code>suppressPackageStartupMessages</code> function I learned from class. Another way to do this is to use <code>Message=F</code> or <code>warn.conflicts=F</code> in the R chunk setting.

``` r
suppressPackageStartupMessages(library(gapminder))
suppressPackageStartupMessages(library(tidyr))
suppressPackageStartupMessages(library(ggplot2))
suppressPackageStartupMessages(library(dplyr))
```

    ## Warning: package 'dplyr' was built under R version 3.5.1

``` r
suppressPackageStartupMessages(library(knitr))
```

Data Reshaping
==============

I pick *prompt 2* in this section: make a tibble with one row per year and columns for life expectancy for two or more countries. This activity combines both table and graph reshaping. - use <code>knitr::kable()</code> to make this table look pretty in your rendered homework

``` r
newgapminder <- gapminder %>% 
  select(year, country, lifeExp) %>% 
  group_by(country) %>% 
  filter(country == "Canada" | country == "China" | country == "Italy")
spread(newgapminder, key = country, value = lifeExp) %>% 
  knitr::kable(format = "markdown", justify = "centre",
               digits = 1, caption = "Data Reshaping, Activity 2: Canada, China, and Italy")
```

|  year|  Canada|  China|  Italy|
|-----:|-------:|------:|------:|
|  1952|    68.8|   44.0|   65.9|
|  1957|    70.0|   50.5|   67.8|
|  1962|    71.3|   44.5|   69.2|
|  1967|    72.1|   58.4|   71.1|
|  1972|    72.9|   63.1|   72.2|
|  1977|    74.2|   64.0|   73.5|
|  1982|    75.8|   65.5|   75.0|
|  1987|    76.9|   67.3|   76.4|
|  1992|    78.0|   68.7|   77.4|
|  1997|    78.6|   70.4|   78.8|
|  2002|    79.8|   72.0|   80.2|
|  2007|    80.7|   73.0|   80.5|

In this process, I used piping to make the code more straightforward. <code>select</code> picks the necessary columns I want. <code>group\_by</code> categories the data into different country groups. <code>filter</code> gives me the countries I want--Canada, China, and Italy--countries I am a resident. <code>kable</code> knits the table into a better table format.

-   take advantage of this new data shape to scatterplot life expectancy for one country against that of another

``` r
newgapminder %>% 
  ggplot(aes(year, lifeExp)) +
  geom_point(aes(colour = country)) +
  xlab("Year") +
  ylab("LifeExp") +
  ggtitle("Life Expectancy, 1952-2007, Canada-China-Italy")
```

![](hw04_YongzhengParkerLi_files/figure-markdown_github/reshaping_01-1.png)

This part takes advantage of the new data shape to scatterplop life expectancy for the three countries I picked. I give them different colors and add explanations (x and y axis, title). We can tell that all countries have an increased lifeExp trend. Canada and Italy, both developed countries, share similar trend and life values; China, on the other hand, also increases but the baseline is low and still catching up.

Data Join
=========

I pick *prompt 1* that explores the different kinds of join functions. I had a hard time deciding which complementary data frame to use. However, I got inspired by one classmate: QinxinLin. I did one peer review for her homework and noticed one brilliant data frame. I want to show my gratitude to her! Thank you! Dataset is from (here)\[<http://www.exploredata.net/Downloads/WHO-Data-Set>\].

``` r
new_dataset <- read.csv("WHO.csv") %>% 
  select(country, Energy_use, Exports_of_goods_and_services) 
kable(head(new_dataset, 10))
```

| country             |  Energy\_use|  Exports\_of\_goods\_and\_services|
|:--------------------|------------:|----------------------------------:|
| Afghanistan         |           NA|                              12.43|
| Albania             |       761.64|                              22.27|
| Algeria             |      1058.25|                              47.84|
| Andorra             |           NA|                                 NA|
| Angola              |       614.97|                              79.28|
| Antigua and Barbuda |           NA|                              59.56|
| Argentina           |      1644.46|                              25.07|
| Armenia             |       847.68|                              27.28|
| Australia           |      5978.28|                              20.30|
| Austria             |      4173.66|                              54.29|

The new data frame share the same *country* category. I added *Energy\_use* and *Exports\_of\_goods\_and\_services* as the two new variables. To make the table reader-friendly, I only demonstrate the first 10 rows. The following sections show the different use of <code>join</code> functions.

``` r
left <- left_join(gapminder, new_dataset, by = "country")
kable(head(left, 10))
```

| country     | continent |  year|  lifeExp|       pop|  gdpPercap|  Energy\_use|  Exports\_of\_goods\_and\_services|
|:------------|:----------|-----:|--------:|---------:|----------:|------------:|----------------------------------:|
| Afghanistan | Asia      |  1952|   28.801|   8425333|   779.4453|           NA|                              12.43|
| Afghanistan | Asia      |  1957|   30.332|   9240934|   820.8530|           NA|                              12.43|
| Afghanistan | Asia      |  1962|   31.997|  10267083|   853.1007|           NA|                              12.43|
| Afghanistan | Asia      |  1967|   34.020|  11537966|   836.1971|           NA|                              12.43|
| Afghanistan | Asia      |  1972|   36.088|  13079460|   739.9811|           NA|                              12.43|
| Afghanistan | Asia      |  1977|   38.438|  14880372|   786.1134|           NA|                              12.43|
| Afghanistan | Asia      |  1982|   39.854|  12881816|   978.0114|           NA|                              12.43|
| Afghanistan | Asia      |  1987|   40.822|  13867957|   852.3959|           NA|                              12.43|
| Afghanistan | Asia      |  1992|   41.674|  16317921|   649.3414|           NA|                              12.43|
| Afghanistan | Asia      |  1997|   41.763|  22227415|   635.3414|           NA|                              12.43|

To make it simpler, I use *a* to emobody *gapminder*, *b* to embody *new\_dataset*. <code>left\_join</code> joins matching rows from b to a.

``` r
right <- right_join(gapminder, new_dataset, by = "country")
kable(head(right, 10))
```

| country     | continent |  year|  lifeExp|       pop|  gdpPercap|  Energy\_use|  Exports\_of\_goods\_and\_services|
|:------------|:----------|-----:|--------:|---------:|----------:|------------:|----------------------------------:|
| Afghanistan | Asia      |  1952|   28.801|   8425333|   779.4453|           NA|                              12.43|
| Afghanistan | Asia      |  1957|   30.332|   9240934|   820.8530|           NA|                              12.43|
| Afghanistan | Asia      |  1962|   31.997|  10267083|   853.1007|           NA|                              12.43|
| Afghanistan | Asia      |  1967|   34.020|  11537966|   836.1971|           NA|                              12.43|
| Afghanistan | Asia      |  1972|   36.088|  13079460|   739.9811|           NA|                              12.43|
| Afghanistan | Asia      |  1977|   38.438|  14880372|   786.1134|           NA|                              12.43|
| Afghanistan | Asia      |  1982|   39.854|  12881816|   978.0114|           NA|                              12.43|
| Afghanistan | Asia      |  1987|   40.822|  13867957|   852.3959|           NA|                              12.43|
| Afghanistan | Asia      |  1992|   41.674|  16317921|   649.3414|           NA|                              12.43|
| Afghanistan | Asia      |  1997|   41.763|  22227415|   635.3414|           NA|                              12.43|

<code>right\_join</code> joins matching rows from a to b.

``` r
inner <- inner_join(gapminder, new_dataset, by = "country")
kable(head(inner, 10))
```

| country     | continent |  year|  lifeExp|       pop|  gdpPercap|  Energy\_use|  Exports\_of\_goods\_and\_services|
|:------------|:----------|-----:|--------:|---------:|----------:|------------:|----------------------------------:|
| Afghanistan | Asia      |  1952|   28.801|   8425333|   779.4453|           NA|                              12.43|
| Afghanistan | Asia      |  1957|   30.332|   9240934|   820.8530|           NA|                              12.43|
| Afghanistan | Asia      |  1962|   31.997|  10267083|   853.1007|           NA|                              12.43|
| Afghanistan | Asia      |  1967|   34.020|  11537966|   836.1971|           NA|                              12.43|
| Afghanistan | Asia      |  1972|   36.088|  13079460|   739.9811|           NA|                              12.43|
| Afghanistan | Asia      |  1977|   38.438|  14880372|   786.1134|           NA|                              12.43|
| Afghanistan | Asia      |  1982|   39.854|  12881816|   978.0114|           NA|                              12.43|
| Afghanistan | Asia      |  1987|   40.822|  13867957|   852.3959|           NA|                              12.43|
| Afghanistan | Asia      |  1992|   41.674|  16317921|   649.3414|           NA|                              12.43|
| Afghanistan | Asia      |  1997|   41.763|  22227415|   635.3414|           NA|                              12.43|

<code>inner\_join</code> retains only rows in both sets.

``` r
full <- full_join(gapminder, new_dataset, by = "country")
kable(head(full, 10))
```

| country     | continent |  year|  lifeExp|       pop|  gdpPercap|  Energy\_use|  Exports\_of\_goods\_and\_services|
|:------------|:----------|-----:|--------:|---------:|----------:|------------:|----------------------------------:|
| Afghanistan | Asia      |  1952|   28.801|   8425333|   779.4453|           NA|                              12.43|
| Afghanistan | Asia      |  1957|   30.332|   9240934|   820.8530|           NA|                              12.43|
| Afghanistan | Asia      |  1962|   31.997|  10267083|   853.1007|           NA|                              12.43|
| Afghanistan | Asia      |  1967|   34.020|  11537966|   836.1971|           NA|                              12.43|
| Afghanistan | Asia      |  1972|   36.088|  13079460|   739.9811|           NA|                              12.43|
| Afghanistan | Asia      |  1977|   38.438|  14880372|   786.1134|           NA|                              12.43|
| Afghanistan | Asia      |  1982|   39.854|  12881816|   978.0114|           NA|                              12.43|
| Afghanistan | Asia      |  1987|   40.822|  13867957|   852.3959|           NA|                              12.43|
| Afghanistan | Asia      |  1992|   41.674|  16317921|   649.3414|           NA|                              12.43|
| Afghanistan | Asia      |  1997|   41.763|  22227415|   635.3414|           NA|                              12.43|

<code>full\_join</code> retains all values, all rows.

``` r
semi <- semi_join(gapminder, new_dataset, by = "country")
kable(head(semi, 10))
```

| country     | continent |  year|  lifeExp|       pop|  gdpPercap|
|:------------|:----------|-----:|--------:|---------:|----------:|
| Afghanistan | Asia      |  1952|   28.801|   8425333|   779.4453|
| Afghanistan | Asia      |  1957|   30.332|   9240934|   820.8530|
| Afghanistan | Asia      |  1962|   31.997|  10267083|   853.1007|
| Afghanistan | Asia      |  1967|   34.020|  11537966|   836.1971|
| Afghanistan | Asia      |  1972|   36.088|  13079460|   739.9811|
| Afghanistan | Asia      |  1977|   38.438|  14880372|   786.1134|
| Afghanistan | Asia      |  1982|   39.854|  12881816|   978.0114|
| Afghanistan | Asia      |  1987|   40.822|  13867957|   852.3959|
| Afghanistan | Asia      |  1992|   41.674|  16317921|   649.3414|
| Afghanistan | Asia      |  1997|   41.763|  22227415|   635.3414|

<code>semi\_join</code> shows all rows in a that a match in b.

``` r
anti <- anti_join(gapminder, new_dataset, by = "country")
kable(head(anti, 10))
```

| country | continent |  year|  lifeExp|       pop|  gdpPercap|
|:--------|:----------|-----:|--------:|---------:|----------:|
| Iran    | Asia      |  1952|   44.869|  17272000|   3035.326|
| Iran    | Asia      |  1957|   47.181|  19792000|   3290.258|
| Iran    | Asia      |  1962|   49.325|  22874000|   4187.330|
| Iran    | Asia      |  1967|   52.469|  26538000|   5906.732|
| Iran    | Asia      |  1972|   55.234|  30614000|   9613.819|
| Iran    | Asia      |  1977|   57.702|  35480679|  11888.595|
| Iran    | Asia      |  1982|   59.620|  43072751|   7608.335|
| Iran    | Asia      |  1987|   63.040|  51889696|   6642.881|
| Iran    | Asia      |  1992|   65.742|  60397973|   7235.653|
| Iran    | Asia      |  1997|   68.042|  63327987|   8263.590|

<code>anti\_join</code> shows all rows in a that do not have a match in b.
