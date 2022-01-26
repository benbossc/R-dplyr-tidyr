# R: Dataframe Manipulation with dplyr and tidyr

<a href="http://creativecommons.org/licenses/by-nc/4.0/" rel="license"><img style="border-width: 0;" src="https://i.creativecommons.org/l/by-nc/4.0/88x31.png" alt="Creative Commons License" /></a>
This tutorial is licensed under a <a href="http://creativecommons.org/licenses/by-nc/4.0/" rel="license">Creative Commons Attribution-NonCommercial 4.0 International License</a>.

# Acknowledgements
This lab is sourced from the The Software Carpentry <a href="https://swcarpentry.github.io/r-novice-gapminder/"> "R for Reproducible Scientific Analysis" </a> lesson material: Thomas Wright and Naupaka Zimmerman (eds): Software Carpentry: R for Reproducible Scientific Analysis. Version 2016.06, June 2016, https://github.com/swcarpentry/r-novice-gapminder, 10.5281/zenodo.57520. The introductory text/content about tidy data is derived from Dr. Katherine Walden's <a href="https://swcarpentry.github.io/r-novice-gapminder/"> "Introduction to Tidy Data in R." </a>

# Setting the Stage: Tidy Data

Hadley Wickham's 2014 article in the Journal of Statistical Software outlines the foundations and principles of tidy data.

Article abstract: "Article abstract: "A huge amount of effort is spent cleaning data to get it ready for analysis, but there has been little research on how to make data cleaning as easy and effective as possible. This paper tackles a small, but important, component of data cleaning: data tidying. Tidy datasets are easy to manipulate, model and visualize, and have a specific structure: each variable is a column, each observation is a row, and each type of observational unit is a table. This framework makes it easy to tidy messy datasets because only a small set of tools are needed to deal with a wide range of un-tidy datasets. This structure also makes it easier to develop tidy tools for data analysis, tools that both input and output tidy datasets. The advantages of a consistent data structure and matching tools are demonstrated with a case study free from mundane data manipulation chores." (Hadley Wickham, Tidy Data, Vol. 59, Issue 10, Sep 2014, Journal of Statistical Software. http://www.jstatsoft.org/v59/i10.)"

Wickham's tidy data principles have become widely used in data science and other statistical software applications. The ethos tidy data is aligned with making data machine readable, which is a major component of open government data. Making data tidy also aligns with best practices associated with research data management and making data FAIR (Findable, Accessible, Interoperable, and Reusable).

# Some Tidy Data Principles

1. Designing spreadsheets that are “tidy, consistent, and as resistant to mistakes as possible”

2. Be Consistent:
- Use consistent codes for categorical variables
- Use a consistent fixed code for any missing values
- Use consistent variable names
- Use consistent subject identifiers
- Use a consistent data layout in multiple files
- Use consistent file names
- Use a consistent format for all dates
- Use consistent phrases in your notes
- Be careful about extra spaces within cells

3. Choose Good Names for Things:
- Avoid spaces
- Avoid special characters
- Be short but meaningful

4. Write Dates as YYYY-MM-DD
- Or have separate columns for YEAR, MONTH, DATE

5. No Empty Cells

6. Put Just One Thing in a Cell

7. Make it a Rectangle
- Single first row with variable names

8. Create a Data Dictionary
- Esentially METADATA!!! An important component of FAIR Open Government Data.
- “This is part of the metadata that you will want to prepare: information about the data” 
- You might also find this information in a codebook that goes with a dataset
- Things to include:
  - The exact variable name as in the data file
  - A version of the variable name that might be used in data visualizations
  - A longer explanation of what the variable means
  - The measurement units
  - Expected minimum and maximum values

9. No Calculations in the Raw Data Files

10. Do Not Use Font Color or Highlighting as Data

11. Make Backups
- Multiple locations (OneDrive, local computer, etc.)
- Version control program (i.e. Git)
- Write protect the file when not entering data

12. Use Data Validation to Avoid Errors

