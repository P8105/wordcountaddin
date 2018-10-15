<!-- README.md is generated from README.Rmd. Please edit that file -->
wordcountaddin <img src="inst/logo.png" align="right" height="130" />
=====================================================================

**This is a fork of the [original package](https://github.com/benmarwick/wordcountaddin). The only change is that results of `wordcountaddin::text_stats` is a data frame rather than the result of `knitr::kable`; this change makes it possible to easily export word counts for many students submissions using `purrr`**

This R package is an [RStudio addin](https://rstudio.github.io/rstudioaddins/) to count words and characters in text in an [R markdown](http://rmarkdown.rstudio.com/) document. It also has a function to compute readability statistics so you can get an indication of how easy or difficult your document is to read.

You can count words in your Rmd file in three ways:

-   In a selection of text in your active Rmd, by selecting some text with your mouse in in RStudio and using the Word count Addin
-   All the words in your active Rmd in RStudio, by using the Word count Addin with no text selected
-   All the words in an Rmd file, directly using the `word_count` function from the console or command line (RStudio not required), and specifiying the filename as an argument to the function (e.g. `wordcountaddin::text_stats("my_file.Rmd")`)

Independent of an Rmd file, you can also count words in a character vector from the console using the `text_stats_chr` function (and there is `readability_chr` for readability).

Word count
----------

When counting words in the text of your Rmd document, these things will be ignored:

-   YAML front matter
-   code chunks and inline code
-   text in HTML comment tags: `<!-- text -->`
-   HTML tags in the text: `<br>`, `</br>`
-   inline URLs in this format: `[text of link](url)`
-   images with captions in this format: `![this is the caption](/path/to/image.png)`
-   header level indicators such as `#` and `##`, etc.

And because my regex is quite simple, the word count function may also ignore parts of your actual text that resemble these things.

The word count will include text in headers, block quotations, verbatim code blocks, tables, raw LaTeX and raw HTML.

In general, there are numerous ways to count words, with no widely accepted standard method. The variety of methods is due to differences in the definitions of a word and a sentence. Run `?stringi::stri_stats_latex` and `?koRpus::describe` to learn more about the word counting methods.

For this addin I've included two methods, mostly out of curiosity to see how they differ from each other. I use functions from the [stringi](https://cran.r-project.org/web/packages/stringi/index.html) and [koRpus](https://cran.r-project.org/web/packages/koRpus/index.html) packages. If you're curious, you can compare the results you get with this addin to an online tool such as <http://wordcounttools.com/>.

The output of the `Word count` function is a markdown table in your R console that might look like this:

    |Method          |koRpus      |stringi       |
    |:---------------|:-----------|:-------------|
    |Word count      |107         |104           |
    |Character count |604         |603           |
    |Sentence count  |10          |Not available |
    |Reading time    |0.5 minutes |0.5 minutes   |

If you want resuse these results in other R functions, you can use an unexported function like this `wordcountaddin:::text_stats_fn_(text)`, where `text` is a character vector of your text (with length one, ie. all your text in a single character string). The output will be a list object, and will include several other items not shown in the markdown table.

Readability
-----------

The readability function ignores all the same parts of the text as the word count function, and then computes the values of a bunch of [readability statistics](https://en.wikipedia.org/wiki/Readability_test).

Most of these readability measurements aim to approximate the years of education required to understand your text. They look at the number of characters and syllables per word, the number of words per sentence, and so on. They don't analyse the meaning of the words. A score of around 10-12 is roughly the reading level on completion of high school in the US. These stats are computed by the [koRpus](https://cran.r-project.org/web/packages/koRpus/index.html) package.

There about 27 measurements that this readability function returns (depending on how long your text is), including the Automated Readability Index (ARI), Coleman-Liau, th Flesch-Kincaid Grade Level, and the Simple Measure of Gobbledygook (SMOG). For the full list of readability measurements that are returned by the readability function, run `?koRpus::readability`. That help page also shows the formulae and citations for each statistic (and an additional 20-odd other readability statistics not used here).

Readability stats are, of course, no substitute for critical self-reflection on the effectiveness of your writing at communicating ideas and information. To help with that, read [*Style: Toward Clarity and Grace*](http://www.amazon.com/dp/0226899152).

The output of the `readability` function is a markdown table in your R console that might look like this:


    |index                 |flavour     |raw   |grade |age  |
    |:---------------------|:-----------|:-----|:-----|:----|
    |ARI                   |            |      |2.31  |     |
    |Coleman-Liau          |            |66    |4.91  |     |
    |Danielson-Bryan DB1   |            |6.46  |      |     |
    |Danielson-Bryan DB2   |            |60.39 |6     |     |
    |Dickes-Steiwer        |            |53.07 |      |     |
    |ELF                   |            |1.83  |      |     |
    |Farr-Jenkins-Paterson |            |66.81 |8-9   |     |
    |Flesch                |en (Flesch) |69.57 |8-9   |     |
    |Flesch-Kincaid        |            |      |4.85  |9.8  |
    |FOG                   |            |      |7.84  |     |
    |FORCAST               |            |      |10.28 |15.3 |
    |Fucks                 |            |23.38 |4.83  |     |
    |Linsear-Write         |            |      |2.35  |     |
    |LIX                   |            |32.41 |< 5   |     |
    |nWS1                  |            |      |4.19  |     |
    |nWS2                  |            |      |4.72  |     |
    |nWS3                  |            |      |4.14  |     |
    |nWS4                  |            |      |3.64  |     |
    |RIX                   |            |1.42  |5     |     |
    |SMOG                  |            |      |8.08  |13.1 |
    |Strain                |            |2.44  |      |     |
    |TRI                   |            |-94   |      |     |
    |Tuldava               |            |2.57  |      |     |
    |Wheeler-Smith         |            |18.33 |2     |     |

Similar to the `word count` function, if you want resuse these results in other R functions, you can use an unexported function like this `wordcountaddin:::readability_fn_(text)`, where `text` is a character vector of your text (with length one, ie. all your text in a single character string). The output will be a list object with slightly more detail than the summary table above.

Inspiration for this addin came from [jadd](https://github.com/jennybc/jadd) and [WrapRmd](https://github.com/tjmahr/WrapRmd).

How to install
--------------

Install with `devtools::install_github("benmarwick/wordcountaddin",  type = "source", dependencies = TRUE)`

Go to `Tools > Addins` in RStudio to select and configure addins.

How to use
----------

1.  Open a Rmd file in RStudio.
2.  Select some text, it can include YAML, code chunks and inline code
3.  Go to `Tools > Addins` in RStudio and click on `Word count` or `Readability`. Computing `Readability` may take a few moments on longer documents because it has to count syllables for some of the stats.
4.  Look in the console for the output

Feedback, contributing, etc.
----------------------------

Please [open an issue](https://github.com/benmarwick/wordcountaddin/issues/new) if you find something that doesn't work as expected. Note that this project is released with a [Guide to Contributing](CONTRIBUTING.md) and a [Contributor Code of Conduct](CONDUCT.md). By participating in this project you agree to abide by its terms.
