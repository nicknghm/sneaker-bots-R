---
title: "sneaker-bot-project"
author: "Nick"
date: "8/20/2021"
output: 
  html_document: 
    keep_md: yes
---
## This is a project about bots... sneaker bots!

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
## install.packages("lubridate")
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
scottbot_r <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/scottbot_renewal.json")
splashforce_r <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/splashforce_renewal.json")
swftaio_r <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/swftaio_renewal.json")
tohru_r <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/tohru_renewal.json")
velox_lt <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/velox_lifetime.json")
velox_r <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/velox_renewal.json")
wrath_lt <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/wrath_lifetime.json")
wrath_r <- fromJSON("C:/Users/herkm/Desktop/R Workspace/sneaker-bots-R/bot-data/wrath_renewal.json")
```
### Transforming data into correct data types 

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


```r
balko_r_e <- balko_r %>% 
  as_tibble() %>% 
  set_names(c("transaction_date", "price"))

balko_r_e <- balko_r_e %>%
  mutate(price = as.numeric(unlist(balko_r_e$price))) %>% 
  mutate(transaction_date = as_datetime(str_replace_all(balko_r_e$transaction_date, "T|Z", " ")))

str(balko_r_e)
```

```
## tibble [2,351 x 2] (S3: tbl_df/tbl/data.frame)
##  $ transaction_date: POSIXct[1:2351], format: "2020-02-02 23:05:38" "2019-12-20 01:29:09" ...
##  $ price           : num [1:2351] 2699 1576 1990 1700 1774 ...
```

It's probably time to create some functions...


```r
bb_tibbler <- function(bot_data) {
  bot_data %>% 
  as_tibble() %>% 
  set_names(c("transaction_date", "price"))
}

bb_tibbled_formatter <- function(bot_df) {
  bot_df %>% 
  mutate(price = as.numeric(unlist(bot_df$price))) %>% 
  mutate(transaction_date = as_datetime(str_replace_all(bot_df$transaction_date, "T|Z", " ")))
}
```

Alright, on with the rest of the bots. (Putting the trial bot here as well for a compiled chunk to run)


```r
#balko
balko_r_e <- bb_tibbler(balko_r)
balko_r_e <- bb_tibbled_formatter(balko_r_e)
str(balko_r_e)

balko_lt_e <- bb_tibbler(balko_lt)
balko_lt_e <- bb_tibbled_formatter(balko_lt_e)
str(balko_lt_e)

#carnage
carnage_r_e <- bb_tibbler(carnage_r)
carnage_r_e <- bb_tibbled_formatter(carnage_r_e)
str(carnage_r_e)

#cyber
cyber_r_e <- bb_tibbler(cyber_r)
cyber_r_e <- bb_tibbled_formatter(cyber_r_e)
str(cyber_r_e)

cyber_lt_e <- bb_tibbler(cyber_lt)
cyber_lt_e <- bb_tibbled_formatter(cyber_lt_e)
str(cyber_lt_e)

#dashe
dashe_r_e <- bb_tibbler(dashe_r)
dashe_r_e <- bb_tibbled_formatter(dashe_r_e)
str(dashe_r_e)

dashe_lt_e <- bb_tibbler(dashe_lt)
dashe_lt_e <- bb_tibbled_formatter(dashe_lt_e)
str(dashe_lt_e)

#ganesh
ganesh_r_e <- bb_tibbler(ganesh_r)
ganesh_r_e <- bb_tibbled_formatter(ganesh_r_e)
str(dashe_r_e)

ganesh_lt_e <- bb_tibbler(ganesh_lt)
ganesh_lt_e <- bb_tibbled_formatter(ganesh_lt_e)
str(dashe_lt_e)

#kage
kage_r_e <- bb_tibbler(kage_r)
kage_r_e <- bb_tibbled_formatter(kage_r_e)
str(kage_r_e)

#mekaio
mekaio_r_e <- bb_tibbler(mekaio_r)
mekaio_r_e <- bb_tibbled_formatter(mekaio_r_e)
str(mekaio_r_e)

#mekpreme
mekpreme_r_e <- bb_tibbler(mekpreme_r)
mekpreme_r_e <- bb_tibbled_formatter(mekpreme_r_e)
str(mekpreme_r_e)

mekpreme_lt_e <- bb_tibbler(mekpreme_lt)
mekpreme_lt_e <- bb_tibbled_formatter(mekpreme_lt_e)
str(mekpreme_lt_e)

#nebula
nebula_r_e <- bb_tibbler(nebula_r)
nebula_r_e <- bb_tibbled_formatter(nebula_r_e)
str(mekpreme_r_e)

