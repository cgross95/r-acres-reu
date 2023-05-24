---
title: Exploring Data Frames
teaching: 20
exercises: 10
source: Rmd
---

::::::::::::::::::::::::::::::::::::::: objectives

- Display basic properties of data frames including size and class of the columns, names, and first few rows.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- How can I manipulate a data frame?

::::::::::::::::::::::::::::::::::::::::::::::::::


## Working with data

Let's work with a realistic dataset in data frame. First, we need to set up our environment:


```r
dir.create("~/R_tutorial/data", recursive = TRUE)
setwd("~/R_tutorial")
```

This makes sure that everything we do from now on happens relative to the `~/R_tutorial` directory. In practice, for better reproducibility, you'll want to set up an [R project](learners/02-project-intro.Rmd).

Now let's download [the data](https://swcarpentry.github.io/r-novice-gapminder/data/gapminder_data.csv):


```r
download.file("https://swcarpentry.github.io/r-novice-gapminder/data/gapminder_data.csv",
              destfile = "data/gapminder_data.csv")
```

And finally, we can read it into a variable


```r
gapminder <- read.csv("data/gapminder_data.csv")
```

:::::::::::::::::::::::::::::::::::::::::  callout

## Miscellaneous Tips

- Another type of file you might encounter are tab-separated value files (.tsv). To specify a tab as a separator, use `"\\t"` or `read.delim()`.

- You can also read in files directly into R from the Internet by replacing the file paths with a web address in `read.csv`. One should note that in doing this no local copy of the csv file is first saved onto your computer. For example,


```r
gapminder <- read.csv("https://raw.githubusercontent.com/swcarpentry/r-novice-gapminder/gh-pages/_episodes_rmd/data/gapminder_data.csv")
```

