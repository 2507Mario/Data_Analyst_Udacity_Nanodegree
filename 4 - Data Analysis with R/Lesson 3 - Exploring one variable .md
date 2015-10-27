
<h2>Exploring One Variable</h2>

<p>We perform EDA to understand the distribution of a variable and to check for anomalies and outliers. Learn how to quantify and visualize individual variables within a data set as we begin to make sense of a pseudo-data set of Facebook users. While the data set does not contain real user data, it does contain a wealth of information. Through the lesson, we will create histograms and boxplots, transform variables, and examine tradeoffs in visualizations.</p>

========================================================
## reading data
```{r}
getwd()
list.files()
## this, points to a tab separator
## pf <- read.csv('pseudo_facebook.tsv', sep='\t')
## otherwise we use:
pf <- read.delim('pseudo_facebook.tsv')
## check the variables (columns)
names(pf)

```

### Histogram of Users' Birthdays
Notes:

```{r Histogram of Users\' Birthdays}

library(ggplot2)

## plot the birthday date (dob_day)
## the second line changes the dates ox X axis to 1 to 31 days
## 3rd line: we split the data by month (faceting), dob_month
## 3 columns in the plot
qplot(x = dob_day, data = pf) +
  scale_x_discrete(breaks=1:31) +
  facet_wrap(~dob_month, ncol = 3)

```
#### What code would you enter to create a histogram of friend counts?

```{r Friend Count}
## friend count
## we add a limit to the x axis

library(ggplot2)
qplot(x = friend_count, data = pf, xlim = c(0, 1000))

```

### Faceting Friend Count
```{r Faceting Friend Count}
# What code would you add to create a facet the histogram by gender?
# Add it to the code below.
# we have to split it into men and women: facet_wrap
qplot(x = friend_count, data = pf, binwidth = 10) +
  scale_x_continuous(limits = c(0, 1000),
                     breaks = seq(0, 1000, 50)) +
  facet_wrap(~gender)
```

***

### Omitting NA Values
Notes:

```{r Omitting NA Values}
## we get rid of the NA values in gender: subset
qplot(x = friend_count, data = subset(pf, !is.na(gender)), binwidth = 10) +
  scale_x_continuous(limits = c(0, 1000), breaks = seq(0, 1000, 50)) +
  facet_wrap(~gender)
```

***

### Statistics 'by' Gender
Notes:

```{r Statistics \'by\' Gender}
## friend count by gender and get the summary
by(pf$friend_count, pf$gender, summary)
```

### Tenure

```{r Tenure}
## creating histogram of tenure, yearly
## also, adding colors and breaks ans limits
qplot(x = tenure/365, data = pf,
      xlab = 'Number of years using Facebook',
      ylab = 'Number of users in sample',
      binwidth = 1,
      color = I('black'), fill = I('#F79420')) +
  scale_x_continuous(breaks = seq(1, 7, 1), limits = c(0,7))
  
```

### User Ages
Notes:

```{r User Ages}
## plot user ages
qplot(x = age, data = pf,
      xlab = 'Ages',
      ylab = 'Number of users in sample',
      binwidth = 1,
      color = I('black'), fill = I('#F79420')) +
  scale_x_continuous(breaks = seq(0, 113, 5))
```

### Transforming Data
Notes:

```{r Transforming Data}

library(gridExtra)

p1 <-qplot(x = friend_count, data = pf)
p2 <-qplot(x = log10(friend_count + 1), data = pf)
p3 <-qplot(x = sqrt(friend_count), data = pf)

grid.arrange(p1, p2, p3, ncol = 1)

## another way to do it:
p1 <- ggplot(aes(x = friend_count), data = pf) + geom_histogram()
p2 <- p1 + scale_x_log10()
p3 <- p1 + sqrt()
grid.arrange(p1, p2, p3, ncol = 1)

```

### Frequency Polygons

```{r Frequency Polygons}
## compare 2 or more distributions at once
#### original
qplot(x = friend_count, data = subset(pf, !is.na(gender)), binwidth = 10) +
  scale_x_continuous(limits = c(0, 1000), breaks = seq(0, 1000, 50)) +
  facet_wrap(~gender)
#### with frequency polygons
## we add geom and instead of using facet_wrap(gender) we use color
qplot(x = friend_count, data = subset(pf, !is.na(gender)),
      binwidth = 10, geom = 'freqpoly', color = gender) +
  scale_x_continuous(limits = c(0, 1000), breaks = seq(0, 1000, 50))

## now with proportions, change Y axis:
qplot(x = friend_count, y = ..count../sum(..count..),
      data = subset(pf, !is.na(gender)),
      xlab = 'Friend count',
      ylab = 'Proportion of users with that friend count',
      binwidth = 10, geom = 'freqpoly', color = gender) +
  scale_x_continuous(limits = c(0, 1000), breaks = seq(0, 1000, 50))
## the above does not tell much
## we do it like follows:
qplot(x = www_likes, data = subset(pf, !is.na(gender)),
      geom = 'freqpoly', color = gender) +
  scale_x_continuous() +
  scale_x_log10()

## numerically 
by(pf$www_likes, pf$gender, sum)

```

***

### Likes on the Web
Notes:

```{r Likes on the Web}
# which gender makes more likes on the www_likes
qplot(x = www_likes, data = subset(pf, !is.na(gender)),
      binwidth = 10, geom = 'freqpoly', color = gender) +
  scale_x_continuous(limits = c(0, 500), breaks = seq(0, 500, 100))
```


***

### Box Plots
Notes:

```{r Box Plots}
## limit to 1000 method 1
qplot(x = gender, y = friend_count,
      data = subset(pf, !is.na(gender)),
                    geom = 'boxplot') +
  scale_y_continuous(limits = c(0, 1000))

## limit to 1000 method 2
qplot(x = gender, y = friend_count,
      data = subset(pf, !is.na(gender)),
                    geom = 'boxplot', ylim = c(0, 1000))

## the 2 methods above remove some data
## it is better then to use the following method:
## coord_cartesian
qplot(x = gender, y = friend_count,
      data = subset(pf, !is.na(gender)),
                    geom = 'boxplot') +
  coord_cartesian(ylim = c(0, 1000))

## now let us close so 250 for more clarity
qplot(x = gender, y = friend_count,
      data = subset(pf, !is.na(gender)),
                    geom = 'boxplot') +
  coord_cartesian(ylim = c(0, 250))
```

### Getting Logical
Notes:

```{r Getting Logical}
####
summary(pf$mobile_likes)

summary(pf$mobile_likes > 0)

mobile_check_in <- NA
pf$mobile_check_in <- ifelse(pf$mobile_likes > 0, 1, 0)
pf$mobile_check_in <- factor(pf$mobile_check_in)
summary(pf$mobile_check_in)

## solution What % of check in using mobile?

sum(pf$mobile_check_in == 1)/length(pf$mobile_check_in)

```
