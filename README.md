# R: Dataframe Manipulation with dplyr

<a href="http://creativecommons.org/licenses/by-nc/4.0/" rel="license"><img style="border-width: 0;" src="https://i.creativecommons.org/l/by-nc/4.0/88x31.png" alt="Creative Commons License" /></a>
This tutorial is licensed under a <a href="http://creativecommons.org/licenses/by-nc/4.0/" rel="license">Creative Commons Attribution-NonCommercial 4.0 International License</a>.

# Acknowledgements
This lab is sourced from the The Software Carpentry <a href="https://swcarpentry.github.io/r-novice-gapminder/"> R for Reproducible Scientific Analysis </a> lesson material: Thomas Wright and Naupaka Zimmerman (eds): Software Carpentry: R for Reproducible Scientific Analysis. Version 2016.06, June 2016, https://github.com/swcarpentry/r-novice-gapminder, 10.5281/zenodo.57520.

# Setting Things Up

Let's use the readr package to read external data into R, for this lesson we are going to use the gapminder data.

```R
library("readr")
gapminder <- read_csv("data/gapminder_data.csv")
```

```{r}
── Column specification ────────────────────────────────────────────────────────
cols(
  country = col_character(),
  year = col_double(),
  pop = col_double(),
  continent = col_character(),
  lifeExp = col_double(),
  gdpPercap = col_double()
)
```
```R
gapminder
```
```{r}
# A tibble: 1,704 x 6
   country      year      pop continent lifeExp gdpPercap
   <chr>       <dbl>    <dbl> <chr>       <dbl>     <dbl>
 1 Afghanistan  1952  8425333 Asia         28.8      779.
 2 Afghanistan  1957  9240934 Asia         30.3      821.
 3 Afghanistan  1962 10267083 Asia         32.0      853.
 4 Afghanistan  1967 11537966 Asia         34.0      836.
 5 Afghanistan  1972 13079460 Asia         36.1      740.
 6 Afghanistan  1977 14880372 Asia         38.4      786.
 7 Afghanistan  1982 12881816 Asia         39.9      978.
 8 Afghanistan  1987 13867957 Asia         40.8      852.
 9 Afghanistan  1992 16317921 Asia         41.7      649.
10 Afghanistan  1997 22227415 Asia         41.8      635.
# … with 1,694 more rows
```
Manipulation of dataframes means many things to many researchers, we often select certain observations (rows) or variables (columns), we often group the data by a certain variable(s), or we even calculate summary statistics. We can do these types of operations using the normal base R operations:
```R
mean(gapminder$gdpPercap)
```
```{r}
[1] 7215.327
```
The question here is how might you summarize split by some group you might be interested in.

# The ```dplyr``` Package 
Luckily, the dplyr package provides a number of very useful functions for manipulating dataframes in a way that will reduce the above repetition, reduce the probability of making errors, and probably even save you some typing. As an added bonus, you might even find the dplyr grammar easier to read.

Here we’re going to cover 5 of the most commonly used functions as well as using pipes (%>%) to combine them.
1. ```select()```
2. ```filter()```
3. ```group_by()```
4. ```summarize()```
5. ```mutate()```

If you have have not installed this package earlier, please do so:
```R
install.packages('dplyr')
```
Now let’s load the package:
```R
library("dplyr")
```
# Using select()

If, for example, we wanted to move forward with only a few of the variables in our dataframe we could use the select() function. This will keep only the variables you select.
```R
year_country_gdp <- select(gapminder, year, country, gdpPercap)
```

![alt text](https://bcgov.github.io/ds-intro-to-r-2-day/fig/07-dplyr-fig1.png)


