# data-analysis-note SQL
# Database management

Create / delete database

```sql
CREATE DATABASE db_name;
DROP DATABASE db_name;
```

Use database

```sql
USE db_name;
```

Create / delete table

```sql
CREATE TABLE tb_name(
	col1 INT NOT NULL PRIMARY KEY,
	col2 VARCHAR(255) FPREIGN KEY
	);
DROP TABLE tb_name
```

Create view

```sql
DROP VIEW IF EXISTS view_name;
CREATE VIEW view_name as ('select query');
```



Create / delete index

```sql
CREATE INDEX idx_name ON tb1(col1);
ALTER TABLE table_name DROP INDEX index_name; 
-- differs from different database
```

Import table

```sql
LOAD DATA LOCAL INFILE 'tb.csv' INTO TABLE tb;
```

Alter table

```sql
ALTER TABLE tb_name ADD col;
```

Create user

```sql
CREATE USER "username" @ "IP" identified by "password";
```

### Example

```sql
DROP TABLE IF EXISTS chartevents;
CREATE TABLE chartevents (	-- rows=327363274
   subject_id INT UNSIGNED NOT NULL,
   hadm_id INT UNSIGNED NOT NULL,
   stay_id INT UNSIGNED NOT NULL,
   charttime DATETIME NOT NULL,
   storetime DATETIME,
   itemid MEDIUMINT UNSIGNED NOT NULL,
   value TEXT,	-- max=156
   valuenum FLOAT,
   valueuom VARCHAR(255),	-- max=17
   warning BOOLEAN NOT NULL)
  CHARACTER SET = UTF8
  PARTITION BY HASH(itemid) PARTITIONS 50;

LOAD DATA LOCAL INFILE 'chartevents.csv' INTO TABLE chartevents
   FIELDS TERMINATED BY ',' ESCAPED BY '' OPTIONALLY ENCLOSED BY '"'
   LINES TERMINATED BY '\n'
   IGNORE 1 LINES;
```



# Search Query

Select

```sql
SELECT * FROM tb_name;
```

Where

```sql
SELECT * FROM tb_name WHERE col_age>18;
```

Like

```sql
SELECT * FROM tb_name WHERE col1 LIKE "%keyword%";
```

Group by

```sql
SELECT AVG(col_age) FROM tb_name GROUP BY col_id;
```

Order by

```sql
SELECT * FROM tb_name ORDER BY col_id;
```

Count

```sql
SELECT COUNT(*) FROM tb_name;
```

Distinct

```sql
SELECT COUNT(DISTINCT col_id) FROM tb_name;
```

Sum / avg / max

```sql
SELECT AVG(col_age) FROM tb_name;
```

Join

```sql
SELECT *.ta, *.tb FROM tb_a AS ta JOIN tb_b AS tb ON ta.col1=tb.col1;
```

Union

```sql
tb_a UNION tb_b;
```

With as

```sql
WITH tb_a AS (SELECT * FROM tb_b), tb_c AS (SELECT * FROM tb_d)
	SELECT * FROM tb_a, tb_c
```

### Example

```sql
WITH ce AS
(
  SELECT
  ce.subject_id, ce.stay_id, ce.charttime
  FROM `physionet-data.mimic_icu.chartevents` ce
  WHERE ce.itemid IN
  (220765, 227989)
)
SELECT
  ce.subject_id, ce.stay_id, ce.charttime, MAX(ce.icp) AS icp
FROM ce
GROUP BY ce.subject_id, ce.stay_id, ce.charttime
```

# Other keywords

IN

```sql
SELECT * FROM tb_name WHERE col_id IN (1,2,3);
```

BETWEEN

```sql
SELECT * FROM tb_name WHERE col_age BETWEEN 18 AND 80;
```

HAVING

```sql
SELECT AVG(col_age) FROM tb_name WHERE col_id IN (1,2,3)
```

DESC / ASC

```sql
SELECT * FROM tb_name PRDER BY col_age DESC;
```

LIMIT

```sql
SELECT * FROM tb_name LIMIT 10;
```

DATE & TIME

```sql
SELECT TIMESTAMPDIFF(day, intime, outtime) AS los FROM tb_name;
```

GROUP_CONCAT

```sql
SELECT col_id, GROUP_CONCAT(col_hobby) FROM tb_name GROUP BY col_id;
SELECT col_id, GROUP_CONCAT(DISTINCT col_hobby ORDER BY col_hobby DESC SEPARATOR "/") FORM tb_name GROUP BY col_id;
```

SUBSTRING_INDEX

```sql
-- SUBSTRING_INDEX('string that need to be cut','cut accordance', position)
SELECT SUBSTRING_INDEX('s1,s2,s3',',',2)
-- s1,s2
```

RANK()OVER(PARTITION BY)

```sql
SELECT *,RANK()OVER(PARTITION BY col_id ORDER BY col_age DESC) FROM tb_name;
```



### Example

```sql
SELECT o.member_id ,
SUBSTRING_INDEX(SUBSTRING_INDEX(GROUP_CONCAT(o.begin_date ORDER BY o.begin_date DESC),',',2),',',-1) AS SECOND_LAST 
	FROM `order` o			
			GROUP BY o.member_id
				HAVING (COUNT(o.member_id) >= 2)
/*
https://blog.csdn.net/C2667378040/article/details/108241326
例如某个顾客在今年5月份、6月份、7月份、8月份分别消费了一笔订单，消费时间分别为2020-5-1、2020-6-1、2020-7-1、2020-8-1

1、首先将订单表（order）中所有订单按照会员的编号（member_id）进行分组，并且使用过滤条件count(o.member_id) >= 2确保所查询到的会员消费订单都在2笔及以上

2、然后再使用函数group_concat将每组数据中多条数据的消费时间倒序之后（begin_date）进行合并，合并后的消费时间为2020-8-1、2020-7-1、2020-6-1、2020-5-1

3、接着使用函数substring_index截取倒序之后的消费时间的前两条，截取后的消费时间为2020-8-1、2020-7-1

4、最后再使用一次substring_index截取两条中的第二条，截取后的消费时间为2020-7-1
*/
```



# Online practice

•Google bigquery

•W3school 

•廖雪峰 [https://](https://www.liaoxuefeng.com/wiki/1177760294764384/1179611432985088)[www.liaoxuefeng.com/wiki/1177760294764384/1179611432985088](https://www.liaoxuefeng.com/wiki/1177760294764384/1179611432985088)

•SQLBOLT https://sqlbolt.com/lesson/select_queries_introduction

•SQLZOO [https](https://sqlzoo.net/wiki/SELECT_basics/zh)[://](https://sqlzoo.net/wiki/SELECT_basics/zh)[sqlzoo.net/wiki/SELECT_basics/zh](https://sqlzoo.net/wiki/SELECT_basics/zh)

•XUESQL http://xuesql.cn/

•[http://sqlfiddle.com](http://sqlfiddle.com/)[/](http://sqlfiddle.com/) (效果一般)

•Leetcode https://leetcode-cn.com/