13. Save a Copy of the Data in Plain Text Files
- File formats can include comma-separated values (CSV) or plain-text (TXT)

# Common Formatting Issues
Check out the <a href="https://swcarpentry.github.io/r-novice-gapminder/"> "formatting issues" page </a> of the Tidy data for librarians Carpentries webpage for a list of common spreadsheet problems.

# Question 1
What questions do you have about these principles? Which ones are unclear are confusing?

# Setting Things Up

Let's use the readr package to read external data into R, for this lesson we are going to use the gapminder data.

```R
library("readr")
gapminder <- read_csv("data/gapminder_data.csv")
# Friendly reminder: remember, the above code assumes that you've placed gapminder_data.csv in the "data" folder of your working directory.
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
<img src = "fig/R-Dataframe-Manipulation-dplyr-fig1.png">

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
## Question 2

As with last time, first we pass the gapminder dataframe to the filter() function, then we pass the filtered version of the gapminder dataframe to the select() function. Note: The order of operations is very important in this case. If we used ‘select’ first, filter would not be able to find the variable continent since we would have removed it in the previous step.

Please now write a command line (which can spans multiples lines by relying on piping) that produces a data frame with the following criteria: 1) Asia and Oceania; 2) values for ```year```, ```country```, ```pop```, and  ```lifeExp```. How many rows did you end up getting and why? 

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
<img src = "fig/R-Dataframe-Manipulation-dplyr-fig2.png">

# Using summarize()
The above was a bit on the uneventful side but group_by() is much more exciting in conjunction with summarize(). This will allow us to create new variable(s) by using functions that repeat for each of the continent-specific data frames. That is to say, using the group_by() function, we split our original dataframe into multiple pieces, then we can run functions (e.g. mean() or sd()) within summarize().
```R
gdp_bycontinents <- gapminder %>%
    group_by(continent) %>%
    summarize(mean_gdpPercap = mean(gdpPercap))
```
```{r}
`summarise()` ungrouping output (override with `.groups` argument)
```
<img src = "fig/R-Dataframe-Manipulation-dplyr-fig3.png">

```{r}
# A tibble: 5 x 2
  continent mean_gdpPercap
  <chr>              <dbl>
1 Africa             2194.
2 Americas           7136.
3 Asia               7902.
4 Europe            14469.
5 Oceania           18622.
```

That allowed us to calculate the mean gdpPercap for each continent, but it gets even better.

## Question 3 

The function group_by() allows us to group by multiple variables. Let’s group by year and continent.

```R
gdp_bycontinents_byyear <- gapminder %>%
    group_by(continent, year) %>%
    summarize(mean_gdpPercap = mean(gdpPercap))
```

```{r}
`summarise()` regrouping output by 'continent' (override with `.groups` argument)
```
That is already quite powerful, but it gets even better! You’re not limited to defining 1 new variable in summarize().
```R
gdp_pop_bycontinents_byyear <- gapminder %>%
    group_by(continent, year) %>%
    summarize(mean_gdpPercap = mean(gdpPercap),
              sd_gdpPercap = sd(gdpPercap),
              mean_pop = mean(pop),
              sd_pop = sd(pop))
```
```{r}
`summarise()` regrouping output by 'continent' (override with `.groups` argument)
```

Create a new dataframe from gapminder. Only include non-European countries and calculate the average life expectancy per country. Which has the longest average life expectancy and which has the shortest average life expectancy?

# Using Using count()

A very common operation is to count the number of observations for each group. The dplyr package comes with a function that helps with this.

For instance, if we wanted to check the number of countries included in the dataset for the year 2002, we can use the count() function. It takes the name of one or more columns that contain the groups we are interested in, and we can optionally sort the results in descending order by adding sort=TRUE:

```R
gapminder %>%
    filter(year == 2002) %>%
    count(continent, sort = TRUE)
```
```{r}
# A tibble: 5 x 2
  continent     n
  <chr>     <int>
