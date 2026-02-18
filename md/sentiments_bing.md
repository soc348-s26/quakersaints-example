Sentiment Analysis using the Bing Lexicon
================

``` r
library(here)
library(dplyr)
library(tidytext)
library(tidyr)
library(knitr)
library(ggplot2)
```

Sentiment analysis allows us to use standard lexicons to explore the
sentiments expressed in texts.

This is a basic sentiment analysis. Notice that it assumes that two data
sets have been created and saved in the data folder. I use
`knitr::knit_exit()` to stop knitting if either of the data sets are not
present.

``` r
if (file.exists(here("data/qs_tokens.rda"))) {
     load(here("data/qs_tokens.rda")) 
} else {
  print("qs_tokens not present.")
  knitr::knit_exit()
}
```

We will use the tokenized data (one word per line). In this data you
should also have a row number variable and a variable that defines which
chapter/story/section the word is in. We will be using those variables
to help us to explore sentiments in the text.

In Bing, some words are both positive and negative so it is possible for
a word to have two matches. Therefore the relationship=“many-to-many”
option is used.

A “join” is a method of putting two data frames together. An inner join
keeps only those rows that are present in both data sets. The “by”
option indicates the variable or variables that will be used to match
the rows in the two data sets.

This data is only going to include words that have sentiment values.

``` r
bing <- get_sentiments("bing")
bing |> inner_join( qs_tokens, by = "word", multiple = "all",
                    relationship = "many-to-many") -> qs_bing
```

``` r
table(qs_bing$sentiment)
```

    ## 
    ## negative positive 
    ##     4157     3529

``` r
table(qs_bing$sentiment) |> prop.table() |>round(2)
```

    ## 
    ## negative positive 
    ##     0.54     0.46

Overall the lannguage is more negative than positive. However, in the
Bing lexicon 70% of the entries are negative, so we wouldn’t say that
the words distribution in the Quaker stories is necessarily overly
negative.

Interestingly, when stories appropriate for younger audiences are
compared to those that are not, the younger-oriented stories are, if
anything, slightly more negative although there are only small
differences..

## Stories for younger versus older readers

``` r
table(qs_bing$sentiment, qs_bing$younger) |> prop.table(2) |> round(2)
```

    ##           
    ##            FALSE TRUE
    ##   negative  0.52 0.56
    ##   positive  0.48 0.44

## Sentiments by type of text

``` r
table(qs_bing$sentiment, qs_bing$text_type) |> prop.table(2) |> round(2)
```

    ##           
    ##            Back Matter Front Matter Story
    ##   negative        0.41         0.41  0.55
    ##   positive        0.59         0.59  0.45

Interestingly the stories are more negative than the other materials.

This does tend to support the idea that the epigraphs are more flowery.

Based on this, I’m going to redo the earlier comparisons of young and
old with just stories.

``` r
qs_bing |> filter(text_type == "Story") -> qs_stories_bing
table(qs_stories_bing$sentiment, qs_stories_bing$younger) |> 
  prop.table(2) |> round(2)
```

    ##           
    ##            FALSE TRUE
    ##   negative  0.53 0.57
    ##   positive  0.47 0.43

The results are essentially the same.

How much variation is there from story to story?

Some chapters tend to have more negatives words compared to other
stories. For example the ‘THE MAN IN LEATHER BREECHES’ story has a
**0.76** negative word ratio compared to **0.24** positive word ratio.

``` r
table( qs_stories_bing$title, qs_stories_bing$sentiment) |> 
  prop.table(1) |> round(2) |>
  kable(caption = "Proportion of words with sentiments")
```

|       | negative | positive |
|:------|---------:|---------:|
| Text  |     0.55 |     0.45 |
| Title |     0.43 |     0.57 |

Proportion of words with sentiments

Let’s use a different method and sort the results.

``` r
 qs_stories_bing |> group_by(section_title) |> 
  summarize(Positive = round(sum(sentiment == "positive")/n(), 2),
            Negative = round(sum(sentiment == "negative")/n(), 2)) |>
  arrange(Positive) |> kable(caption = "Sentiment breakdown by story")
```

