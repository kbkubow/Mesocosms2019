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

#Count instances of vernalized/clone/week	
              hatchcount <- hatch[, .N, by=list(Clone, SampleWeek, Vernalized)]
              hatchcountsub <- hatchcount[Clone!="Mostly D8222" & Clone!="DBunk122"]
              hatchcountwide <- dcast(hatchcountsub, Clone ~ SampleWeek, value.var="N")

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
            
            
