# Coursera Reproducible Research : PA2
Betty Yeo  
20 December 2014  


##Stormy Data
The basic goal of this assignment is to explore the NOAA Storm Database and answer some basic questions about severe weather events. 
Required to use the database to answer the questions:

1. Across the United States, which types of events (as indicated in the EVTYPE variable) are most harmful with respect to population health?

2. Across the United States, which types of events have the greatest economic consequences?

###Sypnosis
.. to include a brief summary of the findings...

###Data processing
1. set working directory
setwd("~/Downloads/repdata_PA2")
2. Read the data

```r
cache=TRUE
storm <- read.csv("repdata-data-StormData.csv")
```

Since the intial data doesn't really have good recordsets,
decide to use the data to reduce any anomalise. so let's filter the records based on the year


```r
storm$bgn_year <-format(as.Date(storm$BGN_DATE, format = "%m/%d/%Y %H:%M:%S"), "%Y")

hist(as.numeric(storm$bgn_year))
```

![](repdata_pa2__files/figure-html/unnamed-chunk-2-1.png) 



1) Ignore all records records before 1990
2) Filter Top 10 death records
3) Filter Top 10  injury records


```r
library(plyr)
stormy <- storm[storm$bgn_year>=1990,]
stormy<- stormy[, c("EVTYPE","FATALITIES","INJURIES", "CROPDMG","CROPDMGEXP","PROPDMG","PROPDMGEXP")]

filteredEvnt <- ddply(stormy, .(EVTYPE), summarize,
                    fatalities = sum(FATALITIES),
                    injuries = sum(INJURIES))
topfatal <- head(filteredEvnt[order(filteredEvnt$fatalities, decreasing = T), ], 10)
topInjury <- head(filteredEvnt[order(filteredEvnt$injuries, decreasing = T), ], 10)
```

###Results
####Most harmful type to population health
To measure harmful, i look at the number of deaths and injuries.
By looking at the number of death cases, you have the clue which are the 3 top events that cause death

![](repdata_pa2__files/figure-html/unnamed-chunk-4-1.png) 


By looking at the number of injuries cases, you have the clue which are the 3 top events that cause injuries


![](repdata_pa2__files/figure-html/unnamed-chunk-5-1.png) 

You can therefore draw conclusion on the events that are damaging to the public's health by looking at the highest casualty event and those that cause injuries. The indirect cases are not included in the analysis.

####Types of event with greatest economic consequences
To examine the economic consequences, let's look at the property damage and the crop damage

Crop damages:
1. Only filter out those records that have crop damages incidences
2. apply the mulitplier which is in the field "CROPDMGEXP" and put them in the field "actualcropdmg"
3. Look at the top 10 events that cause the greatest crop damages



```r
cropdmg <- stormy[stormy$CROPDMG>0,]


for (i in 1:nrow(cropdmg)){
  tmp <- cropdmg$CROPDMGEXP[i]
  mul <- cropdmg$CROPDMG[i]
    if (tmp  %in% c('h', 'H'))
        cropdmg$actualcropdmg[i] <- mul * 100
    else if (tmp %in% c('k', 'K'))
        cropdmg$actualcropdmg[i] <- mul * 1000
    else if (tmp %in% c('m', 'M'))
        cropdmg$actualcropdmg[i] <- mul * 1000000
    else if (tmp %in% c('b', 'B'))
        cropdmg$actualcropdmg[i] <- mul * 1000000000
}

cropdmgSum <- ddply(cropdmg, .(EVTYPE), summarize,
                    cropdmg = sum(actualcropdmg))

topCropDmg <- head(cropdmgSum[order(cropdmgSum$cropdmg, decreasing = T), ], 10)

ggplot(data = topCropDmg, aes(x = topCropDmg$EVTYPE, y = topCropDmg$cropdmg)) + 
    geom_bar(colour = "black", fill = "red", stat = "identity") + xlab("Event Type") + 
    ylab("Crop Damages") + ggtitle("Top 10 Crop-damanged-causing Events (1900 - 2011)") +
    theme(axis.text.x = element_text(angle = 45, hjust = 1))
```

![](repdata_pa2__files/figure-html/unnamed-chunk-6-1.png) 




```r
propdmg <- stormy[stormy$PROPDMG>0,]

for (i in 1:nrow(propdmg)){
  tmp <- propdmg$PROPDMGEXP[i]
  mul <- propdmg$PROPDMG[i]
    if (tmp  %in% c('h', 'H'))
        propdmg$actualpropdmg[i] <- mul * 100
    else if (tmp %in% c('k', 'K'))
        propdmg$actualpropdmg[i] <- mul * 1000
    else if (tmp %in% c('m', 'M'))
        propdmg$actualpropdmg[i] <- mul * 1000000
    else if (tmp %in% c('b', 'B'))
        propdmg$actualpropdmg[i] <- mul * 1000000000
}

propdmgSum <- ddply(propdmg, .(EVTYPE), summarize,
                    propdmg = sum(actualpropdmg))

topPropDmg <- head(propdmgSum[order(propdmgSum$propdmg, decreasing = T), ], 10)

ggplot(data = topCropDmg, aes(x = topPropDmg$EVTYPE, y = topPropDmg$propdmg)) + 
    geom_bar(colour = "black", fill = "red", stat = "identity") + xlab("Event Type") + 
    ylab("Prop Damages") + ggtitle("Top 10 Prop-damanged-causing Events (1900 - 2011)") +
    theme(axis.text.x = element_text(angle = 45, hjust = 1))
```

![](repdata_pa2__files/figure-html/unnamed-chunk-7-1.png) 






