Tokenization part 2
================

``` r
  library(dplyr)
  library(here)
  library(tidytext)
  library(knitr)
  library(tidyr)
  library(ggplot2)
  library(stringr)
```

This file works on the trimmed and cleaned data as created n
curation_toc.

``` r
# If the file of saved data is not present
if (!file.exists(here("data/quakersaints_trimmed.rda"))) {
      message("quakersaints_trimmed needs to be recreated")
# Otherwise, 
} else {
     # load the data set from the file.
     load(here("data/quakersaints_trimmed.rda"))
}
```

``` r
quakersaints_trimmed |> 
  unnest_tokens(output=word, input = text, token = "words") ->
   qs_tokens
```

Once again we will remove the standard stop words.

``` r
 qs_tokens |> anti_join(stop_words) -> qs_tokens
```

    ## Joining with `by = join_by(word)`

``` r
qs_tokens |> count(word, sort = TRUE)
```

    ## # A tibble: 9,454 × 2
    ##    word        n
    ##    <chr>   <int>
    ##  1 fox       338
    ##  2 time      303
    ##  3 day       295
    ##  4 god       286
    ##  5 george    285
    ##  6 friends   243
    ##  7 people    243
    ##  8 lord      219
    ##  9 house     188
    ## 10 life      176
    ## # ℹ 9,444 more rows

Unique words

``` r
unique(qs_tokens$word) |> length()
```

    ## [1] 9454

## Some analysis

Now we can look at some interesting questions. For example, what is the
most common word in each story main text.

``` r
qs_tokens |> filter(text_type == "Story" & title_type =="main") |>
             group_by(section_title, word) |>
             summarize(uses = n()) |>
             group_by(section_title) |>
             arrange(uses) |>
             slice_tail(n = 1) |>
             kable(caption = "Most common word in each story")
```

    ## `summarise()` has grouped output by 'section_title'. You can override using
    ## the `.groups` argument.

| section_title                                            | word     | uses |
|:---------------------------------------------------------|:---------|-----:|
| I. ‘STIFF AS A TREE, PURE AS A BELL’                     | castle   |   16 |
| II\. ‘PURE FOY, MA JOYE’                                 | george   |   46 |
| III\. THE ANGEL OF BEVERLEY                              | joyce    |   42 |
| IV\. TAMING THE TIGER                                    | fox      |   43 |
| IX\. UNDER THE YEW-TREES                                 | margaret |   17 |
| V. ‘THE MAN IN LEATHER BREECHES’                         | moll     |   34 |
| VI\. THE SHEPHERD OF PENDLE HILL                         | shepherd |   16 |
| VII\. THE PEOPLE IN WHITE RAIMENT                        | people   |   15 |
| VIII\. A WONDERFUL FORTNIGHT                             | john     |   31 |
| X. ‘BEWITCHED!’                                          | judge    |   17 |
| XI\. THE JUDGE’S RETURN                                  | husband  |   16 |
| XII\. ‘STRIKE AGAIN!’                                    | fox      |   37 |
| XIII\. MAGNANIMITY                                       | fox      |   16 |
| XIV\. MILES HALHEAD AND THE HAUGHTY LADY                 | miles    |   45 |
| XIX\. THE CHILDREN OF READING MEETING                    | dorcas   |   34 |
| XV\. SCATTERING THE SEED                                 | fox      |   21 |
| XVI\. WRESTLING FOR GOD                                  | london   |   24 |
| XVII\. LITTLE JAMES AND HIS JOURNEYS                     | james    |   32 |
| XVIII\. THE FIRST QUAKER MARTYR                          | james    |   22 |
| XX\. THE SADDEST STORY OF ALL                            | nayler   |   42 |
| XXI\. PALE WIND FLOWERS: OR THE LITTLE PRISON MAID       | thou     |   45 |
| XXII\. AN UNDISTURBED MEETING                            | drawwell |   13 |
| XXIII\. BUTTERFLIES IN THE FELLS                         | town     |   23 |
| XXIV\. THE VICTORY OF AMOR STODDART                      | amor     |   23 |
| XXIX\. FIERCE FEATHERS                                   | meeting  |   25 |
| XXV\. THE MARVELLOUS VOYAGE OF THE GOOD SHIP ‘WOODHOUSE’ | robert   |   40 |
| XXVI\. RICHARD SELLAR AND THE ‘MERCIFUL MAN’             | richard  |   41 |
| XXVII\. TWO ROBBER STORIES. WEST AND EAST                | leonard  |   16 |
| XXVIII\. SILVER SLIPPERS: OR A QUAKERESS AMONG THE TURKS | grand    |   29 |
| XXX\. THE THIEF IN THE TANYARD                           | savery   |   19 |
| XXXI\. HOW A FRENCH NOBLE BECAME A FRIEND                | etienne  |   32 |
| XXXII\. PREACHING TO NOBODY                              | stephen  |   17 |

