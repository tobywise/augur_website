---
title: "Implementing Bayesian prediction models with big datasets"
date: 2023-02-02T15:06:00Z
draft: false
author: "Toby Wise"
categories:
- modelling
tags:
- modelling
- statistics
- machine learning
---

Our analysis approach uses [Bayesian modelling]({{< ref "/posts/modelling_uncertainty" >}}) to provide uncertainty estimates for our predictions. This is central to our goal of informing users not just of how likely someone is to develop mental health problems, seek treatment, or respond to treatment, but to also convey a sense of confidence in these predictions.

However, these models can be complex and computationally expensive to fit. This is particularly true when we consider the large datasets we are working with. For example, [ALSPAC](https://www.bristol.ac.uk/alspac/) includes approximately 7,000 participants and over 10,000 variables. The gold standard approach for estimating the parameters of Bayesian models is using [Markov Chain Monte Carlo (MCMC)](https://en.wikipedia.org/wiki/Markov_chain_Monte_Carlo) methods, which becomes slow for large datasets and complex models. This problem is further compounded by the fact that this process cannot be fully parallelised, as the results of each iteration depend on the previous iteration.

This is post is going to be a **technical** overview of how we've addressed this issue.

# Dimensionality reduction

One straightforward way to reduce the computational cost of fitting a model is to reduce the number of parameters. This is known as [dimensionality reduction](https://en.wikipedia.org/wiki/Dimensionality_reduction). We can do this by reducing the number of variables we are modelling. This could be achieved through manual selection of variables, but a more principled approach is to use a method that looks for common "latent" dimensions in the data based on covariance between variables. This can be done with [principal component analysis (PCA)](https://en.wikipedia.org/wiki/Principal_component_analysis), but we have chosen to use [exploratory factor analysis (EFA)](https://en.wikipedia.org/wiki/Exploratory_factor_analysis) instead as it can provide more interpretable dimensions (this isn't central to our approach, but can be useful).

Our analyses indicate that a very small number of factors (as few as 5) can allow for accurate prediction, which immediately means the number of parameters in our model can be drastically reduced (from thousands to a handful) with no loss (or even a gain) in predictive accuracy.

## Bayesian exploratory factor analysis

However, one issue is that EFA is [difficult](https://rfarouni.github.io/assets/projects/BayesianFactorAnalysis/BayesianFactorAnalysis.html) to implement in a Bayesian framework, particularly with large numbers of variables (we tried... and failed). So how do we deal with this?

Given we can't estimate uncertainty in the EFA solution itself, the best thing we can do is estimate uncertainly in individuals' factor _scores_ given the input variables. We can also use this approach to identify the variables that contribute most to the factors, allowing us to disgard irrelevant variables. This is a much simpler problem, that can be achieved using a simple procedure:

1. Fit the EFA model and extract factor scores for each person
2. Fit a standard Lasso regression model to predict the factor scores from the input variables, using a high degree of regularisation. We then disregard variables whose coefficients are zero in this model.
2. Fit a Bayesian linear regression model to predict the factor scores from the retained input variables

This is similar to an approach I've [used](https://www.nature.com/articles/s41467-020-17977-w) (and [validated](https://psyarxiv.com/q83sh)) before, and provides a convenient way to estimate uncertainty in factor scores using a limited set of input variables.

We can then feed the predicted factor scores from this regression into our final regression model, which is much simpler and faster to fit than it would be otherwise.

![an illustration of the Augur prediction pipeline, showing how exploratory factor analysis is used in conjunction with two stages of linear regression to predict outcomes based on a reduced set of variables](/prediction_pipeline.svg)

# Using hardware acceleration

## Faster models using JAX

We have also taken advantage of some of the latest advances in machine learning to speed up our models. In particular, we have used [JAX](https://jax.readthedocs.io/en/latest/), a package that enables Python functions written with [NumPy](https://numpy.org/) syntax to be compiled to low-level code using [XLA](https://www.tensorflow.org/xla). This allows us to use the same code to fit models on a CPU or GPU, and can provide significant speedups. The package we are using for fitting Bayesian models, [NumPyro](https://num.pyro.ai/en/stable/), is also built on top of JAX, meaning the whole pipeline can be coded in a single, fast framework.

As an example of this, we can take a look at our implementation of [Iterative Imputation]({{< ref "/posts/modelling_uncertainty#step-2-bayesian-iterative-imputation" >}}). This can be implemented using a simple `for` loop:

```python
for i in X.shape[1]:
    model = LinearRegression()
    model.fit(X[:, np.arange(n_variables) != i], X[:, i])
```

But this kind of looping can be slow in Python. We can instead use JAX to vectorise this operation automatically, which is much faster:

```python
def impute_single(i):
    model = LinearRegression()
    model.fit(X[:, np.arange(n_variables) != i], X[:, i])
    return model.predict(X[:, np.arange(n_variables) != i])

impute_single_vmapped = jax.vmap(impute_single)
```

This immediately becomes a lot faster, and can be run even faster with the right hardware.

## Using GPUs

The other advantage of using JAX and NumPyro is that they enable us to run our models flexibly on either CPU or GPU platforms extremely easily. While GPU optimisation is something that's typically associated with deep neural networks, it can also be useful for regression models with many parameters. This is because the matrix operations involved in fitting these models can be parallelised, which can be particularly useful for large datasets. Additionally, the vectorised operations enabled through JAX's `vmap` function can also be mass-parallelised on a GPU.

This is all extremely straightforward with these packages. JAX will automatically detect whether a GPU is available, and if so will use it. NumPyro just requires a single setting:

```python
import numpyro
numpyro.set_platform("gpu")
```

Otherwise, all of the code is the same. This has made it very straightforward to develop and test on multiple platforms with minimal need for rewriting our code.

The result of this is a fairly dramatic speedup: Running an example model on the NSPN dataset (not the largest we are using) takes approximately 80 hours on a CPU, but only 2.5 hours on a GPU. This is a 32x speedup, which is a significant improvement and turns something that would have been infeasible into a practical solution.

![a diagram showing the time taken to fit a model on CPU (80 hours) versus GPU (2.5 hours)](/hardware_comparison.png)

Critically, we only need this speedup for the fitting procedure - making predictions requires far less computation, allowing us to train our models on GPU and then deploy them on a CPU platform.

# Conclusion

A big challenge for us has been the use of complex Bayesian models in large datasets. We have been able to overcome this by using dimensionality reduction to reduce the number of parameters in our models, and by using hardware acceleration to speed up the fitting procedure. This has allowed us to fit models on large datasets that would otherwise be infeasible, and we are now able to use Bayesian models to make predictions on a much larger scale than we were previously able to.

We will be making our pipelines openly available in the near future, and we hope this will enable other researchers to apply our methods to their own datasets. 