---
title: "About"
date: 2019-08-02T11:04:49+08:00
draft: false
description: "About"
images: ["/Apple-Devices-Preview.png"]

lightgallery: true

math:
  enable: true
---

![A person looking at the path ahead](/about.png)

# What is Augur?



Augur is a tool designed as part of the [Wellcome Mental Health Data Prize](https://wellcome.org/grant-funding/schemes/wellcome-mental-health-data-prize) with the goal of predicting:

1. Who develops anxiety and depression
2. Who seeks treatment for anxiety and depression
3. Who responds to treatment for anxiety and depression

We are using data from multiple sources to inform these predictions, including the [NSPN](https://nspn.org.uk/) and [ALSPAC](http://www.bristol.ac.uk/alspac/) datasets.

We want the tool to be useful for researchers, clinicians, and people who experience anxiety and depression. To make this possible, we are developing the tool to be used in three different ways:

## 1. An app-based prediction tool for individuals

![A diagram showing how the tool takes data points about an individual and turns this into a usable predictiom about their future mental health](/tool_diagrams.svg)

We will develop an app that will allow individuals to input their own data and receive predictions about their risk of developing anxiety and depression, and their likelihood of seeking treatment for anxiety and depression, and their likelihood of responding to treatment for anxiety and depression.

This tool will primarily be useful for researchers, to quickly find out which factors are most important to investigate in their own research. However we hope this will also be useful for clinicians, and for people who are interested in their own mental health.

## 2. A package for researchers who wish to generate predictions in large samples

We will develop a package that will allow researchers to generate predictions for large samples of individuals. This will be useful for researchers who wish to use the predictions in their own research, for example investigating how someone's likelihood of developing anxiety and depression relates to other factors, such as brain structure or genetics.

![A diagram showing how the tool takes data points for a sample of people and turns this into a usable predictiom about their future mental health for use in further analysis, such as linking to brain structure](/tool_diagram2.svg)

## 3. A package for researchers who wish to use our prediction approach with their own data

Our final goal is to develop a package that will allow researchers to re-train the types of predictive models we have used with their own data. This will be useful for researchers who wish to use the same approach with their own data, for example to predict the risk of developing anxiety and depression in a different population based on different measures.

![A diagram showing how the tool takes data from new measures in a sample of people and generates an algorithm that can predict risk of future risk based on these measures](/tool_diagram3.svg)