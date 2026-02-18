Sentiment Analysis using the nrc Lexicon
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

This website tells you more about the nrc lexicon.
<https://saifmohammad.com/WebPages/NRC-Emotion-Lexicon.htm>

This explains more about how it was created. How Mechanical Turkers
Crowdsourced a Huge Lexicon of Links Between Words and Emotion. n.d.
Retrieved February 17, 2026.
<https://www.technologyreview.com/2013/09/05/176552/how-mechanical-turkers-crowdsourced-a-huge-lexicon-of-links-between-words-and-emotion/>.

Mohammad, Saif. 2011. “From Once Upon a Time to Happily Ever After:
Tracking Emotions in Novels and Fairy Tales.” Pp. 105–14 in Proceedings
of the 5th ACL-HLT Workshop on Language Technology for Cultural
Heritage, Social Sciences, and Humanities, edited by K. Zervanou and P.
Lendvai. Portland, OR, USA: Association for Computational Linguistics.

NRC distinquishes “sentiment” (positive and negative) from emotion
(specific feelings). For emotions, descriptive terms such as “trust”,
“fear”, “sadness” and “anger” are assigned to words. In some cases a
word may be assigned multiple emotions.

A “join” is a method of putting two data frames together. An inner join
keeps only those rows that are present in both data sets. The “by”
option indicates the variable or variables that will be used to match
the rows in the two data sets.

Because the same emotions and sentiments are assigned to many words AND
specific words are assigned multiple emotions we use what is called a
many-to-many option when carrying out the join.

This data is only going to include words that have sentiment values.

``` r
nrc <- get_sentiments("nrc")
nrc |> inner_join(qs_tokens, by = "word", multiple = "all",
                   relationship = "many-to-many") -> qs_nrc
```

Overall the words in our files are more positive than negative.

Of course there are many questions we can ask about these distributions.

First, descriptively, how many are present?

``` r
qs_nrc |> 
  group_by(sentiment) |> 
  summarize(number = n())
```

    ## # A tibble: 10 × 2
    ##    sentiment    number
    ##    <chr>         <int>
    ##  1 anger          2157
    ##  2 anticipation   3120
    ##  3 disgust        1550
    ##  4 fear           2936
    ##  5 joy            3110
    ##  6 negative       4917
    ##  7 positive       7309
    ##  8 sadness        2445
    ##  9 surprise       1154
    ## 10 trust          4817

In this case we have more positive than negative sentiments and the most
common emotion is trust, followed by anticipation, joy and fear.

``` r
qs_nrc |> 
  group_by(sentiment) |> 
  summarize(number = n(), 
            n_younger = round(sum(younger == TRUE)/number, 2), 
            n_older = round(sum(younger== FALSE)/number, 2)
           )
```

    ## # A tibble: 10 × 4
    ##    sentiment    number n_younger n_older
    ##    <chr>         <int>     <dbl>   <dbl>
    ##  1 anger          2157      0.59    0.41
    ##  2 anticipation   3120      0.54    0.46
    ##  3 disgust        1550      0.54    0.46
    ##  4 fear           2936      0.55    0.45
    ##  5 joy            3110      0.53    0.47
    ##  6 negative       4917      0.55    0.45
    ##  7 positive       7309      0.52    0.48
    ##  8 sadness        2445      0.55    0.45
    ##  9 surprise       1154      0.54    0.46
    ## 10 trust          4817      0.53    0.47

If we look at the proportion of sentiment and emotion words that are
associated with words in older and younger stories we see that
consistnently the majority are in stories for younger readers.

However, we need to be cautious about interpreting this too much because
there are more total words in the younger stories.

Therefore we would expect that there are more sentiment-emotion words in
the stories for younger readers, simply by chance. This is something
that could be explored in a more complex way.

``` r
table(qs_tokens$younger)
```

    ## 
    ## FALSE  TRUE 
    ## 23837 26104

``` r
table(qs_tokens$younger) |> prop.table() |> round(2)
```

    ## 
    ## FALSE  TRUE 
    ##  0.48  0.52

We could look at what the most common emotion in each story is. In this
case we could just focus on the emotion words.

