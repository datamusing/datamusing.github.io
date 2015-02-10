---
layout: post
title: "In Search of the Most Informative Review"
date: 2015-01-18 14:06:09 -0800
comments: true
published: true
categories: 
---


The goal here is to find from the corpus of reviews of a restaurant, the one review that is most informative. 
<!--more-->
##How do we define informative? 

For free form text, it is usually very difficult to come up with a metric for how informative a review is, just based on discrete signatures like tags. Here is where topics come in most handy. We illustrate how we can do this with SF Bay Area reviews. 

## The recipe, and a worked example 

First, I took the __4 or 5 star__ reviews from the last year from all SF Bay area restaurants, and learnt 100 topics from them. 

Then, for each restaurant, I find the most relevant topics that apply to that restaurant. I am glossing over some details here, but basically I have a mathematical formulations that finds the topics that have the highest proportion in that restaurant, and are  also distinctive of the restaurant at hand. So, in the end we have a distribution of topics for that restaurant. 

Let take [La Mar Cabicheria Peruana](http://www.opentable.com/la-mar-cebicheria-peruana) near Embarcadero as an example. I have been there several times, and I know it has excellent ambiance, amazing views of the bay, a great seafood menu, and great service.  

Let us see how topics do in summarizing this information. 
Here is the topic distribution for La Mar:

![](https://dl.dropboxusercontent.com/u/18915298/blog/featuredReviews/LaMarTopics.png)

You can eyeball the  top 5 topics as topics 20, 60, 66, 33 and 79. 
And here is how they look:

![](https://dl.dropboxusercontent.com/u/18915298/blog/featuredReviews/LaMarTopTopics.png)

They seem to be bang on! From the distribution, we see that there are various other topics with significant weight for this restaurant. 

So, the most informative review will be the one that has a topic distribution that is most closely aligned with the above reference topic distribution. 

Lets pick an arbitrary review first. It is a 5-star review and it says:

>'After living in Lima for the past 3 1/2 years, it is good to have this to get our Cebiche and Pisco fix!!'

It's topic distribution looks like:

![](https://dl.dropboxusercontent.com/u/18915298/blog/featuredReviews/LaMarLowInfoReviewDist.png)

It is very different from the reference topic distribution, and has weight only on one topic, the Topic 62 that looks like:

![](https://dl.dropboxusercontent.com/u/18915298/blog/featuredReviews/topic_62.png)

As such, this review is almost of no use to us. 

To find the best review, we look at the topic distribution of each review, and find its distance from the reference topic distribution (this is done with what is known as a cosine distance). Then we can rank all the reviews by increasing distance from the reference distribution.  

If I just pick the top review without any date restriction this is what I find: 

> I discovered this restaurant after returning from a trip to Peru this year.  The menu is extensive, creative, and authentic. On arrival, we were presented with complimentary house-made potato, sweet potato and plantain chips accompanied by wonderful aji (pepper) flavored dips.  The halibut ceviche was out of this world!  The fish was so fresh, citrus bright, and the presentation gorgeous.  Our waiter was very helpful and knowledgeable, guiding us through the menu, which assured us of a terrific meal and dining experience.  Other pluses: easy walk from the Embarcadero BART station, close to the Ferry Building, and nice view of the Bay from the patio tables.  I have been back twice and plan to dine at La Mar again soon!  --- Dined on 2014-11-12

This review has a topic distribution that looks very close to the reference distribution.

![](https://dl.dropboxusercontent.com/u/18915298/blog/featuredReviews/LaMarHighInfoReviewDist.png)

So, we have found this is a beautiful informative review. It touches upon most of the salient aspects of the restaurants. 

## Applying business rules 

One we have all the reviews sorted by information content, it is very easy to apply business rules. We can say, for example, we want the featured  review to be no more than 90 days old. So we can just go down the list and find the top review that meets this criterion:

This gives us:

> Took my company to LaMar for a holiday celebration and it exceeded our expectations. Great view, lively crowd, and unique menu. We shared many plates and they all hit the mark. Finished with five different desserts...all excellent. Can't wait to go back and explore more items on the menu!  
> Dined on   --- 2014-12-10

that is still an excellent review for featuring! 


## TO DO: Finding the most relevant snippet from the featured review

So, now that we have identified the review to be featured, we need to find the snippet that we want to highlight. Right now, we use an excerpt from the beginning of the review, but there are many possibilities here. This needs to be decided at a business rule level. 