1 Africa       52
2 Asia         33
3 Europe       30
4 Americas     25
5 Oceania       2
```
# Using Using mutate()

We can also create new variables prior to (or even after) summarizing information using mutate().
```R
gdp_pop_bycontinents_byyear <- gapminder %>%
    mutate(gdp_billion = gdpPercap*pop/10^9) %>%
    group_by(continent,year) %>%
    summarize(mean_gdpPercap = mean(gdpPercap),
              sd_gdpPercap = sd(gdpPercap),
              mean_pop = mean(pop),
              sd_pop = sd(pop),
              mean_gdp_billion = mean(gdp_billion),
              sd_gdp_billion = sd(gdp_billion))
```
```{r}
`summarise()` regrouping output by 'continent' (override with `.groups` argument)
```
# Connect mutate with logical groupings: ifelse
When creating new variables, we can hook this with a logical condition. A simple combination of mutate() and ifelse() facilitates filtering right where it is needed: in the moment of creating something new. This easy-to-read statement is a fast and powerful way of discarding certain data (even though the overall dimension of the data frame will not change) or for updating values depending on this given condition.
```R
## keeping all data but "filtering" after a certain condition
# calculate GDP only for people with a life expectation above 25
gdp_pop_bycontinents_byyear_above25 <- gapminder %>%
    mutate(gdp_billion = ifelse(lifeExp > 25, gdpPercap * pop / 10^9, NA)) %>%
    group_by(continent, year) %>%
    summarize(mean_gdpPercap = mean(gdpPercap),
              sd_gdpPercap = sd(gdpPercap),
              mean_pop = mean(pop),
              sd_pop = sd(pop),
              mean_gdp_billion = mean(gdp_billion),
              sd_gdp_billion = sd(gdp_billion))
```
```{r}
`summarise()` regrouping output by 'continent' (override with `.groups` argument)
```
```R
## updating only if certain condition is fullfilled
# for life expectations above 40 years, the gpd to be expected in the future is scaled
gdp_future_bycontinents_byyear_high_lifeExp <- gapminder %>%
    mutate(gdp_futureExpectation = ifelse(lifeExp > 40, gdpPercap * 1.5, gdpPercap)) %>%
    group_by(continent, year) %>%
    summarize(mean_gdpPercap = mean(gdpPercap),
              mean_gdpPercap_expected = mean(gdp_futureExpectation))
```
## Question 4

Create a new variable totalAvgLifeExp from gapminder, grouped by continents, that includes: cumulative average life expectancy. Also make sure that totalAvLifeExp only incoporates years 1990-2010 (in other words, when you're calculating cumulative average life expectancy, you only care about the 1990-2010 range). 

# Create Tidy Data with tidyr

Let's now work with ```tidyr```. People who work with data often want to reshape their dataframes from ‘wide’ to ‘longer’ layouts, or vice-versa. The ‘long’ layout or format is where:

1. each column is a variable
2. each row is an observation

In the purely ‘long’ (or ‘longest’) format, you usually have 1 column for the observed variable and the other columns are ID variables.

For the ‘wide’ format each row is often a site/subject/patient and you have multiple observation variables containing the same type of data. These can be either repeated observations over time, or observation of multiple variables (or a mix of both). You may find data input may be simpler or some other applications may prefer the ‘wide’ format. However, many of ```R```‘s functions have been designed assuming you have ’longer’ formatted data. This tutorial will help you efficiently transform your data shape regardless of original format.

<img src = "fig/R-Dataframe-Manipulation-tidyr-fig1.png">

Long and wide dataframe layouts mainly affect readability. For humans, the wide format is often more intuitive since we can often see more of the data on the screen due to its shape. However, the long format is more machine readable and is closer to the formatting of databases. The ID variables in our dataframes are similar to the fields in a database and observed variables are like the database values.

# Getting started with tidyr

First install the packages if you haven’t already done so (including dplyr which should already be installed if you've done this lab in sequential order):

```R
#install.packages("tidyr")
#install.packages("dplyr")
```

Load the packages
```R
library("tidyr")
library("dplyr")
library("readr")
```

First, lets look at the structure of our original gapminder dataframe:

```R
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

