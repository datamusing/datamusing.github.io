---
layout: post
title: "On a mission, in the Mission"
date: 2014-08-02 10:17:34 -0700
comments: true
categories: 
---
###_or, how I leveraged machine learning to find must try dishes in my neighborhood!_


SAN FRANCISCO, CA --- Whether its the yummy _focaccia de recco_ at __Farina__, the scrumptious _steak tartare_ at __Bar Tartine__, the _chicken liver mousse_ at Range, or the absolutely bulletproof _sesame fried chicken_ at __Foreign Cinema__ --- each fine dining restaurant in the Mission District of San Francisco seems to have something mouthwateringly unique to offer.

{%img center https://dl.dropboxusercontent.com/u/18915298/blog/mission_food/bar_tartine.jpg %}

The Mission is my neighborhood.  I endearingly call it  the "SF Foodie Central", not just because some of the best fine dinning restaurants  happen to be here, but also because smaller, fiercely independent purveyors of fine delicacies like _Craftsman & Wolves_, _Dandelion Chocolate_, _Tartine Bakery_, _Bi-Rite Creamery_,  _Four Barrel Coffee_, and _Ritual Roasters_  keep the food scene vibrant and at its delicious best! And then, there is the Mission Mexican food, which is hands down the best in the city. 
  <!--more--> 
  
So it was quite predictable that when analyzing [OpenTable](http://www.opentable.com) diner reviews for a data science concept project, I chose to go after the restaurants in the Mission District first. The result of this analysis is a list of _the most talked about dishes_ in the restaurants. 

The impatient readers who do not want the algorithm to stand between them and the dishes can scroll down straight to the bottom of the page where all will be revealed.  The inquisitive ones please read on. 

## An overview of the algorithm 

Before I inundate you with names and pictures of those amazing dishes, let me get through the crux of the algorithm as quickly as possible. 
{%img right https://dl.dropboxusercontent.com/u/18915298/blog/mission_food/brunch.png 400 400 %}
The first insight I wanted glean from the huge amount of reviews we have was the set of  main themes, or _topics_ that people are talking about. That is done with a method called _topic analysis_, for which I used non-negative matrix factorization (NMF) as the main technique (I will write more about the nuances of the method in a future post).  A topic, is essentially a coherent collection of words - or a distribution of over words in the vocabulary. The top words in the __brunch__ topic, e.g. may look like the one on the right. It is almost magical to see how words like  _brunch, sunday, eggs, benedict, omelette, poached, mimosa, hash, brown_ and so forth come together into a tightly thematic topic that is readily interpretable. Topic analysis gave me topics for food, drinks, ambiance, service, and so on. For the menu item hunt that was the main focus of this post,  I concentrated on all the food topics. Note that the way topic analysis works, we obtain as a free byproduct the weight of the topics in each review, and by extension, the weight of each topic for each restaurant; e.g. the seafood topic will have very little weight in a restaurant that serves only meat dishes. Once I had that information, I could then look for the most frequently occurring words, bigrams, and trigrams within the context of the topics that peak at that restaurant, and quickly identify the most mentioned food items. There is some secret sauce that helps me disambiguate different ways reviewers talk about the same dish (e.g., someone says "cellophane noodles", someone else says "cellophane noodles with crab", yet another reviewer says "the crab noodles", but they are all referring to the dish "cellophane noodles with dungeness crab"). 
In the end, you just count up the mappings to the dish item, rank them by number of mentions, and voila, you have the most mentioned dishes! 

And now lets turn to the results! 

##The goods, delivered

Although for each restaurant in the Mission, I could surface multiple dishes, I decided to only show the top (as in most mentioned) dish per restaurant. Note that this is very beta, and we can do so much more in terms of actually figuring out if the dish is being talked about in a positive manner and then count it in, etc. Those will come in due course! Meanwhile, here are the results (_scroll to center the iframe on your screen and scroll within the frame from one restaurant to the next_).  ENJOY! 

<iframe width='100%' height='768' frameborder='0' src='http://bl.ocks.org/anonymous/raw/dde021cbd234a015c111' allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>

 