---
layout: post
title: "Implicit Feedback and Collaborative Filtering"
date: 2015-01-07 00:15:33 -0800
comments: true
author: "Sudeep Das"
categories: ["recommendation systems"]
sharing: true
---



SAN FRANCISCO, CA --- I have been re-reading  <a href="http://dl.acm.org/citation.cfm?id=1511352" title="Implicit Feedback Paper">this classic paper</a>, and was impressed both by its clarity and simplicity. <!-- more --> 
{% blockquote %} Often the most powerful ideas are also the most elegant! {% endblockquote %}
Here, I will try to summarize the technique, with just enough math thrown in to facilitate understanding from a coding perspective. 
 $$\def\RR{\bf R}$$
$$\def\bx{\bf{x}}$$
$$\def\by{\bf{y}}$$
$$\renewcommand{\bra}[1]{\left\langle{ #1}\right\vert}$$
$$\renewcommand{\bbra}[1]{\left\langle{ #1}\right.}$$
$$\renewcommand{\ket}[1]{\left\vert{ #1}\right\rangle}$$

{%img-responsive https://dl.dropboxusercontent.com/u/18915298/blog/implicitCollab/implicit_small.png 750 %}


__Good Old Explicit__

The classic problem of collaborative filtering (CF) is the one with explicit feedback: given a set of users $$u$$ and items $$i$$ observe the user-item interaction explicitly through, say, a rating $$r_{ui}$$, and then predict the ratings for the user-item pairs that are unknown. If we have $$m$$ users and $$n$$ items, we can envision an $$m\times n$$ ratings matrix $$\bf{R}$$ such that each row corresponds to a given user and non-empty elements in that row correspond to the ratings that this user has given to 
the items. Because each user would typically rate a small proportion of the items, this matrix will be sparse. 
 
The aim of  Recommender Systems is to fill in the blanks of this matrix with predicted ratings using the information at hand: <em>matrix completion</em>. The popular method of accomplishing this is through matrix factorization whereby the ratings matrix (that can routinely be a million users by a million items) is expressed as a product of two lower dimensional matrices, a user-features matrix and a item-features matrix. Typically, one uses 50-200 features. 

Let us introduce a bit of notation. I borrow a bit of notation from quantum mechanics and write vectors as $$\vert \bx \rangle$$, such that it understood to be a column vector. This simplifies some algebraic manipulations later. The latent feature vector of user $$u$$ is denoted as: 

$$\ket{ \bx_u} \equiv \begin{bmatrix} x_u^0 \\ x_u^1 \\ ... \\x_u^{f-1} \end{bmatrix}$$

where the superscripts denote the indices of the elements. 
Its transpose is denoted by the row vector:
 
$$\bra{\bx_u} \equiv {\bx_u}^{T}\equiv \begin{bmatrix} x_u^0,  x_u^1, ..., x_u^{f-1} \end{bmatrix}  $$

so that the squared norm can be written as the inner product: $$ \bbra{\bx_u}\ket{\bx_u}$$. 
Similarly, we can write the latent feature vector for item $$i$$, as 

$$ \ket{ \by_i} \equiv \begin{bmatrix} y_i^0 \\ y_i^1 \\ ... \\y_i^{f-1} \end{bmatrix}$$

The problem is formulated in terms of minimizing an objective function defined as a sum of residuals from knows ratings:

$$\min_{x_*, y_*} \sum_{u,i; \text{ $r_{ui}$ known}}\left(r_{ui} -\bbra{\bx_u}\ket{\by_i}\right)^2 + \lambda (\bbra{\bx_u}\ket{\bx_u} + \bbra{\by_i}\ket{\by_i}  ) $$

In other words, solve for the latent factors until the squared difference between the explicit and predicted ratings is minimized. The $$\lambda$$ term adds regularization to prevent overfitting. By merit of the sparsity of the ratings matrix this problem can be efficiently solved, typically using a Stochastic Gradient Descent type algorithm. 

##Why Implicit? 

The sparsity of the ratings matrix is a good thing, but it is also its problem. In most settings, it is very hard to get a large fraction of users rate a large fraction of the items. So there might just be too little signal if we stuck to explicit ratings only. However the user item interaction can happen in the many different ways other than a rating. A user may be searching for an item (say a restaurant) on the web, or listening to/skipping a track, or clicking on links in an email campaign. and so on and so forth. Usually this type of implicit signals show the attachment of an user to an item through some sort of frequency.  These actions do not explicitly state or quantify any preference of the user for the item, but instead gives us confidence about the user's opinion. And we usually have plenty more of these signals than explicit ratings. 

Consider, for example, listening behavior to songs. When I listen to music on a streaming service, I rarely ever rate a song that I like or dislike. But more often I skip a song, or listen only halfway through it if dislike it. If I really like a song, I come back to it often. Even if I would like to rate a song, sometimes it is simply not possible, as I might be driving, or the song might be playing through sonos, roku or some system which does not allow for a easy rating interface. So, to infer my musical taste profile, my listens, my repeat listens, my skips, and fraction of tracks listened to, etc. are far more valuable signals than explicit ratings. This is what this paper attempted to incorporate within the CF framework.

##The Formulation

To keep it concrete, let us continue with the music streaming example, and lets say that we are only going to use the number of times a user has listened to a track (track listen count) as the only signal. For user $$u$$ and track $$i$$, let us reuse $$r_{ui}$$ to denote that number. We begin by defining a <em>preference</em> $$p_{ui}$$ of the user $$u$$ to track $$i$$ such that it is unity when the user has at least one listen and zero if the user has not interacted with the track at all:

$$p_{ui} = \begin{cases} 1. &\mbox{if } r_{ui} > 0 \\ 0. & \mbox{otherwise}. \end{cases} .$$

So, at the end, the task of the recommendation system will be to predict the preference for the unseen tracks. Next, we use the track listen count to define a <em>confidence</em> $$c_{ui}$$ of the liking of the track by the user:

$$c_{ui} =  1 + \alpha r_{ui} .$$

This is done in sort of an ad hoc way, and the confidence could de  defined in many other ways as long as it is a monotonic function of the frequency.  The confidence has a base value of $$1$$ for all user-item pairs and it gets stronger as the number of listens increase. So a high value of confidence $$c_{ui}$$ would mean that for that user-track pair these is a lot less noise about the preference of that particular user to that track. Therefore, it means that it is more important for the algorithm to predict a preference closer to unity for the pairs where the confidence is high. This leads to the following analog of explicit objective function minimization problem:

$$ \min_{x_*, y_*} \sum_{u,i} c_{ui} \left( p_{ui} - \bbra{\bx_u}\ket{\by_i} \right)^2 + \lambda\left(\sum_u \bbra{\bx_u}\ket{\bx_u} + \sum_i \bbra{\by_i}\ket{\by_i} \right) . $$

A big $$c_{ui}$$ means that the prediction $$\bbra{\bx_u}\ket{\by_i}$$ has to be that much closer to $$p_{ui}$$ for that term not to contribute a lot to the sum.

But suddenly now, the problem is no longer sparse, and the sum contains all $$m\times n$$ terms. 

<strong>How to solve this, then? </strong>

Linear algebra tricks come to the rescue! 

First, note that if the user latent factors are held constant the problem is quadratic in the item latent factors, and vice versa. 
This leads to the alternating least squares  (ALS) approach for this problem. But that by itself does not solve the  lack of sparsity issue. 

The ALS proceeds in two steps:

<em>STEP 1 of ALS:</em> First, we hold the item latent vectors $$\ket{\by_i}$$ fixed, and solve for the user latent vectors $$\ket{\bx_u}$$. For a given user $$u_0$$, the optimization function can be re-written in the matrix notation. 
First we define a $$n\times f$$ item-factor matrix as:
 
$$\bf{Y} = \begin{bmatrix} \bra{\by_0} \\ \bra{\by_1}\\ ... \\ \bra{\by_{n-1}} \end{bmatrix}$$ 

Next, we define a vector of preferences for the user $$u_0$$ as: 

$$\ket{ {\bf{p}}_{u_0} } =  \begin{bmatrix} p_{u_{00} }  \\ p_{u_{01} }\\ ... \\  p_{u_{0 (n-1) }  }  \end{bmatrix}$$ 

and an $$n \times n$$ diagonal matrix of confidence in items for this user $$ {\bf C}^{u_0} $$  as:
	
$$ {\bf C}^{u_0} = \begin{bmatrix} c_{u_{00}} & 0 & 0 & 0 & ... & 0 \\ 0 & c_{u_{01}} & 0 & 0 & ... &0\\ ... \\ ... \\ 0 & 0 & 0 & 0 & ... & c_{u_{0 (n-1) }}\end{bmatrix}.$$
	
With these, the relevant part of the objective function can be rewritten as:

$$ \left[\left(\ket{ {\bf p}_{u_0}} - {\bf Y} \ket{ \bx_{u_0}} \right)^T {\bf C}^{u_0}\left(\ket{ {\bf p}_{u_0} } - {\bf Y} \ket{ \bx_{u_0} } \right) \right] + \lambda \bbra{\bx_{u_0}}\ket{\bx_{u_0}}. $$ 

To minimize this, we set the derivative w.r.t. $$\ket{ \bx_{u_0}}$$ to zero, and we find the solution (see derivation at the bottom) as

$$ \ket{ \bx_{u_0}} = \left( {\bf Y}^T {\bf C}^{u_0} {\bf Y} + \lambda  {\bf I}  \right)^{-1}  {\bf Y}^{T} {\bf C}^{u_0} \ket{ {\bf p}_{u_0}}. \text{..... (1)}$$ 

<em>STEP 2 of ALS:</em> In the next step, in an exactly analogous fashion, we can hold the user latent vectors fixed and vary the item latent vectors to minimize the cost function. We define an $$m\times f$$ user-factor matrix $${\bf X}$$, an $$m\times m$$ diagonal matrix $${\bf C}^{i_0}$$, and a preference vector $$\ket{ {\bf p}_{i_0}}$$ for all users against item $$i_0$$. We end up with the solution: 

$$\ket{ \by_{i_0}} = \left( {\bf X}^T {\bf C}^{i_0} {\bf X} + \lambda  {\bf I}  \right)^{-1}  {\bf X}^{T} {\bf C}^{i_0} \ket{ {\bf p}_{i_0}}.  \text{..... (2)} $$ 

The algorithm then alternates between STEP 1 and STEP 2 until the latent factors converge (usually $$\sim 10$$ sweeps). <strong>Computational Complexity </strong>

Going back to STEP 1, we first note that where $${\bf C}^{u}_{ii}$$ is unity $$p_{ui}$$ is zero by construction. Therefore for each of the $$m$$ users, $${\bf C}^{u} \ket{ {\bf p}_{u}}$$ has only $$n_u$$ non-zero terms where $$n_u$$ are the number of items for which the user $$u$$ has preference equal to unity, and typically $$n_u  \ll n$$. The main bottleneck is the term $$ {\bf Y}^T {\bf C}^{u} {\bf Y}$$ whose naïve calculation would require time $$O(f^2 n)$$ for each user. Speedup can be obtained just by rewriting this term as

$${\bf Y}^T {\bf C}^{u} {\bf Y} =  {\bf Y}^T\left({\bf C}^u - {\bf I}\right) {\bf Y} + {\bf Y}^T {\bf Y}. $$

The beauty of this expression is that the $$f\times f$$ matrix $${\bf Y}^T {\bf Y}$$ has to be calculated only once (being independent of the user), and $$\left({\bf C}^u - {\bf I}\right)$$ is non-zero for only those $$n_u$$ items that the user has expressed any preference for! Therefore this part of the calculation reduces vastly in complexity to $$O(f^2 n_u)$$ for each of the $$m$$ users. 

An analogous trick works for STEP 2 as well! 

Once the solution converges, the predicted preference of a user for an item can be computed as

$$ \hat{p}_{ui} = \bbra{\bx_u}\ket{\by_i}.$$ 

Sort the tracks by this value, and voila, you have recommended tracks for every listener in your sample! 


<strong>Another Trick! </strong>

 Suppose there are 100 million songs. If we are using 200 features, then $${\bf Y}$$ has dimension $$10^8 \times 200$$. 
Therefore, $${\bf Y}^T {\bf Y}$$ is not a trivial multiplication. Conventional matrix multiplication will take a row of $${\bf Y}^T$$ that is 100 million wide and multiply it with a column of $${\bf Y}$$ that is also 100 million tall. Such computations can be costly and slow. However the way we wrote down $${\bf Y}$$ in terms of the vectors $$\ket{\by}$$ gives us another way to compute this: 
$${\bf Y}^T {\bf Y} = \begin{bmatrix} \ket{\by_0} & \ket{\by_1} & ... & \ket{\by_{n-1}} \end{bmatrix}  \begin{bmatrix} \bra{\by_0} \\ \bra{\by_1}\\ ... \\ \bra{\by_{n-1}} \end{bmatrix}\\ = \ket{\by_0}\bra{\by_0}  +  \ket{\by_1}\bra{\by_1} + ...+ \ket{\by_{n-1}}\bra{\by_{n-1}} $$ 
i.e. the matrix product is the sum of the outer products of the rows, each of which is $$200 \times 200$$ and being independent of each other can be computed on different nodes and then added back together! 

<strong>Quick derivation of the update rule </strong>

We expand the cost function by propagating the transpose and then multiplying through :

$$\left[\left(\ket{ {\bf p}_{u_0} } - {\bf Y} \ket{\bx_{u_0}} \right)^T {\bf C}^{u_0}\left(\ket{ {\bf p}_{u_0}} - {\bf Y} \ket{ \bx_{u_0}} \right) \right] + \lambda \bbra{\bx_{u_0}}\ket{\bx_{u_0}} \\ = \left(\bra{ {\bf p}_{u_0}} - \bra{\bx_{u_0}}{\bf Y}^T \right) {\bf C}^{u_0}\left(\ket{ {\bf p}_{u_0}} - {\bf Y} \ket{\bx_{u_0}} \right)  + \lambda \bbra{\bx_{u_0}}\ket{ \bx_{u_0}} \\ = \bra{ {\bf p}_{u_0}}  {\bf C}^{u_0} \ket{ {\bf p}_{u_0}}  - \bra{\bx_{u_0}} {\bf Y}^T {\bf C}^{u_0} \ket{ {\bf p}_{u_0}} - \bra{ {\bf p}_{u_0}}  {\bf C}^{u_0} {\bf Y} \ket{ \bx_{u_0}} +\bra{\bx_{u_0}}{\bf Y}^T {\bf C}^{u_0}  {\bf Y} \ket{ \bx_{u_0}}  + \lambda \bbra{\bx_{u_0}}\ket{\bx_{u_0}}  $$

Taking the derivative w.r.t. $$\bra{ \bx_{u_0}} $$ and setting it to zero gives:

$$ - {\bf Y}^T {\bf C}^{u_0} \ket{ {\bf p}_{u_0}} + {\bf Y}^T {\bf C}^{u_0}  {\bf Y} \ket{ \bx_{u_0}} + \lambda {\bf I} \ket{ \bx_{u_0}} = 0,$$ 

which implies

$$ \left({\bf Y}^T {\bf C}^{u_0}  {\bf Y} + \lambda {\bf I} \right)\ket{\bx_{u_0}} =  {\bf Y}^T {\bf C}^{u_0} \ket{ {\bf p}_{u_0}} $$

that immediately gives us the solution (1) written above. 


<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-58361665-1', 'auto');
  ga('send', 'pageview');

</script>