Sometimes, as with the gapminder dataset, we have multiple types of observed data. It is somewhere in between the purely ‘long’ and ‘wide’ data formats. We have 3 “ID variables” (```continent```, ```country```, ```year```) and 3 “Observation variables” (```pop```, ```lifeExp```, ```gdpPercap```). This intermediate format can be preferred despite not having ALL observations in 1 column given that all 3 observation variables have different units. There are few operations that would need us to make this dataframe any longer (i.e. 4 ID variables and 1 Observation variable).

While using many of the functions in R, which are often vector based, you usually do not want to do mathematical operations on values with different units. For example, using the purely long format, a single mean for all of the values of population, life expectancy, and GDP would not be meaningful since it would return the mean of values with 3 incompatible units. The solution is that we first manipulate the data either by grouping (see the lesson on ```dplyr```), or we change the structure of the dataframe. Note: Some plotting functions in R actually work better in the wide format data.

# From wide to long format with pivot_longer()

Until now, we’ve been using the nicely formatted original gapminder dataset, but ‘real’ data (i.e. our own research data) will never be so well organized. Here let’s start with the wide formatted version of the gapminder dataset.

We’ll load the data file and look at it.

```R
gap_wide <- read_csv("data/gapminder_wide.csv")
```

```{r}
── Column specification ────────────────────────────────────────────────────────
cols(
  .default = col_double(),
  continent = col_character(),
  country = col_character()
)
ℹ Use `spec()` for the full column specifications.
```

```R
gap_wide
```

```{r}
# A tibble: 142 x 38
   continent country pop_1952 lifeExp_1952 gdpPercap_1952 pop_1957 lifeExp_1957
   <chr>     <chr>      <dbl>        <dbl>          <dbl>    <dbl>        <dbl>
 1 Asia      Afghan…  8425333         28.8           779.  9240934         30.3
 2 Europe    Albania  1282697         55.2          1601.  1476505         59.3
 3 Africa    Algeria  9279525         43.1          2449. 10270856         45.7
 4 Africa    Angola   4232095         30.0          3521.  4561361         32.0
 5 Americas  Argent… 17876956         62.5          5911. 19610538         64.4
 6 Oceania   Austra…  8691212         69.1         10040.  9712569         70.3
 7 Europe    Austria  6927772         66.8          6137.  6965860         67.5
 8 Asia      Bahrain   120447         50.9          9867.   138655         53.8
 9 Asia      Bangla… 46886859         37.5           684. 51365468         39.3
10 Europe    Belgium  8730405         68            8343.  8989111         69.2
# … with 132 more rows, and 31 more variables: gdpPercap_1957 <dbl>,
#   pop_1962 <dbl>, lifeExp_1962 <dbl>, gdpPercap_1962 <dbl>, pop_1967 <dbl>,
#   lifeExp_1967 <dbl>, gdpPercap_1967 <dbl>, pop_1972 <dbl>,
#   lifeExp_1972 <dbl>, gdpPercap_1972 <dbl>, pop_1977 <dbl>,
#   lifeExp_1977 <dbl>, gdpPercap_1977 <dbl>, pop_1982 <dbl>,
#   lifeExp_1982 <dbl>, gdpPercap_1982 <dbl>, pop_1987 <dbl>,
#   lifeExp_1987 <dbl>, gdpPercap_1987 <dbl>, pop_1992 <dbl>,
#   lifeExp_1992 <dbl>, gdpPercap_1992 <dbl>, pop_1997 <dbl>,
#   lifeExp_1997 <dbl>, gdpPercap_1997 <dbl>, pop_2002 <dbl>,
#   lifeExp_2002 <dbl>, gdpPercap_2002 <dbl>, pop_2007 <dbl>,
#   lifeExp_2007 <dbl>, gdpPercap_2007 <dbl>
```

<img src = "fig/R-Dataframe-Manipulation-tidyr-fig2.png">

