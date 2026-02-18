Basic tokenization
================

``` r
  library(dplyr)
  library(here)
  library(tidytext)
  library(knitr)
```

    ## Warning: package 'knitr' was built under R version 4.5.2

``` r
  library(tidyr)
```

``` r
# If the file of saved data is not present
if (!file.exists(here("data/quakersaints.rda"))) {
     # download the data from Project Gutenberg
     quakersaints <-  gutenberg_download(c(19605))
     # and save it to that file name.
     save(quakersaints, file= here("data/quakersaints.rda"))
# Otherwise, 
} else {
     # load the data set from the file.
     load(here("data/quakersaints.rda"))
}
```

Tokenization means breaking something down into parts. When working with
text data, that usually means breaking it into words, although sometimes
it means using other units like sets of two words or sentences.

Let’s start by looking at the top or head of the quakersaints data. This
by default shows the first 5 rows of the data frame. As you an see once
it is run, there are two variables, gutenberg_id and text. (Since this
data set only includes one book, the value of gutenberg_id is the same
for all of the rows.)

``` r
head(quakersaints)
```

    ## # A tibble: 6 × 2
    ##   gutenberg_id text                                                         
    ##          <int> <chr>                                                        
    ## 1        19605 "      +----------------------------------------------------…
    ## 2        19605 "      | Transcriber's Note:                                …
    ## 3        19605 "      |                                                    …
    ## 4        19605 "      | Inconsistent hyphenation and spelling in the origin…
    ## 5        19605 "      | document have been preserved.                      …
    ## 6        19605 "      |                                                    …

Now we want to take this data set of 15205 rows, where each row is a
line from the book, and divide them up into a data set with one word per
line.

``` r
quakersaints |> 
  unnest_tokens(output=word, input = text, token = "words") ->
   qs_tokens
head(qs_tokens)
```

    ## # A tibble: 6 × 2
    ##   gutenberg_id word         
    ##          <int> <chr>        
    ## 1        19605 transcriber's
    ## 2        19605 note         
    ## 3        19605 inconsistent 
    ## 4        19605 hyphenation  
    ## 5        19605 and          
    ## 6        19605 spelling

``` r
tail(qs_tokens)
```

    ## # A tibble: 6 × 2
    ##   gutenberg_id word      
    ##          <int> <chr>     
    ## 1        19605 page      
    ## 2        19605 275       
    ## 3        19605 thoughout 
    ## 4        19605 replaced  
    ## 5        19605 with      
    ## 6        19605 throughout

Once we have this list of words, we can do a lot of interesting things.

The `unnest_tokens()` function, by default, does a number of other
things to our data. First, it converts all of the letters to lower case.
Second, it strips most punctuation. That is why the “—-” lines at the
beginning are no longer there.

There are some basic things we can do, such as find out how many unique
words there are.

``` r
unique(qs_tokens$word) |> length()
```

    ## [1] 10079

There are just over 10,000 unque words represented in the 141,639
tokens.

We can also find the most common words.

``` r
qs_tokens |> count(word, sort = TRUE) |> head(20)
```

    ## # A tibble: 20 × 2
    ##    word      n
    ##    <chr> <int>
    ##  1 the    8401
    ##  2 and    4865
    ##  3 to     4168
    ##  4 of     4162
    ##  5 a      2787
    ##  6 in     2680
    ##  7 he     2155
    ##  8 that   2056
    ##  9 was    2027
    ## 10 his    1805
    ## 11 i      1329
    ## 12 it     1247
    ## 13 had    1192
    ## 14 as     1150
    ## 15 for    1127
    ## 16 with   1023
    ## 17 at     1014
    ## 18 him     939
    ## 19 her     937
    ## 20 not     914

The word “i” is used 1329 times and “it” is uses 1329 times.

You might notice that the most common words are almost all pretty short
and are words we would expect to see as most common in almost any piece
of English writing.

We can also make visualizations. A simple one is to make a bar chart of
the most common words.

``` r
qs_tokens |> count(word, sort = TRUE) |>
  filter(n > 600) |>
  mutate(word = reorder(word, n)) |>
  ggplot(aes(n, word)) +
  geom_col() +
  labs(y = NULL)
```

![](/Users/elinwaring/Code/rcode/quakersaints-example/md/tokenization_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

## Stop words

Common words like “the” and “is” overwhelm other words. While for some
puposes they are important, for others they are not useful. To address
the issue of common words generally we use the idea of *stop words*.
These are words that we will remove from our tokenized data.

The tidytext package contains a list of English stop words in the form
of a dataframe `stop_words`. We want to remove all the wordsin the stop
word list from our qs_tokens data. We can do is using an function called
`anti_join()`.

``` r
 qs_tokens |> anti_join(stop_words) -> qs_tokens2
```

    ## Joining with `by = join_by(word)`

``` r
unique(qs_tokens2$word) |> length()
```

    ## [1] 9478

``` r
nrow(qs_tokens2)
```

    ## [1] 50173

``` r
head(qs_tokens2)
```

    ## # A tibble: 6 × 2
    ##   gutenberg_id word         
    ##          <int> <chr>        
    ## 1        19605 transcriber's
    ## 2        19605 note         
    ## 3        19605 inconsistent 
    ## 4        19605 hyphenation  
    ## 5        19605 spelling     
    ## 6        19605 original

In our new data set we are down to 50,173 tokens representing 9478
unique words. So it’s almost as many words as before, but we’ve
eliminated a lot of tokens because we have taken out the extremely
common words.

``` r
qs_tokens2 |> count(word, sort = TRUE)
```

    ## # A tibble: 9,478 × 2
    ##    word        n
    ##    <chr>   <int>
    ##  1 fox       338
    ##  2 time      303
    ##  3 day       295
    ##  4 god       286
    ##  5 george    285
    ##  6 friends   244
    ##  7 people    243
    ##  8 lord      219
    ##  9 house     188
    ## 10 life      176
    ## # ℹ 9,468 more rows

Now we see that the most common words are a lot more interesting.

``` r
qs_tokens2 |> count(word, sort = TRUE)
```

    ## # A tibble: 9,478 × 2
    ##    word        n
    ##    <chr>   <int>
    ##  1 fox       338
    ##  2 time      303
    ##  3 day       295
    ##  4 god       286
    ##  5 george    285
    ##  6 friends   244
    ##  7 people    243
    ##  8 lord      219
    ##  9 house     188
    ## 10 life      176
    ## # ℹ 9,468 more rows

This time instead of making a graph with all the words that appear over
600 times, let’s look at those that appear at least 100 times.

``` r
qs_tokens2 |> count(word, sort = TRUE) |>
  filter(n > 100) |>
  mutate(word = reorder(word, n)) |>
  ggplot(aes(n, word)) +
  geom_col() +
  labs(y = NULL)
```

![](/Users/elinwaring/Code/rcode/quakersaints-example/md/tokenization_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

This is a much more interesting list.

This is a good start but as you can see if you look at the head of
qs_tokens2 there are still a number of issues. For example all of the
text that is about the Project Gutenberg process is in there, and we
don’t want to analyze that.

So even though we have some basic data now, we will still benefit from
some more preparation and curation.
