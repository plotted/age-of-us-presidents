# Age of U.S. Presidents
Gaston Sanchez
2024-01-01

- [<span class="toc-section-number">1</span> Description](#description)
  - [<span class="toc-section-number">1.1</span> Details
    (datasheet)](#details-datasheet)
  - [<span class="toc-section-number">1.2</span> Other
    Sources](#other-sources)
  - [<span class="toc-section-number">1.3</span> R
    Packages](#r-packages)
- [<span class="toc-section-number">2</span> Data](#data)
  - [<span class="toc-section-number">2.1</span> Download
    Data](#download-data)
  - [<span class="toc-section-number">2.2</span> Import
    Data](#import-data)
  - [<span class="toc-section-number">2.3</span> Data
    Preparation](#data-preparation)
    - [<span class="toc-section-number">2.3.1</span> Adding
      Party](#adding-party)
    - [<span class="toc-section-number">2.3.2</span> Clean
      Table](#clean-table)
- [<span class="toc-section-number">3</span> Graphs](#graphs)
  - [<span class="toc-section-number">3.1</span> Version 1](#version-1)
  - [<span class="toc-section-number">3.2</span> Version 2](#version-2)
  - [<span class="toc-section-number">3.3</span> Version 3](#version-3)
  - [<span class="toc-section-number">3.4</span> Version 4](#version-4)
  - [<span class="toc-section-number">3.5</span> Version 5](#version-5)
  - [<span class="toc-section-number">3.6</span> Version 6](#version-6)
  - [<span class="toc-section-number">3.7</span> Version 7](#version-7)
  - [<span class="toc-section-number">3.8</span> Version 8](#version-8)
  - [<span class="toc-section-number">3.9</span> Final
    attempt](#final-attempt)

# Description

The data visualization (see image below) of this module is based on the
following post/article (by Katherine Schaeffer) of Pew Research Center
(Oct 10, 2023):

**Most U.S. presidents have been in their 50s at inauguration**

<https://www.pewresearch.org/short-reads/2023/10/10/most-us-presidents-have-been-in-their-50s-at-inauguration/>

![Median age for U.S. presidents at
inauguration](median-age-for-presidents-at-inauguration.png)

  

## Details (datasheet)

- **Topic(s)**:
  - Politics
  - U.S.
  - Age of presidents
- **Data**:
  - Size: small
  - Format: raw data in HTML table (involves basic web scraping)
  - Requires some cleansing (e.g. regex)
  - Requires soft-coding (e.g. create vector for `party`)
- **Graphic**:
  - Type: scatterplot
  - Interactive: no, but we can use `"plotly"` and `"ggiraph"`

  

## Other Sources

Related to the above post, you can find similar sources from The New
York Times and Wikipedia.

**NYT: World Leader Ages**

<https://www.nytimes.com/2020/07/16/opinion/america-presidents-old-age.html>

<https://www.nytimes.com/2020/10/22/learning/whats-going-on-in-this-graph-world-leader-ages.html>

**Wikipedia: List of presidents of the United States by age**

<https://en.wikipedia.org/wiki/List_of_presidents_of_the_United_States_by_age>

**Wikipedia: List of presidents of the United States**

<https://en.wikipedia.org/wiki/List_of_presidents_of_the_United_States>

## R Packages

``` r
# recent versions of tidyverse include "lubridate" (for working with dates)
library(tidyverse)  # ecosystem of data science packages
library(rvest)      # for web scraping
```

------------------------------------------------------------------------

# Data

The data for ages of US presidents is available in Wikipedia:

<https://en.wikipedia.org/wiki/List_of_presidents_of_the_United_States_by_age>

Specifically, the data is in the HTML table “Presidential age-related
data” of the Wikipedia webpage.

## Download Data

For data archival purposes, it’s a good idea to download the HTML page
containing the table with the data of interest: “Presidential
age-related data”

``` r
wiki = "https://en.wikipedia.org/wiki/"
presidents = "List_of_presidents_of_the_United_States_by_age"

wiki_url = paste0(wiki, presidents)

download.file(wiki_url, destfile = paste0(presidents, ".html"))
```

## Import Data

You can import the data directly from the Wikipedia webpage (via its
URL), or by reading-in the downloaded HTML file. In both cases we can
read-in the HTML page with the function `read_html()` from the package
`"rvest"`

``` r
# import directly from wikipedia
wiki = "https://en.wikipedia.org/wiki/"
presidents = "List_of_presidents_of_the_United_States_by_age"

wiki_url = paste0(wiki, presidents)

doc = read_html(wiki_url)
```

Because we have a local copy of the HTML page, we prefer this option:

``` r
# import from downloaded HTML file
doc = read_html("List_of_presidents_of_the_United_States_by_age.html")
```

The function `read_html()` extracts ALL tables in an HTML document, and
returns them in an R list.

The table we are interested in—**Presidential age-related data**—is the
first one in the list which we can select with the following command:

``` r
tbls = html_table(doc)

tbl = tbls[[1]]

# get rid of first row
tbl = tbl[-1, ]
tbl
```

    # A tibble: 47 × 8
       No.   President           Born  Age atstart of presi…¹ Age atend of preside…²
       <chr> <chr>               <chr> <chr>                  <chr>                 
     1 1     George Washington   Feb … 57 years, 67 daysApr … 65 years, 10 daysMar …
     2 2     John Adams          Oct … 61 years, 125 daysMar… 65 years, 125 daysMar…
     3 3     Thomas Jefferson    Apr … 57 years, 325 daysMar… 65 years, 325 daysMar…
     4 4     James Madison       Mar … 57 years, 353 daysMar… 65 years, 353 daysMar…
     5 5     James Monroe        Apr … 58 years, 310 daysMar… 66 years, 310 daysMar…
     6 6     John Quincy Adams   Jul … 57 years, 236 daysMar… 61 years, 236 daysMar…
     7 7     Andrew Jackson      Mar … 61 years, 354 daysMar… 69 years, 354 daysMar…
     8 8     Martin Van Buren    Dec … 54 years, 89 daysMar … 58 years, 89 daysMar …
     9 9     William Henry Harr… Feb … 68 years, 23 daysMar … 68 years, 54 daysApr …
    10 10    John Tyler          Mar … 51 years, 6 daysApr 4… 54 years, 340 daysMar…
    # ℹ 37 more rows
    # ℹ abbreviated names: ¹​`Age atstart of presidency`, ²​`Age atend of presidency`
    # ℹ 3 more variables: `Post-presidencytimespan` <chr>, Lifespan <chr>,
    #   Lifespan <chr>

## Data Preparation

Here’s the list of (somewhat advanced) steps to do further manipulation
so that we have the necessary data for `ggplot`.

``` r
# age at start of presidential period
age_start = as.numeric(str_extract(tbl$`Age atstart of presidency`, "^\\d+"))

# age at the end of presidential period
age_end = as.numeric(str_extract(tbl$`Age atend of presidency`, "^\\d+"))

# Donald Trump's current age
age_end[47] = 78

year_start = as.numeric(str_extract(tbl$`Age atstart of presidency`, "\\d+$"))

year_end = as.numeric(str_extract(tbl$`Age atend of presidency`, "\\d{4}"))
year_end[47] = 2025
```

### Adding Party

``` r
party = c(
  "Other",      # "Washington"
  "Federalist", # "Adams"     
  "Federalist", # "Jefferson" 
  "Federalist", # "Madison"   
  "Federalist", # "Monroe"    
  "Federalist", # "Adams"     
  "Democratic", # "Jackson"   
  "Democratic", # "Buren"     
  "Whig",       # "Harrison"  
  "Whig",       # "Tyler"     
  "Democratic", # "Polk"      
  "Whig",       # "Taylor"    
  "Whig",       # "Fillmore"  
  "Democratic", # "Pierce"    
  "Democratic", # "Buchanan"  
  "Republican", # "Lincoln"   
  "Democratic", # "Johnson"   
  "Republican", # "Grant"     
  "Republican", # "Hayes"     
  "Republican", # "Garfield"  
  "Republican", # "Arthur"    
  "Democratic", # "Cleveland" 
  "Republican", # "Harrison"  
  "Democratic", # "Cleveland" 
  "Republican", # "McKinley"  
  "Republican", # "Roosevelt" 
  "Republican", # "Taft"      
  "Democratic", # "Wilson"    
  "Republican", # "Harding"   
  "Republican", # "Coolidge"  
  "Republican", # "Hoover"    
  "Democratic", # "Roosevelt" 
  "Democratic", # "Truman"    
  "Republican", # "Eisenhower"
  "Democratic", # "Kennedy"   
  "Democratic", # "Johnson"   
  "Republican", # "Nixon"     
  "Republican", # "Ford"      
  "Democratic", # "Carter"    
  "Republican", # "Reagan"    
  "Republican", # "Bush"      
  "Democratic", # "Clinton"   
  "Republican", # "Bush"      
  "Democratic", # "Obama"     
  "Republican", # "Trump"     
  "Democratic", # "Biden"
  "Republican"  # Trump
)
```

### Clean Table

Having all the ingredients in place we proceed to assemble a “tidy”
table (this can be a `"tibble"` or also a `"data.frame"`)

``` r
dat = tibble(
  "president" = tbl$President, 
  "party" = party,
  "age_start" = age_start, 
  "age_end" = age_end,
  "year_start" = year_start,
  "year_end" = year_end)

dat = dat |>
  mutate(color = case_when(
    party == "Democratic" ~ "#468BFA",
    party == "Republican" ~ "#FC4E4B",
    .default = "#FFD438"
  ))

dat
```

    # A tibble: 47 × 7
       president              party      age_start age_end year_start year_end color
       <chr>                  <chr>          <dbl>   <dbl>      <dbl>    <dbl> <chr>
     1 George Washington      Other             57      65       1789     1797 #FFD…
     2 John Adams             Federalist        61      65       1797     1801 #FFD…
     3 Thomas Jefferson       Federalist        57      65       1801     1809 #FFD…
     4 James Madison          Federalist        57      65       1809     1817 #FFD…
     5 James Monroe           Federalist        58      66       1817     1825 #FFD…
     6 John Quincy Adams      Federalist        57      61       1825     1829 #FFD…
     7 Andrew Jackson         Democratic        61      69       1829     1837 #468…
     8 Martin Van Buren       Democratic        54      58       1837     1841 #468…
     9 William Henry Harrison Whig              68      68       1841     1841 #FFD…
    10 John Tyler             Whig              51      54       1841     1845 #FFD…
    # ℹ 37 more rows

------------------------------------------------------------------------

# Graphs

As usual, let’s go over a series of plotting rounds, starting with a
default bar chart, and then gradually adding more elements, and
customizing its appearance to get as close as possible to our target
visualization.

## Version 1

We use 2 `geom_point()` layers since there are 2 kinds of points: the
starting age-year, and the ending age-year.

``` r
# 1st attempt
ggplot(dat) +
  geom_point(aes(x = year_start, y = age_start)) +
  geom_point(aes(x = year_end, y = age_end))
```

![](age-of-us-presidents_files/figure-commonmark/unnamed-chunk-8-1.png)

## Version 2

Adding color to points

``` r
# 2nd attempt
ggplot(dat) +
  geom_point(aes(x = year_start, y = age_start), color = dat$color) +
  geom_point(aes(x = year_end, y = age_end), color = dat$color)
```

![](age-of-us-presidents_files/figure-commonmark/unnamed-chunk-9-1.png)

## Version 3

Adding median age line

``` r
# 3rd attempt
ggplot(dat) +
  geom_hline(yintercept = median(dat$age_start)) +
  geom_point(aes(x = year_start, y = age_start), color = dat$color) +
  geom_point(aes(x = year_end, y = age_end), color = dat$color) 
```

![](age-of-us-presidents_files/figure-commonmark/unnamed-chunk-10-1.png)

## Version 4

``` r
# 4th attempt
ggplot(dat) +
  geom_hline(yintercept = seq(30, 90, by = 10), linetype = "dotted") +
  geom_hline(yintercept = median(dat$age_start)) +
  geom_point(aes(x = year_start, y = age_start), color = dat$color) +
  geom_point(aes(x = year_end, y = age_end), color = dat$color) +
  scale_y_continuous(breaks = seq(30, 90, by = 10))
```

![](age-of-us-presidents_files/figure-commonmark/unnamed-chunk-11-1.png)

## Version 5

``` r
# 5th attempt
ggplot(dat) +
  geom_hline(yintercept = seq(30, 90, by = 10), linetype = "dotted") +
  geom_hline(yintercept = median(dat$age_start)) +
  geom_point(aes(x = year_start, y = age_start), color = dat$color) +
  geom_point(aes(x = year_end, y = age_end), color = dat$color) +
  geom_segment(aes(x = year_start, y = age_start, xend = year_end, yend = age_end)) +
  scale_y_continuous(breaks = seq(30, 90, by = 10))
```

![](age-of-us-presidents_files/figure-commonmark/unnamed-chunk-12-1.png)

## Version 6

``` r
# 6th attempt
ggplot(dat) +
  geom_hline(yintercept = seq(30, 90, by = 10), linetype = "dotted") +
  geom_hline(yintercept = median(dat$age_start)) +
  geom_point(aes(x = year_start, y = age_start), color = dat$color, shape = 21) +
  geom_point(aes(x = year_end, y = age_end), color = dat$color, shape = 21) +
  geom_segment(aes(x = year_start, y = age_start, xend = year_end, yend = age_end),
               linewidth = 1.5, color = dat$color, alpha = 0.5) +
  scale_y_continuous(breaks = seq(30, 90, by = 10))
```

![](age-of-us-presidents_files/figure-commonmark/unnamed-chunk-13-1.png)

## Version 7

``` r
# 7th attempt
ggplot(dat) +
  geom_hline(yintercept = seq(30, 90, by = 10), linetype = "dotted") +
  geom_hline(yintercept = median(dat$age_start)) +
  geom_point(aes(x = year_start, y = age_start), color = dat$color, shape = 21) +
  geom_point(aes(x = year_end, y = age_end), color = dat$color, shape = 21) +
  geom_segment(aes(x = year_start, y = age_start, xend = year_end, yend = age_end),
               linewidth = 1.5, color = dat$color, alpha = 0.5) +
  geom_text(aes(x = year_start, y = age_start,
                label = str_extract(president, "\\w+$")), size = 2.5, color = dat$color) +
  scale_y_continuous(breaks = seq(30, 90, by = 10))
```

![](age-of-us-presidents_files/figure-commonmark/unnamed-chunk-14-1.png)

## Version 8

``` r
# 8th attempt
ggplot(dat) +
  geom_hline(yintercept = seq(30, 90, by = 10), linetype = "dotted") +
  geom_hline(yintercept = median(dat$age_start)) +
  geom_point(aes(x = year_start, y = age_start), color = dat$color, shape = 21) +
  geom_point(aes(x = year_end, y = age_end), color = dat$color, shape = 21) +
  geom_segment(aes(x = year_start, y = age_start, xend = year_end, yend = age_end),
               linewidth = 1.5, color = dat$color, alpha = 0.5) +
  geom_text(aes(x = year_end, y = age_end, label = str_extract(president, "\\w+$")),
            size = 2.5, color = dat$color, vjust = -0.5) +
  scale_y_continuous(breaks = seq(30, 90, by = 10))
```

![](age-of-us-presidents_files/figure-commonmark/unnamed-chunk-15-1.png)

## Final attempt

``` r
# 9th attempt
ggplot(dat) +
  geom_hline(yintercept = seq(30, 90, by = 10), 
             linetype = "dotted", color = "gray70") +
  geom_hline(yintercept = median(dat$age_start), linewidth = 0.25) +
  geom_segment(aes(x = year_start, y = age_start, xend = year_end, yend = age_end),
               linewidth = 1.5, color = dat$color, alpha = 0.5) +
  geom_point(aes(x = year_start, y = age_start), 
             color = dat$color, shape = 21, fill = "white") +
  geom_point(aes(x = year_end, y = age_end), 
             color = dat$color, shape = 21, fill = "white") +
  geom_text(aes(x = year_end, y = age_end, label = str_extract(president, "\\w+$")),
            size = 2.5, color = dat$color, vjust = -0.5) +
  scale_y_continuous(breaks = seq(30, 90, by = 10)) +
  scale_x_continuous(breaks = seq(1780, 2024, by = 12)) +
  labs(title = "Ages of U.S. presidents, 1789-2025",
       subtitle = "The median age at first inauguration is 55 years old",
       x = "",
       y = "age") +
  theme(axis.line = element_blank(),
        panel.background = element_blank(),
        panel.grid.major.y = element_blank(),
        panel.grid.minor.y = element_blank())
```

![](age-of-us-presidents_files/figure-commonmark/unnamed-chunk-16-1.png)
