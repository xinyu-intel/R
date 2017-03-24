function apply in R
================

本文介绍R语言中的apply函数簇，apply函数簇是使用R做科学计算的基本函数簇之一，下面的例子参考了刘顺祥每天进步一点点R语言教程，编译成notebook形式，便于学习！

如果计算涉及到一个单一的向量，而结果也是一个向量，tapply函数是一个可选项，不同于 aggregate函数，它返回一个向量或数组，这使得其单个元素很容易被访问。

将组定义为矩阵的行或列，即操作目标为矩阵的每一列或行时，apply函数时最佳选择。该 函数通常会返回一个向量或数组，但根据行或列操作的结果维度不同，将返回一个列表。

将组定义为列表中的元素。如果组已经形成列表元素，那么sapply或lapply函数比较适合， 它们的区别是lapply返回一个列表，而sapply可将输出简化为向量或数组。有时可以结合使 用split函数，将需要处理的数据创建为一个列表，然后再使用这两个函数。

如果所要计算函数的参数为一个矩阵或数组，可以考虑使用mapply函数，该函数非常的灵 活和简单，其返回的结果一般是列表形式。

``` r
#如果想分析iris数据集中Sepal.Length在各个花种中的最大值，可以通过tapply函数实现，这里的Sepal.Length和Species为两个向量，且各自的长度均相等。
max_sepal.length<-tapply(iris$Sepal.Length,iris$Species,max)
max_sepal.length
```

    ##     setosa versicolor  virginica 
    ##        5.8        7.0        7.9

``` r
#rpois make a set of poisson distribution;rt make a set of student distribution.
x<-rpois(100,2)
y<-rpois(100,3)
z<-rt(100,2,3)
data<-data.frame(x=x,y=y,z=z)
head(data)
```

    ##   x y         z
    ## 1 2 1 2.3771857
    ## 2 4 3 5.3422753
    ## 3 2 4 0.8927298
    ## 4 1 1 2.0148833
    ## 5 1 4 2.5353424
    ## 6 5 5 4.8125707

``` r
tapply(data$z,INDEX = list(data$x,data$y),FUN = sum)
```

    ##           0         1         2         3         4         5        6
    ## 0  3.810185  6.750107 12.369562  5.438668 20.392904 12.654777 2.849390
    ## 1 40.626444 25.575616 26.953215 24.930124 28.103226  6.628725 1.749775
    ## 2        NA 20.121185 31.358013 43.673581 29.037726 13.647852 3.189423
    ## 3  2.052972 22.001152 12.895461 13.174466 13.104459        NA       NA
    ## 4        NA 19.229854  1.788801  5.342275  6.309771        NA 7.815427
    ## 5        NA  5.266137  8.481525        NA  5.083856  4.812571       NA
    ##          7        9
    ## 0       NA 7.379446
    ## 1       NA       NA
    ## 2       NA       NA
    ## 3       NA       NA
    ## 4 3.067641       NA
    ## 5       NA       NA

``` r
#当数据具备数组的特性，可通过apply函数对数据的每个维度进行运算，该函数需要三个参数：需要计算的数组、运算维度的索引号和使用的函数。
mat<-matrix(1:12, ncol = 3)
mat
```

    ##      [,1] [,2] [,3]
    ## [1,]    1    5    9
    ## [2,]    2    6   10
    ## [3,]    3    7   11
    ## [4,]    4    8   12

``` r
apply(mat,MARGIN = 2,FUN = scale)
```

    ##            [,1]       [,2]       [,3]
    ## [1,] -1.1618950 -1.1618950 -1.1618950
    ## [2,] -0.3872983 -0.3872983 -0.3872983
    ## [3,]  0.3872983  0.3872983  0.3872983
    ## [4,]  1.1618950  1.1618950  1.1618950

``` r
scale(mat)
```

    ##            [,1]       [,2]       [,3]
    ## [1,] -1.1618950 -1.1618950 -1.1618950
    ## [2,] -0.3872983 -0.3872983 -0.3872983
    ## [3,]  0.3872983  0.3872983  0.3872983
    ## [4,]  1.1618950  1.1618950  1.1618950
    ## attr(,"scaled:center")
    ## [1]  2.5  6.5 10.5
    ## attr(,"scaled:scale")
    ## [1] 1.290994 1.290994 1.290994

``` r
#统计各个列的均值，为比较显式循环和apply的隐式循环，程序如下，就可以比较出两种方式的效率：
mat<-matrix(1:100000,ncol = 100)
system.time(apply(mat,2,mean))
```

    ##    user  system elapsed 
    ##   0.000   0.000   0.002

``` r
mean_self<-function(data){
  row_n<-dim(data)[1]
  col_n<-dim(data)[2]
  c_m<-numeric(col_n)
  for(i in 1:col_n){
    sum<-0
    for(j in 1:row_n){
      sum<-sum+data[j,i]
    }
    c_m[i]<sum/row_n
  }
  return(c_m)
}
system.time(mean_self(mat))
```

    ##    user  system elapsed 
    ##   0.052   0.008   0.058

