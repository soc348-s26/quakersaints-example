If the file doesn’t exist go back and run the qs\_nrc file.

    library(here)
    library(dplyr)
    library(tidyr)
    library(cluster)
    library(ggplot2)
    library(factoextra)
    library(knitr)

Let’s make a shorter version of the titles. I realized I needed to do
this after making the graphs.

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

    ## `summarise()` has grouped output by 'section_title'. You can override using the
    ## `.groups` argument.

Remember from the nrc sentiment analysis “the most common emotion is
trust, followed by anticipation, joy and fear”

    prop.table(table(qs_nrc$title_short, qs_nrc$sentiment), 2) |>
      round(2) |> kable(caption = "Sentiment by title")

<table style="width:100%;">
<caption>Sentiment by title</caption>
<colgroup>
<col style="width: 20%" />
<col style="width: 6%" />
<col style="width: 13%" />
<col style="width: 8%" />
<col style="width: 5%" />
<col style="width: 5%" />
<col style="width: 9%" />
<col style="width: 9%" />
<col style="width: 8%" />
<col style="width: 9%" />
<col style="width: 6%" />
</colgroup>
<thead>
<tr>
<th style="text-align: left;"></th>
<th style="text-align: right;">anger</th>
<th style="text-align: right;">anticipation</th>
<th style="text-align: right;">disgust</th>
<th style="text-align: right;">fear</th>
<th style="text-align: right;">joy</th>
<th style="text-align: right;">negative</th>
<th style="text-align: right;">positive</th>
<th style="text-align: right;">sadness</th>
<th style="text-align: right;">surprise</th>
<th style="text-align: right;">trust</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align: left;">A TALK ABOUT SAINTS</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.03</td>
</tr>
<tr>
<td style="text-align: left;">COME-TO-GOOD</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
</tr>
<tr>
<td style="text-align: left;">HISTORICAL NOTES</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
</tr>
<tr>
<td style="text-align: left;">I</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.02</td>
</tr>
<tr>
<td style="text-align: left;">II</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.06</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.05</td>
</tr>
<tr>
<td style="text-align: left;">III</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.06</td>
<td style="text-align: right;">0.05</td>
</tr>
<tr>
<td style="text-align: left;">IV</td>
<td style="text-align: right;">0.06</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.06</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.03</td>
</tr>
<tr>
<td style="text-align: left;">IX</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
</tr>
<tr>
<td style="text-align: left;">PREFACE</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.01</td>
</tr>
<tr>
<td style="text-align: left;">V</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
</tr>
<tr>
<td style="text-align: left;">VI</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
</tr>
<tr>
<td style="text-align: left;">VII</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.02</td>
</tr>
<tr>
<td style="text-align: left;">VIII</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.04</td>
</tr>
<tr>
<td style="text-align: left;">X</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
</tr>
<tr>
<td style="text-align: left;">XI</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.02</td>
</tr>
<tr>
<td style="text-align: left;">XII</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
</tr>
<tr>
<td style="text-align: left;">XIII</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
</tr>
<tr>
<td style="text-align: left;">XIV</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.02</td>
</tr>
<tr>
<td style="text-align: left;">XIX</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
</tr>
<tr>
<td style="text-align: left;">XV</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
</tr>
<tr>
<td style="text-align: left;">XVI</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
</tr>
<tr>
<td style="text-align: left;">XVII</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
</tr>
<tr>
<td style="text-align: left;">XVIII</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
</tr>
<tr>
<td style="text-align: left;">XX</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.04</td>
</tr>
<tr>
<td style="text-align: left;">XXI</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.06</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.05</td>
</tr>
<tr>
<td style="text-align: left;">XXII</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
</tr>
<tr>
<td style="text-align: left;">XXIII</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
</tr>
<tr>
<td style="text-align: left;">XXIV</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
</tr>
<tr>
<td style="text-align: left;">XXIX</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
</tr>
<tr>
<td style="text-align: left;">XXV</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.04</td>
</tr>
<tr>
<td style="text-align: left;">XXVI</td>
<td style="text-align: right;">0.06</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.06</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.05</td>
</tr>
<tr>
<td style="text-align: left;">XXVII</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
</tr>
<tr>
<td style="text-align: left;">XXVIII</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.06</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.05</td>
<td style="text-align: right;">0.04</td>
</tr>
<tr>
<td style="text-align: left;">XXX</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.01</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.01</td>
</tr>
<tr>
<td style="text-align: left;">XXXI</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.04</td>
<td style="text-align: right;">0.04</td>
</tr>
<tr>
<td style="text-align: left;">XXXII</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.03</td>
<td style="text-align: right;">0.02</td>
<td style="text-align: right;">0.02</td>
</tr>
</tbody>
</table>

    table(qs_nrc$title_short, qs_nrc$sentiment) |>
      prop.table() |>
      as.data.frame() |> pivot_wider(values_from = Freq, names_from = Var2) -> nrcdata

