---
title: "Assignment 0"
date: 2025-01-15 00:00:00 +0000
categories: [Academics, STAC33]
tags: [r, quarto, ggplot]
---

# Assignment 0

2025 Winter Semester at University of Toronto Class
STAC33: Assignment 0 basics.
An example using Quarto and Rstudio I built the html file called assignment0.html which i've submitted in my quercus(uoft submission page)

---

### **Section 1: Data Wrangling**

#### **1.1 Load Libraries**
Load the required R packages for analysis.

```r
library(tidyverse)
```

#### **1.2 Clean Dataset**
Explore the summary of the `mtcars` dataset.
mtcars is already loaded within default R itself so no need to worry.

```r
summary(mtcars)
```

---

### **Section 2: Analysis & Visualization**

#### **2.1 Plotting Data**
Visualize the distribution of miles per gallon (`mpg`) using a histogram.

```r
ggplot(mtcars, aes(x = mpg)) + geom_histogram(bins = 10)
```