Most common word in each story

Even a list this simple is interesting. For example, most of the top
words are names. Since this is a book titled “Quaker Saints” it is not
surprising that the main focus of each chapter is on an individual.

George Fox was the founder of Quakerism and what is presumably his last
name is the top word in 5 stories. Of course it is possible that some
instances may be a reference to the animal or to some other person named
fox.

What if we look at how often the word fox appears in all of the stories.

``` r
qs_tokens |> filter(text_type == "Story" & title_type =="main"
                    ) |>
             group_by(section_title) |>
             summarize(uses = sum(word == "fox")) |>
             kable(caption = "Presence of 'fox' by story")
```

| section_title                                            | uses |
|:---------------------------------------------------------|-----:|
| I. ‘STIFF AS A TREE, PURE AS A BELL’                     |    2 |
| II\. ‘PURE FOY, MA JOYE’                                 |   27 |
| III\. THE ANGEL OF BEVERLEY                              |    4 |
| IV\. TAMING THE TIGER                                    |   43 |
| IX\. UNDER THE YEW-TREES                                 |    1 |
| V. ‘THE MAN IN LEATHER BREECHES’                         |    0 |
| VI\. THE SHEPHERD OF PENDLE HILL                         |    0 |
| VII\. THE PEOPLE IN WHITE RAIMENT                        |   13 |
| VIII\. A WONDERFUL FORTNIGHT                             |   14 |
| X. ‘BEWITCHED!’                                          |    1 |
| XI\. THE JUDGE’S RETURN                                  |   10 |
| XII\. ‘STRIKE AGAIN!’                                    |   37 |
| XIII\. MAGNANIMITY                                       |   16 |
| XIV\. MILES HALHEAD AND THE HAUGHTY LADY                 |    6 |
| XIX\. THE CHILDREN OF READING MEETING                    |    2 |
| XV\. SCATTERING THE SEED                                 |   21 |
| XVI\. WRESTLING FOR GOD                                  |    5 |
| XVII\. LITTLE JAMES AND HIS JOURNEYS                     |    9 |
| XVIII\. THE FIRST QUAKER MARTYR                          |    3 |
| XX\. THE SADDEST STORY OF ALL                            |   26 |
| XXI\. PALE WIND FLOWERS: OR THE LITTLE PRISON MAID       |    2 |
| XXII\. AN UNDISTURBED MEETING                            |    4 |
| XXIII\. BUTTERFLIES IN THE FELLS                         |   12 |
| XXIV\. THE VICTORY OF AMOR STODDART                      |   16 |
| XXIX\. FIERCE FEATHERS                                   |    0 |
| XXV\. THE MARVELLOUS VOYAGE OF THE GOOD SHIP ‘WOODHOUSE’ |    3 |
| XXVI\. RICHARD SELLAR AND THE ‘MERCIFUL MAN’             |    2 |
| XXVII\. TWO ROBBER STORIES. WEST AND EAST                |    4 |
| XXVIII\. SILVER SLIPPERS: OR A QUAKERESS AMONG THE TURKS |    0 |
| XXX\. THE THIEF IN THE TANYARD                           |    0 |
| XXXI\. HOW A FRENCH NOBLE BECAME A FRIEND                |    1 |
| XXXII\. PREACHING TO NOBODY                              |    0 |

Presence of ‘fox’ by story

So despite fox being the most common word, there are there are 5 stories
which don’t use it at all. Among those which have the word, there are
big differences. The story “Taming the Tiger” has the most uses, 43.
Later, I might want to look more closely at whether there is anything
correlated with how often the word is used.

One thing that is striking to me is that “THE MAN IN LEATHER BREECHES”
and “THE SHEPHERD OF PENDLE HILL” don’t mention Fox, because both of
those descriptions refer to Fox.

*What about the Epigraphs?*

