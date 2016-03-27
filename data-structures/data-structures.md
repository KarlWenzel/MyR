# Data Structures



### Table of Contents

1. [Table of Contents](#table-of-contents)
2. [Basics](#some-terminology-basics)
3. [Data frame basics](#data-frame-basics)
4. [Filter and Project](#filter-and-project)
5. [Adding Rows and Columns](#adding-rows-and-columns)
6. [Joining and Aggregating](#joining-and-aggregating)


### Some terminology basics

1. Types of **atomic vectors** (see [more](http://arrgh.tim-smith.us/atomic.html) on **vectors** from Tim Smith's page)
    - logical (may contain TRUE, FALSE, NA)
    - integer
    - double
    - complex (as in complex numbers; write as 0+0i)
    - character
    - raw (for bitstreams; printed in hex by default. Logical operators magically operate bitwise on these; they operate elementwise on all other vector types.)
    
2. **Arrays** include
    - **vectors** (i.e. 1 dimensional **arrays**)
    - **matrices** (i.e. 2 dimensional **arrays**)
    
4. Type of dimension labels:
    - `dimnames()` for **arrays**
    - `rownames()`, `colnames()` for **matrices** and **data frames**
    - `names()` for **atomic vectors** and **lists**
	
5. Coercing a **data frame**
    - From data frame to **array**: gets treated like 2d **matrices**
    - From data frame to **list** : gets treated like **list of vectors**

[top](#table-of-contents)

### Data Frame Basics


```r
df = data.frame( 
  col.1 = as.factor(c('a','b','a','b')), 
  col.2 = rnorm(1:4), 
  col.3 = 5:8 
)
print(df)
```

```
##   col.1      col.2 col.3
## 1     a -1.6712098     5
## 2     b  0.1793051     6
## 3     a  0.8901839     7
## 4     b -0.2884902     8
```

```r
# Isolate a column i.e. a dimension, a sample variable
print(df$col.2)
```

```
## [1] -1.6712098  0.1793051  0.8901839 -0.2884902
```

```r
# Isolate a row i.e. a record, a sample observation
print(df[1,])
```

```
##   col.1    col.2 col.3
## 1     a -1.67121     5
```

```r
# An abbreviated view of the data frame
str(df)
```

```
## 'data.frame':	4 obs. of  3 variables:
##  $ col.1: Factor w/ 2 levels "a","b": 1 2 1 2
##  $ col.2: num  -1.671 0.179 0.89 -0.288
##  $ col.3: int  5 6 7 8
```
[top](#table-of-contents)

### Filter and Project


```r
# Create copy of data frame after filtering out rows i.e. records, sample observations
subset(df, df$col.1=='a')
```

```
##   col.1      col.2 col.3
## 1     a -1.6712098     5
## 3     a  0.8901839     7
```

```r
# You may filter and project a data frame with [,]
# From an SQL perspective, visualize it as dataframe[ WHERE, SELECT ]
#
df[ df$col.3 > 6, ] # Note 2nd param in [,] is blank to indicate all variables
```

```
##   col.1      col.2 col.3
## 3     a  0.8901839     7
## 4     b -0.2884902     8
```
[top](#table-of-contents)

### Adding Rows and Columns

```r
# Adding a column (i.e. dimension, variable) is easy
df$col.4 = NA
print(df)
```

```
##   col.1      col.2 col.3 col.4
## 1     a -1.6712098     5    NA
## 2     b  0.1793051     6    NA
## 3     a  0.8901839     7    NA
## 4     b -0.2884902     8    NA
```

```r
# Adding rows is easy too. First, we will make some new rows
df.more = data.frame( 
  col.1 = as.factor(c('a','b')), 
  col.2 = rnorm(1:2), 
  col.3 = 9:10,
  col.4 = rep("new",2)
)

# Now combine the rows, as in an SQL UNION
rbind(df, df.more)
```

```
##   col.1      col.2 col.3 col.4
## 1     a -1.6712098     5  <NA>
## 2     b  0.1793051     6  <NA>
## 3     a  0.8901839     7  <NA>
## 4     b -0.2884902     8  <NA>
## 5     a  0.7096711     9   new
## 6     b -0.4124081    10   new
```
[top](#table-of-contents)

### Joining and Aggregating


```r
# Create a reference table.  We will use this to do a key lookup based on col.1
df.ref = data.frame( 
  col.1 = as.factor(c('a','b')), 
  col.ref =c('AA','BB')
)
print(df.ref)
```

```
##   col.1 col.ref
## 1     a      AA
## 2     b      BB
```

```r
# Using the plyr library we can JOIN the reference data to the original data
library(plyr)
join(df, df.ref, by="col.1")
```

```
##   col.1      col.2 col.3 col.4 col.ref
## 1     a -1.6712098     5    NA      AA
## 2     b  0.1793051     6    NA      BB
## 3     a  0.8901839     7    NA      AA
## 4     b -0.2884902     8    NA      BB
```

```r
# We can also use the plyr library for GROUP BY operations on data projections
library(plyr)
ddply( df, .(col.1), summarize, sum.col.2 = sum(col.2), min.col.3 = min(col.3) )
```

```
##   col.1  sum.col.2 min.col.3
## 1     a -0.7810258         5
## 2     b -0.1091851         6
```
[top](#table-of-contents)