``` r
qs_nrc |> filter (!sentiment %in% c("positive", "negative") ) |>
  group_by(section_title, sentiment) |> 
  summarize(n = n()) |>
  ungroup() |>
  group_by(section_title) |>
    arrange(n) |>
  slice_tail(n =1) |>
  kable(caption = "Most common emotion in each story")
```

    ## `summarise()` has grouped output by 'section_title'. You can override using
    ## the `.groups` argument.

| section_title                                            | sentiment    |   n |
|:---------------------------------------------------------|:-------------|----:|
| A TALK ABOUT SAINTS                                      | trust        | 146 |
| COME-TO-GOOD                                             | trust        | 107 |
| HISTORICAL NOTES                                         | trust        | 140 |
| I. ‘STIFF AS A TREE, PURE AS A BELL’                     | fear         |  93 |
| II\. ‘PURE FOY, MA JOYE’                                 | trust        | 243 |
| III\. THE ANGEL OF BEVERLEY                              | trust        | 257 |
| IV\. TAMING THE TIGER                                    | fear         | 179 |
| IX\. UNDER THE YEW-TREES                                 | trust        | 109 |
| PREFACE                                                  | trust        |  42 |
| V. ‘THE MAN IN LEATHER BREECHES’                         | anticipation |  78 |
| VI\. THE SHEPHERD OF PENDLE HILL                         | trust        |  84 |
| VII\. THE PEOPLE IN WHITE RAIMENT                        | trust        | 100 |
| VIII\. A WONDERFUL FORTNIGHT                             | trust        | 183 |
| X. ‘BEWITCHED!’                                          | trust        |  91 |
| XI\. THE JUDGE’S RETURN                                  | trust        |  89 |
| XII\. ‘STRIKE AGAIN!’                                    | trust        | 121 |
| XIII\. MAGNANIMITY                                       | trust        |  90 |
| XIV\. MILES HALHEAD AND THE HAUGHTY LADY                 | trust        | 102 |
| XIX\. THE CHILDREN OF READING MEETING                    | trust        | 137 |
| XV\. SCATTERING THE SEED                                 | trust        | 138 |
| XVI\. WRESTLING FOR GOD                                  | trust        | 161 |
| XVII\. LITTLE JAMES AND HIS JOURNEYS                     | trust        | 107 |
| XVIII\. THE FIRST QUAKER MARTYR                          | trust        | 109 |
| XX\. THE SADDEST STORY OF ALL                            | trust        | 200 |
| XXI\. PALE WIND FLOWERS: OR THE LITTLE PRISON MAID       | trust        | 230 |
| XXII\. AN UNDISTURBED MEETING                            | trust        |  77 |
| XXIII\. BUTTERFLIES IN THE FELLS                         | trust        | 130 |
| XXIV\. THE VICTORY OF AMOR STODDART                      | trust        | 106 |
| XXIX\. FIERCE FEATHERS                                   | fear         |  89 |
| XXV\. THE MARVELLOUS VOYAGE OF THE GOOD SHIP ‘WOODHOUSE’ | trust        | 207 |
| XXVI\. RICHARD SELLAR AND THE ‘MERCIFUL MAN’             | trust        | 235 |
| XXVII\. TWO ROBBER STORIES. WEST AND EAST                | trust        | 115 |
| XXVIII\. SILVER SLIPPERS: OR A QUAKERESS AMONG THE TURKS | trust        | 216 |
| XXX\. THE THIEF IN THE TANYARD                           | trust        |  67 |
| XXXI\. HOW A FRENCH NOBLE BECAME A FRIEND                | trust        | 212 |
| XXXII\. PREACHING TO NOBODY                              | trust        |  85 |

Most common emotion in each story

It may not be surprising that trust is the most common emotion in many
of the stories. In the lexicon trust is matched with 494 different words
in our data set. Twenty of them are shown.

``` r
qs_nrc |> filter(sentiment == "trust") -> trustwords
length(unique(trustwords$word))
```

    ## [1] 498

``` r
head(unique(trustwords$word), 20)
```

    ##  [1] "abundance"     "accompaniment" "accord"        "account"      
    ##  [5] "accounts"      "accurate"      "admiral"       "admiration"   
    ##  [9] "admire"        "advice"        "advise"        "advised"      
    ## [13] "adviser"       "affection"     "affirm"        "agreed"       
    ## [17] "agreeing"      "akin"          "alive"         "allegiance"
