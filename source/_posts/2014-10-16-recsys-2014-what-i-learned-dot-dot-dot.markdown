---
layout: post
title: "RecSys 2014: Key Takeaways for Me..."
date: 2014-10-16 08:23:01 -0700
comments: true
published: false
categories:  machine learning, conferences, recommendation systems 

---

The weeklong  RecSys 2014 conference that took place from October 6-10 at the calm quarters of Foster City (the next one is in Vienna by the way, a slightly more exciting venue), was tiring, but left me invigorated with so many new ideas. Here are some highlights of the conference, with a very personal bias toward presentations that made a direct connect to the problems I am working on currently. 

I felt that the overarching theme of the conference was moving beyond just rating or interactions as signals for recommendation systems,  and moving toward a more unified approach that included  __content__ ,  __context__,   __feedback__, and __human input__  while making recommendations. 


Xavier Amatrarian (@xamat) of NetFlix gave an amazing overview of the recommendation space, which sort of set the tone for the rest of the conference.

## Xavier Amatrarian 

* Rating is being used as a explanation  (based on your ... )
* Social explanation 
* Rating -> Ranking -> Page Optimization -> Context-aware recommendations 


## Clustering 
New approach: Non-parametric Bayesian (Chinese restaurant) - Alex Smola talk 	KDD 

##Content Based 
"Even a Few Ratings ... " [paper](http://dl.acm.org/citation.cfm?id=1639731). 

## Hybrid Approach way better than just content or collab. 
[This paper](http://www.cs.northwestern.edu/~pardo/courses/mmml/papers/collaborative_filtering/a_framework_for_content_based_demographic_filtering_AIR99.pdf) talks about hybrid methods when data is sparse. 


##Ranking metrics:
* NDCG 
* MRR 
* FCP 

Domain/problem specific. 

* Pointwise 
* Pairwise  
* Listwise:
	 * LambdaMart 
	 * CLiMF or TFMAP 
	 * SVM-MAP 

SimRank  (Jeh & Widom 2002) 

Similarity Ensemble 

### Deep Learning 
Recurrent Network 


### Social and Trust-Based recommenders 
Friendships can be fed into collaborative filtering (user-user similarity).

Demographic methods

Page Composition 

[Paper](http://www.cs.cmu.edu/~amahmed/papers/SVCM_WSDM12.pdf)
 

###Tensor/Factorizations 



