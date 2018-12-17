---
layout: single
title:  "Principal Component Analysis"
date:   2018-10-10 21:30:00 -0700
<!-- tags: PCA feature-selection -->
tags: feature-selection PCA dimensionality-reduction
---
<script type="text/javascript" src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>
[Complete Notebook with a small example of Fisher's Linear Discriminant included.](https://github.com/RishabhPatil/FundamentalsOfStatisticalLearning/blob/master/Assignment%202/PCA%20and%20Fisher's.ipynb)

PCA stands for Principal Component Analysis, or in simpler words: picking out the features which best represent the data.<br>
This is done by finding the "directions" (or features) along which the data has the most variance.<br>
[We will use the terms 'feature' and 'direction' interchangebly through this exercise.]<br>
After PCA each direction is perpendicular to the other directions or which means that extracted feature is independent of the rest.<br>
<br>
Why maximize variance?<br>
Intuitively we can say that if the value of a feature differs a lot for all data points, it is easier to identify them.<br>
Hence we try to maximize the distance from the center or the mean (for each feature individually).<br>
<br>
PCA doesnt reduce the number of features.<br>
For a set of points in a D-dimensional space, PCA will return $$ \text{D} $$ new dimensions which act as the new axes.<br>
We can choose to take the best features from these.

<figure>
    <a href="/assets/images/image-filename-1-large.jpg"><img src="/assets/img/GaussianScatterPCA.svg"></a>
    <figcaption><b>The vectors represent the directions with the most variance, these act as the best features. When dealing with higher dimensional data, we might have to remove some of the features after PCA, to reduce computational cost.</b><a href="https://en.wikipedia.org/wiki/Principal_component_analysis#/media/File:GaussianScatterPCA.svg"><br>Image taken from wikipedia</a>, credit to original author <a href="https://commons.wikimedia.org/wiki/User:Nicoguaro">Nicoguaro</a>. License: <a href='https://creativecommons.org/licenses/by/4.0'>CC BY 4.0</a></figcaption>
</figure>

Sorting (descending) the variance for these directions, will give us the best to worst features.<br>
It important to remember that some amount of information is lost when we remove features.<br>
This is why we remove the features with the smallest amount of variance.<br>
We choose the top k features, such that loss is minimized. We'll see later how percentage loss is calculated.<br>
<br>
Finally we take components of our original data onto the space defined by the k dimensions.<br>
And you are left with the best possible representation of the data in k dimensions.<br><br>
Now let's take a look at the math and its implementation in python.<br>

<h3> Maximize Variance along one direction </h3>
We take the component of $$ \text{X} $$ along a direction such that variance of resulting data is maximized.<br>
Lets call the resulting vector $$ \text{Y} $$.

$$ \text{Y}_{k} = \text{e}_{1}^T \text{X}_{k} $$ <br>
$$ \bar{\text{Y}} = \frac{1}{n}\sum_{k=1}^{n} \text{Y}_{k} = \text{e}_{1}^T \bar{\text{X}} $$ <br>
$$ \text{Variance} = \frac{1}{n} \sum_{k=1}^{n} (\text{Y}_{k} - \bar{\text{Y}})^2 $$ <br>

Which can be converted into:

$$ \text{Variance} = \text{e}_{1}^T \frac{1}{n} \sum_{k=1}^{n} (\text{X}_{k} - \bar{\text{X}})(\text{X}_{k} - \bar{\text{X}})^T \text{e}_{1} $$
$$ \text{Variance} = \text{e}_{1}^T \text{C} \text{e}_{1} $$

Where $$ \text{C} $$ is the covariance matrix.

Now we need to find $$\text{e}_{1}$$ which maximizes variance, with the constraint:

$$ \text{e}_{1}^T \text{e}_{1} = 1$$

Using the Lagrangian Multiplier Method:

$$ \text{L} = \text{e}_{1}^T C \text{e}_{1} - \lambda (\text{e}_{1}^T \text{e}_{1} - 1)$$

Taking derivative wrt $$ \text{e}_{1} $$

$$ \text{ } \frac{\text{dL}}{\text{de}_{1}} = C \text{e}_{1} - \lambda \text{e}_{1} = 0 $$

Therefore,<br>

$$ C \text{e}_{1} = \lambda \text{e}_{1} $$

Using $$\text{e}_{1}$$ we can solve for the next direction vector $$\text{e}_{2}$$:
Since the two directions should be perpendicular, we have two constraints:

$$ \text{e}_{2}^T \text{e}_{2} = 1$$
$$ \text{e}_{2}^T \text{e}_{1} = 0$$

Using the Lagrangian Multiplier Method:

$$ \text{L} = \text{e}_{2}^T C \text{e}_{2} - \lambda_{2} (\text{e}_{2}^T \text{e}_{2} - 1) - \lambda_{3} (\text{e}_{2}^T \text{e}_{1})$$

Which gives us,

$$ C \text{e}_{2} = \lambda_{2} \text{e}_{2} $$

Similarly we can solve for D eigen vectors $$\text{e}_{1}, \text{e}_{2}, \text{e}_{3} ... \text{e}_{D}$$
such that $$ \lambda_{1} > \lambda_{2} > \lambda_{3} ...  > \lambda_{D} $$

The higher the value of $$\lambda$$ for a direction, the higher is the variance of data in that direction.

$$
\mathbf{E} = \begin{vmatrix} 
| & | & | & ... & | \\
\text{e}_{1} & \text{e}_{2} & \text{e}_{3} & ... & \text{e}_{D} \\
| & | & | & ... & | \\
\end{vmatrix}
$$

$$\mathbf{E}$$ is a D by D matrix.

To get the PCA result of our data $$ \text{X} $$ we do:

$$ \text{X}^{'} = \text{E}^{T} \text{X} $$

To take the k most important, we simply take the k directions sorted (descending) by $$\lambda$$. Hence the directions with low variance are ignored.

$$
\mathbf{E}_{k} = \begin{vmatrix} 
| & | & | & ... & | \\
\text{e}_{1} & \text{e}_{2} & \text{e}_{3} & ... & \text{e}_{k} \\
| & | & | & ... & | \\
\end{vmatrix}
$$

$$\mathbf{E}_{k}$$ is a D by k matrix.

PCA in k dimensions:

$$ \text{X}^{'} = \text{E}_{k}^{T} \text{X} $$

Although this causes loss of information, this is the least possible loss.

$$ \text{Fraction of variance retained} = \frac{\sum_{i=1}^{k} \lambda_i}{\sum_{i=1}^{D} \lambda_i} $$