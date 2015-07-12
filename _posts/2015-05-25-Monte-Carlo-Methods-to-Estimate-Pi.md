---
layout: post
title: Monte-Carlo Methods to Estimate Pi
---



  Just this weekend, I was mucking around with the Monte Carlo method for estimating &pi; and stumbled across quite a few interesting articles. It was intriguing to see the diversity of situatons that Monte-Carlo methods were adapted across and though I'll never get my head around most of them, I've - in this post - tried my hand at some classic problems just to get a sense of how well it works. 

  I don't suppose even for a second that Monte Carlo experiments find much favor with mathematical purists. Indeed anyone, through mere brute force, might come up with the right solutions to perplexing thought experiments like the [Monty-Hall problem](http://en.wikipedia.org/wiki/Monty_Hall_problem) or the [Boy-or-girl paradox](http://en.wikipedia.org/wiki/Boy_or_Girl_paradox). That said, there is no denying the efficacy of Monte Carlo methods in deriving quick estimates or in situations entailing less elegant solutions. Also, some of these estimates/solutions are quite fun to pull off :). 

  **Say we wanted to estimate the value of &pi;(pi)**. Imagine a circular dartboard of radius *r* against a square background of side length *2r*. You aim a dart at this setup a *looooot* of times - the more the better - with probability of the dart landing anywhere within the square being uniform. Then count the number of the times the dart landed within the circle, and that should give you a fair estimate of the value of &pi;. Cool, no? 
  
  $$\left( \frac{Area of Circle}{Area of Square} \right)= \frac{\pi r^2}{(2r)^2}= \frac{\pi}{4}$$
  
  $$\pi=\left( \frac{Area of Circle}{Area of Square} \right)*4$$

  Find the R code below. As you can see, I start off with a measly 1000 runs and then repeat the analysis for an increasing number of runs. I've provided the plots in a grid for ease of comparison. 
  
    

{% highlight r %}
n<-1000

x<-runif(n,min=0,max=1)
y<-runif(n,min=0,max=1)

circle <- (x-0.5)^2 + (y-0.5)^2 <= (0.5)^2
pi <- (sum(circle)/n)*4
{% endhighlight %}


{% highlight r %}
plot(x,y,pch='.',cex=2,col=ifelse(circle,"tomato","grey")
     ,xlab='',ylab='',asp=1,
     main=paste("Approximate Value of Pi =",pi," (n=",n,")"))
{% endhighlight %}


<img src="http://velavar.github.io/images/MCM/unnamed-chunk-3-1.png" title="center" alt="center" style="display: block; margin: auto;width: 350px;" /><img src="http://velavar.github.io/images/MCM/unnamed-chunk-3-2.png" title="center" alt="center" style="display: block; margin: auto;width: 350px;" /><img src="http://velavar.github.io/images/MCM/unnamed-chunk-3-3.png" title="center" alt="center" style="display: block; margin: auto;width: 350px;" />

