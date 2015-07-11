---
layout: post
title: Metal Bands - Analysis II
---

##Metal Bands - Analysis II: Mapping the origins of metal bands

Continuing with my analysis on metal bands, this time I look at the provenance of these bands as I attempt to graph - by country - where most of our metal music originates from. Posts such as this [one](http://www.reddit.com/r/Metal/comments/q2flb/map_of_countries_by_metal_bands_per_capita/) have already elaborated on this question. I merely attempt to replicate the findings with the data I obtained from this [site](http://www.metal-archives.com/). In this post, I use the *rworldmap* package to help me represent the number of metal bands by country on a world map. 

The following code shows all the packages this post utilizes. Using the same data that I had scraped for the earlier analysis, I now have a dataset detailing the country of origin of 77,317 bands. 

{% highlight r %}
library(reshape2)
library(rworldmap)
library(RColorBrewer)

colourPalette <- brewer.pal(9,'YlOrRd')
{% endhighlight %}




{% highlight r %}
tables<-readRDS(file="ppndata.Rda")

df1 = read.csv("heavy.csv", header = TRUE)
df2 = read.csv("black.csv", header = TRUE)
df3 = read.csv("death.csv", header = TRUE)
{% endhighlight %}

Next, I scrape the current population (as on 18th June, 2015) from this [site](http://www.worldometers.info/world-population/population-by-country/) which gives me the population by country. As expected, some of the names needed to be tweaked so as to allow the merging of the two data frames, all the steps of which are listed below. Once the datasets have been merged, I create a new column that stores the number of bands per capita for each country. A cursory look at this data shows such extreme outliers that I have to take the logarithmic values for this data to be reasonably represented on a graph. 


{% highlight r %}
ppndat<-tables
colnames(ppndat)<-gsub('\\t','',colnames(ppndat))
colnames(ppndat)<-gsub('\\n','',colnames(ppndat))
colnames(ppndat)<-gsub(',','',colnames(ppndat))
colnames(ppndat)[3]<-"Population"
colnames(ppndat)[2]<-"country"
ppndat$Population<-gsub(',','',ppndat$Population)

ppndat$Population<-as.numeric(as.character(ppndat$Population))
ppndat$country<-as.character(ppndat$country)
ppndat$country[ppndat$country=="United States of America"]<-"United States"
ppndat$country[ppndat$country=="U. K."]<-"United Kingdom"

df_all<-rbind(df1,df2,df3)
country<-(df_all$Country)
dat<-sort(table(country),decreasing=TRUE)
trim <- function (x) gsub("^\\s+|\\s+$", "", x)
dat<-melt(dat)
dat$country<-trim(gsub("\\b([a-z])([a-z]+)", "\\U\\1\\L\\2" ,dat$country, perl=TRUE))

final<-merge(dat,ppndat,by="country")
final$Pop1<-final$Population/1000000
final$num<-log(final$value)
final$npc<-log(final$value/final$Pop1)

spdf <- joinCountryData2Map(final, joinCode="NAME", nameJoinColumn="country", verbose='FALSE')
{% endhighlight %}



{% highlight text %}
## 122 codes from your data successfully matched countries in the map
## 0 codes from your data failed to match with a country code in the map
## 122 codes from the map weren't represented in your data
{% endhighlight %}

Now I can move on to plotting the data on a world map. First I graph the number of metal bands by the country of origin. As can be seen, the United States leads with 3107 bands, followed by Germany(1460 bands), Italy(886 bands), United Kingdom(810 bands), Brazil(781 bands) and so on. 

{% highlight r %}
mapParams<-mapCountryData(spdf, nameColumnToPlot="npc", catMethod="pretty",numCats=20,colourPalette=colourPalette,missingCountryCol="light grey", addLegend=FALSE)
do.call(addMapLegend
,c(mapParams
,legendLabels="all"
,legendWidth=0.5
,legendIntervals="data"
,legendMar = 2))
{% endhighlight %}


<img src="http://velavar.github.io/images/metal_map/unnamed-chunk-6-1.png" title="center" alt="center" style="display: block; margin: auto;" />

The above graph kind of highlights why we need to redo the analysis with the numbers drawn per capita; some of the leading countries are also the countries with very high populations and countries like Finland, Norway etc don't figure anywhere near the top. So now I use the bands-per-capita column to redraw the same graph. The graph below does just that. 
This time the graph does the expected, the countries Finland, Sweden, Denmark, Norway figure at the top, with Greece making a surprise entry at the number 3 spot.

<img src="http://velavar.github.io/images/metal_map/unnamed-chunk-7-1.png" title="center" alt="center" style="display: block; margin: auto;" />
The end result of this analysis did not surprise me much - most of the bands I've listened to so far happen to originate from Northern Europe. However, I did have a few surprises while doing this analysis; I was glad to see India featured so high up the order in the number of metal bands. Also, I was surprised to find Greece wedged in right between the Scandinavian nations in the bands per capita rank order. I should give them a listen sometime. 

**References:**
<br>
1. [Encyclopaedia Metallum](http://www.metal-archives.com/)
<br>
2. http://www.reddit.com/r/Metal/comments/q2flb/map_of_countries_by_metal_bands_per_capita/
<br>
3. http://www.worldometers.info/world-population/population-by-country/