nebula_lt_e <- bb_tibbler(nebula_lt)
nebula_lt_e <- bb_tibbled_formatter(nebula_lt_e)
str(nebula_lt_e)

#ominous
ominous_r_e <- bb_tibbler(ominous_r)
ominous_r_e <- bb_tibbled_formatter(ominous_r_e)
str(ominous_r_e)

#pd
pd_r_e <- bb_tibbler(pd_r)
pd_r_e <- bb_tibbled_formatter(pd_r_e)
str(pd_r_e)

pd_lt_e <- bb_tibbler(pd_lt)
pd_lt_e <- bb_tibbled_formatter(pd_lt_e)
str(pd_lt_e)

#prism
prism_r_e <- bb_tibbler(prism_r)
prism_r_e <- bb_tibbled_formatter(prism_r_e)
str(prism_r_e)

#scottbot
scottbot_r_e <- bb_tibbler(scottbot_r)
scottbot_r_e <- bb_tibbled_formatter(scottbot_r_e)
str(scottbot_r_e)

#splashforce
splashforce_r_e <- bb_tibbler(splashforce_r)
splashforce_r_e <- bb_tibbled_formatter(splashforce_r_e)
str(splashforce_r_e)

#swftaio
swftaio_r_e <- bb_tibbler(swftaio_r)
swftaio_r_e <- bb_tibbled_formatter(swftaio_r_e)
str(swftaio_r_e)

#tohru
tohru_r_e <- bb_tibbler(tohru_r)
tohru_r_e <- bb_tibbled_formatter(tohru_r_e)
str(tohru_r_e)

#velox
velox_r_e <- bb_tibbler(velox_r)
velox_r_e <- bb_tibbled_formatter(velox_r_e)
str(velox_r_e)

velox_lt_e <- bb_tibbler(velox_lt)
velox_lt_e <- bb_tibbled_formatter(velox_lt_e)
str(velox_lt_e)

#wrath
wrath_r_e <- bb_tibbler(wrath_r)
wrath_r_e <- bb_tibbled_formatter(wrath_r_e)
str(wrath_r_e)

wrath_lt_e <- bb_tibbler(wrath_lt)
wrath_lt_e <- bb_tibbled_formatter(wrath_lt_e)
str(wrath_lt_e)
```
### Mutate() for the additional data we need before joining tables together

Before we join the tables, we need to first `mutate()` two columns onto each tibble, one that shows the key type (lifetime or renewal) of the bot, and another that shows the name of the bot (so we can have a tidy, long rather that wide, dataset) before we join all rows together to form a giant dataframe of all bots.

The tables should all have four variables with the same column names, ordered as such: `bot_name`, `key_type`, `transaction_date`, and `price`. We'll do this with the help of `select()`, 


```r
#balko
balko_lt_e <- balko_lt_e %>% 
  mutate(key_type = "lifetime", bot_name = "balko") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

balko_r_e <- balko_r_e %>% 
  mutate(key_type = "renewal", bot_name = "balko") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

#carnage
carnage_r_e <- carnage_r_e %>% 
  mutate(key_type = "renewal", bot_name = "carnage") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

#cyber
cyber_lt_e <- cyber_lt_e %>% 
  mutate(key_type = "lifetime", bot_name = "cyber") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

cyber_r_e <- cyber_r_e %>% 
  mutate(key_type = "renewal", bot_name = "cyber") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

#dashe
dashe_lt_e <- dashe_lt_e %>% 
  mutate(key_type = "lifetime", bot_name = "dashe") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

dashe_r_e <- dashe_r_e %>% 
  mutate(key_type = "renewal", bot_name = "dashe") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

#ganesh
ganesh_lt_e <- ganesh_lt_e %>% 
  mutate(key_type = "lifetime", bot_name = "ganesh") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

ganesh_r_e <- ganesh_r_e %>% 
  mutate(key_type = "renewal", bot_name = "ganesh") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

#kage
kage_r_e <- kage_r_e %>% 
  mutate(key_type = "renewal", bot_name = "kage") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

#mekaio
mekaio_r_e <- mekaio_r_e %>% 
  mutate(key_type = "renewal", bot_name = "mekaio") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

#mekpreme
mekpreme_lt_e <- mekpreme_lt_e %>% 
  mutate(key_type = "lifetime", bot_name = "mekpreme") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

mekpreme_r_e <- mekpreme_r_e %>% 
  mutate(key_type = "renewal", bot_name = "mekpreme") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

#nebula
nebula_lt_e <- nebula_lt_e %>% 
  mutate(key_type = "lifetime", bot_name = "nebula") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

nebula_r_e <- nebula_r_e %>% 
  mutate(key_type = "renewal", bot_name = "nebula") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

