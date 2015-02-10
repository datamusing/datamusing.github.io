---
layout: post
title: "A/B Testing: Maths and Common Sense"
date: 2015-01-25 19:37:07 -0800
comments: true
categories:
- Machine Learning
- A/B testing
keywords: A/B testing, A/B testing maths, mathematics,  G-test, t-test, Fisher's exact Test, binomial distribution, central limit theorem, standard error, sample mean, conversion
description: A mathematical introduction to A/B Testing with some sprinkling of common sense.
published: false
---

# The G-test 


Let's look at the table of conversions again.

![](https://dl.dropboxusercontent.com/u/18915298/blog/abtest/BookingEventDisplaySegment.png)

 Let us write it down in the following $2\times2$ contingency table format:


| Group     | Converted      |  Did not Convert | Total |
| ----------- | ------------ | --------| -------|
|A  (base)| $O_{00} = $  399192           | $O_{01} = $  6759860|  $t_{0} = $  __7159052__ |
|B (variant) | $O_{10} = $ 399165 | $O_{11} = $ 6760047  | $t_{1} = $  __7159212__ |
|Total | $s_{0} = $  __798357__ | $s_{1} = $   __13519907__ | $T = $ __14318264__|


G-test is a general test of significance applied to contingency tables such as above. The G-statistic stems from a likelihood ratio test, under the assumptions of a multinomial probability distribution of the random variables involved. 

The null hypothesis of a G-test is that there is no difference between group A and group B.  If we can reject this null hypothesis at a high significance (booking uses a 90% confidence) then it means that the variant does exhibit an effect (i.e. the A/B test is conclusive).

To evaluate the G-statistic, we first need to find the __expectation values__ in each of the four cells of the contingency table under the assumption that the null hypothesis is true. 

First, let us estimate the probability under the null hypothesis that a random user will convert. Because under the null hypothesis, there is no difference in the inherent probability between A and B, this probability is best estimated using the total numbers in the bottom row. So the estimated probability of conversion is:

$$P = s_0/T = 798357/4318104.  =  0.0557585$$ 

and the probability of not converting is  $(1-P) = s_1/T=   0.9442414$. 
	
 Therefore, the expected number of conversions in group A is  this probability times the total number of subjects in channel A. 
 
$E_{00} = P t_0 =  t_0 s_0/ T$

Similarly, the estimated number of non-conversions in channel A is $E_{01} = (1-P) t_0 =   t_0 s_1 /T $
	
Continuing this way,  we can generate an __expected values__ matrix as: 

| Group     | Converted      |  Did not Convert |
| ----------- | ------------ | --------| 
|A  (base) | $E_{00} =  t_0 s_0/ T$    |  $E_{01} = t_0 s_1/ T $ |
|B (variant) | $ E_{10} = t_1 s_0 /T $  |  $E_{11} = s_1 t_1/T$  |


Putting in the numerical values we get 
	
| Group     | Converted      |  Did not Convert |
| ----------- | ------------ | --------| 
|A  (base) | $E_{00} =  399174.039 $    |  $E_{01} = 6759877.9606 $ |
|B (variant) | $ E_{10} = 399182.9606 $  |  $E_{11} = 6760029.039 $  |

The G-statistic can be now calculated as: 

$$ G = 2 \left( O_{00} \ln( E_{00}/O_{00}  ) +O_{01} \ln( E_{01}/O_{01} ) + O_{10} \ln( E_{10}/O_{10}  )  + O_{11} \ln( E_{11}/O_{11}  )    \right). $$
	
Substituting the numerical values, we get 

$$ G = 0.0017 $$

For large samples, the G statistic is approximately distributed with a chisq probability density function with 1 degree of freedom. 

To interpret what this G value means, we find the $p$-value: the probability that the G value is at least as large as its observed value under the null hypothesis. 

In Python this can be computed with ``1. - scipy.stats.chi2.cdf(G, 1)``. 

The above $G$ gives a $p$-value of  0.97. Which means in 97% cases, the null hypothesis  (that there is no difference between treatment and variant) can produce a G-value at least as large as the one observed just by random fluctuations.  Hence, this would suggest that given the data there is no reason of to believe that the control and variant are different. By the looks of it, this seems like an A/A test and this is what you would expect for an A/A test. 
Another way to put this, is to say that  we have a confidence of only $(1-p) = 0.03$ or 3% that the control and variant are different.  This is the number under the Test column in the Booking.com snippet. 

__When to reject the null hypothesis then?__

Most companies use a 95% confidence,  or a p-value of < 0.05 for calling  an experiment conclusive. Some experts profess waiting for a p-value of <0.01 until you call it conclusive.

Some point to note: 

* Booking uses 90% as the bar. 
* Most tests should be run with a balanced sample (50/50) in each branch.
* They run most of their experiments for about 3 weeks to smooth out any hourly/daily/weekly effects. 
* Note that is duration is a function of the total user base on which the test is run. 
* All Booking tests run on ALL users.
* I would advocate a 95% confidence level as we are just starting out. 
* Given an estimate of our traffic flow, and an idea of the lift we want, it will be easy to calculate how long a typical experiment should be run at OpenTable. 

