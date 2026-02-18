Sentiment Analysis using the AFINN Lexicon
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

In AFINN, each word (in the lexicon) has a numeric rating between -5 and
5.

A “join” is a method of putting two data frames together. An inner join
keeps only those rows that are present in both data sets. The “by”
option indicates the variable or variables that will be used to match
the rows in the two data sets.

This data is only going to include words that have sentiment values.

``` r
afinn <- get_sentiments("afinn")
afinn |> inner_join( qs_tokens, by = "word", multiple = "all") -> qs_afinn
```

``` r
table(qs_afinn$value)
```

    ## 
    ##   -5   -4   -3   -2   -1    1    2    3    4    5 
    ##    2   11  498 1603  825 1275 1384  615  107    1

``` r
table(qs_afinn$value) |> prop.table() |>round(2)
```

    ## 
    ##   -5   -4   -3   -2   -1    1    2    3    4    5 
    ## 0.00 0.00 0.08 0.25 0.13 0.20 0.22 0.10 0.02 0.00

Overall the words in our files are more positive than negative.

``` r
ggplot(qs_afinn, aes(x = value)) + geom_histogram(bins = 11) +
  labs(title = "AFINN in the quaker stories tokens")
```

![](/Users/elinwaring/Code/rcode/quakersaints-example/md/sentiments_afinn_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

``` r
ggplot(afinn, aes(x = value)) + geom_histogram(bins = 11) +
  labs(title= "Overall distribution of AFINN")
```

![](/Users/elinwaring/Code/rcode/quakersaints-example/md/sentiments_afinn_files/figure-gfm/unnamed-chunk-5-2.png)<!-- -->

Interestingly, when stories appropriate for younger audiences are
compared to those that are not, the younger-oriented stories are, if
anything, slightly more negative although there are only small
differences..

## Stories for younger versus older readers

Since the values are numeric we can do things such as make box plots
comparing type of sections or those for older and younger readers.

``` r
qs_afinn |>
ggplot(aes(x = text_type, y = value)) + 
  geom_boxplot() +
  labs(title = "AFINN in the quaker stories tokens")
```

![](/Users/elinwaring/Code/rcode/quakersaints-example/md/sentiments_afinn_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->
In this case what we see is that the medians are not that different.

``` r
qs_afinn |>
  filter(text_type == "Story") |>
ggplot(aes(x = younger, y = value)) + 
  geom_boxplot() +
  labs(title = "AFINN in the quaker stories tokens")
```

![](/Users/elinwaring/Code/rcode/quakersaints-example/md/sentiments_afinn_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

The results are essentially the same.

How much variation is there from story to story?

``` r
qs_afinn |>
ggplot(aes(x = reorder(title, value, FUN = median),
           y = value)) + 
  geom_boxplot() +
  coord_flip() +
  labs(title = "AFINN in the quaker stories tokens")
```

![](/Users/elinwaring/Code/rcode/quakersaints-example/md/sentiments_afinn_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

The distributions largely overlap, but there seem to be three groups of
stories. There is some overlap with what we get using bing, but they are
also different.

Another thing to look at will be how sentiments change during the
stories.

How do we have to fix this to make it meaningful? we can change the
visualization of the charts by giving a different graph to each story.

``` r
 qs_afinn |> 
  filter(text_type == "Story") |>
  # Select just the first 10 stories
  filter(title %in% unique(title)[1:10]) |>
  # I'm going to make groups of 10 rows to do together.
  # I could use modulo instead.
  mutate(row_10 = round(row_number/10), 0) |>
  arrange(row_10) |>
  group_by(title, row_10) |>
  summarize(mean_sentiment = mean(value)) |>
ggplot( aes(x= row_10, y = mean_sentiment)) +
  geom_col(show.legend = FALSE) +
  facet_wrap(~title, ncol = 2, scales = "free_x") +
  labs(title = "Sentiments over the course of the stories",
      subtitle = "Stories 1 to 10")
```

    ## `summarise()` has grouped output by 'title'. You can override using the
    ## `.groups` argument.

![](/Users/elinwaring/Code/rcode/quakersaints-example/md/sentiments_afinn_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

``` r
 qs_afinn |> 
  filter(text_type == "Story") |>
  # Select just the first 10 stories
  filter(section_title %in% unique(section_title)[11:20]) |>
  # I'm going to make groups of 10 rows to do together.
  # I could use modulo instead.
  mutate(row_10 = round(row_number/10), 0) |>
  arrange(row_10) |>
  group_by(section_title, row_10) |>
  summarize(mean_sentiment = mean(value)) |>
ggplot( aes(x= row_10, y = mean_sentiment)) +
  geom_col(show.legend = FALSE) +
  facet_wrap(~section_title, ncol = 2, scales = "free_x") +
  labs(title = "Sentiments over the course of the stories",
      subtitle = "Stories 11 to 20")
```

    ## `summarise()` has grouped output by 'section_title'. You can override using
    ## the `.groups` argument.

![](/Users/elinwaring/Code/rcode/quakersaints-example/md/sentiments_afinn_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

``` r
  qs_afinn |> 
  filter(text_type == "Story") |>
  # Select just the first 10 stories
  filter(section_title %in% unique(section_title)[21:32]) |>
  # I'm going to make groups of 10 rows to do together.
  # I could use modulo instead.
  mutate(row_10 = round(row_number/10), 0) |>
  arrange(row_10) |>
  group_by(section_title, row_10) |>
  summarize(mean_sentiment = mean(value)) |>
ggplot( aes(x= row_10, y = mean_sentiment)) +
  geom_col(show.legend = FALSE) +
  facet_wrap(~section_title, ncol = 2, scales = "free_x") +
  labs(title = "Sentiments over the course of the stories",
      subtitle = "Stories 21 to 32")
```

    ## `summarise()` has grouped output by 'section_title'. You can override using
    ## the `.groups` argument.

![](/Users/elinwaring/Code/rcode/quakersaints-example/md/sentiments_afinn_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

This shows us a bit more in the way of narrative structure than using
the bing classificiations. There seem to be some stories with positive
endings and some with negative endings, and we might want to investigate
whether there are patterns to these differences.

Just as with the bing analysis, there are many additional ways we could
analyze these data. What is useful is going to depend on your text and
the questions you are interested in.