To change this very wide dataframe layout back to our nice, intermediate (or longer) layout, we will use one of the two available ```pivot``` functions from the ```tidyr``` package. To convert from wide to a longer format, we will use the ```pivot_longer()``` function. ```pivot_longer()``` makes datasets longer by increasing the number of rows and decreasing the number of columns, or ‘lengthening’ your observation variables into a single variable.

<img src = "fig/R-Dataframe-Manipulation-tidyr-fig3.png">

```R
gap_long <- gap_wide %>%
  pivot_longer(
    cols = pop_1952:gdpPercap_2007,
    names_to = "obstype_year", values_to = "obs_values"
  )
gap_long
```

```{r}
# A tibble: 5,112 x 4
   continent country     obstype_year   obs_values
   <chr>     <chr>       <chr>               <dbl>
 1 Asia      Afghanistan pop_1952        8425333  
 2 Asia      Afghanistan lifeExp_1952         28.8
 3 Asia      Afghanistan gdpPercap_1952      779. 
 4 Asia      Afghanistan pop_1957        9240934  
 5 Asia      Afghanistan lifeExp_1957         30.3
 6 Asia      Afghanistan gdpPercap_1957      821. 
 7 Asia      Afghanistan pop_1962       10267083  
 8 Asia      Afghanistan lifeExp_1962         32.0
 9 Asia      Afghanistan gdpPercap_1962      853. 
10 Asia      Afghanistan pop_1967       11537966  
# … with 5,102 more rows
```

Here we have used piping syntax which is similar to what we were doing in the previous lesson with dplyr. In fact, these are compatible and you can use a mix of tidyr and dplyr functions by piping them together.

We first provide to ```pivot_longer()``` a vector of column names that will be pivoted into longer format. We could type out all the observation variables, but as in the ```select()``` function, we can use the ```:``` operator to select our desired variables. ```pivot_longer()``` also allows the alternative syntax of using the ```-``` symbol to identify which variables are not to be pivoted (i.e. ID variables).

The next arguments to ```pivot_longer()``` are ```names_to``` for naming the column that will contain the new ID variable (```obstype_year```) and ```values_to``` for naming the new amalgamated observation variable (```obs_value```). We supply these new column names as strings.

<img src = "fig/R-Dataframe-Manipulation-tidyr-fig4.png">

```R
gap_long <- gap_wide %>%
  pivot_longer(
    cols = c(-continent, -country),
    names_to = "obstype_year", values_to = "obs_values"
  )
gap_long
```

```{r}
# A tibble: 5,112 x 4
   continent country     obstype_year   obs_values
   <chr>     <chr>       <chr>               <dbl>
 1 Asia      Afghanistan pop_1952        8425333  
 2 Asia      Afghanistan lifeExp_1952         28.8
 3 Asia      Afghanistan gdpPercap_1952      779. 
 4 Asia      Afghanistan pop_1957        9240934  
 5 Asia      Afghanistan lifeExp_1957         30.3
 6 Asia      Afghanistan gdpPercap_1957      821. 
 7 Asia      Afghanistan pop_1962       10267083  
 8 Asia      Afghanistan lifeExp_1962         32.0
 9 Asia      Afghanistan gdpPercap_1962      853. 
10 Asia      Afghanistan pop_1967       11537966  
# … with 5,102 more rows
```

That may seem trivial with this particular dataframe, but sometimes you have 1 ID variable and 40 observation variables with irregular variable names. The flexibility is a huge time saver!

Now ```obstype_year``` actually contains 2 pieces of information, the observation type (```pop```, ```lifeExp```, or ```gdpPercap```) and the year. We can use the ```separate()``` function to split the character strings into multiple variables

```R
gap_long <- gap_long %>% separate(obstype_year,into=c('obs_type','year'),sep="_")
gap_long$year <- as.integer(gap_long$year)
```

## Question 5
Using ```gap_long```, calculate the mean life expectancy, population, and gdpPercap for each continent. Hint: use the ```group_by()``` and ```summarize()``` functions we learned in the dplyr lesson

