If the file doesn’t exist go back and run the qs_nrc file.

``` r
library(here)
library(dplyr)
library(tidyr)
library(cluster)
library(ggplot2)
library(factoextra)
library(knitr)
```

Let’s make a shorter version of the titles. I realized I needed to do
this after making the graphs.

``` r
if (!exists("qs_nrc" )){
  load(here("data/qs_nrc.rda"))
}
if (!exists("qs_tokens" )){
  load(here("data/qs_tokens.rda"))
}

qs_nrc$title_short <- sub("\\..*","", qs_nrc$section_title)

title_translate <- qs_nrc |> 
  group_by(section_title, title_short) |> 
  summarize(n_tokens = n())
```

    ## `summarise()` has grouped output by 'section_title'. You can override using the
    ## `.groups` argument.

Remember from the nrc sentiment analysis “the most common emotion is
trust, followed by anticipation, joy and fear”

``` r
prop.table(table(qs_nrc$title_short, qs_nrc$sentiment), 2) |>
  round(2) |> kable(caption = "Sentiment by title")
```

|  | anger | anticipation | disgust | fear | joy | negative | positive | sadness | surprise | trust |
|:-------------|----:|--------:|-----:|----:|----:|------:|------:|-----:|------:|----:|
| A TALK ABOUT SAINTS | 0.01 | 0.03 | 0.01 | 0.01 | 0.04 | 0.02 | 0.03 | 0.02 | 0.04 | 0.03 |
| COME-TO-GOOD | 0.01 | 0.03 | 0.01 | 0.02 | 0.03 | 0.02 | 0.03 | 0.02 | 0.02 | 0.02 |
| HISTORICAL NOTES | 0.01 | 0.02 | 0.02 | 0.02 | 0.02 | 0.02 | 0.02 | 0.02 | 0.02 | 0.03 |
| I | 0.04 | 0.01 | 0.04 | 0.03 | 0.01 | 0.03 | 0.02 | 0.03 | 0.01 | 0.02 |
| II | 0.05 | 0.05 | 0.04 | 0.04 | 0.06 | 0.04 | 0.05 | 0.04 | 0.04 | 0.05 |
| III | 0.05 | 0.05 | 0.04 | 0.04 | 0.05 | 0.04 | 0.05 | 0.04 | 0.06 | 0.05 |
| IV | 0.06 | 0.04 | 0.05 | 0.06 | 0.04 | 0.05 | 0.03 | 0.04 | 0.04 | 0.03 |
| IX | 0.02 | 0.03 | 0.01 | 0.02 | 0.03 | 0.03 | 0.03 | 0.03 | 0.03 | 0.02 |
| PREFACE | 0.01 | 0.01 | 0.01 | 0.01 | 0.01 | 0.01 | 0.01 | 0.01 | 0.01 | 0.01 |
| V | 0.03 | 0.03 | 0.03 | 0.02 | 0.02 | 0.03 | 0.02 | 0.03 | 0.03 | 0.02 |
| VI | 0.01 | 0.01 | 0.01 | 0.01 | 0.02 | 0.01 | 0.02 | 0.01 | 0.02 | 0.02 |
| VII | 0.01 | 0.02 | 0.01 | 0.01 | 0.02 | 0.01 | 0.02 | 0.01 | 0.01 | 0.02 |
| VIII | 0.02 | 0.04 | 0.04 | 0.02 | 0.04 | 0.03 | 0.04 | 0.02 | 0.04 | 0.04 |
| X | 0.03 | 0.02 | 0.03 | 0.02 | 0.01 | 0.03 | 0.02 | 0.04 | 0.03 | 0.02 |
| XI | 0.01 | 0.01 | 0.01 | 0.01 | 0.01 | 0.02 | 0.02 | 0.02 | 0.01 | 0.02 |
| XII | 0.03 | 0.02 | 0.03 | 0.02 | 0.02 | 0.02 | 0.02 | 0.03 | 0.03 | 0.03 |
| XIII | 0.02 | 0.02 | 0.02 | 0.02 | 0.02 | 0.02 | 0.02 | 0.02 | 0.02 | 0.02 |
| XIV | 0.03 | 0.02 | 0.03 | 0.01 | 0.02 | 0.03 | 0.02 | 0.02 | 0.01 | 0.02 |
| XIX | 0.02 | 0.03 | 0.02 | 0.02 | 0.03 | 0.03 | 0.03 | 0.03 | 0.02 | 0.03 |
| XV | 0.02 | 0.03 | 0.04 | 0.03 | 0.03 | 0.02 | 0.03 | 0.02 | 0.03 | 0.03 |
| XVI | 0.03 | 0.03 | 0.03 | 0.03 | 0.03 | 0.03 | 0.03 | 0.02 | 0.03 | 0.03 |
| XVII | 0.02 | 0.02 | 0.02 | 0.02 | 0.02 | 0.03 | 0.02 | 0.03 | 0.03 | 0.02 |
| XVIII | 0.03 | 0.02 | 0.04 | 0.03 | 0.02 | 0.03 | 0.02 | 0.04 | 0.02 | 0.02 |
| XX | 0.05 | 0.04 | 0.05 | 0.05 | 0.05 | 0.05 | 0.04 | 0.05 | 0.04 | 0.04 |
| XXI | 0.04 | 0.04 | 0.04 | 0.04 | 0.05 | 0.04 | 0.05 | 0.06 | 0.04 | 0.05 |
| XXII | 0.02 | 0.02 | 0.02 | 0.02 | 0.02 | 0.02 | 0.02 | 0.02 | 0.03 | 0.02 |
| XXIII | 0.02 | 0.02 | 0.03 | 0.02 | 0.03 | 0.02 | 0.03 | 0.02 | 0.02 | 0.03 |
| XXIV | 0.02 | 0.02 | 0.02 | 0.02 | 0.02 | 0.02 | 0.02 | 0.02 | 0.02 | 0.02 |
| XXIX | 0.02 | 0.02 | 0.02 | 0.03 | 0.02 | 0.02 | 0.02 | 0.02 | 0.02 | 0.02 |
| XXV | 0.03 | 0.05 | 0.03 | 0.03 | 0.03 | 0.03 | 0.05 | 0.02 | 0.03 | 0.04 |
| XXVI | 0.06 | 0.05 | 0.04 | 0.06 | 0.04 | 0.05 | 0.05 | 0.05 | 0.05 | 0.05 |
| XXVII | 0.03 | 0.03 | 0.03 | 0.04 | 0.02 | 0.03 | 0.03 | 0.03 | 0.02 | 0.02 |
| XXVIII | 0.05 | 0.05 | 0.05 | 0.06 | 0.05 | 0.05 | 0.05 | 0.04 | 0.05 | 0.04 |
| XXX | 0.02 | 0.01 | 0.03 | 0.02 | 0.01 | 0.02 | 0.01 | 0.02 | 0.02 | 0.01 |
| XXXI | 0.04 | 0.04 | 0.03 | 0.04 | 0.04 | 0.04 | 0.04 | 0.04 | 0.04 | 0.04 |
| XXXII | 0.03 | 0.02 | 0.03 | 0.03 | 0.02 | 0.02 | 0.02 | 0.03 | 0.02 | 0.02 |

