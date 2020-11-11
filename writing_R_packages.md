---
title: "Writing R packages in R studio - a very simple intro"
author: "Petr Keil, Department of Spatial Sciences, FZP CZU"
date: "Nov 11, 2020"
output: 
  html_document: 
    highlight: tango
    theme: cerulean
    toc: yes
    number_sections: yes
    keep_md: yes
---



This is material that I will use for an approx. 1-hour seminar giving intro
to the very basic of R packaging.

********************************************************************************

# Why R packages?

- Clean and standardized way to **document** and **share** your code (and data!), even if you don't plan to make a big 'cran' package.
- It forces you to organize the code better, and to respect the conventions.
- They are easier to write than you might think.
- They save everyone's time!

********************************************************************************

# Tutorials

Simple, useful, great:  [Hadley Wickham's website](http://r-pkgs.had.co.nz/)

Comprehensive: [Writing R Extensions](https://cran.r-project.org/doc/manuals/r-release/R-exts.html)

Other nice tutorials are [this one](http://web.mit.edu/insong/www/pdf/rpackage_instructions.pdf) or 
[this one](http://tinyheero.github.io/jekyll/update/2015/07/26/making-your-first-R-package.html). 

********************************************************************************


# Initialize

Before we start, you might need the following packages:

```
install.packages(c("devtools", "roxygen2", "knitr"))
```

We also need a **name**! Needs to be unique, easily "googleable", avoid mixtures
of upper and lower cases (RColorBrewer or Rcolorbrewer? EcoSimR or ecosimR or ecosimr?). Note: the winner in the best package name contest is `lubridate`.

Now we can initialize a package.

**1. From within R console:**

Using a function from `utils`:

```
package.skeleton("shampoo")
```

Using a function from `devtools`:

```
create_package("shampoo")
```

Note that all objects and functions that are currently in the R environment are exported!

**2. With R studio:**

File -> New Project -> New Directory -> New Package

********************************************************************************

# What is in the folder?

### R/

That is where the code lives. Good practice is to give each R function its own file, unless...

### DESCRIPTION

This is where the metadata are.

Two important lines are `Imports` and `Suggests`, e.g.:

```
Imports: plyr, raster
Suggests: sp
```

### NAMESPACE

Advanced topic, not that necessary if packages are for personal use, we will skip this for now.

Also, `roxygen2` takes care of this.

### man/

This is where the help documentaion is. We don't need to worry about this
with `roxygen2`.

### data/

This is where all data and stored objects (non-functions) live. The simplest
way to bundle data with an R package is by putting `.RData` files in here.

If you want the datasets to have their own documentation, insert a `data.R`
file into the R/ folder for roxygen2 to parse.


### src/

This is where compiled code is. If you plan to include C++ code in your package, I strongly recommend to use a predefined template in R studio, i.e.:

File -> New Project -> New Directory -> R package with C++ code

For a really smooth intro to C++ in R see [Hadley Wickham's tutorial](http://adv-r.had.co.nz/Rcpp.html).

### vignettes/ 

```
devtools::use_vignette("how_shampoo_works")
```

### tests/

### README.md, .gitignore, .git

This is all for the GitHub repository, if we have one.

********************************************************************************

# Documentation

You can directly edit thee `.Rd` files in the `man` folder, but it is much more convenient and easier to write the documentation right above the R functions in the `R` folder.

To do this, install and load package `roxygen2`. For a nice intro see [this site](https://cran.r-project.org/web/packages/roxygen2/vignettes/roxygen2.html).

Once you are done with the documentation, run `roxygen2::roxygenize()` in your R command line. In R studio, you can also set this to happen automatically before each build: Build -> More -> Configure Build Tools

### For functions
```
#' Function that adds together two numbers.
#' 
#' This is a really cool and complex function that adds two numbers. I have got
#' the inspiration for this from Hadley Wickahm's web.
#' @param x A number.
#' @param y A number.
#' @return The sum of \code{x} and \code{y}.
#' @examples
#' add(1, 1)
#' add(10, 1)
#' @export

add <- function(x, y) 
{
  x + y
}
```

Examples of other useful declarations are:

```
#' @inherit params
#' @import vegan
#' @references Arita H. (2016) Species co-occurrence analysis: pairwise versus
#' matrix-level approaches. Global Ecology and Biogeography, 25: 1397-1400.

```

### For data 

Create `data.R` file in the `R/` folder and for each dataset insert the following
snippet:

```
#' Title of your data object
#'
#' Description of your data object. Plus, you can add something like:
#'
#' @format A data frame with 53940 rows and 10 variables:
#' \describe{
#'   \item{price}{price, in US dollars}
#'   \item{carat}{weight of the diamond, in carats}
#'   ...
#' }
#' @source \url{http://www.diamondse.info/}
"diamonds"
```

********************************************************************************
# Build and install

Before the package can be used, we must **build** it. This is a procedure that "glues"
everything together, compiles it, and makes the package available for installation.

The procedure is as follows:

1. **Save** all your files (code, documentation, descriptions, data, ...)
2. **Update documentation** with `roxygen2::roxygenize()`. You can also set this to happen 
   automatically in R studio in  Build -> More -> Configure Build Tools
3. In R studio, hit the **Install and Restart button** in the "Build" tab. 

Alternatively, you can build your package using command prompt. The procedure is:

- Edit DESCRIPTION file
- Change R code and/or data files.
- Edit NAMESPACE file
- Update man files, e.g. with the help of `roxygen2::roxygenize`.

Then in your terminal (command prompt): 

```
R CMD build shampoo
R CMD check shampoo
R CMD INSTALL shampoo
```

********************************************************************************

# Making R package available

Let's not care about CRAN for now. Let's publish on GitHub!

First, create the repository on GitHub.

Second, initialize git for your package, and push it to the repository:

```
git init
git add README.md
git commit -m "first commit"
git remote add origin https://github.com/petrkeil/shampoo.git
git push -u origin master
```

Now you can install your package on any machine using:

```
library(devtools)
install_github("petrkeil/shampoo")
```
