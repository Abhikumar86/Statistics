Boxplot
================
Abhishek Kumar
24 August 2020

Packages
--------

``` r
library(multcompView)
```

    ## Warning: package 'multcompView' was built under R version 4.0.2

``` r
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

Data
----

``` r
mydata <- read.csv("D:/Publications/Land_Degradation_&_Development/Data/Ldd_rep.csv")
dat <- mydata %>% select(Species:Legume, Np_Total, Bm_Foliage) %>%
      mutate(pi = Np_Total/Bm_Foliage)
```

ANOVA
-----

``` r
mod <- aov(pi~Species, data = dat)

TUKEY <- TukeyHSD(mod)

generate_label_df <- function(TUKEY, variable){
      
      Tukey.levels <- TUKEY[[variable]][,4]
      Tukey.labels <- data.frame(multcompLetters(Tukey.levels)['Letters'])
      Tukey.labels$Species = rownames(Tukey.labels)
      Tukey.labels = Tukey.labels[order(Tukey.labels$Species), ]
      
      return(Tukey.labels)
}

LABELS <- generate_label_df(TUKEY , "Species")

par(mar = c(5, 12, 4, 2), #margin for plot
    mgp = c(11, 1, 0) # axis title margin
    )
a <- boxplot(pi~Species, data = dat, horizontal = TRUE, yaxt = "none", 
        xlab = "Production Efficiency", ylim = c(2,6),
        col = terrain.colors(6)[as.factor(LABELS[,1])])

axis(2, at = 1:8, labels = unique(dat$Species), font = 3, las = 1)

text(y = c(1:nlevels(as.factor(dat$Species))), 
     x = a$stats[nrow(a$stats),] + 0.15, 
     labels = LABELS[,1])
```

![](Boxplot_Anova_files/figure-markdown_github/unnamed-chunk-3-1.png)

Source Credit: [R Graph Gallery](https://www.r-graph-gallery.com/84-tukey-test.html)