``` r
qs_tokens |> filter(title_type == "epigraph" & text_type == "Story") |>
             group_by(section_title, word) |>
             summarize(uses = n()) |>
             ungroup() |>
             group_by(section_title) |>
             arrange(uses) |>
             slice_tail(n = 1) |>
             kable(caption = "Most common word in each epigraph")
```

    ## `summarise()` has grouped output by 'section_title'. You can override using
    ## the `.groups` argument.

| section_title                                            | word      | uses |
|:---------------------------------------------------------|:----------|-----:|
| I. ‘STIFF AS A TREE, PURE AS A BELL’                     | fox       |    3 |
| II\. ‘PURE FOY, MA JOYE’                                 | god       |    5 |
| III\. THE ANGEL OF BEVERLEY                              | people    |    3 |
| IV\. TAMING THE TIGER                                    | war       |    2 |
| IX\. UNDER THE YEW-TREES                                 | spiritual |    5 |
| V. ‘THE MAN IN LEATHER BREECHES’                         | spirit    |    3 |
| VI\. THE SHEPHERD OF PENDLE HILL                         | weather   |    3 |
| VII\. THE PEOPLE IN WHITE RAIMENT                        | god       |    5 |
| VIII\. A WONDERFUL FORTNIGHT                             | words     |    3 |
| X. ‘BEWITCHED!’                                          | judge     |    4 |
| XI\. THE JUDGE’S RETURN                                  | spirit    |    4 |
| XII\. ‘STRIKE AGAIN!’                                    | days      |    2 |
| XIII\. MAGNANIMITY                                       | soul      |    3 |
| XIV\. MILES HALHEAD AND THE HAUGHTY LADY                 | friends   |    3 |
| XIX\. THE CHILDREN OF READING MEETING                    | thou      |    4 |
| XV\. SCATTERING THE SEED                                 | wait      |    2 |
| XVI\. WRESTLING FOR GOD                                  | god       |    8 |
| XVII\. LITTLE JAMES AND HIS JOURNEYS                     | wild      |    6 |
| XVIII\. THE FIRST QUAKER MARTYR                          | quakers   |    3 |
| XX\. THE SADDEST STORY OF ALL                            | liberty   |    2 |
| XXI\. PALE WIND FLOWERS: OR THE LITTLE PRISON MAID       | power     |    3 |
| XXII\. AN UNDISTURBED MEETING                            | thee      |    3 |
| XXIII\. BUTTERFLIES IN THE FELLS                         | fox       |    4 |
| XXIV\. THE VICTORY OF AMOR STODDART                      | world     |    2 |
| XXIX\. FIERCE FEATHERS                                   | life      |    5 |
| XXV\. THE MARVELLOUS VOYAGE OF THE GOOD SHIP ‘WOODHOUSE’ | god       |    4 |
| XXVI\. RICHARD SELLAR AND THE ‘MERCIFUL MAN’             | words     |    2 |
| XXVII\. TWO ROBBER STORIES. WEST AND EAST                | nations   |    3 |
| XXVIII\. SILVER SLIPPERS: OR A QUAKERESS AMONG THE TURKS | thy       |    3 |
| XXX\. THE THIEF IN THE TANYARD                           | mind      |    3 |
| XXXI\. HOW A FRENCH NOBLE BECAME A FRIEND                | thy       |    4 |
| XXXII\. PREACHING TO NOBODY                              | god       |    3 |

Most common word in each epigraph

Here we see that the words are largely different. With the exception of
“fox” they are not names. There are also some unusual (in modern times)
words such at “thy” and “thee”. These words are “second person singular”
equivalent to “yours” and “you”, which in modern English are used for
both one person and groups of people, and both formally and informally.
You may know other languages (such as Spanish and French) where the
distinction between formal and informal is used. Quakers maintained this
distinction, and used the informal for everyone to reflect their belief
in the spiritial equality of all. For some, this was maintained well
into the twentieth century.

There are many more spiritual or religious terms such as god, spirt and
spiritual.

Fox’s writings are the source of a number of the quotations in the
epigraphs; the references to him are *by* him, not *about* him.

This is a good start but as you can see if you look at the head of
qs_tokens there are still a number of smaller issues. For example:

1.  there are some words that either begin with “*” or end with ”*”. (If
    you sort the qs_tokens data by word you can see these.)  
    2.There are also words such as “charles’s” wheree mightwant to
    remove the “’s”.