Sentiment by title

``` r
table(qs_nrc$title_short, qs_nrc$sentiment) |>
  prop.table() |>
  as.data.frame() |> pivot_wider(values_from = Freq, names_from = Var2) -> nrcdata
```

I’m going to subset the data so it is just the stories and to drop the
“negative” and “positive” sentiments.

``` r
nrcdata |> filter(!Var1 %in% c("A TALK ABOUT SAINTS", 
                               "COME-TO-GOOD",
                               "HISTORICAL NOTES",
                               "PREFACE") ) |>
            select(-positive, -negative) ->
  nrcdata
```

I am interested in the question of whether there are patterns of
similarity between the stories.

First I am going to calculate “how far apart” the stories are using a
distance measure (see separate file explaining this). For this I use the
function `dist()`.  
By reading ?dist you can see that there are difference methods to
calculate distance:

“euclidean”, “maximum”, “manhattan”, “canberra”, “binary” or “minkowski”

The default is euclidean so I’ll use that first.

I don’t want to use the first column for the calculation so to keep the
labels I am going to creat row.names.

``` r
nrcdata <- as.data.frame(nrcdata)
rownames(nrcdata) <- nrcdata$Var1
nrcdata <- nrcdata[-1]
```

The distance object shows how far apart the stories are from each other.
This is a distance – think of it like inches. Higher distances means the
chapters are farther apart. A distance of 0 means that they are in the
same location.

``` r
qs_dist <- dist(nrcdata)
#qs_dist 

max(qs_dist)
```

    ## [1] 0.008779445

``` r
min(qs_dist)
```

    ## [1] 0.0005001657

The furthest apart are XXVI and VI.  
XXVI. RICHARD SELLAR AND THE ‘MERCIFUL MAN’  
VI. THE SHEPHERD OF PENDLE HILL

The closes are XXIV and XIII  
XIII. MAGNANIMITY XXIV. THE VICTORY OF AMOR STODDART

When I write up the project The Shepherd of Pendle Hill is one I will
definitely talk about because it has come up in a number of the
analyses.

``` r
fviz_nbclust(nrcdata, pam, method = "silhouette")+
  theme_classic()
```

![](clustering_nrc_files/figure-markdown_github/unnamed-chunk-7-1.png)

So there are two clusters. This is interesting because in some of the
other analyses it looked like there are stories about George Fox and
everything else.

``` r
pam(qs_dist, k = 2, metric = "euclidean", stand = FALSE) -> k2
dd <- cbind(nrcdata, cluster = k2$cluster) 
dd$title_short <- rownames(dd)
dd <- dd |>  left_join(title_translate, by = "title_short")
qs_tokens |>  
  filter(text_type == "Story" & title_type == "main") |>
  group_by(section_title) |>
  summarize(`George Fox` = sum(word == "george_fox"), 
            `Quaker Words` = sum(quaker_word == "Yes"),
            `Younger Readers` = unique(younger)) ->
  quaker_counts
```