I’m going to subset the data so it is just the stories and to drop the
“negative” and “positive” sentiments.

    nrcdata |> filter(!Var1 %in% c("A TALK ABOUT SAINTS", 
                                   "COME-TO-GOOD",
                                   "HISTORICAL NOTES",
                                   "PREFACE") ) |>
                select(-positive, -negative) ->
      nrcdata

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

    nrcdata <- as.data.frame(nrcdata)
    rownames(nrcdata) <- nrcdata$Var1
    nrcdata <- nrcdata[-1]

The distance object shows how far apart the stories are from each other.
This is a distance – think of it like inches. Higher distances means the
chapters are farther apart. A distance of 0 means that they are in the
same location.

    qs_dist <- dist(nrcdata)
    #qs_dist 

    max(qs_dist)

    ## [1] 0.008779445

    min(qs_dist)

    ## [1] 0.0005001657

The furthest apart are XXVI and VI.  
XXVI. RICHARD SELLAR AND THE ‘MERCIFUL MAN’  
VI. THE SHEPHERD OF PENDLE HILL

The closes are XXIV and XIII  
XIII. MAGNANIMITY XXIV. THE VICTORY OF AMOR STODDART

When I write up the project The Shepherd of Pendle Hill is one I will
definitely talk about because it has come up in a number of the
analyses.

    fviz_nbclust(nrcdata, pam, method = "silhouette")+
      theme_classic()

![](clustering_nrc_files/figure-markdown_strict/unnamed-chunk-7-1.png)

So there are two clusters. This is interesting because in some of the
other analyses it looked like there are stories about George Fox and
everything else.

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

    k2$data <- nrcdata
    k2$diss <- qs_dist
    fviz_cluster(k2,
        ##palette = c("#00AFBB", "#FC4E07"), # color palette
        ellipse.type = "t", # Concentration ellipse
        repel = TRUE, # Avoid label overplotting (slow)
        ggtheme = theme_classic()
    )

![](clustering_nrc_files/figure-markdown_strict/unnamed-chunk-9-1.png)

Story 1 is ” ‘STIFF AS A TREE, PURE AS A BELL’” Story 9 is “Strike
again”

Stand somewhat outside the ellipses. Both are retellings (sometimes
direct quotations from) of passages in the Journal of George Fox.

    dd |> select(cluster, section_title) |> 
      arrange(cluster, section_title) |>
      kable(caption = "Cluster assignments for stories")

