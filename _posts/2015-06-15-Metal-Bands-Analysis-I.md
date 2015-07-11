---
layout: post
title: Metal Bands - Analysis I
---
##Metal Bands - Analysis I: What's in a metal band's name? 

<p><a href="http://velavar.github.io/">Back to Home Page</a></p>

  Metal bands, since forever, have appeared to follow particularly homogenous themes, esp. when it comes to naming themselves. Metal band names - to my mind - have a tendency to be excessively gory, stark and graphic like *Cannibal Corpse*, *Morbid Angel* or even just *Gojira* (my favourite, BTW). **So what are the most commonly (mis?)used words by metal bands? Do different kinds of metal bands such as death, black etc. elect to follow different motifs in naming themselves?** The [Encyclopaedia Metallum](http://www.metal-archives.com/) is a good place to find out. I had previously heard plenty of good things about this site, but it was only when I started to analyse the data within that I realized really how comprehensive this site was. Seriously, if you are a metal fan you absolutely *must* check this site out. 

  I start the analysis by attempting to read the genre pages pertaining to [heavy](https://en.wikipedia.org/?title=Heavy_metal_music), [black](https://en.wikipedia.org/wiki/Black_metal) and [death](https://en.wikipedia.org/wiki/Death_metal) metal. Grabbing the HTML from the webpages using the *XML* package proved to be of no use as the data was displayed using javascript. I mulled using *RSelenium* or *phantomjs* but eventually opted to take the lazy way out by getting the JSON version of the data from [this URL](http://www.metal-archives.com/browse/ajax-genre/g/heavy/json/1). I particularly liked this method because it let me right at the data, without having to deal with the accompanying cruft that comes with scraping the HTML from a website. However if you ever need to scrape data from pages that utilize Javascript, any of the aforementioned packages would be a good way to go. 

Once I was done reading the data, I saved these into three separate dataframes - one each for heavy, black and death metal and loaded the required packages. The data comprises of 14,932 heavy metal bands, 26,368 black metal bands, and 36,017 death metal bands. 

{% highlight r %}
library(reshape2)
library(ggplot2)
library(gridExtra)
{% endhighlight %}



{% highlight text %}
## Loading required package: grid
{% endhighlight %}


{% highlight r %}
df1 = read.csv("heavy.csv", header = TRUE)
df2 = read.csv("black.csv", header = TRUE)
df3 = read.csv("death.csv", header = TRUE)
{% endhighlight %}



  I would obviously want to ignore such words as *the*, *in* and so on, so I make a list of stopwords to eliminate before proceeding with the analysis.  

{% highlight r %}
myStopwords<-c("a","is", "to", "by", "i", "the", "in", "of", "for", "de", "from", "my", "no", "and", "metal")
pattern <- paste0("\\b(", paste0(myStopwords, collapse="|"), ")\\b")
{% endhighlight %}
  
  Next, I take all the heavy metal band names, collapse them into a single row, convert them to lowercase and remove all the stopwords. Then I split them again into rows, each containing a single word, so it becomes easy for me to find the word frequency. Some of these rows contain empty strings so I remove all such rows as shown below. Finally, I use the *sort* function to get the top 15 commonly used words sorted in the descending order. I repeat the same for black and death metal separately. You can have a look at the words in the output below. 

{% highlight r %}
#heavy metal
heavydat<-tolower(paste(df1$Band,collapse=" "))
heavydat1<-gsub(pattern, "", heavydat)
words_heavy <- strsplit(heavydat1, " ")

final.list.heavy <- unlist(words_heavy)
not.blanks <- which(final.list.heavy != "")
final.list.heavy <- final.list.heavy[not.blanks]

dat.heavy<-sort(table(final.list.heavy),decreasing=TRUE)[1:15]
dat.heavy<-melt(dat.heavy)
dat.heavy1 <- transform(dat.heavy, final.list.heavy = reorder(final.list.heavy, value))
p1<-ggplot(dat.heavy1, aes(x = final.list.heavy, y = value,)) + geom_bar(stat="identity", color="cornsilk4", fill="cornsilk4")+coord_flip()

#Now do the same for black metal and death metal. 
{% endhighlight %}


  It almost makes me smile to look at the words thrown up. There also seem to be slight differences in the nomenclature of the bands of the three genres. **Heavy Metal bands** use somewhat reasonable words like "black", "steel", "fire", "angel", etc.;  words that describe typical metal themes. **Black Metal bands** names have a more ominous tinge - words like "death", "funeral", "winter", "infernal" etc. make an appearance. **Death Metal bands** clearly take things to an extreme with words like "blood", "hate", "flesh", "decay", "corpse" etc. featuring prominently in their names. 

{% highlight r %}
dat.heavy$final.list.heavy
{% endhighlight %}



{% highlight text %}
##  [1] black   steel   dark    fire    angel   force   death   iron   
##  [9] hell    wild    project x       evil    white   cross  
## 15 Levels: black steel dark fire angel force death iron hell ... cross
{% endhighlight %}



{% highlight r %}
dat.black$final.list.black
{% endhighlight %}



{% highlight text %}
##  [1] black     dark      death     blood     darkness  funeral   forest   
##  [8] eternal   infernal  winter    nocturnal lord      throne    moon     
## [15] goat     
## 15 Levels: black dark death blood darkness funeral forest ... goat
{% endhighlight %}



{% highlight r %}
dat.death$final.list.death
{% endhighlight %}



{% highlight text %}
##  [1] death   dead    blood   dark    hate    black   flesh   dawn   
##  [9] human   decay   chaos   corpse  eternal terror  pain   
## 15 Levels: death dead blood dark hate black flesh dawn human ... pain
{% endhighlight %}


{% highlight r %}
grid.arrange(p1,p2,p3, ncol=1)
{% endhighlight %}

<img src="http://velavar.github.io/images/metal_analysis_1/unnamed-chunk-8-1.png" title="center" alt="center" style="display: block; margin: auto;" />
  Many interesting words like "cross", "goat", "terror" etc. show up in the above lists; I would be interested in delving into the bands that named themselves so, if I ever have the time. Most names are in keeping with the spirit of the typical metal scene - despondent and dreary. Indeed I wonder if any metal band ever named themselves after something cheery.. if you know, I would love to hear about it. 

<br>**Resources:**
<br>1. Encyclopaedia Metallum: http://www.metal-archives.com/
<br>2. http://stackoverflow.com/questions/9249940/scraping-javascript-with-r