| section_title                                            | Positive | Negative |
|:---------------------------------------------------------|---------:|---------:|
| V. ‘THE MAN IN LEATHER BREECHES’                         |     0.26 |     0.74 |
| XVIII\. THE FIRST QUAKER MARTYR                          |     0.30 |     0.70 |
| I. ‘STIFF AS A TREE, PURE AS A BELL’                     |     0.33 |     0.67 |
| XVII\. LITTLE JAMES AND HIS JOURNEYS                     |     0.33 |     0.67 |
| X. ‘BEWITCHED!’                                          |     0.34 |     0.66 |
| XXXII\. PREACHING TO NOBODY                              |     0.34 |     0.66 |
| IV\. TAMING THE TIGER                                    |     0.35 |     0.65 |
| VI\. THE SHEPHERD OF PENDLE HILL                         |     0.35 |     0.65 |
| XX\. THE SADDEST STORY OF ALL                            |     0.36 |     0.64 |
| XII\. ‘STRIKE AGAIN!’                                    |     0.39 |     0.61 |
| XXI\. PALE WIND FLOWERS: OR THE LITTLE PRISON MAID       |     0.41 |     0.59 |
| XIV\. MILES HALHEAD AND THE HAUGHTY LADY                 |     0.42 |     0.58 |
| XXIV\. THE VICTORY OF AMOR STODDART                      |     0.42 |     0.58 |
| XXIX\. FIERCE FEATHERS                                   |     0.42 |     0.58 |
| XXVI\. RICHARD SELLAR AND THE ‘MERCIFUL MAN’             |     0.42 |     0.58 |
| XXX\. THE THIEF IN THE TANYARD                           |     0.42 |     0.58 |
| XIII\. MAGNANIMITY                                       |     0.43 |     0.57 |
| XXII\. AN UNDISTURBED MEETING                            |     0.43 |     0.57 |
| IX\. UNDER THE YEW-TREES                                 |     0.44 |     0.56 |
| XI\. THE JUDGE’S RETURN                                  |     0.45 |     0.55 |
| XXVII\. TWO ROBBER STORIES. WEST AND EAST                |     0.45 |     0.55 |
| XXXI\. HOW A FRENCH NOBLE BECAME A FRIEND                |     0.49 |     0.51 |
| XIX\. THE CHILDREN OF READING MEETING                    |     0.50 |     0.50 |
| III\. THE ANGEL OF BEVERLEY                              |     0.51 |     0.49 |
| II\. ‘PURE FOY, MA JOYE’                                 |     0.54 |     0.46 |
| VII\. THE PEOPLE IN WHITE RAIMENT                        |     0.54 |     0.46 |
| XV\. SCATTERING THE SEED                                 |     0.54 |     0.46 |
| XXVIII\. SILVER SLIPPERS: OR A QUAKERESS AMONG THE TURKS |     0.55 |     0.45 |
| XVI\. WRESTLING FOR GOD                                  |     0.57 |     0.43 |
| XXV\. THE MARVELLOUS VOYAGE OF THE GOOD SHIP ‘WOODHOUSE’ |     0.58 |     0.42 |
| XXIII\. BUTTERFLIES IN THE FELLS                         |     0.61 |     0.39 |
| VIII\. A WONDERFUL FORTNIGHT                             |     0.65 |     0.35 |

Sentiment breakdown by story

Visualizing this might be better. This is one possible way. For yours
make sure to add labels. In this case I might also want to add a line
highlighting .5 or to change the tick marks to help visualize that. Do
what makes sense for your data.

``` r
 qs_stories_bing |> group_by(section_title) |> 
  summarize(Positive = round(sum(sentiment == "positive")/n(), 2)) |>
  arrange(Positive) |> 
  ggplot(aes(x=reorder(section_title, Positive), y =Positive)) +
  geom_point(size = 4) +
   geom_segment(aes(x=section_title, xend=section_title, y=0, yend=Positive)) +
  coord_flip() +
  labs(title = "Stories ranked by proportion of positive words")
```