# From long to intermediate format with pivot_wider()
It is always good to check work. So, let’s use the second ```pivot``` function, ```pivot_wider()```, to ‘widen’ our observation variables back out. ```pivot_wider()``` is the opposite of ```pivot_longer()```, making a dataset wider by increasing the number of columns and decreasing the number of rows. We can use ```pivot_wider()``` to pivot or reshape our ```gap_long``` to the original intermediate format or the widest format. Let’s start with the intermediate format.

The ```pivot_wider()``` function takes ```names_from``` and ```values_from``` arguments.

To ```names_from``` we supply the column name whose contents will be pivoted into new output columns in the widened dataframe. The corresponding values will be added from the column named in the ```values_from``` argument.

```R
gap_normal <- gap_long %>%
  pivot_wider(names_from = obs_type, values_from = obs_values)
dim(gap_normal)
```

```{r}
[1] 1704    6
```

```R
dim(gapminder)
```

```{r}
[1] 1704    6
```

```R
names(gap_normal)
```

```{r}
[1] "continent" "country"   "year"      "pop"       "lifeExp"   "gdpPercap"
```

```R
names(gapminder)
```

```{r}
[1] "country"   "year"      "pop"       "continent" "lifeExp"   "gdpPercap"
```

Now we’ve got an intermediate dataframe ```gap_normal``` with the same dimensions as the original ```gapminder```, but the order of the variables is different. Let’s fix that before checking if they are ```all.equal()```.

```R
gap_normal <- gap_normal %>% 
    select(country, year, pop, continent, lifeExp, gdpPercap) 
all_equal(gap_normal, gapminder)
```

```{r}
[1] TRUE
```

```R
gap_normal
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

We’re almost there, the original was sorted by ```country```, then ```year```.

```R
gap_normal <- gap_normal %>% arrange(country, year)
all_equal(gap_normal, gapminder)
```

```{r}
[1] TRUE
```

That’s great! We’ve gone from the longest format back to the intermediate and we didn’t introduce any errors in our code.

Now let’s convert the long all the way back to the wide. In the wide format, we will keep country and continent as ID variables and pivot the observations across the 3 metrics (```pop```, ```lifeExp```, ```gdpPercap```) and time (```year```). First we need to create appropriate labels for all our new variables (time [times] metric combinations) and we also need to unify our ID variables to simplify the process of defining ```gap_wide```.

```R
gap_temp <- gap_long %>% unite(var_ID, continent, country, sep = "_")
gap_temp
```

```{r}
# A tibble: 5,112 x 4
   var_ID           obs_type   year obs_values
   <chr>            <chr>     <dbl>      <dbl>
 1 Asia_Afghanistan pop        1952  8425333  
 2 Asia_Afghanistan lifeExp    1952       28.8
 3 Asia_Afghanistan gdpPercap  1952      779. 
 4 Asia_Afghanistan pop        1957  9240934  
 5 Asia_Afghanistan lifeExp    1957       30.3
 6 Asia_Afghanistan gdpPercap  1957      821. 
 7 Asia_Afghanistan pop        1962 10267083  
 8 Asia_Afghanistan lifeExp    1962       32.0
 9 Asia_Afghanistan gdpPercap  1962      853. 
10 Asia_Afghanistan pop        1967 11537966  
# … with 5,102 more rows
```

```R
gap_temp <- gap_long %>%
    unite(ID_var, continent, country, sep = "_") %>%
    unite(var_names, obs_type, year, sep = "_")
gap_temp
```

```{r}
# A tibble: 5,112 x 3
   ID_var           var_names      obs_values
   <chr>            <chr>               <dbl>
 1 Asia_Afghanistan pop_1952        8425333  
 2 Asia_Afghanistan lifeExp_1952         28.8
 3 Asia_Afghanistan gdpPercap_1952      779. 
 4 Asia_Afghanistan pop_1957        9240934  
 5 Asia_Afghanistan lifeExp_1957         30.3
 6 Asia_Afghanistan gdpPercap_1957      821. 
 7 Asia_Afghanistan pop_1962       10267083  
 8 Asia_Afghanistan lifeExp_1962         32.0
 9 Asia_Afghanistan gdpPercap_1962      853. 
