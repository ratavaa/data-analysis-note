# data-analysis-note R
# R Data Analysis

## Data format

### vector

elements in the same vector has to be the same data type

```   R
v1<-c(1,2,3)
v2<-c('a','b','c')
v1[2]
2
v1[2:3]
2,3 (2 to 3)
v1[c(1,3)]
1,3 (1 and 3)
```



### matrix

``` R
m1<-matrix(11:30,4 5)
11 to 30, 4 rows and 5 columns
m1[1,2] # 1st row 2nd col
m1[1,] #1st row and all cols
m1[,1] #1st col
m1[c(1,3),2] #2nd col of the 1st 3rd row
```



### array

```R
a1<-array(11:30, c(2,5,2))
#2 row, 5 col, 2 dimensions
a1[1,2,1] #1row,2 col, 1 dimemsion
```

matrix is a kind of array



### list

list can be rather complicated, there's more to replenish

```R
mylist<-(df,a1,m1)
mylist[[1]]  #returns df
```

unlist

```R
l1 <- list(a = "a", b = 2, c = pi+2i) # $a 'a' $b 2 $c 3.141593+2i
unlist(l1) #['a',2,3.141593+2i] 
```



### data frame

```R
age<-c(1,2,3)
name<-c('a','b','c')
df<-data.frame(age, name)
df[1,] #1st row
df[1,2] #1st row 2nd col
#there's more to look into in dplyr::select
```

rename the df

```R 
#check df col names
names(df)
df<-data.frame(col1, col2)
df<-data.frame('age'=col1, 'gender'=col2)
colnames(df)<-c('col1','col2','col3')
dplyr::renam(df, newcolname1=col1, newcolname3=col3)
```

attach and detach (save the effert to refer to the name of the df every time)

```R
attach(df)
#age equals to df$age
detch
#age != df$age
```



### check data type

```R
class(df) #data.frame
class(df$name) #character
class(df['name']) #data.frame
mode(df) #list
mode(df$name) #character
mode(df['name']) #list
str(df) #returns the data type of each column

#mode is similar with type of, old, refer to storage type, class for calculation type(my own understanding)
```





## Missing Value

### detect

find missing/ not missing values and return true or false

```R 
is.na(df$age)
!is.na(df$age)
```



### replace

replace with average/median in a column

```R
df[is.na(df)]<-mean(df$age)
df[is.na(df$col), 'col']<-median(df$col, na.rm=T) #na.rm=T: ignore NA
```

replace with similar data in a row

```R 
library(cluster)
dist.mtx<-as,matrix(daisy(df, stand=T)) #daisy: calculate distance; stand=T:normalize data
which(!complete.cases(df)) #find rows with NA, return index
sort(dist.mtx[38])[1:10] #find the top 10 rows that has smallest distance with row 38
r<- as.integer(names(sort(dist.mtx[38,])[2:11]))
medi<-median(df[c(r),'col'], na.rm=T) #col: columns that have missing value, return median
#if only one missing value in a row
df[38,'col']<-medi
#if multiple missing value in a fow
apply(df[c(r), which(is.na(df[38,]))],2,median,na.rm=T) #2 for columns, 1 for rows
```

replace with NA

```R
df$age[df$age<=0]<-NA
```



### omit

delete rows that have missing value

```R
na.omit(df)
```



## Operating with list

### paste

```R
name<- c('a','b','c')
gender<-c('f','m','f')
paste(name, gender, sep='-')
# 'a-f', 'b-m', 'c-f'

paste('a','b',sep='-')
#'a-b'
```



### add to list

convert the added part to list

```R
l<-c(l,c(5))
```



## Operating with data frame

### read and write

```R
setwd('directory') #getwd() the presnt directory
df<-read.csv('df.csv')  #stringsAsFactors = FALSE if need to grpup with certain
df<-read.table('df.csv')
write.csv(df,'df.csv',sep=',')
write.table(df, file='takename.csv', sep=',')
```

package 'xlsx' allows read and write from excel file



### reform

```R
# transposition
t(df)

# add column
df$new_col <-value*10
df$newcol[df$age>30 | df$age<40 & df$gender=='F'] <-'A'

#bind
cbind(df1, df2) #column bind
rbind(df1, df2) #row bind
```



### order

```R
order(df$age) #return index
df[order(df$age),] #return value
dfa<-dplyr::arrange(df, desc(age), race) #default asc
```



### apply

```R
apply(m1,1, mean) # return matrix, 1 for row, 2 for column, return average of each row
lapply(list, sum) # return list
sapply(list, sum) # reutn vector
tapply(df$age, df$gender, mean) # return F mean age, M mean age, average age according to gender
```



### select

```R
df[df$age>=10]

#subset
subset(df,'age'>30, c('gender')) # return col gender

#sample
len<-floor(nrow(df)*0.3) # round down to 30% of the number of rows
sample_size<-sample(nrow(df),len) # reuturn index, in total number of rows, take len number of samples
dfs<-df[sample_size,]

#filter
dff<-dplyr::filter(df, age>30, race=='A') #',':and ; '|': or    favourite

#slice
dfs<-dplyr::slice(df, 80:100) #choose row 80-100
```



### grouping

#### table

```R
#counting
table(df$gender)
table(df$gender, df$race)
```

| Male | Female |
| ---- | ------ |
| a    | b      |

|        | race1 | race2 |
| ------ | ----- | ----- |
| Male   | a     | b     |
| Female | c     | d     |

#### tapply

```R
tapply(df$age, df$gender, mean) #average age, group by gender
tapply(df$age, list(df$gender, df$race), mean) #average age, group by gender and race
```

|        | race1 | race2 |
| ------ | ----- | ----- |
| Male   | age1  | age2  |
| Female | age3  | age4  |

#### by

```R
by(df, indices, function)
```

#### aggregate

```R
aggregate(age~sex+race, df, sum) #sum age, group by gender and race
```

#### row sum

```R
rowsum(x, group)
rowsum(df$age, df$sex)
#equals to 
aggregate(age~sex, df,sum)
```

## package

```R
install.packages('pkgname')
library(pkgname)
detach('package:pkgname')
```

