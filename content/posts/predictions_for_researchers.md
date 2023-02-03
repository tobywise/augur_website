---
title: "Building a flexible and usable tool for mental health researchers"
date: 2023-02-02T14:06:00Z
draft: false
author: "Toby Wise, Alexandra Pike"
categories:
- usability
tags:
- modelling
- uncertainty
---

The primary goal of our tool is to help mental health researchers. We want to do this in ways that are both powerful _and_ accessible, providing both flexible and extensible software packages alongside easy to use web applications.

In this post, we'll talk about our goals for supporting mental health researchers with Augur.

# 1. Immediate predictions for researchers

The first goal of Augur is to provide researchers with a tool that can make predictions about mental health outcomes, without any need for specialist skills or coding. 

While our primary hope is that this will enable researchers to determine the importance of specific variables (e.g., _if I collect data on this variable, will it help me predict mental health outcomes?_), we also hope that it will enable researchers to quickly test their own hypotheses about potential __active ingredients__ without the need for new data collection (e.g., _If someone is far away from green space, are they more likely to develop anxiety and depression?_).

Importantly, we are making this available as a web application, meaning that any researcher can easily access it without needing to install any software or write any code. We hope this will make it as widely accessible as possible.

![an illustration of a prediction from the Augur app](/web_app.png)

# 2. Algorithms that can be integrated into research workflows

The second goal of Augur is to provide researchers with software packages that can be integrated into their own research workflows, for researchers who are looking for a more flexible approach to integrating our predictions into their own work. These packages will allow researchers to make predictions about mental health outcomes using their own data, and to integrate these predictions into their own research. 

We are creating a [Python](https://www.python.org/) package that will use our pre-trained models to make predictions about outcomes for groups of individuals (e.g. a sample of participants in a study). All the researcher will need to do is collect relevant variables (for example those our model shows to be most highly predictive of outcomes) and feed them into the packages. Importantly, we also want to provide information about [uncertainty]({{< ref "/posts/modelling_uncertainty" >}}) in these predictions, so that researchers know how much to weight them in their analysis.

Ultimately, we want this to be as simple and usable as possible. For example, the package will allow predictions in a single line of code:

```python
from augur import predict
predictions = predict(data)
```

We foresee a variety of uses for this package. As well as facilitating decisions about specific variables or active ingredients, this has the potential to be integrated into a wide array of research studies to better understand factors impacting upon mental health outcomes. For example, researchers could use our package to determine how likely a sample of individuals are to develop mental health problems, and look at how this risk correlates with brain structure or genetic factors.

# 3. A package for researchers to train their own models

One of our main achievements during the Discovery phase of this project was to develop a [modelling approach]({{< ref "/posts/implementation" >}}) that allows us to predict mental health outcomes with accuracy while providing estimates of confidence in these predictions. However, our ultimate goal is for this basic approach to be used by other researchers on different types of datasets. By making this approach available as a Python package, we hope that our modelling approach can be used across a variety of different datasets, for example by researchers who wish to use a specific set of active ingredients to predict response to treatment.

Again, we want this to be as simple and usable as possible. Our package is designed so that the only work researchers need to do is formatting their data into a specific format, and then running a single line of code:

```python
from augur import train
model = train(predictors, outcome)
```
