---
title: "Recoding GSS religion"
output: 
  html_document:
    keep_md: true
---



***

R function that recodes GSS religion from three variables (`relig`, `denom`, `other`) to 12 categories, according to Darren E. Sherkat and Derek Lehman, ["After The Resurrection: The Field of the Sociology of Religion in the United States"](https://iranianredneck.wordpress.com/2016/11/29/why-reltrad-sucks-contesting-the-measure-of-american-religion/){target="_blank"}.^[Paper contains SPSS syntax based on which I wrote function - also some of the comments in the code are pasted from there.]

***

Main goal is to address four problems:

1. Recode religion successfully.
2. Easily (automatically) recode whole family of religion variables in GSS (religion, religion at 16, parents' religion, and special modules on friends' religion).
3. Recode no matter how GSS data is imported to R.
4. Provide two ways of grouping religion (12 and 7 groups).

At this point, only 1 works well, while 2 and 3 are partial. Beside huge number of categories (cca 200 for other), main problem is that punch codes from GSS codebook do not correspond to factor values once data is imported (some codes and blocks of numbers are just skipped).

## Example of use


```r
library(foreign)

GSS <- read.spss("GSS2014.sav",
                 to.data.frame = TRUE,
                 trim.factor.names = TRUE,
                 trim_values = TRUE,
                 use.missings = FALSE
                 )

summary(GSS[, c("relig", "denom", "other")])
```

```
##         relig                   denom                      other     
##  PROTESTANT:1125   IAP             :1264   IAP                :2269  
##  CATHOLIC  : 606   NO DENOMINATION : 298   Pentecostal        :  51  
##  NONE      : 522   OTHER           : 254   Mormon             :  32  
##  CHRISTIAN : 134   BAPTIST-DK WHICH: 151   Jehovah's Witnesses:  22  
##  JEWISH    :  40   SOUTHERN BAPTIST: 146   Church of Christ   :  17  
##  OTHER     :  27   UNITED METHODIST: 110   NA                 :  16  
##  (Other)   :  84   (Other)         : 315   (Other)            : 131
```

If we simply apply function to the GSS 2014 data,^[Data is not included to github repository since it is larger than 10MB. It can be downloaded freely from NORC website.] we get following:


```r
GSS$religion <- rec_relig_12(GSS$relig, GSS$denom, GSS$other)
```

```
##                           Freq Relative  Cumul
## Baptist                    324    12.77  12.77
## Catholic or Orthodox       615    24.23  37.00
## Christian, no group given  307    12.10  49.09
## Dont know                    3     0.12  49.21
## Episcopalian                39     1.54  50.75
## Jewish                      40     1.58  52.32
## Liberal Protestant          77     3.03  55.36
## Lutheran                    92     3.62  58.98
## Moderate Protestant        206     8.12  67.10
## Mormon                      32     1.26  68.36
## No answer                   15     0.59  68.95
## None                       522    20.57  89.52
## Other religion              88     3.47  92.99
## Sectarian Protestant       178     7.01 100.00
```

```r
summary(GSS$religion)
```

```
##                   Baptist      Catholic or Orthodox 
##                       324                       615 
## Christian, no group given                 Dont know 
##                       307                         3 
##              Episcopalian                    Jewish 
##                        39                        40 
##        Liberal Protestant                  Lutheran 
##                        77                        92 
##       Moderate Protestant                    Mormon 
##                       206                        32 
##                 No answer                      None 
##                        15                       522 
##            Other religion      Sectarian Protestant 
##                        88                       178
```

```r
tail(GSS[, c("relig", "denom", "other", "religion")])
```

```
##           relig            denom                          other
## 2533 PROTESTANT BAPTIST-DK WHICH                            IAP
## 2534 PROTESTANT UNITED METHODIST                            IAP
## 2535       NONE              IAP                            IAP
## 2536       NONE              IAP                            IAP
## 2537   CATHOLIC              IAP                            IAP
## 2538 PROTESTANT            OTHER Congregationalist, 1st Congreg
##                  religion
## 2533              Baptist
## 2534  Moderate Protestant
## 2535                 None
## 2536                 None
## 2537 Catholic or Orthodox
## 2538   Liberal Protestant
```

## Attention!

Function is usable but with certain limitations. At this moment it successfully works only for respondent's religion and religion at the age of 16 (although the process can be used on other derived variables), and when data is imported from SPSS file to data frame, with trimmed factor names and values, without use of missing values.

*If this does not suit your need, ad hoc workaround is to import GSS dataset in your fashion of choice (e.g. with use of `NA`) and identical dataset as required by this function. You should imediatlly use the function on the second dataframe, and assing values of new variable to the first. Ideally, you would also assing the variables used for recoding, and check them against main dataframe that everything is OK.*

## Explanation of how it works

There is probably an easier way to do this, but I started using imported GSS data from .sav files with categorical variables as factors using foreign package. However, R's nominal values of factors do not correspond to SPSS codes for factors (i.e. corresponding punches in GSS codebook) which causes problem in trying to directly replicate SPSS syntax.

Here we can see the sample from 2014 GSS file (including new recoded var):


```r
str(GSS[, c("relig", "denom", "other", "religion")])
```

```
## 'data.frame':	2538 obs. of  4 variables:
##  $ relig   : Factor w/ 16 levels "IAP","PROTESTANT",..: 3 3 2 3 3 2 3 3 3 3 ...
##  $ denom   : Factor w/ 30 levels "IAP","AM BAPTIST ASSO",..: 1 1 16 1 1 26 1 1 1 1 ...
##  $ other   : Factor w/ 202 levels "IAP","Hungarian Reformed",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ religion: Factor w/ 14 levels "Baptist","Catholic or Orthodox",..: 2 2 8 2 2 5 2 2 2 2 ...
```


As a starting point for a more universal recoding function, this one uses three tables (in `religion_punches` folder, heads presented here) from which three vectors are formed (`relig`, `denom`, and `other`) where index corresponds to punch number, and label is value.


```
## $relig.csv
##   punch      label
## 1     0        IAP
## 2     1 PROTESTANT
## 3     2   CATHOLIC
## 4     3     JEWISH
## 5     4       NONE
## 6     5      OTHER
## 
## $denom.csv
##   punch               label
## 1     0                 IAP
## 2    10     AM BAPTIST ASSO
## 3    11   AM BAPT CH IN USA
## 4    12 NAT BAPT CONV OF AM
## 5    13   NAT BAPT CONV USA
## 6    14    SOUTHERN BAPTIST
## 
## $punch.csv
##   punch                              label
## 1     0                                IAP
## 2     1                 Hungarian Reformed
## 3     2         Evangelical Congregational
## 4     3 Ind Bible, Bible, Bible Fellowship
## 5     5                 Church of Prophecy
## 6     6            New Testament Christian
```

For each *new* religious identification, separate vectors (for `relig`, `denom`, and/or `other`) are created that contain punch codes and labels, per paper and SPSS syntax. In addition, logical vectors are created for Sectarian Protestants with valid denomination codes for sorting between 'Sectarian Protestants', 'Christian - no group given', and 'other religions'.

*This approach is somewhat superior to usual practices of recoding, since it can be confirmed through basic set operations that there is no overlap between groups or that some categories are left out.*

Next, twelve logical vectors, for respondent's belonging to each group, are created by checking against appropriate vectors containing labels. Names are assigned, function prints frequency table, and returns vector that can be assigned to a new variable.

This allows that basic structure can be generalized for recoding of all three-variable sets of religious identification that can be imported as either factors with names or numerical values (or combination, as is the case with some groups), easy recoding into different number of groups, and printing of detailed classification that includes names of all religions and denominations.


## Final remarks

Function still needs some fixing/generalization for handling different coding of variables. I will periodically update it, as I occasionally improve it.

Please feel free to contact me with any questions or comments.

If you find function handy and use it, cite the source paper; in addition acknowledging my contribution is appreciated.
