# Data Structures



### Table of Contents

1. [Table of Contents](#table-of-contents)
2. [General](#general)
3. [Matrix](#matrix)
    - [Transpose](#transpose)
    - [Basic Matrix Algebra](#basic-matrix-algebra)
    - [Plot as Image](#plot-as-image)
4. [Data Frame](#data-frame)
    - [Get Rows and Columns](#get-rows-and-columns)
    - [Add Rows and Columns](#add-rows-and-columns)
    - [Change Columns](#change-columns)
    - [Filter and Project](#filter-and-project)
    - [Order By](#order-by)
    - [Warning with Brackets](#warning-with-brackets)
    - [Join and Aggregate](#join-and-aggregate)
5. [List](#list)

### General

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
    - From data frame to **list** : gets treated like a **list of vectors**

[top](#table-of-contents)

### Matrix

In mathematics, a [matrix](https://en.wikipedia.org/wiki/Matrix_(mathematics))
is a 2-dimensional array of numbers, symbols, or expressions which are arranged by rows and columns.  A row may be thought of as a tuple or a vector, and each column may be thought of as a dimesion of a vector.

![Mathematics-Matrix](./494px-Matrix.svg.png)


```r
# Make a simple matrix consiting on 4 vectors (3-dimensional)
M = matrix(data = 1:12, nrow=4, ncol=3)
print(M)
```

```
##      [,1] [,2] [,3]
## [1,]    1    5    9
## [2,]    2    6   10
## [3,]    3    7   11
## [4,]    4    8   12
```

[top]()

#### Transpose

```r
# t() for transpose (visualize as an 180 degree flip on the diagnoal axis from [1,1] to [4,3]
t(M)
```

```
##      [,1] [,2] [,3] [,4]
## [1,]    1    2    3    4
## [2,]    5    6    7    8
## [3,]    9   10   11   12
```

[top]()

#### Basic Matrix Algebra

```r
# Basic matrix algebra is easy
M + 1000
```

```
##      [,1] [,2] [,3]
## [1,] 1001 1005 1009
## [2,] 1002 1006 1010
## [3,] 1003 1007 1011
## [4,] 1004 1008 1012
```

```r
M * 10
```

```
##      [,1] [,2] [,3]
## [1,]   10   50   90
## [2,]   20   60  100
## [3,]   30   70  110
## [4,]   40   80  120
```

```r
M / 12
```

```
##            [,1]      [,2]      [,3]
## [1,] 0.08333333 0.4166667 0.7500000
## [2,] 0.16666667 0.5000000 0.8333333
## [3,] 0.25000000 0.5833333 0.9166667
## [4,] 0.33333333 0.6666667 1.0000000
```

```r
# We can also do a cross product between 2 matrices
M2 = matrix( 1:6, nrow=ncol(M) )
M2
```

```
##      [,1] [,2]
## [1,]    1    4
## [2,]    2    5
## [3,]    3    6
```

```r
# M: 4x3, M2: 3x2, (M x M2): 4x2
crossprod(t(M), M2)
```

```
##      [,1] [,2]
## [1,]   38   83
## [2,]   44   98
## [3,]   50  113
## [4,]   56  128
```

```r
# Conjugate transpose (or the star matrix: M*)
Conj(t(M))
```

```
##      [,1] [,2] [,3] [,4]
## [1,]    1    2    3    4
## [2,]    5    6    7    8
## [3,]    9   10   11   12
```

[top]()

#### Plot as Image


```r
# image() provides a way to visualize the matrix.  Visualize as a spreadsheet
#   Note: due to how image() orders points, we have to transpose and reorder rows
#   to make it orient the same way as our matrix output appears in the console.
image( t( M[ nrow(M):1, ] ) )
```

![](data-structures_files/figure-html/plot-as-image-1.png)

[top](#table-of-contents)

### Data Frame

```r
set.seed(1234) # Use same random data each time

# Here is a data frame
DF = data.frame( 
  col.1 = as.factor(c('a','b','a','b')), 
  col.2 = rnorm(1:4), 
  col.3 = 5:8 
)
print(DF)
```

```
##   col.1      col.2 col.3
## 1     a -1.2070657     5
## 2     b  0.2774292     6
## 3     a  1.0844412     7
## 4     b -2.3456977     8
```

```r
# An abbreviated view of the data frame.  Good for investigation of newly read/created data
str(DF)
```

```
## 'data.frame':	4 obs. of  3 variables:
##  $ col.1: Factor w/ 2 levels "a","b": 1 2 1 2
##  $ col.2: num  -1.207 0.277 1.084 -2.346
##  $ col.3: int  5 6 7 8
```
[top](#table-of-contents)

#### Get Rows and Columns

```r
# You may get or set the column names 
#   i.e. names of dimensions / sample variables
colnames(DF)
```

```
## [1] "col.1" "col.2" "col.3"
```

```r
# Isolate a column, NOTE that this yields a vector (array of homogenous type)
print( DF$col.2 )
```

```
## [1] -1.2070657  0.2774292  1.0844412 -2.3456977
```

```r
# You may get or set the row names
#   i.e. names of records / tuples / vectors / sample observations
rownames(DF)
```

```
## [1] "1" "2" "3" "4"
```

```r
# Isolate a row , NOTE that this yields a data.frame (with only one row)
print( DF[1,] )
```

```
##   col.1     col.2 col.3
## 1     a -1.207066     5
```
[top](#table-of-contents)

#### Add Rows and Columns

```r
# Adding a column is easy
DF$col.4 = NA
print(DF)
```

```
##   col.1      col.2 col.3 col.4
## 1     a -1.2070657     5    NA
## 2     b  0.2774292     6    NA
## 3     a  1.0844412     7    NA
## 4     b -2.3456977     8    NA
```

```r
# Adding rows is easy too. First, we will make some new rows
MORE = data.frame( 
  col.1 = as.factor(c('a','b')), 
  col.2 = rnorm(1:2), 
  col.3 = 9:10,
  col.4 = rep("new",2)
)

# Now combine the rows, as in an SQL UNION
rbind(DF, MORE)
```

```
##   col.1      col.2 col.3 col.4
## 1     a -1.2070657     5  <NA>
## 2     b  0.2774292     6  <NA>
## 3     a  1.0844412     7  <NA>
## 4     b -2.3456977     8  <NA>
## 5     a  0.4291247     9   new
## 6     b  0.5060559    10   new
```
[top](#table-of-contents)

#### Change Columns

```r
# Currently DF$col.1 is a factor vector. Lets change it to a character vector
DF$col.1 = as.character(DF$col.1)
str(DF)
```

```
## 'data.frame':	4 obs. of  4 variables:
##  $ col.1: chr  "a" "b" "a" "b"
##  $ col.2: num  -1.207 0.277 1.084 -2.346
##  $ col.3: int  5 6 7 8
##  $ col.4: logi  NA NA NA NA
```

```r
# Now change DF$col.1 back to a vector using a different syntax
DF = transform(DF, col.1 = as.factor(col.1))
str(DF)
```

```
## 'data.frame':	4 obs. of  4 variables:
##  $ col.1: Factor w/ 2 levels "a","b": 1 2 1 2
##  $ col.2: num  -1.207 0.277 1.084 -2.346
##  $ col.3: int  5 6 7 8
##  $ col.4: logi  NA NA NA NA
```
[top](#table-of-contents)

#### Filter and Project

```r
# Create filtered subset of rows
subset(DF, DF$col.1 == 'a')
```

```
##   col.1     col.2 col.3 col.4
## 1     a -1.207066     5    NA
## 3     a  1.084441     7    NA
```

```r
# You may filter and project a data frame with [,]
# From an SQL perspective, visualize it as dataframe[ WHERE, SELECT ]
#
DF[ DF$col.1 == 'a', ] # Note 2nd param in [,] is blank to indicate all variables
```

```
##   col.1     col.2 col.3 col.4
## 1     a -1.207066     5    NA
## 3     a  1.084441     7    NA
```

```r
# Here it is again but with specific columns being projected
DF[ DF$col.1 == 'a', c("col.1", "col.3") ]
```

```
##   col.1 col.3
## 1     a     5
## 3     a     7
```
[top](#table-of-contents)

#### Order By

```r
# Recall you may access a row by its name, which by default is an integer sequence: 1, 2, ..., nrow
print( DF[1,] )
```

```
##   col.1     col.2 col.3 col.4
## 1     a -1.207066     5    NA
```

```r
# You may also access rows in order of your choice
DF[ nrow(DF):1, ]
```

```
##   col.1      col.2 col.3 col.4
## 4     b -2.3456977     8    NA
## 3     a  1.0844412     7    NA
## 2     b  0.2774292     6    NA
## 1     a -1.2070657     5    NA
```

```r
# The order() function in base R takes column names (use '-' for descending)
DF[ order(DF$col.1, -DF$col.2), ]
```

```
##   col.1      col.2 col.3 col.4
## 3     a  1.0844412     7    NA
## 1     a -1.2070657     5    NA
## 2     b  0.2774292     6    NA
## 4     b -2.3456977     8    NA
```
[top](#table-of-contents)

#### Warning with Brackets

```r
# Be careful when using [] with dataframe instead of [,]
#   this specifies what column(s) to project

# From an SQL perspective, visualize it as dataframe[ SELECT ] 
DF[1]
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
DF["col.1"]
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
REF = data.frame( 
  col.1 = as.factor(c('a','b')), 
  col.ref1 = c('AA','BB'),
  col.ref2 = c('aaa','bbb')
)
print(REF)
```

```
##   col.1 col.ref1 col.ref2
## 1     a       AA      aaa
## 2     b       BB      bbb
```

```r
# Using the plyr library we can JOIN the reference data to the original data
library(plyr)
join(DF, REF, by="col.1")
```

```
##   col.1      col.2 col.3 col.4 col.ref1 col.ref2
## 1     a -1.2070657     5    NA       AA      aaa
## 2     b  0.2774292     6    NA       BB      bbb
## 3     a  1.0844412     7    NA       AA      aaa
## 4     b -2.3456977     8    NA       BB      bbb
```

```r
# We can also use the plyr library for GROUP BY operations on data projections
library(plyr)
ddply( DF, .(col.1), summarize, sum.col.2 = sum(col.2), min.col.3 = min(col.3) )
```

```
##   col.1  sum.col.2 min.col.3
## 1     a -0.1226246         5
## 2     b -2.0682685         6
```
[top](#table-of-contents)

### List


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
# Convert from list subset to vector of contained elements (with coercion)
unlist( x[1:3] )
```

```
## [1] "1"     "2"     "three"
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
# However trying to access an element that does not exist is an error state
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





