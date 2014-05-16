Modify
===============================================================

**Author: Sebastian Kranz, Ulm University** 

Description: modify is essentially just a wrapper for data.table syntax but can be used for other data containers. It is thought as an addition to dplyr functions. While the functionality can be replicated by mutate, modify can be much faster and more concise if only values for selected rows shall be modified.

## 1. Installation

To install from github, first install devtools from CRAN and then run

```s
library(devtools)
install_github(repo = "modify", username = "skranz")
```


## 2. Example code
```s

  library(microbenchmark)
  library(modify)

  n = 10
  df = data.frame(a= sample(1:3,n,replace=TRUE),
                   b= sample(1:100,n,replace=TRUE),
                   x=rnorm(n))
  # Set x to 100 where a==2
  modify(df,a==2, x=100)
  df
  # Set x to the mean value of b*100 in each group of a
  modify(df,.by=c("a"),
         x=mean(b)*100)
  df
  
  # Call with strings
  com = "x=200"
  s_modify(df,"a==2", com)
  

  
  # Benckmark compared to directly using data.table or dplyr 
  n = 1e6
  df = data.frame(a= sample(1:5,n,replace=TRUE),
                   b= sample(1:100,n,replace=TRUE),
                   x=rnorm(n))
  dt = as.data.table(df)
  
  tbl = as.tbl(df)  
  modify(tbl, a==2,x = x+100)
  mutate(df, x=ifelse(a==2,x+100,x))
  
  tbl
  microbenchmark(times = 5L,
    modify(tbl,a==2, x = x+100),
    modify(df,a==2, x = x+100),
    modify(dt,a==2, x = x+100),
    dt[a==2,x:=x+100],
    mutate.df = mutate(df, x=ifelse(a==2,x+100,x)),
    mutate.tbl = mutate(tbl, x=ifelse(a==2,x+100,x))
  )
  
#  Unit: milliseconds
                             expr       min        lq    median        uq        max neval
# modify(tbl, a == 2, x = x + 100)  51.37875  52.75087  53.43773  56.89211   65.82984     5
#  modify(df, a == 2, x = x + 100)  62.41217  73.70730  81.48766  95.45519  104.04200     5
#  modify(dt, a == 2, x = x + 100)  55.61156  58.30331  62.36006  65.13786   82.47056     5
#     dt[a == 2, `:=`(x, x + 100)]  61.03452  69.32567  69.56923  74.70600   81.87845     5
#                        mutate.df 766.40427 881.66008 887.30054 940.57168  964.20248     5
#                       mutate.tbl 769.94984 836.24982 881.46705 883.49444 1015.74663     
```
