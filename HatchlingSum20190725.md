#!/usr/bin/env Rscript

### libraries
        library(data.table)
        library(ggplot2)
        library(foreach)
        library(lattice)
        library(tidyr)
        library(tidyverse)

### Read in data file

hatch <- fread("HatchlingSummary20190725.csv")
hatch$Clone <- str_replace(hatch$Clone, "AXB", "AxB")

#Count instances of vernalized/clone/week	
              hatchcount <- hatch[, .N, by=list(Clone, SampleWeek, Vernalized)]
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


#Count instances of vernalized/week	
              hatchcountnoclone <- hatch[, .N, by=list(SampleWeek, Vernalized)]
              hatchcountnoclonewk4_6 <- hatchcountnoclone[SampleWeek=="4" | SampleWeek=="5" | SampleWeek=="6"]
              colnames(hatchcountnoclonewk4_6) <- c("SampleWeek", "Vernalized", "N")
              hatchcountnoclonewk4_6wide <- dcast(hatchcountnoclonewk4_6, SampleWeek~Vernalized, value.var="N")
              colnames(hatchcountnoclonewk4_6wide) <- c("SampleWeek", "NoVern", "Vern")
              ggplot(data=hatchcountnoclonewk4_6, aes(x=SampleWeek, y=N, fill=as.factor(Vernalized))) + geom_col()
              hatchcountnoclonewk4_6wide$propvernalized <- hatchcountnoclonewk4_6wide$Vern/
                (hatchcountnoclonewk4_6wide$Vern+hatchcountnoclonewk4_6wide$NoVern)
              ggplot(data=hatchcountnoclonewk4_6wide, aes(x=SampleWeek, y=propvernalized)) + geom_point()

#Count instances of vernalized/clone/week/rep	
              hatchcountrep <- hatch[, .N, by=list(Clone, Replicate, SampleWeek, Vernalized)]
              hatchcountrepsub <- hatchcountrep[Clone!="Mostly D8222" & Clone!="DBunk122"]
              hatchcountrepsubwk4_6 <- hatchcountrepsub[SampleWeek=="4" | SampleWeek=="5" | SampleWeek=="6"]
              ggplot(data=hatchcountrepsubwk4_6, aes(x=SampleWeek, y=N, fill=as.factor(Vernalized))) + geom_col() + facet_grid(Clone~Replicate)
              hatchcountrepsubwk4_6wide <- dcast(hatchcountrepsubwk4_6, Clone+Replicate+SampleWeek~Vernalized, value.var="N")
              colnames(hatchcountrepsubwk4_6wide) <- c("Clone", "Rep", "SampleWeek", "NoVern", "Vern")
              hatchcountrepsubwk4_6wide[is.na(NoVern),NoVern:=0]
              hatchcountrepsubwk4_6wide[is.na(Vern),Vern:=0]
              hatchcountrepsubwk4_6wide$propvernalized <- hatchcountrepsubwk4_6wide$Vern/
                (hatchcountrepsubwk4_6wide$Vern+hatchcountrepsubwk4_6wide$NoVern)
              ggplot(data=hatchcountrepsubwk4_6wide, aes(x=SampleWeek, y=propvernalized)) + geom_point() + facet_grid(Clone~Rep)
           
            
