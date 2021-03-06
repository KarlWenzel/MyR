# Linear Regression and Multivariable Regression

See https://leanpub.com/regmods

And https://github.com/bcaffo/courses/blob/master/07_RegressionModels/


We now extend linear regression so that our models can contain more variables. A natural first approach is to assume additive effects, basically extending our line to a plane, or generalized version of a plane as we add more variables. Multivariable regression represents one of the most widely used and successful methods in statistics.

Multivariate linear regression models are a very solid tool for prediction, and generally an excellent starting point in analysis.

$$ Y_i = \beta_1 X_{1i} + \beta_2 X_{2i} + \ldots + \beta_{p} X_{pi} + \epsilon_{i} = \sum_{k=1}^p X_{ik} \beta_j + \epsilon_{i} $$

Note that X_1i ($X_{1i}$) is typically equal to 1, so that we can use B1 to provide our Y intercept.

Least squares (and hence ML estimates under iid Gaussianity of the errors) minimizes $$ \sum_{i=1}^n \left(Y_i - \sum_{k=1}^p X_{ki} \beta_j\right)^2 $$

Note, the important linearity is linearity in the coefficients. Thus $$ Y_i = \beta_1 X_{1i}^2 + \beta_2 X_{2i}^2 + \ldots + \beta_{p} X_{pi}^2 + \epsilon_{i} $$ is still a linear model. (We've just squared the elements of the predictor variables.)



```r
library(UsingR)
library(dplyr)
library(ggplot2)
```


