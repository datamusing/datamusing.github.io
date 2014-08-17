---
layout: post
title: "Detecting people in photographs using skin tone"
date: 2014-07-06 00:26:21 -0700
comments: true
categories: 
- python,
- image processing
- machine learning
---

 SAN FRANCISCO, CA --- As a Data Scientist at [OpenTable](http://opentable.com), my computer screen often fills up with images of scrumptious food items (_effectively keeping my metabolic rate on  a high gear!_). Many of these photographs are professionally or semi-professionally taken by food photographers or enthusiasts (aka FoodSpotters!).  {%img right https://dl.dropboxusercontent.com/u/18915298/blog/detectSkin/tea-party.jpg  200 200 tea-party tea-party 2%} Annoyingly, a lot of photos, especially those from social media channels,  come with portraits of eaters posing with the eaten, such as the one shown here.   Of course, one could use face detection algorithms, and other sophisticated techniques to weed out these photos. These techniques often require a lot of overhead and dependency on external libraries. Also, there may not even be a face in the photo to detect, but a hand or some portion of the torso might be showing. Over the weekend, I have been thinking about very fast ways of finding a human subject in a photograph, so that I could generate some quick features for classifying photos. It turns out that one promising way to do that could be to detect human skin pixels in the photos. 
 
##_Detecting skin pixels_

A quick digging into the subject of detecting skin pixels revealed a rich literature on this subject. As this is a blog post and not a review paper, I will only describe the bits I used, and leave the reader with this [paper](http://academic.aua.am/Skhachat/Public/Papers%20on%20Face%20Detection/Survey%20on%20Skin%20Color%20Techniques.pdf) or [this one](http://pdf.aminer.org/000/367/151/image_chromatic_adaptation_using_anns_for_skin_color_adaptation.pdf) as an entry point into this subject. _The fundamental concept behind pixel based skin detection is that the color of human skin (across various races and ethnicities) occupies a very tight region in the space of colors._  In brief, there are three main ways to detect skin pixels: 
  <!--more--> 
  
1.  **Explicit Skin Model Based Method**:  This class of methods try to use machine learning to find the best colorspace and a simple decision rule to define the boundaries the skin cluster in that colorspace. 
2. __Non-parametric Methods__:  The key idea here is to estimate skin color distribution from a training data without deriving an explicit model of the skin color, e.g. a Naive-Bayes classifier. A skin/non-skin training data set can be found [here](https://archive.ics.uci.edu/ml/datasets/Skin+Segmentation).
3.  __Parametric Methods__:  Here, one models the skin color distributions as parameterized probability distributions, such as Gaussians, or mixtures of Gaussians.

As a first stab, I decided to go with method (1) above. A little more investigation led me to the  [Gomez and Morales (2002)](http://www.cse.unsw.edu.au/~tatjana/ICMLWS02/MLCV/Morales.pdf) paper where the authors used a constructive induction algorithm which produces a single rule that defines the skin color boundary conditions in the RGB colorspace. Their method leads to an  extremely simple rule that goes as follows: 

* Extract R, G, B pixel values from the input image. 
* Normalize:  $$r=R/(R+G+B)$$,   $$g=G/(R+G+B)$$ and $$b=B/(R+G+B)$$, so that $$(r+g+b) = 1$$ for each pixel.
* Next, generate three quantities (note that $(r+g+b)$ being unity is redundant here, but the authors probably left it in to make the normalization explicit): 

$$  
\begin{eqnarray}
	\alpha &=& \frac{3b r^2}{(r+g+b)^3}\\
	\beta &=& \frac{r+g+b}{3r} + \frac{r-g}{r+g+b}	\\
	\gamma &=& \frac{r b+g^2}{g b}
\end{eqnarray}
$$

* Finally, a pixel is categorized as "skin" if the pixel satisfies **all** of the following three conditions: 

$$ \alpha>0.1276$$, $$\beta \le 0.9498$$ and  $$\gamma \le 2.7775 $$.

Note that these rules were based on the training set available when the paper was written, and I should probably be regenerating the rules with more training examples available now. But, the color of skin has not changed significantly in 10 years, so lets continue! 

Here is a quick Python implementation:

```python
import matplotlib.image as mpimg
# read in the image
values = mpimg.imread("tea-party.jpg")
# separate out r, g, b channels
r = values[:,:,0].astype('f')
g = values[:,:,1].astype('f')
b = values[:,:,2].astype('f')
# generate the three quantities 
alpha = 3*b*r**2/(r+b+g)**3
beta =  (r+g+b)/(3*r) + (r-g)/(r+g+b)
gamma = (r*b+g**2)/(g*b)
# finally we apply the rules:
pylab.imshow((alpha>0.1276)&(beta<=0.9498)&(gamma<=2.7775),cmap='gray')
```
which gives us the following result, where white represents pixels labeled as skin (the original image is also below for easy comparison):  
{%img center  https://dl.dropboxusercontent.com/u/18915298/blog/detectSkin/tea_party_binary.png 300 300%}
	
{%img center https://dl.dropboxusercontent.com/u/18915298/blog/detectSkin/tea-party.jpg 300 300%}

As  you can see, this one simple rule does remarkably well in isolating skin pixels. 

Here is another example:

{%img center https://dl.dropboxusercontent.com/u/18915298/blog/detectSkin/slanted_door_comp.png %}
