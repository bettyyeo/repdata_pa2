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

![](repdata_pa2_files/figure-html/unnamed-chunk-2-1.png) 



Records that we will ignore
1) before 1990 records

Group by events, sum the fatal and injuries cases and take the top 10 cases

2) Top 10 death records
3) Top 10  injury records


```r
library(plyr)
stormy <- storm[storm$bgn_year>=1990,]
filteredEvnt <- ddply(stormy, .(EVTYPE), summarize,
                    fatalities = sum(FATALITIES),
                    injuries = sum(INJURIES))
##stormyInj<-stormy[stormy$INJURIES>1,]
##stormyFat<-stormy[stormy$FATALITIES>=1,]
topfatal <- head(filteredEvnt[order(filteredEvnt$fatalities, decreasing = T), ], 10)
topInjury <- head(filteredEvnt[order(filteredEvnt$injuries, decreasing = T), ], 10)
```






###Results
####Most harmful type to population health
To measure harmful, i look at the number of deaths and injuries

#### By looking at the number of death cases, you have the clue which are the 3 top events that cause death


```
##             EVTYPE fatalities injuries
## 124 EXCESSIVE HEAT       1903     6525
## 826        TORNADO       1752    26674
## 151    FLASH FLOOD        978     1777
## 271           HEAT        937     2100
## 453      LIGHTNING        816     5230
## 167          FLOOD        470     6789
## 572    RIP CURRENT        368      232
## 846      TSTM WIND        327     5022
## 343      HIGH WIND        248     1137
## 19       AVALANCHE        224      170
```

![](repdata_pa2_files/figure-html/unnamed-chunk-4-1.png) 


#### By looking at the number of injuries cases, you have the clue which are the 3 top events that cause injuries



```
##                EVTYPE fatalities injuries
## 826           TORNADO       1752    26674
## 167             FLOOD        470     6789
## 124    EXCESSIVE HEAT       1903     6525
## 453         LIGHTNING        816     5230
## 846         TSTM WIND        327     5022
## 271              HEAT        937     2100
## 422         ICE STORM         89     1975
## 151       FLASH FLOOD        978     1777
## 753 THUNDERSTORM WIND        133     1488
## 962      WINTER STORM        206     1321
```

![](repdata_pa2_files/figure-html/unnamed-chunk-5-1.png) 

You can therefore draw conclusion on the events that are damaging to the public's health.



####Types of event with greatest economic consequences
To examine the economic consequences, let's look at the property damage and the crop damage

Need to convert the indicator of the damage index


You can also embed plots, for example:



