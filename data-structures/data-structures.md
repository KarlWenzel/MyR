# Data Structures



### Table of Contents

1. [Table of Contents](#table-of-contents)
2. [Basics](#some-basics)
3. [Data Frames](#data-frame-basics)
    - [Get Rows and Columns](#get-rows-and-columns)
    - [Add Rows and Columns](#add-rows-and-columns)
    - [Filter and Project](#filter-and-project)
    - [Warning with Brackets](#warning-with-brackets)
    - [Join and Aggregate](#join-and-aggregate)
4. [Lists](#list-basics)


### Some Basics

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
#Here is a data frame
df = data.frame( 
  col.1 = as.factor(c('a','b','a','b')), 
  col.2 = rnorm(1:4), 
  col.3 = 5:8 
)
print(df)
```

```
##   col.1       col.2 col.3
## 1     a -0.60899513     5
## 2     b -0.29201327     6
## 3     a  0.56647824     7
## 4     b  0.09076276     8
```

```r
# An abbreviated view of the data frame.  Good for investigation of newly read/created data
str(df)
```

```
## 'data.frame':	4 obs. of  3 variables:
##  $ col.1: Factor w/ 2 levels "a","b": 1 2 1 2
##  $ col.2: num  -0.609 -0.292 0.5665 0.0908
##  $ col.3: int  5 6 7 8
```
[top](#table-of-contents)

#### Get Rows and Columns


```r
# You may get/set the column names 
#   i.e. names of dimensions 
#   i.e. names of sample variables
colnames(df)
```

```
## [1] "col.1" "col.2" "col.3"
```

```r
# Isolate a column
print( df$col.2 )
```

```
## [1] -0.60899513 -0.29201327  0.56647824  0.09076276
```

```r
# You may get/set the row names
#   i.e. names of records 
#   i.e. names of tuples 
#   i.e. names of sample observations
rownames(df)
```

```
## [1] "1" "2" "3" "4"
```

```r
# Isolate a row 
print( df[1,] )
```

```
##   col.1      col.2 col.3
## 1     a -0.6089951     5
```
[top](#table-of-contents)

#### Add Rows and Columns

```r
# Adding a column is easy
df$col.4 = NA
print(df)
```

```
##   col.1       col.2 col.3 col.4
## 1     a -0.60899513     5    NA
## 2     b -0.29201327     6    NA
## 3     a  0.56647824     7    NA
## 4     b  0.09076276     8    NA
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
##   col.1       col.2 col.3 col.4
## 1     a -0.60899513     5  <NA>
## 2     b -0.29201327     6  <NA>
## 3     a  0.56647824     7  <NA>
## 4     b  0.09076276     8  <NA>
## 5     a -1.81260909     9   new
## 6     b -0.42322309    10   new
```
[top](#table-of-contents)

#### Filter and Project


```r
# Create filtered subset of rows
subset(df, df$col.1 == 'a')
```

```
##   col.1      col.2 col.3 col.4
## 1     a -0.6089951     5    NA
## 3     a  0.5664782     7    NA
```

```r
# You may filter and project a data frame with [,]
# From an SQL perspective, visualize it as dataframe[ WHERE, SELECT ]
#
df[ df$col.1 == 'a', ] # Note 2nd param in [,] is blank to indicate all variables
```

```
##   col.1      col.2 col.3 col.4
## 1     a -0.6089951     5    NA
## 3     a  0.5664782     7    NA
```

```r
# Here it is again but with specific columns being projected
df[ df$col.1 == 'a', c("col.1", "col.3") ]
```

```
##   col.1 col.3
## 1     a     5
## 3     a     7
```
[top](#table-of-contents)

#### Warning with Brackets

```r
# Be careful when using [] with dataframe instead of [,]
#   this specifies what column(s) to project

# From an SQL perspective, visualize it as dataframe[ SELECT ] 
df[1]
```

```
##   col.1
## 1     a
## 2     b
## 3     a
## 4     b
```

```r
# May also use a column name as a key
df["col.1"]
```

```
##   col.1
## 1     a
## 2     b
## 3     a
## 4     b
```
[top](#table-of-contents)

#### Join and Aggregate


```r
# Create a reference table.  We will use this to do a key lookup based on col.1
df.ref = data.frame( 
  col.1 = as.factor(c('a','b')), 
  col.ref1 = c('AA','BB'),
  col.ref2 = c('aaa','bbb')
)
print(df.ref)
```

```
##   col.1 col.ref1 col.ref2
## 1     a       AA      aaa
## 2     b       BB      bbb
```

```r
# Using the plyr library we can JOIN the reference data to the original data
library(plyr)
join(df, df.ref, by="col.1")
```

```
##   col.1       col.2 col.3 col.4 col.ref1 col.ref2
## 1     a -0.60899513     5    NA       AA      aaa
## 2     b -0.29201327     6    NA       BB      bbb
## 3     a  0.56647824     7    NA       AA      aaa
## 4     b  0.09076276     8    NA       BB      bbb
```

```r
# We can also use the plyr library for GROUP BY operations on data projections
library(plyr)
ddply( df, .(col.1), summarize, sum.col.2 = sum(col.2), min.col.3 = min(col.3) )
```

```
##   col.1   sum.col.2 min.col.3
## 1     a -0.04251689         5
## 2     b -0.20125051         6
```
[top](#table-of-contents)

### List Basics


```r
# Create a list.  The '.' function from the plyr library is handy for this.
library(plyr)
x = .(1, 2, "three", as.factor(4))
str(x)
```

```
## List of 4
##  $ 1           : num 1
##  $ 2           : num 2
##  $ "three"     : chr "three"
##  $ as.factor(4): language as.factor(4)
##  - attr(*, "env")=<environment: R_GlobalEnv> 
##  - attr(*, "class")= chr "quoted"
```

```r
# Subset the list
x[1:3]
```

```
## List of 3
##  $ 1      : num 1
##  $ 2      : num 2
##  $ "three": chr "three"
##  - attr(*, "env")=<environment: R_GlobalEnv> 
##  - attr(*, "class")= chr "quoted"
```

```r
# Subset the list to only include a specific element
x[1]
```

```
## List of 1
##  $ 1: num 1
##  - attr(*, "env")=<environment: R_GlobalEnv> 
##  - attr(*, "class")= chr "quoted"
```

```r
# Obtain element from list
x[[1]] # equivalent to unlist(x[1]), see below
```

```
## [1] 1
```

```r
# Convert from list subset to vector of contained elements (assuming consistent element types)
unlist( x[1:2] )
```

```
## [1] 1 2
```

```r
# If a subset query matches no elements, then a list with NULL as its only element is returned
x[5000]
```

```
## List of 1
##  $ NULL: NULL
##  - attr(*, "env")=<environment: R_GlobalEnv> 
##  - attr(*, "class")= chr "quoted"
```

```r
# However trying to access an element that does not exist is a error state
tryCatch({
    x[[5000]]
  }
  ,error=function(cond) {
    print(cond)
  }
)
```

```
## <simpleError in x[[5000]]: subscript out of bounds>
```
[top](#table-of-contents)





