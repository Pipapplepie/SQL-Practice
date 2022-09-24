# 窗口函数

```sql
<窗口函数> OVER ([ PARTITION BY <列名> ]
                     [ ORDER BY <排序用列名> ])  
```

* PARTITON BY 子句 和 ORDER BY 子句 都是可选参数，前者分组，后者根据所分组进行函数指定的排序。两个参数**不能同时没有**（最少二选一）。

* 不同的排序函数：rank(), dense_rank(), row_number(). 区别如下：

```sql
SELECT  product_name
       ,product_type
       ,sale_price
       ,RANK() OVER (ORDER BY sale_price) AS ranking
       ,DENSE_RANK() OVER (ORDER BY sale_price) AS dense_ranking
       ,ROW_NUMBER() OVER (ORDER BY sale_price) AS row_num
  FROM product;  
```

<img src='https://github.com/Pipapplepie/wonderful-sql/raw/main/img/ch05/ch0503.png' width='500'>

## ROLLUP - 分类的合计

```sql
SELECT  product_type
       ,regist_date
       ,SUM(sale_price) AS sum_price
  FROM product
 GROUP BY product_type, regist_date WITH ROLLUP;  
 ```
 
 <img src='https://github.com/Pipapplepie/wonderful-sql/raw/main/img/ch05/ch0508.png' width='500'>
 
 **注意：** 这里ROLLUP 对product_type, regist_date两列进行合计汇总。结果实际上有三层聚合，如下图 模块3是常规的 GROUP BY 的结果，需要注意的是衣服 有个注册日期为空的，这是本来数据就存在日期为空的，不是对衣服类别的合计；
 模块2和1是 ROLLUP 带来的合计，模块2是对产品种类的合计，模块1是对全部数据的总计。
 
 即 1.对同品类、同一日期的售价求和；2.对同品类的售价求和；3.所有品类商品售价的总和。
 
 <img src='https://github.com/Pipapplepie/wonderful-sql/raw/main/img/ch05/ch0510.png' width='500'>

# Exercise

## 5.1

<img src='https://user-images.githubusercontent.com/107236740/192085692-54dff3a3-ea5a-4fb7-9de6-b99bf0c44da4.png' width='500'>


返回到当前位置sale_price的最大值。

## 5.2

```sql
select  product_id
       ,product_name
       ,sale_price
       ,regist_date
       ,sum(sale_price) OVER (partition by regist_date
                                  order by regist_date) AS Current_sum_price
from product;
```

<img src='https://user-images.githubusercontent.com/107236740/192085731-f15bada8-2e95-4010-83c4-0f154045f7da.png' width='500'>

## 5.3

1）函数直接作用在整个表上，而非各个分区内；

2）我也不是太清楚，虽然两者都可行，我个人觉得在order by中结果的分类、排序可能会不够直观。

(CSDN的一篇博客：https://blog.csdn.net/LeFran/article/details/118275520 )

(Answer from Stackoverflow: https://stackoverflow.com/questions/14111321/windowed-functions-can-only-appear-in-the-select-or-order-by-clauses )

## 5.4

创建表格：

```sql
delimiter //
drop procedure if exists create_table;
create procedure create_table()
begin
		create table table01 like shop.product; #change table name here
end//

call create_table()//
```

逐次创建二十个表格非常繁琐，我们可以使用**prepare statement**:

（写得比较艰难，参考了这则博客：https://www.cnblogs.com/geaozhang/p/9891338.html ）

```sql
delimiter //
drop procedure if exists create_table;
create procedure create_table()
begin
		declare i int;
		set i=1;
    while i<20 do
				set @tablename=concat('table',i);
				set @sql_text=concat('create table ',@tablename, ' like shop.product');
				prepare stmt from @sql_text;
				execute stmt;
				set i=i+1;
    end while;
end//

call create_table()//
```

<img src='https://user-images.githubusercontent.com/107236740/192094063-c43d9e9b-feea-46be-a117-d3ba3f545a74.png' width='250'>

 # 存储过程或函数
 
 ```sql
 [delimiter //]($$，可以是其他特殊字符）
CREATE
    [DEFINER = user]
    PROCEDURE sp_name ([proc_parameter[,...]])
    [characteristic ...] 
[BEGIN]
  routine_body
[END//]($$，可以是其他特殊字符）
``` 

1. 即创建函数，要将该例程明确地与一个给定的数据库相关联，需要在创建该例程时将其名称指定为 db_name.sp_name。

2. routine_body 由一个有效的SQL例程语句组成。它可以是一个简单的语句，如 SELECT 或 INSERT，或一个使用 BEGIN 和 END 编写的复合语句。
复合语句可以包含声明、循环和其他控制结构语句。在实践中，存储函数倾向于使用复合语句，除非例程主体由一个 RETURN 语句组成。

3. 使用 CALL 语句调用一个存储过程。而要调用一个存储的函数时，则要在表达式中引用它。在表达式计算期间，该函数返回一个值。

# Prepare Statement 预处理

<img src='https://github.com/Pipapplepie/wonderful-sql/raw/main/img/ch05/ch0511MySQL-Prepared-Statement.png' width='500'>