- You can read directly from excel spreadsheets without
  converting them to plain text first by using the [readxl](https://cran.r-project.org/package=readxl) package.

::::::::::::::::::::::::::::::::::::::::::::::::::

Let's investigate gapminder a bit; the first thing we should always do is check
out what the data looks like with `str`:


```r
str(gapminder)
```

```{.output}
'data.frame':	1704 obs. of  6 variables:
 $ country  : chr  "Afghanistan" "Afghanistan" "Afghanistan" "Afghanistan" ...
 $ year     : int  1952 1957 1962 1967 1972 1977 1982 1987 1992 1997 ...
 $ pop      : num  8425333 9240934 10267083 11537966 13079460 ...
 $ continent: chr  "Asia" "Asia" "Asia" "Asia" ...
 $ lifeExp  : num  28.8 30.3 32 34 36.1 ...
 $ gdpPercap: num  779 821 853 836 740 ...
```

An additional method for examining the structure of gapminder is to use the `summary` function. This function can be used on various objects in R. For data frames, `summary` yields a numeric, tabular, or descriptive summary of each column. Numeric or integer columns are described by the descriptive statistics (quartiles and mean), and character columns by its length, class, and mode.


```r
summary(gapminder)
```

```{.output}
   country               year           pop             continent        
 Length:1704        Min.   :1952   Min.   :6.001e+04   Length:1704       
 Class :character   1st Qu.:1966   1st Qu.:2.794e+06   Class :character  
 Mode  :character   Median :1980   Median :7.024e+06   Mode  :character  
                    Mean   :1980   Mean   :2.960e+07                     
                    3rd Qu.:1993   3rd Qu.:1.959e+07                     
                    Max.   :2007   Max.   :1.319e+09                     
    lifeExp        gdpPercap       
 Min.   :23.60   Min.   :   241.2  
 1st Qu.:48.20   1st Qu.:  1202.1  
 Median :60.71   Median :  3531.8  
 Mean   :59.47   Mean   :  7215.3  
 3rd Qu.:70.85   3rd Qu.:  9325.5  
 Max.   :82.60   Max.   :113523.1  
```

To extract a column, we can use the `$` operator. We use `head` to just see the first few entries:


```r
head(gapminder$lifeExp)
```

```{.output}
[1] 28.801 30.332 31.997 34.020 36.088 38.438
```

We can examine the types of individual columns of the data frame with the `typeof` function:


```r
typeof(gapminder$year)
```

```{.output}
[1] "integer"
```

```r
typeof(gapminder$country)
```

```{.output}
[1] "character"
```

```r
str(gapminder$country)
```

```{.output}
 chr [1:1704] "Afghanistan" "Afghanistan" "Afghanistan" "Afghanistan" ...
```

No matter how complicated data gets, in R, it is always one of 5 main types: `double`, `integer`, `complex`, `logical`, and `character`.

We can also interrogate the data frame for information about its dimensions;
remembering that `str(gapminder)` said there were 1704 observations of 6
variables in gapminder, what do you think the following will produce, and why?


```r
length(gapminder)
```

```{.output}
[1] 6
```

A fair guess would have been to say that the length of a data frame would be the
number of rows it has (1704), but this is not the case. Data frames are stored as lists of vectors, so the length is the number of separate columns of data.

::: callout

## Lists vs vectors

What's the difference between a list and a vector in R? A vector is a collection of objects of the same type:


```r
chr_vec <- c('a', 'b', 'c')
int_vec <- c(1, 2, 3)
```

A list on the other hand can contain multiple types:


```r
ex_list <- list_example <- list(1, "a", TRUE, 1+4i)
```

:::

A data frame is nothing more than a fancy list!


```r
typeof(gapminder)
```

```{.output}
[1] "list"
```

When `length` gave us 6, it's because gapminder is built out of a list of 6
columns. To get the number of rows and columns in our dataset, try:


```r
nrow(gapminder)
```

```{.output}
[1] 1704
```

```r
ncol(gapminder)
```

```{.output}
[1] 6
```

Or, both at once:


```r
dim(gapminder)
```

```{.output}
[1] 1704    6
```

We'll also likely want to know what the titles of all the columns are, so we can
ask for them later:


```r
colnames(gapminder)
```

```{.output}
[1] "country"   "year"      "pop"       "continent" "lifeExp"   "gdpPercap"
```

At this stage, it's important to ask ourselves if the structure R is reporting
matches our intuition or expectations; do the basic data types reported for each
column make sense? If not, we need to sort any problems out now before they turn
into bad surprises down the road, using what we've learned about how R
interprets data, and the importance of *strict consistency* in how we record our
data.

Once we're happy that the data types and structures seem reasonable, it's time
to start digging into our data proper. Check out the first few lines:


```r
head(gapminder)
```

```{.output}
      country year      pop continent lifeExp gdpPercap
1 Afghanistan 1952  8425333      Asia  28.801  779.4453
2 Afghanistan 1957  9240934      Asia  30.332  820.8530
3 Afghanistan 1962 10267083      Asia  31.997  853.1007
4 Afghanistan 1967 11537966      Asia  34.020  836.1971
5 Afghanistan 1972 13079460      Asia  36.088  739.9811
6 Afghanistan 1977 14880372      Asia  38.438  786.1134
```

:::::::::::::::::::::::::::::::::::::::  challenge

## Challenge 1

It's good practice to also check the last few lines of your data and some in the middle. How would you do this?

*Hint*: You can get help on a command by typing `?` before the command name in the console, e.g., `?head`.

Searching for lines specifically in the middle isn't too hard, but we could ask for a few lines at random. If you have time after finishing the other challenges, think of a way to code this.

*Hint*: You can search for commands by typing `??` before a search term, e.g., `??random`. 

:::::::::::::::  solution

## Solution to Challenge 1

To check the last few lines it's relatively simple as R already has a function for this:

```r
tail(gapminder)
tail(gapminder, n = 15)
```

What about a few arbitrary rows just in case something is odd in the middle?

## Tip: There are several ways to achieve this.

The solution here presents one form of using nested functions, i.e. a function passed as an argument to another function. This might sound like a new concept, but you are already using it!
Remember my\_dataframe[rows, cols] will print to screen your data frame with the number of rows and columns you asked for (although you might have asked for a range or named columns for example). How would you get the last row if you don't know how many rows your data frame has? R has a function for this. What about getting a (pseudorandom) sample? R also has a function for this.

```r
gapminder[sample(nrow(gapminder), 5), ]
```

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

To make sure our analysis is reproducible, we should put the code
into a script file so we can come back to it later.

:::::::::::::::::::::::::::::::::::::::  challenge

## Challenge 2

Make a `scripts/` directory inside your working directory. Go to File -> New File -> R Script, and write an R script to load in the gapminder dataset. Save the script in your `scripts/` directory.

Run the script using the `source` function, using the file path
as its argument (or by pressing the "source" button in RStudio).

:::::::::::::::  solution

## Solution to Challenge 2

The `source` function can be used to use a script within a script.
Assume you would like to load the same type of file over and over
again and therefore you need to specify the arguments to fit the
needs of your file. Instead of writing the necessary argument again
and again you could just write it once and save it as a script. Then,
you can use `source("Your_Script_containing_the_load_function")` in a new
script to use the function of that script without writing everything again.
Check out `?source` to find out more.


```r
download.file("https://raw.githubusercontent.com/swcarpentry/r-novice-gapminder/gh-pages/_episodes_rmd/data/gapminder_data.csv", destfile = "data/gapminder_data.csv")
gapminder <- read.csv(file = "data/gapminder_data.csv")
```

To run the script and load the data into the `gapminder` variable:


```r
source(file = "scripts/load-gapminder.R")
```

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::  challenge

## Challenge 3

Read the output of `str(gapminder)` again;
this time, use what you've learned about lists and vectors,
as well as the output of functions like `colnames` and `dim`
to explain what everything that `str` prints out for gapminder means.
If there are any parts you can't interpret, discuss with your neighbors!

:::::::::::::::  solution

## Solution to Challenge 4

The object `gapminder` is a data frame with columns

- `country` and `continent` are character strings.
- `year` is an integer vector.
- `pop`, `lifeExp`, and `gdpPercap` are numeric vectors.

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: keypoints

- Use `str()`, `summary()`, `nrow()`, `ncol()`, `dim()`, `colnames()`, `rownames()`, `head()`, and `typeof()` to understand the structure of a data frame.
- Read in a csv file using `read.csv()`.
- Understand what `length()` of a data frame represents.

::::::::::::::::::::::::::::::::::::::::::::::::::


