## Predictive Machine Learning - Assignment



The goal of the assignment was to be able to predict reliably, the correctness with which the Weight-Lifting activity was being executed by the participant. The execution could belong to any of 5 classes - A (correct) or B,C,D,E (incorrect). 

### Preliminaries

A quick look at the data in MS Excel revealed 19622 observations of 159 variables. It also revealed the presence of a number of superfluous columns that were either empty or filled with NA values. These were easily removed and so were some other columns with unimportant information such as record number, name of participant etc. In this way, the dataset was whittled down to 52 predictor variables and the "classe" variable. 

### Creating Training and Testing sets

The data was stored into the dataframe *dat1*. 


{% highlight r %}
dat=read.csv("pml-training.csv",header=TRUE)
dattemp<-dat[,!sapply(dat,function(x) any(is.na(x)))]
dat1<-dattemp[,!sapply(dattemp, function(x) any(x == ""))]
{% endhighlight %}

The *createDataPartition* was used to divide the dataset into the **training** and **testing** sets. 


{% highlight r %}
inTrain=createDataPartition(y=dat1$classe,p=0.70,list=FALSE)
training<-dat1[inTrain,]
testing<-dat1[-inTrain,]
{% endhighlight %}

A quick check using the *princomp* function reveals that factoring the variables may probably not be very useful here. 


{% highlight r %}
dat2_pca<-princomp(formula=~.-classe,data=training,cor=TRUE,na.action=na.exclude)
plot(dat2_pca)
{% endhighlight %}

[Principal Components Analysis](dat2pca.jpg)

