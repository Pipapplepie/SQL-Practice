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

## 聚合函数在窗口函数上的使用

## Frame - 移动平均

# GROUPING

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