#ominous
ominous_r_e <- ominous_r_e %>% 
  mutate(key_type = "renewal", bot_name = "ominous") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

#pd
pd_lt_e <- pd_lt_e %>% 
  mutate(key_type = "lifetime", bot_name = "pd") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

pd_r_e <- pd_r_e %>% 
  mutate(key_type = "renewal", bot_name = "pd") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

#prism
prism_r_e <- prism_r_e %>% 
  mutate(key_type = "renewal", bot_name = "prism") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

#scottbot
scottbot_r_e <- scottbot_r_e %>% 
  mutate(key_type = "renewal", bot_name = "scottbot") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

#splashforce
splashforce_r_e <- splashforce_r_e %>% 
  mutate(key_type = "renewal", bot_name = "splashforce") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

#swftaio
swftaio_r_e <- swftaio_r_e %>% 
  mutate(key_type = "renewal", bot_name = "swftaio") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

#tohru
tohru_r_e <- tohru_r_e %>% 
  mutate(key_type = "tohru", bot_name = "tohru") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

#velox
velox_lt_e <- velox_lt_e %>% 
  mutate(key_type = "lifetime", bot_name = "velox") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

velox_r_e <- velox_r_e %>% 
  mutate(key_type = "renewal", bot_name = "velox") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

#wrath
wrath_lt_e <- wrath_lt_e %>% 
  mutate(key_type = "lifetime", bot_name = "wrath") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)

wrath_r_e <- wrath_r_e %>%  
  mutate(key_type = "renewal", bot_name = "wrath") %>% 
  arrange(transaction_date) %>% 
  select(bot_name, key_type, transaction_date, price)
```

### Time to join tables!

Now that all JSON files have been wrangled into tibbles with the right data types, it's time to join tables that have both renewal and lifetime copies.

These bots are - balko, cyber, dashe, ganesh, mekpreme, nebula, pd, velox, and wrath.

We'll use the `bind_rows()` function from `dplyr` to achieve this.


```r
balko_lt_r_e <- bind_rows(balko_lt_e, balko_r_e)
cyber_lt_r_e <- bind_rows(cyber_lt_e, cyber_r_e)
dashe_lt_r_e <- bind_rows(dashe_lt_e, dashe_r_e)
ganesh_lt_r_e <- bind_rows(ganesh_lt_e, ganesh_r_e)
mekpreme_lt_r_e <- bind_rows(mekpreme_lt_e, mekpreme_r_e)
nebula_lt_r_e <- bind_rows(nebula_lt_e, nebula_r_e)
pd_lt_r_e <- bind_rows(pd_lt_e, pd_r_e)
velox_lt_r_e <- bind_rows(velox_lt_e, velox_r_e)
wrath_lt_r_e <- bind_rows(wrath_lt_e, wrath_r_e)
```
### Different bots bot different sites

One of the other things we should add at this point is what sites the bots support. The main sites to consider are Shopify, Footsites (FootLocker, Eastbay, Champs, FootAction), YeezySupply, Supreme, Collectibles (Funko), Retail (amazon, walmart), Mesh(JD, footpatrol, size?). 

As we will be converting them into factors later on, if the bot supports a site, the value will be the site name (e.g. `shopify`), but if it does not, the value in the column will have 'no' in front of it (e.g. `no_shopify`) so we can assign levels to each column later on.

Also, columns need to be created for bots that are US focused, and EU focused.

Alright, onto the next `mutate()` chunk.


```r
#balko - shopify 
balko_lt_r_e <- balko_lt_r_e %>% 
  mutate(shopify = "shopify", footsites = "no_footsites", yeezysupply = "no_yeezysupply", supreme = "no_supreme", collectibles = "collectibles", retail = "no_retail", mesh = "no_mesh", focus = "US")

#carnage - collectibles, retail
carnage_r_e <- carnage_r_e %>% 
  mutate(shopify = "no_shopify", footsites = "no_footsites", yeezysupply = "no_yeezysupply", supreme = "no_supreme", collectibles = "collectibles", retail = "retail", mesh = "no_mesh", focus = "EU")

#cyber - shopify, footsites, ys, supreme
cyber_lt_r_e <- cyber_lt_r_e %>% 
  mutate(shopify = "shopify", footsites = "footsites", yeezysupply = "yeezysupply", supreme = "supreme", collectibles = "no_collectibles", retail = "no_retail", mesh = "no_mesh", focus = "BOTH")

#dashe - shopify, ys, supreme
dashe_lt_r_e <- dashe_lt_r_e %>% 
  mutate(shopify = "shopify", footsites = "no_footsites", yeezysupply = "yeezysupply", supreme = "supreme", collectibles = "no_collectibles", retail = "no_retail", mesh = "no_mesh", focus = "US")

