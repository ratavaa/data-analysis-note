# data-analysis-note python
## Calculation

  ```Python
#round
7//4=1
#remainder
10%4=2
#power 2^3
2**3=8
  ```



## Series

```python
from pandas import Series
x=Series(
	['a', True, 1],
    index=['1st','2nd','3rd'] #default 0,1,2...
)
x[1]==x['2nd']==True

# can only append series format data
n=Series([2])
x=x.append(n) #index=0, position=4

#check
2 in x.values

#slice
x[1:3]==x[[1,2]]  #1>=n>3 

#delete
x.drop('1st')==x.drop(x.index[0])

#keep one
x[2 != x.values]
```



## Array

```python
import numpy as np
r=np.array(1,5,0.1) # start end step
np.power(r,5) #5 times every value in r 

r[r>3] # keeps values in r bigger than 3
```



## List

create list

```python
l=[0 for i in range(10)]
l=[i for i in range(1,10)]
l= [0] * 10
```

delete certain element 

```python
l.remove('e') #element is 'e'
s.pop(3) #index is 3, return the deleted element
del l[3] #index is 3

#delete all the copies of the same element from the list
list(filter(lambda x:x!='ele' and x!='ele2', listname))
```

join list

```python
l=['a','b','c']
'-'.join(l)
```

join lists

```python
l=[[1,2],[3,4],[5,6]]
sum(l,[])
[j for i in l for j in i]
```

list to dictionary

```python
# combine list of keys and list of values
l1=['k1','k2','k3']
l2=['v1','v2','v3']
dict(zip(l1,l2))

# convert key and value pairs
lkv=[['k1','v1'],['k2','v2']]
dict(lkv)
```



## Directory

```python
import osos.getcwd()
```

dictionary to list

```python
lk=list(dict.keys())lv=list(dict.values())
```

reverse

```python
new_dict = {v : k for k, v in student.items()}
```



## String

```python
#delete/ replace
s.strip() 
#delete blank space at the start and end
s.strip('s') 
#delete certain letter at both sides
s.replace('_','-')  #replace all '_' with '-'
```



## Data Frame

### create 

```python
from pandas import DataFrame
df.DataFrame(data={'age':[1,2,3], 'name':['a','b','c']},index=['1st','2nd', '3rd'])
```

### read from table

```python
import pandas as pd
df=pd.read_csv('dictionary or just file name') #encoding='gbk'  'urf-8'
df=pd.read_table('dictionary', names=['col1','col2'], sep=',', encoding='gbk')
df=pd.read_excel('directory', sheetname='Sheet1', names=['col1','col2'])
#write
df.to_csv('file path or just file name', sep=',', index=True, header=True) #weather to save index and headers
```

### read from dictionary

```python
df=pd.DataFrame(pd.Series(dict), columns=['col'])
df=df.reset_index().rename(columns={'idx':'id'})
#or
df=pd.DataFrame.from_dict(dict, orient='idx', columns=['col'])
df=df.reset_index().rename(columns={'idx':'id'})
```

### rename

```python
df.columns=['col1','col2','col3'] #have to change all cols
df.rename(columns={'col1':'A', 'col3':'C'}, inplace=True) #only change part of the cols
```

### access

```python
#columns
df['age']
df[['age','name']]

#rows
df[1:2] #2nd col
df.loc['second col']
df[:2] #first two rows

#delete
df.drop('first', axis=1) #0:row, 1: column
df.drop(df.columns[[0,1,2]], axis=1)

#add
df.loc[len(df)]=[4,'D'] # add new row
df['newcol']=[4,5,6] # add new col

#subsetdf.at['1st','age']  
df.iat[1,2]
df.iloc[0:1, 0:1]  #[row, col]
df.loc[idx,'col']
df.loc[list_to_choose,:] #some rows and all cols
df[df['col']==1]
df[(df['col']==1) & (df['col2']=='a')]
```

loc/iloc return df, at/iat return single value
loc/at use col names
iloc/iat use only number index

ix is not recommend anymore

### transform

```python
df2 = pd.DataFrame(df.values.T, index=df.columns, columns=df.index)
```

### sort

