---
layout: post
title: "Learning by example: Bayesian Sets"
date: 2014-09-11 18:59:51 -0700
comments: true
published: false
categories: 
---

Bayes Sets is an algorithm that learns by example - so to speak. The main idea is as follows: In a large collection of items $D$, there usually exists smaller subsets 
of similar items into which items cluster. Suppose $D_c$ is one such subset. Given a small number of examples from $D_c$, the problem is to complete the set $D_c$. Using 
Bayesian language, given a small number of items $\{x\}\in D_c$ and any item $x'$ from $D$, how likely is it that $x' \in D_c$ i.e. compute $p(x'| D_c)/p(x')$. We can compute
this score for every item in $D$ and rank them in order of decreasing score. 

To give an example, given the set of all movies on IMDB, cult horror movies will be such a subset. However, we may not have the  genre information at all, yet this 
subset will be distinctive in that the horror movie buffs will all rate them highly.  The problem is given, say, only two examples of the set $D_c$ (say, the Texas Chainsaw Massacre, and The Evil Dead), how to algothimically complete the set $D_c$ of all cult horror movies. This is what Bayes Set algorithm attempts to achieve. 


##Preparing the data

The Bayes Sets algorithm expects the data in a binarized matrix of size $n_{items}$ $\times n_{\text{features}}$ where each entry is either one or zero. To continue with our movie  example, we can represent the data as a $n_{items} \times n_{users}$ matrix where are entries are 1.0 if the movie has been rated higher than 3 by that user, and zero 
otherwise. This greatly reduces the memory usage, as the matrix can be stored as a sparse matrix. 

For this example, I have generated a data file in a JSON format, where the file has the structure: 

`{'movie_0':['user_id_11', 'user_id_23', 'user_id_45', ... ], 'movie_1':['user_123', 'user_34', 'user_id_45', ...], ...}` 

for each  movie, the features are the user_id s that have rated the movie higher than 3. 
