---
layout: post
title: Is there a good time to go UFO-spotting?
---


A few days ago, I serendipitously happened upon [this](http://www.nuforc.org/webreports/ndxevent.html) gem of a website that recorded all known UFO sightings (in the USA) to date. The data was simply begging to be analyzed so I immediately set myself on the job. 

A  description of the code used in the analysis follows. You can find the complete code [here](https://github.com/velavar/velavar.github.io/blob/master/scripts/ufo_analysis.R). 

This analysis uses the following packages: *XML*, *ggplot2* and *plyr* 


{% highlight r %}
#Load the required libraries
library(XML)
library(ggplot2)
library(plyr)
{% endhighlight %}

Once we are done loading the packages, it's time to pull the data from the site. The aforementioned *XML* package makes this a piece of cake. The data on the site is conveniently arranged in HTML tables so all it takes is a simple call to the *readHTMLTable()* and the we have the data neatly packed into a dataframe. The default datatype for all columns is "factor"; it is but small trouble as we easily cast each column into its relevant datatype. 


{% highlight r %}
#Read table data from the National UFO Reporting Center's site
base_url<-"http://www.nuforc.org/webreports/"
url0<-"http://www.nuforc.org/webreports/ndxevent.html"
table0<-lapply(url0, function(url) {
  total<-readHTMLTable(url)
  n.rows<-unlist(lapply(total, function(t) dim(t)[1]))
  as.data.frame(total[[which.max(n.rows)]])
}  )
table0[[1]]$Month<-substring(table0[[1]]$Reports,1,2)
table0[[1]]$Year<-substring(table0[[1]]$Reports,4,7)
{% endhighlight %}



{% highlight r %}
#Converting the "factor" format to the more usable Date and numeric format
table0[[1]]$Date<-as.Date(paste("01/",table0[[1]]$Reports,sep=""), "%d/%m/%Y")
table0[[1]]$Count<-as.numeric(as.character(table0[[1]]$Count))
table0[[1]] <- table0[[1]][nrow(table0[[1]]):3,]
{% endhighlight %}

Now that we have a data frame containing counts of UFO sightings over time, we can head straight over and make a no-frills line plot. The following line plot has time by the month (starting from 1950) on the x-axis and the number of sightings on the y-axis. 


{% highlight r %}
#Plotting no. of sightings over time.. Interesting results are in store!
try2<-tail(table0[[1]],n=762)
ggplot(try2, aes(x=try2$Date, y=try2$Count, group=1))+geom_line(color="#CD4F39", size=0.75)+xlab("Date (Year)") + ylab("Count (#Sightings)")+theme_grey(base_size = 10)
{% endhighlight %}

<img src="http://velavar.github.io/images/ufo_sightings/unnamed-chunk-4-1.png" title="center" alt="center" style="display: block; margin: auto;" />

The graph looks very intriguing at first glance! Number of UFO sightings have seen a steep increase over the past few years but something else catches one's eye. **There is a clear seasonality to the number of sightings as can be seen by the regularly-spaced spikes in sightings.** Closer inspection shows that extra-terrestrial visitations seem to spike around the summer months June to September. Now this is definitely not what I expected when I began this analysis. What fun! 

While I was at it, I decided to make a heatmap of the same data because, you know, pretty graphs. The following heat map shows number of sightings as a colour distribution over time (conveniently set as a grid). The graph drives home the same point - there is a definite increase in the number of UFO sightings around the months June to September every year. 


{% highlight r %}
#Heatmap of the last 15 years
try3<-tail(table0[[1]],n=185)
ggplot(try3, aes(try3$Month, try3$Year)) + 
  geom_tile(aes(fill = Count), colour = "white") + 
  scale_fill_gradient(low = "steelblue",high = "firebrick3")+
  theme_grey(base_size = 10)+
  labs(x = "Month", y = "Year")+
  scale_x_discrete(expand = c(0, 0))+
  scale_y_discrete(expand = c(0, 0))
{% endhighlight %}

<img src="http://velavar.github.io/images/ufo_sightings/unnamed-chunk-5-1.png" title="center" alt="center" style="display: block; margin: auto;" />


Say we wanted to see the sightings by state, we would need to pull the data out of the webpages dedicated to the corresponding months and years. This is precisely what the code below does. We pick monthly UFO data for the year 2014 using the same package as before and load it a data frame yet again. 



{% highlight r %}
#Attempting to visit each page and read the HTML tables
urls<-paste(base_url,"ndxe",
            table0[[1]]$Year,table0[[1]]$Month,
            ".html", sep="")
urls<-urls[grepl("ndxe2014",urls)]
tabla<-lapply(urls, function(url) {
  total<-readHTMLTable(url)
  n.rows<-unlist(lapply(total, function(t) dim(t)[1]))
  as.data.frame(total[[which.max(n.rows)]])
  }  )
{% endhighlight %}




Here I go plotting the sightings over time for the states with the highest total sightings for the year 2014. We could have plotted the same for all the states but the graphs would end up looking too much of a jumble. I replicate the same graphs for the 2009 and 2004 as well. 


{% highlight r %}
#Plotting no. of sightings in 2014, by state
tabla1<-ldply(tabla, data.frame)
tabla1$DateTime<-as.character(tabla1$Date...Time)
tabla1$Month<-sub("[//].*$", "", tabla1$DateTime)
tabla1$Month<-as.numeric(tabla1$Month)
try4<-count(tabla1, c("Month", "State"))
try5<-count(tabla1, "State")
try6<-merge(try4, try5, "State")
try6<-try6[(try6$freq.y>=200),]

#ANd finally one more plot
ggplot(try6, aes(x=try6$Month, y=try6$freq.x, group=try6$State, colour=try6$freq.y))+geom_line(size=1)+scale_colour_gradient(low="coral4", high="coral1")+labs(x = "Month (in 2014)", y = "#Sightings", colour="UFO Count")+theme_grey(base_size = 10)+ geom_smooth(method="loess", colour="#00000000", fill="orangered4", alpha=0.1)
{% endhighlight %}

<img src="http://velavar.github.io/images/ufo_sightings/unnamed-chunk-8-1.png" title="center" alt="center" style="display: block; margin: auto;" />


<img src="http://velavar.github.io/images/ufo_sightings/unnamed-chunk-9-1.png" title="center" alt="center" style="display: block; margin: auto;" /><img src="http://velavar.github.io/images/ufo_sightings/unnamed-chunk-9-2.png" title="center" alt="center" style="display: block; margin: auto;" />

Looks like we have states like CA and FL which show consistent number of sightings over the year, while the the spikes are a result of increases in the sightings in the remaining states in this period. 

Why do we have such patterns? - I don't know. It could be because of clearer skies at this time of the year (Would that explain why CA and FL have consistent trends all year round? Oooh.). It could also be merely because people are less preoccupied at this time of the year compared to any other time. Suffice to say that if you wanted to increase the chances of your sighting a UFO, you would do well to position yourself in one of the above US states around late summer and hope for the best. 

**Data Source:** http://www.nuforc.org/webreports/ndxevent.html
