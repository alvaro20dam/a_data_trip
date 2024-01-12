---
title: R project for data cleaning, reshaping and merging
author: Alvaro
date: '2024-01-11'
slug: r-project-for-data-cleaning-reshaping-and-merging
categories:
  - R
tags:
  - plot
  - R Markdown
---
## Packages


```r
library(dplyr)
library(readr)
library(lubridate)
library(stringr)
library(tidyr)
library(ggplot2)
billboard100 <- read.csv("charts.csv")
```

## Selecting


```r
music_df = billboard100 %>%
  select(date:artist, weeks_popular='weeks.on.board')
str(music_df)
```

```
## 'data.frame':	330087 obs. of  5 variables:
##  $ date         : chr  "2021-11-06" "2021-11-06" "2021-11-06" "2021-11-06" ...
##  $ rank         : int  1 2 3 4 5 6 7 8 9 10 ...
##  $ song         : chr  "Easy On Me" "Stay" "Industry Baby" "Fancy Like" ...
##  $ artist       : chr  "Adele" "The Kid LAROI & Justin Bieber" "Lil Nas X & Jack Harlow" "Walker Hayes" ...
##  $ weeks_popular: int  3 16 14 19 18 8 7 24 20 56 ...
```


## Fixed date format and artist column



```r
music_df.cleaned =
music_df %>%
  mutate(date = ymd(date),
        primary_artist = ifelse(str_detect(artist, 'Featuring'),
                                str_match(artist, '(.*)\\sFeaturing')[,2],
                                artist),
        featured_artist = str_match(artist, 'Featuring\\s(.*)')[,2])
```

## Using pivot_longer from tidyr


```r
music_df.cleaned %>%
  distinct(song, primary_artist, featured_artist) %>%
  pivot_longer(2:3, names_to = "artist_type", values_to = "artist_name") %>%
  filter(artist_name=="Drake") %>%
  count(artist_type)
```


```r
music_df.cleaned %>%
  filter(rank <= 3) %>%
  select(date, rank, song) %>%
  pivot_wider(names_from = "rank", values_from = "song")
```


```r
mtv_data1 = read_csv("mtvArtists/10000-MTV-Music-Artists-page-1.csv")
```

```
## Rows: 2999 Columns: 6
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (6): name, facebook, twitter, website, genre, mtv
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
mtv_data2 = read_csv("mtvArtists/10000-MTV-Music-Artists-page-2.csv")
```

```
## Rows: 2999 Columns: 6
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (6): name, facebook, twitter, website, genre, mtv
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
mtv_data3 = read_csv("mtvArtists/10000-MTV-Music-Artists-page-3.csv")
```

```
## Rows: 1329 Columns: 6
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (6): name, facebook, twitter, website, genre, mtv
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
mtv_data4 = read_csv("mtvArtists/10000-MTV-Music-Artists-page-4.csv")
```

```
## Rows: 1017 Columns: 6
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (6): name, facebook, twitter, website, genre, mtv
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
mtv_data <- rbind(mtv_data1, mtv_data2, mtv_data3, mtv_data4)
```


```r
head(mtv_data)
```

```
## # A tibble: 6 × 6
##   name            facebook                           twitter website genre mtv  
##   <chr>           <chr>                              <chr>   <chr>   <chr> <chr>
## 1 Adele           http://www.facebook.com/9770929278 http:/… <NA>    Pop   http…
## 2 Joey + Rory     http://www.facebook.com/150445078… http:/… <NA>    Coun… http…
## 3 Draaco Aventura http://www.facebook.com/856796091… http:/… http:/… Pop … http…
## 4 Justin Bieber   http://www.facebook.com/309570926… http:/… http:/… Pop   http…
## 5 Peer van Mladen http://www.facebook.com/264487966  http:/… http:/… House http…
## 6 Chris Janson    http://www.facebook.com/296647641… http:/… http:/… Coun… http…
```



```r
top5_genres =
music_df.cleaned %>%
  inner_join(mtv_data, by = c("primary_artist" = "name")) %>%
  select(song, primary_artist, genre) %>%
  filter(!is.na(genre)) %>%
  distinct() %>%
  count(genre) %>%
  top_n(5) %>%
  pull(genre)
```

```
## Warning in inner_join(., mtv_data, by = c(primary_artist = "name")): Detected an unexpected many-to-many relationship between `x` and `y`.
## ℹ Row 67 of `x` matches multiple rows in `y`.
## ℹ Row 10 of `y` matches multiple rows in `x`.
## ℹ If a many-to-many relationship is expected, set `relationship =
##   "many-to-many"` to silence this warning.
```

```
## Selecting by n
```



```r
music_df.cleaned %>%
  inner_join(mtv_data, by = c("primary_artist" = "name")) %>%
  mutate(date=floor_date(date, unit="year")) %>%
  select(date, song, genre) %>%
  filter(genre %in% top5_genres) %>%
  count(date, genre) %>%
  ggplot(aes(x=date,y=n,color=genre)) + geom_line()
```

```
## Warning in inner_join(., mtv_data, by = c(primary_artist = "name")): Detected an unexpected many-to-many relationship between `x` and `y`.
## ℹ Row 67 of `x` matches multiple rows in `y`.
## ℹ Row 10 of `y` matches multiple rows in `x`.
## ℹ If a many-to-many relationship is expected, set `relationship =
##   "many-to-many"` to silence this warning.
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-7-1.png" width="672" />


