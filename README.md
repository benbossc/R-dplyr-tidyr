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
![select(data.frame,a,c)](https://raw.githubusercontent.com/benbossc/R-Dataframe-Manipulation-dplyr/main/fig/R-Dataframe-Manipulation-dplyr-fig1.png?token=AMNE5BMZREVTP6VYBHYIQGTBYDZQK)

# Using filter()

To select on some subset of rows:
```R
filter(gapminder, continent == "Europe")
```
```{r}
# A tibble: 360 x 6
   country  year     pop continent lifeExp gdpPercap
   <chr>   <dbl>   <dbl> <chr>       <dbl>     <dbl>
 1 Albania  1952 1282697 Europe       55.2     1601.
 2 Albania  1957 1476505 Europe       59.3     1942.
 3 Albania  1962 1728137 Europe       64.8     2313.
 4 Albania  1967 1984060 Europe       66.2     2760.
 5 Albania  1972 2263554 Europe       67.7     3313.
 6 Albania  1977 2509048 Europe       68.9     3533.
 7 Albania  1982 2780097 Europe       70.4     3631.
 8 Albania  1987 3075321 Europe       72       3739.
 9 Albania  1992 3326498 Europe       71.6     2497.
10 Albania  1997 3428038 Europe       73.0     3193.
# … with 350 more rows
```
Or select another subset of rows:
```R
filter(gapminder, pop > 1000000)
```
```{r}# A tibble: 1,524 x 6
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
# … with 1,514 more rows
```
Above we used ‘normal’ grammar, but the strengths of dplyr lie in combining several functions using pipes. This <a href="https://twitter.com/WeAreRLadies/status/1172576445794803713">description</a> is very useful:
```{r}
I work up %>% 
  showered %>% 
  dressed %>% 
  had coffee %>% 
  came to an R workshop
```
Since the pipes grammar is unlike anything we’ve seen in R before, let’s repeat what we’ve done above using pipes.
```R
year_country_gdp <- gapminder %>% select(year, country, gdpPercap)
```
To help you understand why we wrote that in that way, let’s walk through it step by step. First we summon the gapminder dataframe and pass it on, using the pipe symbol %>%, to the next step, which is the select() function. In this case we don’t specify which data object we use in the select() function since in gets that from the previous pipe. Fun Fact: There is a good chance you have encountered pipes before in the shell. In R, a pipe symbol is %>% while in the shell it is | but the concept is the same!

# Using filter() with pipes
If we now wanted to move forward with the above, but only with European countries, we can combine select and filter
```R
year_country_gdp_euro <- gapminder %>%
    filter(continent == "Europe") %>%
    select(year, country, gdpPercap)
```
## Question 1

As with last time, first we pass the gapminder dataframe to the filter() function, then we pass the filtered version of the gapminder dataframe to the select() function. Note: The order of operations is very important in this case. If we used ‘select’ first, filter would not be able to find the variable continent since we would have removed it in the previous step.

# Using group_by() and summarize()

Now, we were supposed to be reducing the error prone repetitiveness of what can be done with base R, but up to now we haven’t done that since we would have to repeat the above for each continent. Instead of filter(), which will only pass observations that meet your criteria (in the above: continent=="Europe"), we can use group_by(), which will essentially use every unique criteria that you could have used in filter.
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
```R
gapminder %>% group_by(continent)
```
```{r}
# A tibble: 1,704 x 6
# Groups:   continent [5]
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
You will notice that the structure of the dataframe where we used group_by() (grouped_df) is not the same as the original gapminder (data.frame). A grouped_df can be thought of as a list where each item in the list is a data.frame which contains only the rows that correspond to the a particular value continent (at least in the example above).