<table>
<caption>Cluster assignments for stories</caption>
<thead>
<tr>
<th style="text-align: right;">cluster</th>
<th style="text-align: left;">section_title</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align: right;">1</td>
<td style="text-align: left;">I. ‘STIFF AS A TREE, PURE AS A BELL’</td>
</tr>
<tr>
<td style="text-align: right;">1</td>
<td style="text-align: left;">IX. UNDER THE YEW-TREES</td>
</tr>
<tr>
<td style="text-align: right;">1</td>
<td style="text-align: left;">V. ‘THE MAN IN LEATHER BREECHES’</td>
</tr>
<tr>
<td style="text-align: right;">1</td>
<td style="text-align: left;">VI. THE SHEPHERD OF PENDLE HILL</td>
</tr>
<tr>
<td style="text-align: right;">1</td>
<td style="text-align: left;">VII. THE PEOPLE IN WHITE RAIMENT</td>
</tr>
<tr>
<td style="text-align: right;">1</td>
<td style="text-align: left;">X. ‘BEWITCHED!’</td>
</tr>
<tr>
<td style="text-align: right;">1</td>
<td style="text-align: left;">XI. THE JUDGE’S RETURN</td>
</tr>
<tr>
<td style="text-align: right;">1</td>
<td style="text-align: left;">XII. ‘STRIKE AGAIN!’</td>
</tr>
<tr>
<td style="text-align: right;">1</td>
<td style="text-align: left;">XIII. MAGNANIMITY</td>
</tr>
<tr>
<td style="text-align: right;">1</td>
<td style="text-align: left;">XIV. MILES HALHEAD AND THE HAUGHTY
LADY</td>
</tr>
<tr>
<td style="text-align: right;">1</td>
<td style="text-align: left;">XIX. THE CHILDREN OF READING MEETING</td>
</tr>
<tr>
<td style="text-align: right;">1</td>
<td style="text-align: left;">XV. SCATTERING THE SEED</td>
</tr>
<tr>
<td style="text-align: right;">1</td>
<td style="text-align: left;">XVI. WRESTLING FOR GOD</td>
</tr>
<tr>
<td style="text-align: right;">1</td>
<td style="text-align: left;">XVII. LITTLE JAMES AND HIS JOURNEYS</td>
</tr>
<tr>
<td style="text-align: right;">1</td>
<td style="text-align: left;">XVIII. THE FIRST QUAKER MARTYR</td>
</tr>
<tr>
<td style="text-align: right;">1</td>
<td style="text-align: left;">XXII. AN UNDISTURBED MEETING</td>
</tr>
<tr>
<td style="text-align: right;">1</td>
<td style="text-align: left;">XXIII. BUTTERFLIES IN THE FELLS</td>
</tr>
<tr>
<td style="text-align: right;">1</td>
<td style="text-align: left;">XXIV. THE VICTORY OF AMOR STODDART</td>
</tr>
<tr>
<td style="text-align: right;">1</td>
<td style="text-align: left;">XXIX. FIERCE FEATHERS</td>
</tr>
<tr>
<td style="text-align: right;">1</td>
<td style="text-align: left;">XXVII. TWO ROBBER STORIES. WEST AND
EAST</td>
</tr>
<tr>
<td style="text-align: right;">1</td>
<td style="text-align: left;">XXX. THE THIEF IN THE TANYARD</td>
</tr>
<tr>
<td style="text-align: right;">1</td>
<td style="text-align: left;">XXXII. PREACHING TO NOBODY</td>
</tr>
<tr>
<td style="text-align: right;">2</td>
<td style="text-align: left;">II. ‘PURE FOY, MA JOYE’</td>
</tr>
<tr>
<td style="text-align: right;">2</td>
<td style="text-align: left;">III. THE ANGEL OF BEVERLEY</td>
</tr>
<tr>
<td style="text-align: right;">2</td>
<td style="text-align: left;">IV. TAMING THE TIGER</td>
</tr>
<tr>
<td style="text-align: right;">2</td>
<td style="text-align: left;">VIII. A WONDERFUL FORTNIGHT</td>
</tr>
<tr>
<td style="text-align: right;">2</td>
<td style="text-align: left;">XX. THE SADDEST STORY OF ALL</td>
</tr>
<tr>
<td style="text-align: right;">2</td>
<td style="text-align: left;">XXI. PALE WIND FLOWERS: OR THE LITTLE
PRISON MAID</td>
</tr>
<tr>
<td style="text-align: right;">2</td>
<td style="text-align: left;">XXV. THE MARVELLOUS VOYAGE OF THE GOOD
SHIP ‘WOODHOUSE’</td>
</tr>
<tr>
<td style="text-align: right;">2</td>
<td style="text-align: left;">XXVI. RICHARD SELLAR AND THE ‘MERCIFUL
MAN’</td>
</tr>
<tr>
<td style="text-align: right;">2</td>
<td style="text-align: left;">XXVIII. SILVER SLIPPERS: OR A QUAKERESS
AMONG THE TURKS</td>
</tr>
<tr>
<td style="text-align: right;">2</td>
<td style="text-align: left;">XXXI. HOW A FRENCH NOBLE BECAME A
FRIEND</td>
</tr>
</tbody>
</table>

    dd <- dd |> left_join(quaker_counts)

    ## Joining with `by = join_by(section_title)`

    table(dd$cluster, dd$`George Fox`)

    ##    
    ##     0 1 2 3 4 5 7 8 9 11 13 15 17 20
    ##   1 6 2 1 1 3 2 1 1 3  0  1  1  0  0
    ##   2 1 1 2 2 0 0 0 0 1  1  0  0  1  1

    table(dd$cluster, dd$`Quaker Words`)

    ##    
    ##     0 1 2 3 4 5 6 7 9 14 15 16 17 18 19 23 25 28 118
    ##   1 5 3 1 1 0 2 1 2 1  1  0  1  0  1  1  0  1  1   0
    ##   2 1 1 1 0 1 0 0 0 0  0  1  0  1  0  1  1  1  0   1

    dd |> group_by(cluster) |> 
      summarize(`N of Stories` = n(),
                `Quaker Words` = sum(`Quaker Words` > 0),
                `George Fox` = sum(`George Fox` > 0),
                `For Younger` = sum(`Younger Readers`)) |>
      kable(caption = "Characteristics of Stories")

<table>
<caption>Characteristics of Stories</caption>
<thead>
<tr>
<th style="text-align: right;">cluster</th>
<th style="text-align: right;">N of Stories</th>
<th style="text-align: right;">Quaker Words</th>
<th style="text-align: right;">George Fox</th>
<th style="text-align: right;">For Younger</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align: right;">1</td>
<td style="text-align: right;">22</td>
<td style="text-align: right;">17</td>
<td style="text-align: right;">16</td>
<td style="text-align: right;">9</td>
</tr>
<tr>
<td style="text-align: right;">2</td>
<td style="text-align: right;">10</td>
<td style="text-align: right;">9</td>
<td style="text-align: right;">9</td>
<td style="text-align: right;">7</td>
</tr>
</tbody>
</table>

The Stories in Cluster 1 are less likely to include “george\_fox” or use
Quaker words, but even more dramatic is that they are less than half as
likely to be in the appropriate for younger children category.