```python
#sort by col value
df.sort_values(by=['col1','col2'],axis=0,ascending=[False,True])

#sort by row value
df.sort_values(by=[3,0],axis=1,ascending=[True,False]) 

#row 3#sort by col idx
df.sort_index(axis=1, ascending=True)

#sort by row idx
df.sort_index(axis=0, ascending = False)
```

### drop

```python
df.drop('col', axis=1)df.drop('rowidx', axis=0)
```

### combine columns

```python
df['newCol']=df['col1']+'-'+df['col2']
```



## Duplicates

```python
#find duplicate
df.duplicated() #return boolean, the second appearance is True, first time False
df.duplicated(['id','key']) #return index

#list duplicated values
dup_idx=df.duplicated(['id'])
df[dup_idx]

#remove all duplicate
data.drop_duplicates(inplace=True)

#remove duplicate in certain col
df.drop_duplicates(subset=['A','B'], keep='first', inplace=True) #keep can also be last or False (remove all duplicated value)
```



##  Missing value





## Database operation

### connect to mysql

```python
import pymysql.cursors
connection= pymysql.connect(host='10.33.3.149', port=3306, user='root', password='mypsw', db='dbname', charset='utf8mb4', cursorclass=pymysql.cursors.DictCursor) #quote psw
cursor=connection.cursor()
sql="SELECT * FROM DBNAME LIMIT 10"
cursor.execute(sql)
result=cursor.fetchall()
cursor.close()
connection.close()
```

### connect to postgres

```python
import psycopg2
connection=psycopg2.connect(host='10.33.3.149', port='5432',user='root',password='mypsw',database='dbname')
cursor=connection.cursor()
#sql='CREATE SCHEMA mimiciii;'
sql="set search_path to dbname"
cursor.execute(sql)
connection.commit()
result=cursor.fetchall()
cursor.close()
connection.close()
```

```python
#print result
for row in result:    
    print(row)
    
#write to dataframe
df=pd.read_sql(sql=sql, con=connection)
```

### create database

```python
from sqlalchemy import create_engine
engine=create_engine("sqlite:///dbname.db") # create SQLAlchemy engine and blank database dbname
df.to_sql('dfname', engine, index=False)

#read from created database
df=pd.read_sql("SELECT * FROM DBNAME LIMIT 10", engine)
```



## Group by

```python
#pandas
pdSeries.value_counts()
pdDataframe['col'].value_counts()
pdDataframe.col.value_counts()

#list
from collections import Counter
Counter(list)
```



## Shuffle

```python
#dataframe
df.sample(len(df))
df.sample(frac=1).reset_index(drop=True) #frac: percentage, 1 is 100%

from sklearn.utils import shuffle
df = shuffle(df)

#list
import random
random.shuffle(list)
```



## Distinct

```python
#dataframe
df.col.unique()
df.drop_duplicates(['col1','col2'])

#list
set(list)
```



## Apply lambda

```python
#dataframe

    #change this col according to other col
df['newcol']=df.oldcol.apply(lambda x: x.replace('a','b') if x[1]=='s' else x) #x refers to element in old col
df['newcol']=df['oldcol'].apply(lambda x: 'A' if x=='a' else 'B')

    #change this col according to this coldf['col']=df.col.apply(lambda x: 0 if x!=3 else 1)
    #change dataframedef 
fun(a,b):    
    if a==b:         
        return 'n'    
    else:        
        return 'y'
df['newcol']=df.apply(lambda x: fun(x.col1, s,col2), axis=1) # x refers to df
    
    #select, select rows with col value >0
df.loc[lambda _df: _df.col>0,:]
df.loc[df['col']>0,:]
df[df['col']>0]
```

##   Pickle

```python
import pickle
#dump
with open('data.pickle','wb') as f:
    pickle.dump(data,f)
    
#load 
with open('data.pickle','rb') as f:
    data=pickle.load(f)
```

## datetime

```python
from datetime import datetime

#string to datettime
s=datetime.strptime(str,'%Y/%m/%d %H:%M:%S')

#time difference
d=s1,date()-s2.date()  #in year: d/365
s.date() # return date
s.time() # return time
t=s1-s2  # returns x days h:m:s
t.days+t.seconds/(24*3600) #time delta to days
```

