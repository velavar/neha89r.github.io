---
layout: post
title: Hello World in Brainfk
---


This weekend, in a fit of boredom I thought I'd give Brainf\*\*k a go. One of the most popular and recognized esoteric programming languages, I can't think of any real advantages that Brainf\*\*k provides (If you can, let me know :) ). [Richie Cotton](http://4dpiecharts.com/2013/04/24/a-brainfuck-interpreter-for-r/) has very kindly coded an interpreter for Brainf\*\*k; you may find the code for the same [here](https://bitbucket.org/richierocks/brainfuck). I use his interpreter to write a *HelloWorld* program and a program that prints the integers 0 to 9. 




{% highlight r %}
if(!exists("foo", mode="function")) source("brainfuck.R")
{% endhighlight %}

Here goes the *HelloWorld* code:

{% highlight r %}
helloworld<-"
+++++ +++++[>+++++ +++++<-] >++++.---.+++++ ++..+++.>>>+++[>+++++ +++++<-]
>++.<<<<+++++ +++.----- ---.+++.----- -.----- ---.>>+++++ +++++.

"
bfi <- fuckbrain()
bfi$interpret(helloworld)
{% endhighlight %}



{% highlight text %}
## hello world
{% endhighlight %}

And this beauty prints the integers 0 to 9:

{% highlight r %}
print0to9 <- "++++[>+++++ +++++<-]+++++ +++++
>+++++ ++<[>+.<-]
"
bfi <- fuckbrain()
bfi$interpret(print0to9)
{% endhighlight %}



{% highlight text %}
## 0123456789
{% endhighlight %}

In keeping with the spirit of these languages, I haven't included any comments to help understand the code. If you need more understandable code, well-commented Brainf\*\*k HelloWorld codes are present dime a dozen online. This [Stackoverflow post](http://stackoverflow.com/questions/16836860/how-does-the-brainfuck-hello-world-actually-work) gives a very engaging introduction to the language. 
<br>
The codes here are just something I threw together to see if I could get the hang of this language and are neither optimised for length nor efficiency. I would love to know how they can be improved on both counts, if you have any thoughts. 

**Resources: **
<br>
1. http://4dpiecharts.com/2013/04/24/a-brainfuck-interpreter-for-r/
<br>
2. http://stackoverflow.com/questions/16836860/how-does-the-brainfuck-hello-world-actually-work
<br>
3. http://en.wikipedia.org/wiki/Brainfuck
