# Recoding religious identification in General Social Survey

R function that recodes GSS religion from three variables (`relig`, `denom`, `other`) to 12 categories, according to Darren E. Sherkat and Derek Lehman, "After The Resurrection: The Field of the Sociology of Religion in the United States". Paper can be accessed through [link on this post](https://iranianredneck.wordpress.com/2016/11/29/why-reltrad-sucks-contesting-the-measure-of-american-religion/) and it contains SPSS syntax based on which I wrote function - also some of the comments in the code are pasted from there.

**Note**

Function is usable but with certain limitations. It successfully works only when *all* final groups are present in data (e.g. 2014), at this moment only for respondent's religion and religion at the age of 16 (although the process can be used on other derived variables), and when data is imported from SPSS file to data frame, with trimmed factor names and values, without use of missing values.

2014 GSS data is used as test, and it can be freely downloaded from NORC's [website](http://gss.norc.org/get-the-data).

### Explanation
There is probably an easier way to do this, but I started using imported GSS data from .sav files with categorical variables as factors using `foreign` package. However, R's nominal values of factors do not correspond to SPSS codes for factors (i.e. corresponding codes in GSS codebook) which causes problem in trying to directly replicate SPSS syntax. This is especially the case with use of variable `other` in recoding, since it has more than 200 values, and in addition some derived variables are coded only with numbers (e.g. mother's or father's `other`).

As a starting point for a more universal recoding function, this one uses three tables (in 'relig' folder) from which it pulls punch codes and labels of three variables that are assigned to appropriate vectors for every religious identification, per the paper and SPSS syntax. In addition, logical vectors are created for Sectarian Protestants with valid denomination codes for sorting between 'Sectarian Protestants', 'Christian - no group given', and 'other religions'. Twelve logical vectors, for respondent's belonging to each group, are created by checking against appropriate vectors containing codes and/or labels. Names are assigned and ordered, function prints frequency table of groups, and returns vector that can be assigned to a new variable. 

This allows that basic structure can be used for recoding of all three-variable sets of religious identification that can be imported as either factors with names or numerical values (or combination, as is the case with some sets), easy recoding into different number of groups, and printing of detailed classification that includes names of all religions and denominations.

Function still needs some cleaning, so it can work when not all twelve groups are present, as well as generalization for handling different coding of variables. I will periodically update it, as I occasionally improve it.

Please feel free to contact me with any questions or comments.