2.  There are also a lot of numbers; some look like years, but others
    are harder to interpret.

Before moving to further analysis I am going to deal with the first two
issues. And because these changes may reveal some new stop words I need
to recheck those.

``` r
qs_tokens |> mutate(word = case_when(
                   str_sub(word, 1, 1) == "_"  ~ gsub("_", "" , word),
              str_sub(word, -1, -1) == "_"  ~ gsub("_", "" , word),
              str_sub(word, -2, -1) == "'s"  ~ gsub("'s", "" , word),
              TRUE ~ word)
)  |> anti_join(stop_words) ->  qs_tokens
```

    ## Joining with `by = join_by(word)`

Over time, working with this book, I concluded it was important to track
“Quaker words”. The solution I came up with was to add a variable
indicating if a token was one of these words. (I also considered
treating them as stop words.) This is an example of how any text you are
working with may have very specific issues that may need to be
considered.

``` r
quaker_words <- c("thou", "thee", "thy", "art", "hast", "thine")
qs_tokens |> mutate(quaker_word = ifelse(word %in% quaker_words, 
                                         "Yes", "No")) -> qs_tokens
```

``` r
qs_tokens |> filter(text_type == "Story" & title_type =="main"
                    ) |>
             group_by(section_title) |>
             summarize(uses = sum(quaker_word == "Yes")) |>
             kable(caption = "Presence of 'fox' by story")
```

| section_title                                            | uses |
|:---------------------------------------------------------|-----:|
| I. ‘STIFF AS A TREE, PURE AS A BELL’                     |    2 |
| II\. ‘PURE FOY, MA JOYE’                                 |    2 |
| III\. THE ANGEL OF BEVERLEY                              |   25 |
| IV\. TAMING THE TIGER                                    |   17 |
| IX\. UNDER THE YEW-TREES                                 |    7 |
| V. ‘THE MAN IN LEATHER BREECHES’                         |   16 |
| VI\. THE SHEPHERD OF PENDLE HILL                         |    7 |
| VII\. THE PEOPLE IN WHITE RAIMENT                        |    0 |
| VIII\. A WONDERFUL FORTNIGHT                             |    4 |
| X. ‘BEWITCHED!’                                          |    0 |
| XI\. THE JUDGE’S RETURN                                  |    3 |
| XII\. ‘STRIKE AGAIN!’                                    |    5 |
| XIII\. MAGNANIMITY                                       |    1 |
| XIV\. MILES HALHEAD AND THE HAUGHTY LADY                 |   28 |
| XIX\. THE CHILDREN OF READING MEETING                    |   25 |
| XV\. SCATTERING THE SEED                                 |    1 |
| XVI\. WRESTLING FOR GOD                                  |    0 |
| XVII\. LITTLE JAMES AND HIS JOURNEYS                     |   20 |
| XVIII\. THE FIRST QUAKER MARTYR                          |    5 |
| XX\. THE SADDEST STORY OF ALL                            |    1 |
| XXI\. PALE WIND FLOWERS: OR THE LITTLE PRISON MAID       |  118 |
| XXII\. AN UNDISTURBED MEETING                            |    0 |
| XXIII\. BUTTERFLIES IN THE FELLS                         |    1 |
| XXIV\. THE VICTORY OF AMOR STODDART                      |    0 |
| XXIX\. FIERCE FEATHERS                                   |    9 |
| XXV\. THE MARVELLOUS VOYAGE OF THE GOOD SHIP ‘WOODHOUSE’ |   23 |
| XXVI\. RICHARD SELLAR AND THE ‘MERCIFUL MAN’             |   19 |
| XXVII\. TWO ROBBER STORIES. WEST AND EAST                |    6 |
| XXVIII\. SILVER SLIPPERS: OR A QUAKERESS AMONG THE TURKS |   15 |
| XXX\. THE THIEF IN THE TANYARD                           |   18 |
| XXXI\. HOW A FRENCH NOBLE BECAME A FRIEND                |    0 |
| XXXII\. PREACHING TO NOBODY                              |   16 |

Presence of ‘fox’ by story

Let’s save the tokenized data with the new variable.

``` r
save(qs_tokens, file = here("data/qs_tokens.rda"))
```

So even though we have basic data now, we still may need to do
additional curation because other issues may appear as we do the
analysis.