``` r
k2$data <- nrcdata
k2$diss <- qs_dist
fviz_cluster(k2,
    ##palette = c("#00AFBB", "#FC4E07"), # color palette
    ellipse.type = "t", # Concentration ellipse
    repel = TRUE, # Avoid label overplotting (slow)
    ggtheme = theme_classic()
)
```

![](clustering_nrc_files/figure-markdown_github/unnamed-chunk-9-1.png)

Story 1 is ” ‘STIFF AS A TREE, PURE AS A BELL’” Story 9 is “Strike
again”

Stand somewhat outside the ellipses. Both are retellings (sometimes
direct quotations from) of passages in the Journal of George Fox.

``` r
dd |> select(cluster, section_title) |> 
  arrange(cluster, section_title) |>
  kable(caption = "Cluster assignments for stories")
```

| cluster | section_title                                            |
|--------:|:---------------------------------------------------------|
|       1 | I. ‘STIFF AS A TREE, PURE AS A BELL’                     |
|       1 | IX\. UNDER THE YEW-TREES                                 |
|       1 | V. ‘THE MAN IN LEATHER BREECHES’                         |
|       1 | VI\. THE SHEPHERD OF PENDLE HILL                         |
|       1 | VII\. THE PEOPLE IN WHITE RAIMENT                        |
|       1 | X. ‘BEWITCHED!’                                          |
|       1 | XI\. THE JUDGE’S RETURN                                  |
|       1 | XII\. ‘STRIKE AGAIN!’                                    |
|       1 | XIII\. MAGNANIMITY                                       |
|       1 | XIV\. MILES HALHEAD AND THE HAUGHTY LADY                 |
|       1 | XIX\. THE CHILDREN OF READING MEETING                    |
|       1 | XV\. SCATTERING THE SEED                                 |
|       1 | XVI\. WRESTLING FOR GOD                                  |
|       1 | XVII\. LITTLE JAMES AND HIS JOURNEYS                     |
|       1 | XVIII\. THE FIRST QUAKER MARTYR                          |
|       1 | XXII\. AN UNDISTURBED MEETING                            |
|       1 | XXIII\. BUTTERFLIES IN THE FELLS                         |
|       1 | XXIV\. THE VICTORY OF AMOR STODDART                      |
|       1 | XXIX\. FIERCE FEATHERS                                   |
|       1 | XXVII\. TWO ROBBER STORIES. WEST AND EAST                |
|       1 | XXX\. THE THIEF IN THE TANYARD                           |
|       1 | XXXII\. PREACHING TO NOBODY                              |
|       2 | II\. ‘PURE FOY, MA JOYE’                                 |
|       2 | III\. THE ANGEL OF BEVERLEY                              |
|       2 | IV\. TAMING THE TIGER                                    |
|       2 | VIII\. A WONDERFUL FORTNIGHT                             |
|       2 | XX\. THE SADDEST STORY OF ALL                            |
|       2 | XXI\. PALE WIND FLOWERS: OR THE LITTLE PRISON MAID       |
|       2 | XXV\. THE MARVELLOUS VOYAGE OF THE GOOD SHIP ‘WOODHOUSE’ |
|       2 | XXVI\. RICHARD SELLAR AND THE ‘MERCIFUL MAN’             |
|       2 | XXVIII\. SILVER SLIPPERS: OR A QUAKERESS AMONG THE TURKS |
|       2 | XXXI\. HOW A FRENCH NOBLE BECAME A FRIEND                |

Cluster assignments for stories

``` r
dd <- dd |> left_join(quaker_counts)
```

    ## Joining with `by = join_by(section_title)`

``` r
table(dd$cluster, dd$`George Fox`)
```

    ##    
    ##     0 1 2 3 4 5 7 8 9 11 13 15 17 20
    ##   1 6 2 1 1 3 2 1 1 3  0  1  1  0  0
    ##   2 1 1 2 2 0 0 0 0 1  1  0  0  1  1

``` r
table(dd$cluster, dd$`Quaker Words`)
```

    ##    
    ##     0 1 2 3 4 5 6 7 9 14 15 16 17 18 19 23 25 28 118
    ##   1 5 3 1 1 0 2 1 2 1  1  0  1  0  1  1  0  1  1   0
    ##   2 1 1 1 0 1 0 0 0 0  0  1  0  1  0  1  1  1  0   1

``` r
dd |> group_by(cluster) |> 
  summarize(`N of Stories` = n(),
            `Quaker Words` = sum(`Quaker Words` > 0),
            `George Fox` = sum(`George Fox` > 0),
            `For Younger` = sum(`Younger Readers`)) |>
  kable(caption = "Characteristics of Stories")
```

| cluster | N of Stories | Quaker Words | George Fox | For Younger |
|--------:|-------------:|-------------:|-----------:|------------:|
|       1 |           22 |           17 |         16 |           9 |
|       2 |           10 |            9 |          9 |           7 |

Characteristics of Stories

The Stories in Cluster 1 are less likely to include “george_fox” or use
Quaker words, but even more dramatic is that they are less than half as
likely to be in the appropriate for younger children category.
