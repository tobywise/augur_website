---
title: "Embracing uncertainty in predicting mental health outcomes"
date: 2023-02-02T15:06:00Z
draft: false
author: "Toby Wise"
categories:
- modelling
tags:
- modelling
- uncertainty
- statistics
- machine learning
---

The primary goal of Augur is to predict future symptoms of common mental health problems, alongside and treatment seeking an outcome. In an ideal world, we would hope to be able to do so with absolute certainty, but this is rarely possible; people are complex and they live in changeable and complicated environments. As a result, the best thing we can do is to be as transparent as possible about this uncertainty, and be sure that we communicate it to our users.

Such an approach presents a challenge when building models to predict mental health outcomes. This post will be a fairly technical overview of how we've sought to address this challenge in Augur.

![an illustration of a prediction from the Augur app, showing a high likelihood of depression or anxiety with a high level of confidence](/augur_prediction.png)

# Types and sources of uncertainty

Uncertainty in our predictions comes in different forms, and has different sources. 

## Aleatoric uncertainty

We can never be absolutely certain that someone will develop a mood or anxiety disorder. However, we can try to estimate the likelihood of this happening. That is, we can estimate the probability that this will happen to a given individual, and provide the user with this value as a percentage. So, rather than saying _"this person will develop depression"_ we can say _"this person has an 85% chance of developing depression"_. The uncertainty here is relatively simple in nature, and hopefully easy to understand.

### Incorporating aleatoric uncertainty into our predictions

