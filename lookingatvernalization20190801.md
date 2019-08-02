#!/usr/bin/env Rscript

### libraries
        library(data.table)
        library(ggplot2)
        library(foreach)
        library(lattice)
        library(tidyr)
        library(tidyverse)

### Read in data file

hatch <- fread("PlayingAround20190801.csv")
hatch$Clone <- str_replace(hatch$Clone, "AXB", "AxB")
hatch$deadbefrep <- ifelse(hatch$Dead=="1" & hatch$Reproduced!="1", 1, 0)

#Count instances of vernalized/clone/week	
              hatchcount <- hatch[, .N, by=list(Clone, SampleWeek, Vernalized, deadbefrep)]
              hatchcountsub <- hatchcount[Clone!="Mostly D8222" & Clone!="DBunk122"]
              hatchcountsubwk4_6 <- hatchcountsub[SampleWeek=="4" | SampleWeek=="5" | SampleWeek=="6"]
              ggplot(data=hatchcountsubwk4_6, aes(x=SampleWeek, y=N, fill=as.factor(Vernalized))) + geom_col() + facet_wrap(~Clone)
              hatchcountwk4_6wide <- dcast(hatchcountsubwk4_6, Clone+SampleWeek~Vernalized, value.var="N")
              colnames(hatchcountwk4_6wide) <- c("Clone", "SampleWeek", "NoVern", "Vern")
              hatchcountwk4_6wide[is.na(NoVern),NoVern:=0]
              hatchcountwk4_6wide[is.na(Vern),Vern:=0]
              hatchcountwk4_6wide$propvernalized <- hatchcountwk4_6wide$Vern/
                (hatchcountwk4_6wide$Vern+hatchcountwk4_6wide$NoVern)
              ggplot(data=hatchcountwk4_6wide, aes(x=SampleWeek, y=propvernalized)) + geom_point() + facet_wrap(~Clone)

#Count instances of vernalized/clone/week	
              hatchcount <- hatch[, .N, by=list(Clone, Vernalized, deadbefrep)]
              hatchcountsub <- hatchcount[Clone!="Mostly D8222" & Clone!="DBunk122"]
              ggplot(data=hatchcountsub, aes(x=as.factor(Vernalized), y=N, fill=as.factor(deadbefrep))) + geom_col() + facet_wrap(~Clone)
 
              hatchcountSW <- hatch[, .N, by=list(Clone, SampleWeek, deadbefrep)]
              hatchcountSWsub <- hatchcountSW[Clone!="Mostly D8222" & Clone!="DBunk122"]
              ggplot(data=hatchcountSWsub[SampleWeek > 3 & SampleWeek < 7], aes(x=as.factor(SampleWeek), y=N, fill=as.factor(deadbefrep))) + geom_col() + facet_wrap(~Clone)
              hatchcountSWsubwide <- dcast(hatchcountSWsub, Clone+SampleWeek~deadbefrep, value.var="N")
              colnames(hatchcountSWsubwide) <- c("Clone", "SampleWeek", "Rep", "DeadbefRep")
              hatchcountSWsubwide[is.na(Rep),Rep:=0]
              hatchcountSWsubwide[is.na(DeadbefRep),DeadbefRep:=0]
              hatchcountSWsubwide$propdeadbefrep <- hatchcountSWsubwide$DeadbefRep/
                (hatchcountSWsubwide$DeadbefRep+hatchcountSWsubwide$Rep)
              ggplot(data=hatchcountSWsubwide[SampleWeek > 3 & SampleWeek < 7], aes(x=SampleWeek, y=propdeadbefrep)) + geom_point() + facet_wrap(~Clone)
