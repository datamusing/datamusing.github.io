---
layout: post
title: "Fun with interactive visualizations in astrophysics"
date: 2014-09-03 18:33:25 -0700
comments: true
categories: interactive, visualization, d3.js, dimple.js, astrophysics, pandas
---

Recently, I have been playing  a lot with interactive data visualizations for my data science introspection work. While making these interactive plots, and experiencing their sheer power in expressing so many aspects of the data in clean and simple charts, I have often wondered how much more enriching such interactive visualizations would have been in my astrophysicist life.  <!--more-->  The most versatile tool for this purpose is undoubtedly the [d3.js](http://d3js.org/) javascript library. There is a  steep learning curve in mastering d3, and honestly, I am climbing it right now. So maybe, it would have been a bit  too much to invest a lot of time in picking up this tool while I was busy writing code, papers and grant proposals.  

> Luckily, it turns out that there are high level libraries that have been built on top of d3.js, which are way simpler to use, and in as little as half an hour, one could have a rich interactive plot up and running!  

So, here is my attempt to  share that sliver of wisdom, so that you, my  esteemed astrophysicist colleague, can make plots like this pretty easily. I show a case study here with a recent data release from the South Pole Telescope collaboration. 


 
##The  2500 square-degree SPT-SZ Cluster Sample

On September 2, 2014 the South Pole Telescope (SPT) collaboration published a paper on the arXiv entitled _Galaxy Clusters Discovered via the Sunyaev-Zel'dovich Effect in the 2500-square-degree SPT-SZ survey_ [(Bleem et al. 2014)](http://arxiv.org/abs/1409.0850).  Incidentally, the work was led by Lindsey Bleem  whom I had the pleasure to interact with as  a fellow postdoc at the Argonne National Laboratory. 

{%img right https://dl.dropboxusercontent.com/u/18915298/blog/sptpaper/bleemEtAl.png   400 200 %}

This paper represents solid work  of following up 677 Sunyaev-Zel'dovich (SZ) clusters detected (at more than 4.5-$\sigma$ significance) by the temperature decrements they cause in the 95 and 150 GHz cosmic microwave background (CMB) maps. To confirm  these detections,  ground- and space-based imaging  was used to find their optical and near-infrared (NIR) counterparts. In 516 of the 677 candidates, the counterparts were found and a redshift could be derived. Here is a plot from the paper showing the mass vs. redshift of these 516 clusters as black crosses. From each point on the figure, you can read off the the mass ($M_{500}$)  and the redshift of the cluster. But there is so much more information for each galaxy cluster: 
 	
 * the cluster ID
 * the  detection significance ($\xi$)
 * whether the cluster has an x-ray companion (XRAY)
 * whether the cluster exhibits strong lensing (SL)
 * the integrated Comptonization parameter, $Y_{SZ}$. 
 * the  core radius ($\theta_c$)  (a measure of the size of the central core)
 
to name the most interesting ones. 

So naturally, I was interested in  if we could put all this rich information into a single plot!   
I came up with two very quick versions of the visualization. They both have all the above info, but they are presented 
with different purposes in mind. 

Here they are:
	
##Visualization 1: I am more interested in the detection significance, and which clusters have strong lensing and/or an X-ray counterparts

Mouse over each bubble to activate the tooltip. 


<iframe width='100%' height='600' frameborder='0' src='http://bl.ocks.org/datamusing/raw/d353dd63e013448727b4/' allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>

What I have done in the above plot is make the size of the bubble for each cluster proportional to its detection significance. You can immediately see that  bubbles get bigger as you go higher in the plot (along the mass axis), showing that more massive clusters were detected much more easily (that is a characteristic of the SZ effect). Then, I also color coded the ones that have only SZ, SZ+SL, SZ+XRAY, and SZ+SL+XRAY so that you can quickly investigate the properties of any specific class of cluster (e.g. the strong lensing clusters). Note that all the other information are contained in the tooltip, as you hover over the cluster. ##Visualization 2: I am more interested in the size and flux of the clusters, and visually see if there are trends.
 

 <iframe width='100%' height='600' frameborder='0' src='http://bl.ocks.org/datamusing/raw/19ed8e9811c11a4ed9be/' allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>
 
In this case, I made the size of the cluster bubble proportional to, well, its size ($\theta_c$), and its color a hue of orange that deepens as the Comptonization parameter $Y_{SZ}$  increases. 
	You can visually appreciate here the fact that $Y_{SZ}$ increases as mass increases, and that fact is fairly insensitive to redshift (this is a very typical scaling relation of the SZ effect). You can also see that the low mass, low redshift clusters have larger core radii. If you have spent a lot of time with SZ clusters, you can appreciated various other relations in this plot,
which I am not going to belabor with here. Again, all other information is now neatly contained in the tooltip. 


#Do It Yourself 

So, how do you quickly make this yourself! 

Here is what I did:

* For the data wrangling part I used Python.  I read in the data from the FITS file using ``pyfits`` and then processed it using `Pandas`. Then end result was a tab separated text file with 516 rows. 
* For the visualization, I have used a library here called [dimple.js](http://dimplejs.org/) that is a high level abstraction of the d3 library.  I pretty much took the [bubble chart example](http://dimplejs.org/examples_viewer.html?id=bubbles_standard) and started modifying it. 

The code is essentially about 30 lines of html:

{% codeblock lang:html %}
<div id="chartContainer">
  <script src="http://cdnjs.cloudflare.com/ajax/libs/d3/3.4.11/d3.min.js"></script>
  <script src="http://dimplejs.org/dist/dimple.v2.1.0.min.js"></script>
  <script type="text/javascript">
    var svg = dimple.newSvg("#chartContainer", 750, 500);
    d3.tsv("spt_opt_labels.tsv", function (data) {
      var myChart = new dimple.chart(svg, data);
      myChart.setBounds(95, 25, 600, 445)
      var x = myChart.addMeasureAxis("x", "REDSHIFT");
      var y = myChart.addMeasureAxis("y", "M500 (1e14)");
      var z = myChart.addMeasureAxis("z", "XI");
      var s = myChart.addSeries(["SPT_ID","M500 (1e14)","THETA_CORE","YSZ","XI","LABEL"], dimple.plot.bubble);
      s.getTooltipText = function (e) {
                return [
                    "Cluster ID: " + e.aggField[0],
                    "Mass M500 (x 10^14) = " + parseFloat(e.aggField[1]).toFixed(2),
                    "Core radius: " + parseFloat(e.aggField[2]).toFixed(2),
                    "Y_SZ = " + parseFloat(e.aggField[3]).toExponential(2),
                    "Detection significance = "+parseFloat(e.aggField[4]).toFixed(2)
                ];
      };

      z.overrideMax = 150.0;
      var myLegend = myChart.addLegend(540, 30, 150, 50, "right");
      myLegend.fontSize = "10px";
      myChart.assignColor("SZ+SL+XRAY", "#a6611a",0.8);
      myChart.assignColor("SZ+XRAY", "#dfc27d");
      myChart.assignColor("SZ", "#80cdc1");
      myChart.assignColor("SZ+SL", "#018571");
      myChart.draw();
    });
  </script>
</div>
{% endcodeblock %}

And that is all you need, to produce this powerful graphic!

## Learning by example

To get started I would suggest downloading the html file and the tsv file that I have made availably publicly.  
Here is my [gist](https://gist.github.com/datamusing/d353dd63e013448727b4).

The steps:

* Clone my gist using

{%codeblock%}
 $ git clone https://gist.github.com/d353dd63e013448727b4.git
 {%endcodeblock%}

* Inside the gist directory, you will find all necessary files. The main file to look at is  `index.html`
* Fire up a server from inside that directory using:

{%codeblock%}
 $ python -m SimpleHTTPServer 9999
 {%endcodeblock%}
 
* Point your browser (no IE8 please!) to localhost:9999
* You should be able to see the viz on your browser now! 
* Get your own table and make your own viz. 
* Once you are happy with your results you can post it to a Github Gist, and point to it from bl.ocks.org as I have done [here](http://bl.ocks.org/datamusing/d353dd63e013448727b4). 



~ FIN ~ 

 