#ganesh - footsites, ys, mesh
ganesh_lt_r_e <- ganesh_lt_r_e %>% 
  mutate(shopify = "no_shopify", footsites = "footsites", yeezysupply = "yeezysupply", supreme = "no_supreme", collectibles = "no_collectibles", retail = "no_retail", mesh = "mesh", focus = "BOTH")

#kage - footsites, retail
kage_r_e <- kage_r_e %>% 
  mutate(shopify = "shopify", footsites = "no_footsites", yeezysupply = "no_yeezysupply", supreme = "no_supreme", collectibles = "no_collectibles", retail = "retail", mesh = "no_mesh", focus = "US")

#mekaio - shopify, footsites, ys
mekaio_r_e <- mekaio_r_e %>% 
  mutate(shopify = "shopify", footsites = "footsites", yeezysupply = "yeezysupply", supreme = "no_supreme", collectibles = "no_collectibles", retail = "no_retail", mesh = "no_mesh", focus = "US")

#mekpreme - supreme
mekpreme_lt_r_e <- mekpreme_lt_r_e %>% 
  mutate(shopify = "no_shopify", footsites = "no_footsites", yeezysupply = "no_yeezysupply", supreme = "supreme", collectibles = "no_collectibles", retail = "no_retail", mesh = "no_mesh", focus = "BOTH")

#nebula - shopify, footsites, yeezysupply
nebula_lt_r_e <- nebula_lt_r_e %>% 
  mutate(shopify = "shopify", footsites = "footsites", yeezysupply = "yeezysupply", supreme = "no_supreme", collectibles = "no_collectibles", retail = "no_retail", mesh = "no_mesh", focus = "US")

#ominous - footsites, yeezysupply, retail
ominous_r_e <- ominous_r_e %>% 
  mutate(shopify = "no_shopify", footsites = "footsites", yeezysupply = "yeezysupply", supreme = "no_supreme", collectibles = "no_collectibles", retail = "retail", mesh = "no_mesh", focus = "US")

#pd - shopify, footsites, yeezysupply
pd_lt_r_e <- pd_lt_r_e %>% 
  mutate(shopify = "shopify", footsites = "footsites", yeezysupply = "yeezysupply", supreme = "no_supreme", collectibles = "no_collectibles", retail = "no_retail", mesh = "no_mesh", focus = "US")

#prism - shopify, footsites, yeezysupply
prism_r_e <- prism_r_e %>% 
  mutate(shopify = "shopify", footsites = "footsites", yeezysupply = "yeezysupply", supreme = "no_supreme", collectibles = "no_collectibles", retail = "no_retail", mesh = "no_mesh", focus = "US")

#scottbot - collectibles
scottbot_r_e <- scottbot_r_e %>% 
  mutate(shopify = "no_shopify", footsites = "no_footsites", yeezysupply = "no_yeezysupply", supreme = "no_supreme", collectibles = "collectibles", retail = "retail", mesh = "no_mesh", focus = "US")

#splashforce - footsites, yeezysupply, adidas (kiv)
splashforce_r_e <- splashforce_r_e %>% 
  mutate(shopify = "no_shopify", footsites = "footsites", yeezysupply = "yeezysupply", supreme = "no_supreme", collectibles = "no_collectibles", retail = "no_retail", mesh = "no_mesh", focus = "US")

#swftaio - retail
splashforce_r_e <- splashforce_r_e %>% 
  mutate(shopify = "no_shopify", footsites = "no_footsites", yeezysupply = "no_yeezysupply", supreme = "no_supreme", collectibles = "no_collectibles", retail = "retail", mesh = "no_mesh", focus = "US")

#tohru - footsites, yeezysupply, supreme, nike (kiv)
tohru_r_e <- tohru_r_e %>% 
  mutate(shopify = "no_shopify", footsites = "footsites", yeezysupply = "yeezysupply", supreme = "supreme", collectibles = "no_collectibles", retail = "no_retail", mesh = "no_mesh", focus = "US")

#velox - yeezysupply, supreme
velox_lt_r_e <- velox_lt_r_e %>% 
  mutate(shopify = "no_shopify", footsites = "no_footsites", yeezysupply = "yeezysupply", supreme = "supreme", collectibles = "no_collectibles", retail = "no_retail", mesh = "no_mesh", focus = "BOTH")

#wrath - shopify, footsites, yeezysupply, supreme
wrath_lt_r_e <- wrath_lt_r_e %>% 
  mutate(shopify = "shopify", footsites = "footsites", yeezysupply = "yeezysupply", supreme = "supreme", collectibles = "no_collectibles", retail = "no_retail", mesh = "no_mesh", focus = "US")
```

Almost ready to bind all of these tibbles together...
