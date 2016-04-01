# Hierarchical Clustering



[Clustering](https://en.wikipedia.org/wiki/Cluster_analysis)
is organizing things that are similar to each other into groups.
The definition of "similar" is determined by various algorithms that differ significantly in their notion of what constitutes a cluster and how to efficiently find them. 

[Hierarchical clustering](https://en.wikipedia.org/wiki/Hierarchical_clustering),
also known as **hierarchical cluster analysis**, or **HCA** is a method of cluster analysis
which seeks to build a hierarchy of clusters using one of two general approaches:

- **Agglomerative**: This is a "bottom up" approach: 
each observation starts in its own cluster,
and pairs of clusters are merged as one moves up the hierarchy.
- **Divisive**: This is a "top down" approach: 
all observations start in one cluster,
and splits are performed recursively as one moves down the hierarchy.

In general, the merges and splits are determined in a greedy manner. The results of hierarchical clustering are usually presented in a [dendrogram](https://en.wikipedia.org/wiki/Dendrogram),
i.e. a tree diagram used to illustrate the hierarchical arrangment of clusters,
and may be overlayed with [heatmaps](https://en.wikipedia.org/wiki/Heat_map).


```r
# dist() function creates distance matrix
set.seed(1234)

# First we shall make some random data that has a pattern.
PTS = data.frame(
    x = rnorm( 12, mean=rep(1:3, each=4), sd=0.2 ),
    y = rnorm( 12, mean=rep(c(1,2,1), each=4), sd=0.2 )
)
plot(PTS$x, PTS$y, pch=19)
```

![](hierarchical-clustering_files/figure-html/example-1.png)

```r
# dist() is our distance method, and it will uses Euclidean distance
#   i.e. sqrt( (a1-a2)^2 + (b1-b2)^2 + ... + (z1-z2)^2 )
D.PTS <- dist(PTS)
str(D.PTS)
```

```
## Class 'dist'  atomic [1:66] 0.341 0.575 0.264 1.694 1.658 ...
##   ..- attr(*, "Size")= int 12
##   ..- attr(*, "Diag")= logi FALSE
##   ..- attr(*, "Upper")= logi FALSE
##   ..- attr(*, "method")= chr "euclidean"
##   ..- attr(*, "call")= language dist(x = PTS)
```

```r
# hclust() turns the distance matrix into a hieararchical cluster i.e. dendrogram
HC = hclust(D.PTS)

# Note that rows (i.e. vectors) are the domain, and their distance values are the range
plot(HC)
```

![](hierarchical-clustering_files/figure-html/example-2.png)

```r
# heatmap() wants to use a matrix.  Notice that the dimensions are the domain now. 
heatmap( as.matrix(PTS) )
```

![](hierarchical-clustering_files/figure-html/example-3.png)

```r
# Using a dendrogram + heatmap is more useful for multivariate data.  
# Let's make a 40 x 10 matrix full of random data.
BIGM = matrix(rnorm(400), nrow=40)
str(BIGM)
```

```
##  num [1:40, 1:10] -0.6937 -1.4482 0.5748 -1.0237 -0.0151 ...
```

```r
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
    BIGM[i,] = BIGM[i,] + pattern
  }
}

# Now use hierarchical cluster analysis to find the pattern
heatmap(BIGM) 
```

![](hierarchical-clustering_files/figure-html/example-4.png)