To produce such predictions, we had to choose a modelling approach that is able to provide probabilities as well as binary predictions. Thankfully, many classes of models are able to do this. In fact, the most commonly used Python package for machine learning, [scikit-learn](https://scikit-learn.org/stable/), provides a number of models that can do this with a simple interface:

```python
from sklearn.linear_model import LogisticRegression

model = LogisticRegression()
model.fit(X_train, y_train)
y_pred = model.predict_proba(X_test)
```

The most simple, and often most powerful, model for this purpose is logistic regression. This is a linear model that is trained to predict the probability of a binary outcome, and therefore naturally provides probability estimates for its predictions.

## Epistemic uncertainty

This form of uncertainty is a little more complex. It represents the uncertainty that is inherent in our model, perhaps because we've not trained it on enough data, or we've used noisy data or data with missing values. It can also be caused by the fact that our model is not complex enough to capture the underlying relationships in the data. We may be able to say that a person has an 85% chance of developing depression, but we might not be very confident in this percentage estimate. We could train a model on only 10 people's data if we wanted, and we could ask it to produce a probabilistic prediction - however we would not be very confident in the results. This is epistemic uncertainty.

Ideally, we want to be able to provide our users with an idea of _both_ these kinds of uncertainty. We want to be able to say how likely someone is to develop depression, _and_ how confident we are in that prediction. For this reason, we've turned to **Bayesian modelling**. By modelling relevant parameters as probability distributions, we can easily provide estimates of both the probability of an outcome, and the uncertainty in that probability.

In our case, we are modelling uncertainty that arises from multiple sources:

### Missing data

One major problem in developing any kind of predictive algorithm is incomplete datasets. This can arise either in the training dataset, or the test dataset: perhaps we have a perfectly complete training dataset, but the user wants to use the trained models even though they don't have all of the variables used to train the model. This produces uncertainty in our predictions, because we don't know what the missing values are.

Our approach has been to use imputation to determine these missing values. This is a commonly used approach, but in our models we have used Bayesian modelling to ensure that we are able to incorporate uncertainty in the imputed values into our predictions.

This essentially involves a two step process:

#### Step 1: Initial imputation

First, we need to provide a simple guess for the missing values. Non-Bayesian approaches will typically impute the mean or median value for the variable, but this is not ideal as it doesn't incorporate uncertainty. Instead, we estimate a probability distribution over the missing values. We're using [NumPyro](https://num.pyro.ai/en/stable/) for our modelling, and it provides a simple interface for this:

```python
import numpyro
import numpy as np

def impute(
    X: np.ndarray,
    loc: float,
    scale: float,
)

    # Impute missing X data
    X_impute = numpyro.sample(
        "X_impute",
        # Standard normal prior
        dist.Normal(loc, scale).expand(  
            [int(np.isnan(X).sum())]
        ),
    )
    
    return X_impute

```

This means that in place of missing values, we have a probability distribution over the possible values. This is a simple approach, but it allows incorporation of uncertainty.

#### Step 2: Bayesian iterative imputation

Simple imputation works, but it's not the most accurate. There is most likely going to be information in the non-missing variables that can help us determine likely values for the missing ones. Someone who scores highly on a measure of depression is likely to have a higher score on a measure of anxiety, for example. We can use this information to improve our imputation using an approach called Bayesian iterative imputation (this is basically the method implemented in Scikit-Learn's `IterativeImputer` [class](https://scikit-learn.org/stable/modules/generated/sklearn.impute.IterativeImputer.html#sklearn.impute.IterativeImputer) and is related to the [MICE](https://www.jstatsoft.org/article/view/v045i03) algorithm). This is a fairly simple process:

Our simple imputation gives us a complete dataset. We can then iteratively run a regression model on this dataset, predicting values on variable `A` from variables `X`, `Y`, and `Z`:

```python
for i in X.shape[1]:

    # Fit a regression model to predict A from X, Y, and Z
    model = LinearRegression()
    model.fit(
        # All variables in X except i
        X[:, np.arange(n_variables) != i],
        # Predicting vaaible i
        X[:, i],
    )

```

If we have a variable with missing values, we can then use this model to predict the missing values:

```python
# Predict missing values for variable i
X_impute[:, i] = model.predict(
    # Based on all variables in X except i
    X[:, np.arange(n_variables) != i],
)
```

Again, we in fact use a Baysian regression model to do this. Again, this allows us to incorporate uncertainty in our predictions about the missing values as the weights in our regression models are themselves probability distributions. As a result, we combine the uncertainty in the initial simple imputed values with the uncertainty in the predicted values from the regression model, giving us a better estimate of uncertainty in the missing values.

This is important for users of Augur; we want to train the model on as much data as possible, but we want people to be able to use it and get usable predictions even if the only have access to a subset of that data. But we need to make sure that the resulting uncertainty is communicated to the user.

### Uncertainty in the model parameters

As well as uncertainty in the missing values, we also need to consider uncertainty in the model parameters. We're using a logistic regression model to predict outcomes, and as with any general linear model this predicts outcomes as a linear combination of input variables (apologies for the maths):

$$\hat{y} = \beta_0 + \beta_1 x_1 + \beta_2 x_2 + \dots + \beta_n x_n$$

Where $\hat{y}$ is the predicted outcome, $x_i$ is the $i$th input variable, $\beta_i$ is the weight for that variable, and $\beta_0$ is the intercept. The Bayesian modelling approach allows us to represent the weights ($\beta_i$) as probability distributions, rather than single values. This enables us to incorporate uncertainty in the model parameters into our predictions. This uncertainty might arise simply from noise in our dataset, or from the fact that our model inevitably will not account for every possible factor that affects the outcome.

Again, this is easy to do in NumPyro:

```python
def model(X:np.ndarray, y:np.ndarray):

    # Prior for intercept
    intercept = numpyro.sample("intercept", dist.Normal(0, 1))
    
    # Prior for weights, using a normal distribution
    weights = numpyro.sample("weights", dist.Normal(0, 1).expand([X.shape[1]]))
    
    # Predicted outcome
    y_hat = intercept + numpyro.deterministic("y_hat", X @ weights)
    
    # Likelihood
    numpyro.sample("y", dist.Bernoulli(logits=y_hat), obs=y)
```

Here, our `weights` variable is modelled using a normal distribution with mean `0` and standard deviation `1` as the prior (i.e., our expectation of where the true value will fall).

## Putting everything together

This Bayesian modelling approach enables us to incorporate multiple sources of uncertainty into our model, and ensure that our predictions are as informative as possible - both in terms of their accuracy _and_ their communication of uncertainty. Our full model is a little more complicated than the examples given here, and incorporates other sources of uncertainty, but the basic approach is the same. Ultimately, this is a little more complicated (and time-consuming) than a simple regression model, but we believe that it is worth it to ensure that our predictions are as informative, and transparent about our confidence in these predictions, as possible.