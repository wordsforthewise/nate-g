---
title: "Grupo Bimbo R Analysis and Prediction"
author: "Nate George"
date: "9/11/2016"
output: html_document
runtime: shiny
---

Recently I've been ramping up my machine learning skills and competing in competitions.  The [Grupo Bimbo competition on Kaggle.com](https://www.kaggle.com/c/grupo-bimbo-inventory-demand/) just ended a few days ago, so I thought I'd share a summary of the competition and one solution.

## The problem
Grupo Bimbo delivers many products to a plethora of stores around Mexico.  Bimbo will take returns from stores when a product has expired, ostensibly to protect their brand reputation.  Ideally, Bimbo doesn't want to take any returns, and would like to deliver the exact amount of product that each store needs on a weekly basis.

The main goal of the competition is to predict what the demand for certain products will be for various clients (grocery stores/shops) in Mexico.  The data provided is 7 weeks worth of orders and returns (labeled as weeks 3-9) from the stores Bimbo serves in Mexico.  The competition is scored based on 2 weeks of data, where the demand is hidden (labeled weeks 10 and 11).

## Preliminary analyses
First, let's take a look at the data in general.  We need to load some libraries/packages before going further:


```r
library(data.table)
library(ggplot2)
library(treemap)
library(dplyr) # seems to be necessary for %>% operator for me
library(xgboost)
```

I was running some of these scripts on an AWS server, and some on my laptop, so I created this small snippet to find the correct path:

```r
paths = c("/home/ubuntu/kaggle/bimbo/input/", "/media/nate/Windows/kaggle/bimbo/input/")

for (p in paths){
  if (file.exists(paste(p, 'train.csv', sep=''))){
    print(paste('path is', p))
    break
  }
}
```

```
## [1] "path is /home/ubuntu/kaggle/bimbo/input/"
```

Using the `data.table` package, we can read csv files much faster than with `readr`'s `read_csv()` or other csv reading functions in R:


```r
test <- fread(paste0(p, 'test.csv'))
prods <- fread(paste0(p, 'producto_tabla.csv'))
town <- fread(paste0(p, 'town_state.csv'))
client <- fread(paste0(p, 'cliente_tabla.csv'))
```


```r
# plot distribution of weeks - we can see the distribution of weeks is roughly equal
ggplot(test %>% sample_frac(0.1)) +
  geom_histogram(aes(x = Semana), color = "black", fill = "red", alpha = 0.5)+
  scale_x_continuous(breaks = 1:10)+
  scale_y_continuous(name = "Client / Product deliveries")+
  theme_bw()
```

![plot of chunk plot_week_dist](figure/plot_week_dist-1.png)

```r
gc()
```