10 Asia_Afghanistan pop_1967       11537966  
# … with 5,102 more rows
```

Using ```unite()``` we now have a single ID variable which is a combination of ```continent```, ```country```,and we have defined variable names. We’re now ready to pipe in ```pivot_wider()```

```R
gap_wide_new <- gap_long %>%
  unite(ID_var, continent, country, sep = "_") %>%
  unite(var_names, obs_type, year, sep = "_") %>%
  pivot_wider(names_from = var_names, values_from = obs_values)
gap_wide_new
```

```{r}
# A tibble: 142 x 37
   ID_var pop_1952 lifeExp_1952 gdpPercap_1952 pop_1957 lifeExp_1957
   <chr>     <dbl>        <dbl>          <dbl>    <dbl>        <dbl>
 1 Asia_…  8425333         28.8           779.  9240934         30.3
 2 Europ…  1282697         55.2          1601.  1476505         59.3
 3 Afric…  9279525         43.1          2449. 10270856         45.7
 4 Afric…  4232095         30.0          3521.  4561361         32.0
 5 Ameri… 17876956         62.5          5911. 19610538         64.4
 6 Ocean…  8691212         69.1         10040.  9712569         70.3
 7 Europ…  6927772         66.8          6137.  6965860         67.5
 8 Asia_…   120447         50.9          9867.   138655         53.8
 9 Asia_… 46886859         37.5           684. 51365468         39.3
10 Europ…  8730405         68            8343.  8989111         69.2
# … with 132 more rows, and 31 more variables: gdpPercap_1957 <dbl>,
#   pop_1962 <dbl>, lifeExp_1962 <dbl>, gdpPercap_1962 <dbl>, pop_1967 <dbl>,
#   lifeExp_1967 <dbl>, gdpPercap_1967 <dbl>, pop_1972 <dbl>,
#   lifeExp_1972 <dbl>, gdpPercap_1972 <dbl>, pop_1977 <dbl>,
#   lifeExp_1977 <dbl>, gdpPercap_1977 <dbl>, pop_1982 <dbl>,
#   lifeExp_1982 <dbl>, gdpPercap_1982 <dbl>, pop_1987 <dbl>,
#   lifeExp_1987 <dbl>, gdpPercap_1987 <dbl>, pop_1992 <dbl>,
#   lifeExp_1992 <dbl>, gdpPercap_1992 <dbl>, pop_1997 <dbl>,
#   lifeExp_1997 <dbl>, gdpPercap_1997 <dbl>, pop_2002 <dbl>,
#   lifeExp_2002 <dbl>, gdpPercap_2002 <dbl>, pop_2007 <dbl>,
#   lifeExp_2007 <dbl>, gdpPercap_2007 <dbl>
```

You can take this 1 step further and create a ```gap_ludicrously_wide``` format data by pivoting over countries, year and the 3 metrics.

```R
gap_ludicrously_wide <- gap_long %>%
   unite(var_names, obs_type, year, country, sep = "_") %>%
   pivot_wider(names_from = var_names, values_from = obs_values)
```

Let's now return to the ```gap_wide_new``` object we created before ```gap_ludicrously_wide```

Notice how we have a great ‘wide’ format dataframe. However, the ```ID_var``` could be more usable... so let’s separate it into 2 variables with ```separate()```

```R
gap_wide_betterID <- separate(gap_wide_new, ID_var, c("continent", "country"), sep="_")
gap_wide_betterID <- gap_long %>%
    unite(ID_var, continent, country, sep = "_") %>%
    unite(var_names, obs_type, year, sep = "_") %>%
    pivot_wider(names_from = var_names, values_from = obs_values) %>%
    separate(ID_var, c("continent","country"), sep = "_")
