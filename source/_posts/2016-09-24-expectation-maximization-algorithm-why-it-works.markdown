---
layout: post
title: "Expectation Maximization Algorithm: Why it works"
date: 2016-09-24 22:43:36 -0700
comments: true
categories: 
keywords: 
description: 
---
Lately, inspired by a series of lectures by my friend and colleague Ehtsham Elahi at Netflix, I have been reading up on mixture models and the expectation maximization (EM) algorithm. While most expositions on the EM algoritm motivate it by using Jensen's inequality while exploiting the convex nature of the natural logarithm, I found the approach rather unsatisfying. While it is understandable that logarithms make computations easier by converting products into sums, in the explanations of EM that use Jensen's inequality the logarithm seems to be rather forcefully introduced to invoke the inequality. 

Luckily, Bishop (2006) presents an extremely solid motivation for using the logarithm by introducing the KL divergenece into the picture and simultanously also proving why the method actually converges.  