![](/Users/elinwaring/Code/rcode/quakersaints-example/md/sentiments_bing_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

``` r
 qs_bing |> group_by(section_title) |> 
  summarize(Negative = round(sum(sentiment == "negative")/n(), 2)) |>
  arrange(Negative) |> 
  ggplot(aes(x=reorder(section_title, Negative), y =Negative)) +
  geom_point(size = 4) +
   geom_segment(aes(x=section_title, xend=section_title, y=0, yend=Negative)) +
  coord_flip() +
  labs(title = "Stories ranked by proportion of negative words")
```

![](/Users/elinwaring/Code/rcode/quakersaints-example/md/sentiments_bing_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

Another thing to look at will be how sentiments change during the
stories.

How do we have to fix this to make it meaningful? we can change the
visualization of the charts by giving a different graph to each story.

``` r
 qs_stories_bing |> 
  # Select just the first 10 stories
  filter(title %in% unique(title)[1:10]) |>
  # I'm going to make groups of 10 rows to do together.
  # I could use modulo instead.
  mutate(row_10 = round(row_number/10), 0) |>
  arrange(row_10) |>
  group_by(title, row_10) |>
  summarize(negative = round(sum(sentiment == "negative")/n(), 2)) |>
ggplot( aes(x= row_10, y = negative)) +
  geom_col(show.legend = FALSE) +
  facet_wrap(~title, ncol = 2, scales = "free_x") +
  labs(title = "Negative sentiments over the course of the stories",
      subtitle = "Stories 1 to 10")
```

    ## `summarise()` has grouped output by 'title'. You can override using the
    ## `.groups` argument.

![](/Users/elinwaring/Code/rcode/quakersaints-example/md/sentiments_bing_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

``` r
 qs_stories_bing |> 
  filter(section_title %in% unique(section_title)[11:20]) |>
  arrange(row_number) |>
  group_by(section_title, row_number) |>
  summarize(negative = round(sum(sentiment == "negative")/n(), 2)) |>
ggplot( aes(x= row_number, y = negative)) +
  geom_col(show.legend = FALSE) +
  facet_wrap(~section_title, ncol = 2, scales = "free_x")
```

    ## `summarise()` has grouped output by 'section_title'. You can override using
    ## the `.groups` argument.

![](/Users/elinwaring/Code/rcode/quakersaints-example/md/sentiments_bing_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

``` r
 qs_stories_bing |> 
  # Select just the first 10 stories
  filter(section_title %in% unique(section_title)[11:20]) |>
  # I'm going to make groups of 10 rows to do together.
  # I could use modulo instead.
  mutate(row_10 = round(row_number/10), 0) |>
  arrange(row_10) |>
  group_by(section_title, row_10) |>
  summarize(negative = round(sum(sentiment == "negative")/n(), 2)) |>
ggplot( aes(x= row_10, y = negative)) +
  geom_col(show.legend = FALSE) +
  facet_wrap(~section_title, ncol = 2, scales = "free_x") +
  labs(title = "Negative sentiments over the course of the stories",
      subtitle = "Stories 11 to 20")
```

    ## `summarise()` has grouped output by 'section_title'. You can override using
    ## the `.groups` argument.

![](/Users/elinwaring/Code/rcode/quakersaints-example/md/sentiments_bing_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

``` r
 qs_stories_bing |> 
  # Select just the first 10 stories
  filter(section_title %in% unique(section_title)[21:32]) |>
  # I'm going to make groups of 10 rows to do together.
  # I could use modulo instead.
  mutate(row_10 = round(row_number/10), 0) |>
  arrange(row_10) |>
  group_by(section_title, row_10) |>
  summarize(negative = round(sum(sentiment == "negative")/n(), 2)
            ) |>
ggplot( aes(x= row_10, y = negative)) +
  geom_col(show.legend = FALSE) +
  facet_wrap(~section_title, ncol = 2, scales = "free_x") +
  labs(title = "Negative sentiments over the course of the stories",
      subtitle = "Stories 21 to 32")
```

    ## `summarise()` has grouped output by 'section_title'. You can override using
    ## the `.groups` argument.

![](/Users/elinwaring/Code/rcode/quakersaints-example/md/sentiments_bing_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

There are many additional ways we could analyze these data. What is
useful is going to depend on your text and the questions you are
interested in.
