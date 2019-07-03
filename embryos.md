Look at embryo genotypes over time in A/B tanks

```
#!/usr/bin/env Rscript

### libraries
        library(data.table)
        library(ggplot2)
        library(foreach)
        library(lattice)
        library(tidyr)
        library(tidyverse)

### Read in data file
        embryo <- fread("ABGelResults.csv")
        colnames(embryo) <- c("Week", "Plate", "Well", "Row", "Column", "Clone", "Info", "ID", "RowB", "ABHybrid", "Repeat")
        embryonomiss <- embryo[ABHybrid=="A" | ABHybrid=="B" | ABHybrid=="H"]
        embryonomissAB <- embryonomiss[1:293]
        
### Make subset data file where each ephippia is only represented once
        embryonomissAB$cloneid <- paste(embryonomissAB$Clone, embryonomissAB$ID, sep="_")
        embryonomissABbyepp <- embryonomissAB[!duplicated(embryonomissAB$cloneid),]

### Plot distribution of A, B, H by tank by week
        ggplot(data=embryonomissABbyepp, aes(x=as.factor(Week), y=ABHybrid, fill=ABHybrid)) + geom_col() + facet_wrap(~Clone)
