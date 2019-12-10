---
layout: post
title:      "Hypothesis Test Around the Mean"
date:       2019-12-10 01:01:43 -0500
permalink:  hypothesis_test_around_the_mean
---


Before our last instructor left, she left us with some good tips to keep in mind for some beginners level statistics. I figured I would share this for anyone getting started with comparing data samples around an average or mean statistic. 

The first question you should ask is, **"How many do I have samples?"**.

Below I have compiled a list of tests and assumptions, given the number of samples you have. 


**One Sample Tests**

When dealing with one sample tests, you have to as if the variance is known or unknown.
* If variane is **known**, the suggested test is a **One Sample Z-Test**.
* If variane is **unknown**, the suggested test is a **One Sample T-Test**.


**Two Sample Tests**

When dealing with two sample tests, you have to first ask if the samples are paired or unpaired. Second, you have to determine if your samples are normally distributed with an unkonwn variance, or non-normally distributed with known variance. I can get a little complicated at first, but my goal is to help any beginners. 

*Paired:*
* If we have **paired** samples with a **normal** distributions and **unknown** variance, the suggested test is a **Paired T-Test**.
* If we have **paired** samples with **non-normal** distributions and **known** variance, the suggested test is a **Wilcoxon Signed Rank Test**.

*Unpaired:*
* If we have **unpaired** samples with a **normal** distributions and **unknown** variance, the suggested test is a **Two-Sample T-Test**
* If we have **unpaired** samples with a **non-normal** distributions and **known** variance, the suggested test is a **Mann-Whitney Test**


**Two or More Sample Tests**

In some cases, we will have more than two samples that correspond to different levels of one or two variable(s). First, determine if you have one or two variables? Next, determine if your samples are normally distributed with an unknown variance. If you have two, variables, you have to determine if cells are replicated in your sample. 

*One variable:*
* If you have **one variable** where samples correspond to different levels, **normal** distribution with **unknown** variance, the suggested test is a **One-way ANOVA**.
* If you have **one variable** where samples correspond to different levels, **non-normal** distribution with **known** variance, the suggested test is a **Kruskal Wallis Test**.

*Two variables:*
* If you have **two variables** where samples correspond to different levels, and **replicated** cells, the suggested test is a **Two-Way ANOVA**.
* If you have **two variables** where samples correspond to different levels, and cells are  **not replicated** , samples have **normal** distribution with **unknown** variance, the suggested test is a **Randomized Block Design**
* If you have **two variables** where samples correspond to different levels, and cells are  **not replicated** , samples have **non-normal** distribution with **known** variance, the suggested test is a **Friedman's Test **.


This was a brief helper post on Statistical Testing. I hope to provide more guidance if there is any interest. Feel free to drop a line, follow my blog, and stay in touch. 