``` r
#lapply()函数和sapply()函数把一个列表或向量作为其第一个参数，再把需要应用到每个列表元素的函数作为它的第二个参数。其实它也应用到了循环，是一种隐式的循环，对列表的每一个元素做同样的函数计算。
x<-c('Today is Sunday','The Weather is very nice','Please check your R language synax','R语言 是统计学中 非常实用的 工具')
#strsplit函数是根据某个分隔符将字符串分割为多个单词
split_x<-strsplit(x,' ')
mem_length1<-lapply(split_x,length)
mem_length2<-sapply(split_x,length)
unlist(mem_length1)
```

    ## [1] 3 5 6 4

``` r
unlist(mem_length2)
```

    ## [1] 3 5 6 4

``` r
#使用sapply函数的另一个重要问题涉及到数据框。当数据框被视为列表时，数据框的每一列看着独立的列表元素。
sapply(iris,class)
```

    ## Sepal.Length  Sepal.Width Petal.Length  Petal.Width      Species 
    ##    "numeric"    "numeric"    "numeric"    "numeric"     "factor"

``` r
sapply(ChickWeight,mode)
```

    ##    weight      Time     Chick      Diet 
    ## "numeric" "numeric" "numeric" "numeric"

``` r
#通过以上的应用，可以提取满足特定条件的数据框的列
r1<-iris[,sapply(iris,class)=='numeric']
r2<-CO2[,sapply(CO2,class)=='factor']
head(r1)
```

    ##   Sepal.Length Sepal.Width Petal.Length Petal.Width
    ## 1          5.1         3.5          1.4         0.2
    ## 2          4.9         3.0          1.4         0.2
    ## 3          4.7         3.2          1.3         0.2
    ## 4          4.6         3.1          1.5         0.2
    ## 5          5.0         3.6          1.4         0.2
    ## 6          5.4         3.9          1.7         0.4

``` r
head(r2)
```

    ##     Type  Treatment
    ## 1 Quebec nonchilled
    ## 2 Quebec nonchilled
    ## 3 Quebec nonchilled
    ## 4 Quebec nonchilled
    ## 5 Quebec nonchilled
    ## 6 Quebec nonchilled

``` r
#接下来使用自编函数加入到sapply函数中，实现循环。该自编函数的目的是计算出1000个100*5的矩阵中最大相关系数的均值。这里很关键的一点是给自编函数传一个虚拟参数i用来循环。
fun2<-function(i,n,m){
  mat=matrix(rnorm(n*m),ncol=m)
  corr=cor(mat)
  diag(corr)=NA
  max(corr,na.rm = TRUE)
}
max_r<-sapply(1:1000,FUN = fun2,n=100,m=5)
mean_r<-mean(max_r)
mean_r
```

    ## [1] 0.154922

``` r
#mapply函数的应用：该函数的第一个参数为指定的函数，第二个参数为指定函数的参数。如果根据某种正则表达式将一个字符向量的对应特征取出来，例如取出'qaws1few4g'中的'1f'和'4g'
#生成一组字符串向量
char<-c('qwd121dad12d2edas','12123sd12asd12da2d12','qwe1ad123da','zdad1b')
#采用gregexpr函数，使用的正则表达式为'[0-9][a-z]'
parts<-gregexpr(pattern = '[0-9][a-z]',char)
parts
```

    ## [[1]]
    ## [1]  6 11 13
    ## attr(,"match.length")
    ## [1] 2 2 2
    ## attr(,"useBytes")
    ## [1] TRUE
    ## 
    ## [[2]]
    ## [1]  5  9 14 17
    ## attr(,"match.length")
    ## [1] 2 2 2 2
    ## attr(,"useBytes")
    ## [1] TRUE
    ## 
    ## [[3]]
    ## [1] 4 9
    ## attr(,"match.length")
    ## [1] 2 2
    ## attr(,"useBytes")
    ## [1] TRUE
    ## 
    ## [[4]]
    ## [1] 5
    ## attr(,"match.length")
    ## [1] 2
    ## attr(,"useBytes")
    ## [1] TRUE

``` r
#自编函数，取出相应的字符串
fun3<-function(text,len) substring(text,len,len+attr(len,'match.length')-1)
#取出相应的字符串存放到res中
res<-mapply(fun3,char,parts)
res
```

    ## $qwd121dad12d2edas
    ## [1] "1d" "2d" "2e"
    ## 
    ## $`12123sd12asd12da2d12`
    ## [1] "3s" "2a" "2d" "2d"
    ## 
    ## $qwe1ad123da
    ## [1] "1a" "3d"
    ## 
    ## $zdad1b
    ## [1] "1b"

最后总结一下： tapply()的被分析对象必须且只能是向量 apply()的被分析对象必须且只能是矩阵或数组 sapply()的被分析对象必须且只能是向量或列表 lapply()的被分析对象必须且只能是向量或列表 mapply()的被分析对象必须是函数
