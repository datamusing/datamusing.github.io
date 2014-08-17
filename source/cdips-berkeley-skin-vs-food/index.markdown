---
layout: page
title: "Skin vs. Food Problem"
date: 2014-07-18 14:49
comments: true
sharing: true
footer: true
---

**Data Science starts with? .... Data!**


So lets get some images from the web! 

# Getting Photos from Instagram 

###Get an Instagram Client ID/SECRET

Go to [instagram's developer page](http://instagram.com/developer/), create an account and register a new application. 
Just give it an Application Name, a Description, a Website (you can use your personal website, reuse the website in the ``OAuth redirect_uri `` field.  This should create a **CLIENT ID** and a **CLIENT SECRET** for you. 


### Install the Instagram Python Client 

https://github.com/Instagram/python-instagram 

To install do:

`sudo pip install python-instagram`


### Fire up an ipython notebook 

So, we will import python-instagram, and then set up an api connection. Then  we will grab the images from a restaurant, say "Slanted Door"

{%img https://dl.dropboxusercontent.com/u/18915298/CDIPS/instagram_crawl.png 800 800 ipnb ipnb%}

Let's see what is happening here! 


* First, we import and initialize the Instagram API with the ``client_id`` and the ``client_secret`` obtained when registering your app with instagram.
* Then, we did a location search with something called a ``foursquare_v2_id``.   We are doing this because it is easier to find the foursquare id associated with a restaurant, and then derive the instagram location id. To find the FourSquare ID  got to foursquare.com and search for the place. The url will have the id. For example the FourSquare url for "Slanted Door" was ````https://foursquare.com/v/slanted-door/3fd66200f964a52018ed1ee3````
which explains the ID I used. 
* With that location ID, we then do a media search, which gives us a bunch of media objects plus a link to the next page as ``next``
* Then, we cycled through the media list, and got the urls for the standard_resolution images. 
* Note that the ``next`` variable contains a new ``max_id``, which we can use to get the next page of images. 
* So, in the next call, we would use: 

```python 
mm, next = api.location_recent_media(location_id=797,max_id=766864469753630841)

```
Now, you can easily loop through the pages to get all the images for this location. Note, that you need to throttle the requests to instagram so that you roughly do one request per second. Use the ``time.sleep`` function to limit the requests. 

Let's try to get the urls for the photos for these 20 restaurants

1. Slanted Door 
2. House of Prime Rib
3. Greens Restaurant  
4. Mustards Grill 
5.  Bouchon
6.  Sutro's at the Cliff House
7.  Bistro Jeanty
8.  Boulevard 
9.  Waterbar 
10. The Dead Fish 
11. Ad Hoc
12. Farallon 
13.  Wayfare Tavern
14.   Kokkari Estiatorio 
15.   Zuni Cafe
16.   Skates on the Bay
17.   The Stinking Rose
18.  Foreign Cinema 
19.   Kuleto's 
20.   Redd

#Feature Selection

# Finding connected blobs

Scipy has a tool called labels which can be very useful in detecting connected pixel blobs. 

Check this out:

{%img https://dl.dropboxusercontent.com/u/18915298/CDIPS/labels.png 800 800 connected-pixels connected-pixels %}


	