gap_wide_betterID
```

```{r}
# A tibble: 142 x 38
   continent country pop_1952 lifeExp_1952 gdpPercap_1952 pop_1957 lifeExp_1957
   <chr>     <chr>      <dbl>        <dbl>          <dbl>    <dbl>        <dbl>
 1 Asia      Afghan…  8425333         28.8           779.  9240934         30.3
 2 Europe    Albania  1282697         55.2          1601.  1476505         59.3
 3 Africa    Algeria  9279525         43.1          2449. 10270856         45.7
 4 Africa    Angola   4232095         30.0          3521.  4561361         32.0
 5 Americas  Argent… 17876956         62.5          5911. 19610538         64.4
 6 Oceania   Austra…  8691212         69.1         10040.  9712569         70.3
 7 Europe    Austria  6927772         66.8          6137.  6965860         67.5
 8 Asia      Bahrain   120447         50.9          9867.   138655         53.8
 9 Asia      Bangla… 46886859         37.5           684. 51365468         39.3
10 Europe    Belgium  8730405         68            8343.  8989111         69.2
# … with 132 more rows, and 31 more variables: gdpPercap_1957 <dbl>,
#   pop_1962 <dbl>, lifeExp_1962 <dbl>, gdpPercap_1962 <dbl>, pop_1967 <dbl>,
#   lifeExp_1967 <dbl>, gdpPercap_1967 <dbl>, pop_1972 <dbl>,
#   lifeExp_1972 <dbl>, gdpPercap_1972 <dbl>, pop_1977 <dbl>,
#   lifeExp_1977 <dbl>, gdpPercap_1977 <dbl>, pop_1982 <dbl>,
#   lifeExp_1982 <dbl>, gdpPercap_1982 <dbl>, pop_1987 <dbl>,
#   lifeExp_1987 <dbl>, gdpPercap_1987 <dbl>, pop_1992 <dbl>,
#   lifeExp_1992 <dbl>, gdpPercap_1992 <dbl>, pop_1997 <dbl>,
#   lifeExp_1997 <dbl>, gdpPercap_1997 <dbl>, pop_2002 <dbl>,
#   lifeExp_2002 <dbl>, gdpPercap_2002 <dbl>, pop_2007 <dbl>,
#   lifeExp_2007 <dbl>, gdpPercap_2007 <dbl>
```

```R
all_equal(gap_wide, gap_wide_betterID)
```

```{r}
[1] TRUE
```

There and back again!

# Question 6
The file GenHos_Admits.csv (available at the url “https://raw.githubusercontent.com/benbossc/R-dplyr-tidyr/main/data/GenHos_Admits.csv”) examines general reasons for people being hospitalized in the financial years ranging from July 1993 to June 1998. The variable “Separations” describes how many patient discharges occured in that year, while the variable “PatientDays” describes how many days in total patients spent in the hospital for that reason.

- Briefly explain why it is not considered to be tidy data and what changes need to be made to tidy it. 
- Use ```separate()``` and ```pivot_longer()``` to create a tidy data set with columns ```IcdCode```, ```IcdText```, ```Year```, ```Field```, and ```Count```. The ```IcdCode``` is the numeric component of ```IcdChapter``` (HINT #1: pay attention to variable types, you might need to manipulate variable types using the ```as.numeric()``` and/or ```as.character()``` functions. HINT #2: The ```sep``` argument for when you utilize ```seperate()``` will equal "\\. ")


# Other Sources
• <a href="https://r4ds.had.co.nz/">R for Data Science</a>

• <a href="https://www.rstudio.com/wp-content/uploads/2015/02/data-wrangling-cheatsheet.pdf">Data Wrangling Cheat sheet</a>

• <a href="https://dplyr.tidyverse.org/">Introduction to dplyr</a>

• <a href="https://cran.r-project.org/web/packages/tidyr/vignettes/tidy-data.html">Introduction to tidyr</a>

• <a href="https://www.rstudio.com/resources/webinars/data-wrangling-with-r-and-rstudio/">Data wrangling with R and RStudio</a>
