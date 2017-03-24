R语言中的条件和循环语句
================

一般在**数据处理或建模过程中会使用到R语言中的控制流**，控制流主要有以下三类，即:

1.  if或switch分支语句
2.  for循环
3.  while循环

下面就说说这几种控制流.

一、if或switch分支语句
----------------------

**if分支语句，一般有双分支和多分支**,对于二分支，我建议使用**ifelse()函数**,该函数是R语言内置的函数，**运行速度是非常快的**。下面举个例子来说明一下：

``` r
x <- rnorm(100000)
y <- numeric()
system.time(
  for(i in 1:length(x)){
    if(x[i] < 0.5) y[i] = 0
    else y[i] = 1
    }
  )
```

    ##    user  system elapsed 
    ##  22.160   0.872  23.034

``` r
system.time(y <- ifelse(x < 0.5, 0, 1))
```

    ##    user  system elapsed 
    ##   0.012   0.000   0.011

同样的目的，但两者的差距是非常大的。

如果在实际业务中，二分支不能够解决问题的话，这时就不得不考虑多分支的if语句了，下面举个应用的场景：

``` r
#随机生成性别向量
gender <- sample(c('F','M','Unknow'), size = 100000, replace = TRUE)
#现在需要解决的一个问题是，重编码，即F改为Female，M改为Male，其他不变，
#一般会考虑使用if的多分支语句解决该问题
gender2 <- character()
system.time(
  for(i in 1:length(gender)){
    if(gender[i]=='F') gender2[i] = 'Female'
    else if(gender[i]=='M') gender2[i] = 'Male'
    else gender2[i] = 'Unknow'
    }
  )
```

    ##    user  system elapsed 
    ##  41.380   0.616  41.997

很明显，对于10W行的数据，**这样的多分支仍然显得非常慢**，这个时候**我建议使用switch分支语句**

``` r
#将字符变量设为有序因子
gender3 <- factor(gender, levels = c('F','M','Unknow'))
#自定义switch函数返回值
s <- function(case) switch(case,'Female','Male','Unknow')
system.time(gender4 <- sapply(as.numeric(gender3),s))
```

    ##    user  system elapsed 
    ##   0.092   0.004   0.099

这样的效率还是非常高的，**一般多水平的离散变量重编码（一一对应时）问题建议使用switch分支语句，当然你还可以使用within语句实现该功能**，如：

``` r
system.time(
  within(data.frame(gender),{
    gender5 <- ''
    gender5[gender=='F'] <- 'Female'
    gender5[gender=='M'] <- 'Male'
    gender5[gender=='Unknow'] <- 'Unknow'
    })
  )
```

    ##    user  system elapsed 
    ##   0.020   0.000   0.022

虽然使用within()函数实现重编码有一点点复杂，但计算效率还是非常高的！

二、for循环
-----------

其实在讲if分支语句时，我们就用到了for循环，**for循环实质上是对某个对象进行遍历**。**一般for循环会配合if语句进行遍历的条件判断**，下面通过一个实例来看看**for循环的应用**：

### 方法一：

``` r
score <- round(runif(100000, min = 40, max = 92))
#计数器
i <- 1
score2 <- ''
system.time(
  for (score in score){
    if (score < 60) score2[i] <- '不及格'
    else if (score >= 60 & score < 80) score2[i] <- '合格'
    else score2[i] <- '优秀'
    i <- i+ 1
    }
  )
```

    ##    user  system elapsed 
    ##  37.980   0.536  38.513

**该方法是不知道该循环多少次时**，可以使用这种for语句直接遍历向量中的每一个值。

### 方法二：

``` r
score <- round(runif(100000, min = 40, max = 92))
score3 <- ''
system.time(
  for (i in 1:length(score)){
    if (score[i] < 60) score3[i] <- '不及格'
    else if (score[i] >= 60 & score[i] < 80) score3[i] <- '合格'
    else score3[i] <- '优秀'
    }
  )
```

    ##    user  system elapsed 
    ##  40.796   0.616  41.414

**在知道循环多少次的前提下可使用这样的for语句**。上面这个例子**不是一对一的重编码问题**，**建议使用within()内置函数解决编码**，可以大大提高计算速度。

``` r
score <- round(runif(100000, min = 40, max = 92))
system.time(
  score <- within(
    data.frame(score),{
      score4 <- ''
      score4[score < 60] <- '不及格'
      score4[score >= 60 & score < 80] <- '合格'
      score4[score >= 80] <- '优秀'
      })
  )
```

    ##    user  system elapsed 
    ##   0.008   0.000   0.008

如果不是这样的重编码问题，可能就需要硬着头皮使用for循环和if判断条件了，这种显式for循环会拉低R的计算效率。当然，**还有一种可弥补的方法就是使用sapply()函数**。

三、while循环
-------------

``` r
# while循环语法：
# i <- 1
# while(condition){
#   statements
#   i = i + 1
# }

# 这里必须强调的是，while循环语句中必须给一个计数器i，否则无法进行一轮又一轮的循环迭代操作。
#这里举一个简单的计算1~1000的和：
s <- 0
i <- 1
while(i <= 1000){
  s <- s + i
  i <- i + 1
  }
s
```

    ## [1] 500500

其实，**for循环基本可以替代while循环语句，在实际的工作中，我比较喜欢使用for循环**，因为for循环可以实现“已知循环次数”和“未知循环次数”的功能，而且for循环也更加直观。
