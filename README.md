# Recoding religious identification in General Social Survey

R code for recoding GSS religion from three variables (`relig`, `denom`, `other`) according to Darren E. Sherkat and Derek Lehman, "After The Resurrection: The Field of the Sociology of Religion in the United States". Paper can be accessed through [link on this post](https://iranianredneck.wordpress.com/2016/11/29/why-reltrad-sucks-contesting-the-measure-of-american-religion/) and it contains SPSS syntax based on which I wrote function - also some of the coments in the code are pasted from there.

**Note**

Function is still not fully copmlete. It successfully works only when *all* final groups are present in data (e.g. 2014), at this moment only for respondent's religion and religion at the age of 16, and when data is imported from SPSS file to data frame, with trimed factor names and values, without use off missing values.

### Explanatoin
There is probably an easier way to do this, but 