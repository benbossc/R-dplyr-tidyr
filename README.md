# R-introduction

<a href="http://creativecommons.org/licenses/by-nc/4.0/" rel="license"><img style="border-width: 0;" src="https://i.creativecommons.org/l/by-nc/4.0/88x31.png" alt="Creative Commons License" /></a>
This tutorial is licensed under a <a href="http://creativecommons.org/licenses/by-nc/4.0/" rel="license">Creative Commons Attribution-NonCommercial 4.0 International License</a>.

# Acknowledgements
This lab is sourced from the The Software Carpentry <a href="https://swcarpentry.github.io/r-novice-gapminder/"> R for Reproducible Scientific Analysis </a> lesson material: Thomas Wright and Naupaka Zimmerman (eds): Software Carpentry: R for Reproducible Scientific Analysis. Version 2016.06, June 2016, https://github.com/swcarpentry/r-novice-gapminder, 10.5281/zenodo.57520.

Let's use the readr package to read external data into R, for this lesson we are going to use the gapminder data.

```R
library("readr")
gapminder <- read_csv("data/gapminder_data.csv")
```

```R
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
