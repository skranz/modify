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
```
