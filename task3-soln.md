# VIEW 视图

_视图不是表，视图是虚表，视图依赖于表。_

## 注意：

1. 在一般的DBMS中定义视图时不能使用ORDER BY语句。
**为什么：** 因为视图和表一样，数据行都是没有顺序的。

2. 修改视图用 **Alter View <Existing_View_Name> AS ...**， 类似表。

3. 更新（Update）视图内容时，原表内容（视图窗口可见范围内）也会相应地更新。
However, for the consistency of the data, 并**不推荐**这种使用方式。

## Exercise

**3.1**

```sql
create view ViewPractice5_1 as
(
select product_name, sale_price, regist_date
from product
where sale_price >= 1000
AND regist_date = '2009-09-20'
);
```

**3.2**

[SQL] INSERT INTO ViewPractice5_1 VALUES (' 刀子 ', 300, '2009-11-02');

[Err] 1423 - Field of view 'shop.viewpractice5_1' underlying table doesn't have a default value


**3.3**

```sql
select product_id, 
product_name, 
product_type, 
sale_price,
(select avg(sale_price) from product) as sale_price_avg
from product;
```

**3.4**

## 子查询

(关联子查询的Query策略)

a) 首先执行不带WHERE的主查询;

b) 根据主查询讯结果匹配product_type，获取子查询结果;

c) 将子查询结果再与主查询结合执行完整的SQL语句;

```sql
select product_id, 
product_name, 
product_type, 
sale_price,
(
select avg(sale_price) 
from product as p2
where p1.product_type = p2.product_type
group by product_type
) as sale_price_avg

from product as p1;
```

**注意：** SQL会先运行“外层\母查询”的from statement, 然后是子查询的，所以应在子查询的where中建立关联。

# SQL的各种函数

**1. Arithmetic functions:**

ABS(number), 

MOD(被除数，除数), 

ROUND（float, INT:保留位数）,...

**2. String functions:**

CONCAT(str1, str2, str3), 

LENGTH(str), 

REPLACE( 对象字符串，替换前的字符串，替换后的字符串 ), 

SUBSTRING （str FROM int:position FOR int:length）,...

(字符串函数类似于python，但不同于python：SQL字符排序从1开始; python从0开始。)

**3. Time/Date functions:** CURRENT_DATE, CURRENT_TIME, CURRENT_TIMESTAMP --> (data time), EXTRACT(日期元素 FROM 日期)

**4. 谓词 Like**  where <col_name> like ...

"abc%": 返回以abc开头的字符串，如“abcd”

"%abc"：返回以abc结尾的字符串，如“xxabc”；

"%abc%"：返回中间含abc的字符串，如“xxabcyy”；

“abc_”: 下划线为占位符，其使用位置同%，一条下划线对应一个字符。如"abc__" --> "abcde";

## Exercise

**3.5**

Yes: 四则运算中含有 NULL 时（不进行特殊处理的情况下），运算结果会变为NULL。

**3.6**

**谓词 IN:** OR的简便用法：

<img src='https://github.com/Pipapplepie/Solutions-to-Datawhale-SQL-Open-Source-Learnig---2022-9/blob/main/3.61.png' width='400'>

<img src='https://github.com/Pipapplepie/Solutions-to-Datawhale-SQL-Open-Source-Learnig---2022-9/blob/main/3.62.png' width='400'>

**注意：** 在使用**谓词** IN 和 NOT IN 时是无法选取出NULL数据的。NULL 只能使用 **IS NULL** 和 **IS NOT NULL** 来进行判断。

**3.7**

```sql
select 
count(Case when sale_price <= 1000 then product_name else null end) as low_price,
count(Case when sale_price between 1001 and 3000 then product_name else null end) as mid_price,
count(Case when sale_price >= 3001 then product_name else null end) as high_price
from product;
```
**注意：** **谓词BETWEEN** 的输出内容包含临界值，即闭区间。
