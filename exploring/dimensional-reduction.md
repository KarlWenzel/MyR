# Dimension Reduction



The purpose of [Dimension Reduction](https://en.wikipedia.org/wiki/Dimensionality_reduction)
is to reduce a large dataset with high dimensionality into a more manageable subset, yet still retaining the insight provided by the entire original set.  By high dimensionality, we are generally talking about 10 or more dimensions (i.e. sample variables) per sample observation.

This is done to mitigate the challenges that arise from the
[curse of dimensionality](https://en.wikipedia.org/wiki/Curse_of_dimensionality),
which refers to various phenomena that arise when analyzing and organizing data that is in high-dimensional spaces that do not occur in low-dimensional settings.  These types of problems include weakened effects on distance algorithms (large, noisy data makes distances regress to the mean, and thus harder to distinguish), and computational effeciency problems for runtime.

Dimension Reduction covers a number of different strategies and methods, classified in two broad categories:

- [Feature Selection](https://en.wikipedia.org/wiki/Feature_selection) (not covered)
- [Feautre Extraction](https://en.wikipedia.org/wiki/Feature_extraction) (including PCA and SVD covered herein)

Feature extraction may done using linear or nonlinear techiniques.
The main linear technique for dimension reduction is called
[Principal Component Analysis](https://en.wikipedia.org/wiki/Principal_component_analysis),
or **PCA**, which performs linear mapping of the data to a lower-dimensional space in such a way that the variance of the data in the low-dimensional representation is maximized, thereby reducing the effects of dependent variables and highlighting the effects of independent variables within the dataset.

Note that the rationale behind maximizing variance in our smaller subset may not be immediately intuitive to the naive perspective, because it is likely that a data scientist is ultimately searching for correlation, not trying to eliminate it. It may help the reader to consider a database design analogy to
[Database Normalization](https://en.wikipedia.org/wiki/Database_normalization), 
whereby we are concerned with decomposing a table into less redundant, smaller tables.  It may also help to consider this reductio ad absurdum: 

> "Aha!  I discovered from this data that people's zip codes correlate strongly with the city that they live in!"
>
> \- *some fool that didn't maximize variance in their subset*

PCA works by using an 
[Orthogonal Transformation](https://en.wikipedia.org/wiki/Orthogonal_transformation)
to convert an initial set of observations into a set of linearly uncorrelated variables called **principal components**.  This transformation is defined in such a way that the first principal component has the largest possible variance, and each succeeding component in turn has the highest possible variance under the constraint that it is orthogonal to the preceding components.

The results of a PCA are usually discussed in terms of component scores, sometimes called factor scores (the transformed variable values corresponding to a particular data point), and loadings (the weight by which each standardized original variable should be multiplied to get the component score).

PCA may be done by performing
[Singular Value Decomposition](https://en.wikipedia.org/wiki/Singular_value_decomposition)
of a data matrix, usually after mean centering (and normalizing or using Z-scores) the data matrix for each attribute.

Singular value decomposition, or **SVD**, is the factorization of a matrix (real or complex).  It says that for a matrix M, then M = UDV such that:

- U is the left singular vectors (rotates M)
- D is a diagonal matrix (stretches M)
- V is the right singular vectors (rotates M)

The following R functions for dimension reduction are covered herein:

- `scale()`
- `svd()`
- `prcomp()`


```r
# Let's make a 40 x 10 matrix full of random data.
M = matrix(rnorm(400), nrow=40)

# This is too random, so we'll introduce a pattern. 
pattern = rep(c(0,3), each=5)
str(pattern)
```

```
##  num [1:10] 0 0 0 0 0 3 3 3 3 3
```

```r
for (i in 1:40) {
  coinFlip <- rbinom(1, size=1, prob=0.5)
  if (coinFlip) {
    M[i,] = M[i,] + pattern
  }
}
head( round(M, digits=2) )
```

```
##       [,1]  [,2]  [,3]  [,4]  [,5]  [,6]  [,7] [,8] [,9] [,10]
## [1,]  0.52  0.55 -0.44 -0.16  1.08  3.44  1.90 2.96 3.05  3.31
## [2,]  1.69  0.56 -1.56 -0.23 -1.31  2.21  1.26 3.08 4.21  2.46
## [3,]  0.60  0.93 -1.10 -0.62  0.51 -0.52 -0.42 0.66 1.70 -1.94
## [4,]  0.57 -0.42 -1.22 -0.44 -1.23  1.88  2.55 1.59 3.09  3.85
## [5,]  0.58  0.95  1.20  0.06  0.09  5.23  5.65 3.76 4.11  4.51
## [6,] -1.17 -0.30  0.61  0.45  0.61  3.10  1.48 3.98 2.49  1.77
```

```r
# Plot an image that demonstrates the results of the U and V singular vectors.
par(mar = rep(4, 4))
par(mfrow = c(1,3))

SVD <- svd(scale(M))

image(t(M)[, nrow(M):1], main="Raw Data", xlab="Columns", ylab="Rows")
plot(SVD$u[,1], 40:1, xlab="Rows", main="Left Singular Vector (1st)", pch=19)
plot(SVD$v[,1], xlab="Columns", main="Right Singular Vector (1st)", pch=19)
```

![](dimensional-reduction_files/figure-html/example-1.png)

```r
# take another look, except focus on 1st and 2nd right singlur vectors
image(t(M)[, nrow(M):1], main="Raw Data", xlab="Columns", ylab="Rows")
plot(SVD$v[,1], xlab="Columns", main="Right Singular Vector (1st)", pch=19)
plot(SVD$v[,2], xlab="Columns", main="Right Singular Vector (2nd)", pch=19)
```

![](dimensional-reduction_files/figure-html/example-2.png)

```r
par(mfrow=c(1,1))

# what about the D matix? will show us in order of decreasing variance
plot( SVD$d, xlab="Column", ylab="Singular value", pch=19 )
```

![](dimensional-reduction_files/figure-html/example-3.png)

```r
# prcomp() is PCA, and we shall compare its rotation matrix to SVD's V matrix
PCA = prcomp(M, scale=TRUE)
plot( PCA$rotation[,1], SVD$v[,1], pch=19, xlab="Principal Component 1", ylab="Right Singular Vector 1")
abline(c(0,1))
```

![](dimensional-reduction_files/figure-html/example-4.png)

```r
# Missing values screw up SVD!  This will be misinterpreted
# option 1: impute... takes missing rows and computes it using k-nearest neighbors
library(impute)
M2 = M
M2[sample(1:100, size=40, replace=FALSE)] <- NA
M2 = impute.knn(M2)$data
SVD2 = svd(scale(M2))
par(mfrow=c(1,2));
plot( SVD$v[,1], pch=19, main="V of Raw Data")
plot( SVD2$v[,1], pch=19, main="V of Imputed Data")
```

![](dimensional-reduction_files/figure-html/example-5.png)
