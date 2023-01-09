---
title: "Reflections and learnings on lived experience coproduction for data-driven research in mental health"
date: 2022-12-05T11:53:46Z
draft: false
author: "Alexandra Pike"
categories:
- lived experience
tags:
- secondary data
---

Lived experience engagement is critical for ensuring that research in applied areas (such as mental health) follows the principle of ‘nothing about us, without us’ - a policy that, it turns out, [has a long history in European political tradition](https://en.wikipedia.org/wiki/Nothing_about_us_without_us). However, lived experience engagement isn’t always easy, and we would argue that it can be a challenge in data-driven research especially.  

Why do we think this? Because ensuring that lived experience input is meaningful, rather than tokenistic, requires: 
That input is given at multiple stages of a project, from conception to dissemination
That input guides the questions and methods used, rather than just the wording of those questions and methods
That those with lived experience and those who work as researchers can understand each other to make truly joint decisions

![an abstract, impressionist watercolour-style image of a group of people in bright colours (yellows, pinks and oranges) are sat around a table](/le_image.png)

# Input at multiple stages
The first of these points is rarely met in data-driven research, because much of the data has already been obtained. In an ideal world, all the large cohort datasets that we use would be based around data that is considered important and acceptable to collect by people with lived experience - however, as the data-driven researcher, this step is often out of your control. From our lived experience sessions we did note that there were some significant gaps in these datasets, that our panel thought were important to young people’s mental health: these included how people felt about the treatment they’d received, the extent to which treatment was ‘cookie-cutter’ or sufficiently personalised/specialised, and whether it was delivered consistently by the same practitioner. One thing researchers can do is try to think about how the variables we do have might allow us to indirectly measure some of the issues that people with lived experience tell us are important.

# Input guides questions and methods
Relating to the second point, the type of machine learning approach we were using is somewhat hypothesis-free. We wanted to use the entirety of datasets to build predictive models - rather than using lived experience input to guide the selection of variables, or define whether a particular construct is well captured by the data. Our solution to this was to use an existing classification scheme for datasets, based on a suggestion by another data prize team, and ask our lived experience panel for their thoughts on which of these categories of data were more important. Our challenge now is to combine these with our algorithms in a data-driven way - our initial thought is to set priors over these variables, in essence a Bayesian way of letting our prior knowledge (derived from lived experience) inform the eventual best predictive model. However, translating what is often a complex set of thoughts and opinions into a simple number is not straightforward.

# Understanding each other
Finally, the third point - we spend much of our time discussing hyperparameters, operating systems, and how to clean data. These conversations require highly specialised knowledge, and requiring lived experience experts to have this technical background would be exclusionary and not allow for a fully diverse group of individuals with lived experience (and there’s also something to be said about how, given that lived experience is becoming rightly more centred in research, those with technical backgrounds could become significantly overworked). Our solution has been to keep these technical discussions separate, and focus on incorporating lived experience input where it will be of most value, but of course this then does not meet the definition of true coproduction. One partial solution would be to provide training in more technical areas as a necessary part of the lived experience component of a project: however, this is likely to be resource-intensive and not appealing to all lived experience experts, particularly if they are taking on lived experience roles in conjunction with other work or caring responsibilities. We don’t have a good solution to this, and would love to converse more with others who have thoughts! 

# Practical considerations
Other reflections and challenges we have are more about the practical considerations of incorporating lived experience input. Initially, we planned to run our lived experience sessions in person - however, we found that it was possible to recruit a more diverse group (as facilitated by the wonderful [Involvement@York](https://www.york.ac.uk/research/themes/health-and-wellbeing/involvement@york/) team) for online sessions. We also had expert input from a lived experience facilitator, and would definitely recommend this to anyone else who is considering this - who was able to advise us about the most appropriate mechanisms for payment, and ensure that sessions were planned in a way that allowed conversation to flow. We asked our lived experience panel about how they preferred to work, which was very informative - they preferred more frequent shorter sessions, often over a lunchtime to fit within the working day; and preferred some initial polls when discussing data variables to get the conversation started. On that note, we initially set the polls up using zoom polls, and they did not work very well and weren’t always visible to everyone - our panel suggested [Jamboard](https://jamboard.google.com/?pli=1) for future sessions. We also considered using [Mentimeter](https://www.mentimeter.com/), but as this relies on having a phone to respond, and some of the panel joined via their phone, we decided this might not be the best solution. 

# Conclusion
This foray into public engagement for data-driven research has been fascinating, and has thrown up plenty of questions for us to consider - both broad ones about how large cohort datasets are collected, and what information is and isn’t included, and practical questions about the best online polling options. Others who are participating in this data prize have also been considering these questions - you can see another blog about similar issues here: https://harmonydata.org/ppie-for-secondary-data-analysis/. 