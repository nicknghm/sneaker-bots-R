---
title: "sneaker-bot-project"
author: "Nick"
date: "8/20/2021"
output: 
  html_document: 
    keep_md: yes
  md_document:
    preserve_yaml:false
---
## This is a project about bots... sneaker bots.

This is a passion project to practice my data analysis skills with R, while combining my love for the world of sneakers. 

With sneakers become more and more coveted, and a wider number of people getting into them, some have turned to using "bots", which use a combination of unconventional and interesting methods to allow a user to run hundreds of tasks (appearing as hundres of unique users with the help of residential / data center proxies) on a given website. This allows the botter to have a higher chance of not only getting limited shoes, but also in volumes that the average person would almost find impossible to do manually without it.

Bots are created by different software developers, and thus vary in their quality and performance on different sites. This project just aims to show trends between certain bots, and why some are cheaper than others.

This project analyzes data from [the BotBroker website](https://botbroker.io/bots), pulled out from the website as a list of lists with the help of Postman. 

The data in this project was pulled on the 11th of August, 2021. 

Let's see how this turns out!

First, we'll install the necessary packages if you need them.

```r
## install.packages("tidyverse")
## install.packages("jsonlite")
## install.packages("lubridate:)
```

Next, run the packages.

```r
library(tidyverse)
library(jsonlite)
library(lubridate)
```

### Initial data pull

We'll first construct a snippet that can efficiently allow us to convert file path backslashes to forward slashes. You can insert this code into your global options. For a more detailed explanation snippets, you can [find it on rstudio](https://support.rstudio.com/hc/en-us/articles/204463668-Code-Snippets). You could also use a program like Path Copy Copy.


```r
## snippet pp
##    "`r gsub('"', "", gsub("\\\\", "/", readClipboard()))`"
```

At this point, there are 27 json files that need to be converted into a huge data frame containing all bot transaction data. 

The additional obstacle is that some have lifetime copies, and some only have renewal copies.

The endgame of this data should contain

1. A column of factors containing bot names
2. Date time column of transaction (in datetime format)
3. Price transacted (int)
4. A column that differentiates lifetime and renewal copies (which could also be factors)
5. What sites the bot supports (Shopify, YeezySupply, Footsites, Supreme)

### Converting JSON files into R objects

After the data was pulled from Botbroker, they were extracted as JSON files with the help of Postman, and transformed to an R friendly format using the function `fromJSON()` from the `jsonlite` library.

This converts the JSON file into two character columns, transaction time and price sold.

Note backslashes have to be converted to forward slashes.


```r
balko_lt <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/balko_lt.json")
balko_r <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/balko_renewal.json")
carnage_r <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/carnage_renewal.json")
cyber_lt <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/cyber_lt.json")
cyber_r <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/cyber_renewal.json")
dashe_lt <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/dashe_lt.json")
dashe_r <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/dashe_renewal.json")
ganesh_lt <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/ganesh_lt.json")
ganesh_r <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/ganesh_renewal.json")
kage_r <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/kage_renewal.json")
mekaio_r <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/mekaio_renewal.json")
mekpreme_lt <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/mekpreme_lt.json")
mekpreme_r <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/mekpreme_renewal.json")
nebula_lt <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/nebula_lt.json")
nebula_r <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/nebula_renewal.json")
ominous_r <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/ominous_renewal.json")
pd_lt <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/pd_lt.json")
pd_r <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/pd_renewal.json")
prism_r <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/prism_renewal.json")
scottbott_r <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/scottbot_renewal.json")
splashforce_r <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/splashforce_renewal.json")
swftaio_r <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/swftaio_renewal.json")
tohru_r <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/tohru_renewal.json")
velox_lt <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/velox_lifetime.json")
velox_r <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/velox_renewal.json")
wrath_lt <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/wrath_lifetime.json")
wrath_r <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/wrath_renewal.json")
```

### Transforming data into correct data types. 

This process returns us a 2 column character matrix, of transaction date and price that looks like this:


```r
str(balko_lt)
```

```
##  chr [1:359, 1:2] "2019-12-29T20:35:42.818Z" "2019-12-10T22:02:22.849Z" ...
```

```r
head(balko_lt)
```

```
##      [,1]                       [,2]  
## [1,] "2019-12-29T20:35:42.818Z" "2490"
## [2,] "2019-12-10T22:02:22.849Z" "2200"
## [3,] "2019-12-20T03:11:07.109Z" "1510"
## [4,] "2019-12-24T13:47:05.384Z" "1910"
## [5,] "2020-01-12T17:12:10.301Z" "2300"
## [6,] "2020-01-16T00:12:52.320Z" "2350"
```
Thus, we'll have to rename and change the columns into the right data types - date time and numeric before we go further, which also forces us to turn these matrixes into tibbles for easy wrangling later on.

We'll first turn this matrix into a tibble.


```r
balko_lt_e <- balko_lt %>% 
  as_tibble() %>% 
  set_names(c("transaction_date", "price"))
```

```
## Warning: The `x` argument of `as_tibble.matrix()` must have unique column names if `.name_repair` is omitted as of tibble 2.0.0.
## Using compatibility `.name_repair`.
```
Next, we'll turn the columns into the right datatypes.


```r
balko_lt_e <- balko_lt_e %>%
  mutate(price = as.numeric(unlist(balko_lt_e$price))) %>% 
  mutate(transaction_date = as_datetime(str_replace_all(balko_lt_e$transaction_date, "T|Z", " ")))

str(balko_lt_e)
```

```
## tibble [359 x 2] (S3: tbl_df/tbl/data.frame)
##  $ transaction_date: POSIXct[1:359], format: "2019-12-29 20:35:42" "2019-12-10 22:02:22" ...
##  $ price           : num [1:359] 2490 2200 1510 1910 2300 ...
```
Now that one is done, all we have to do is do it another 26 times :)


