# 集合运算

在标准 SQL 中, 分别对检索结果使用 UNION, INTERSECT, EXCEPT 来将检索结果进行并,交和差运算，类似于集合。

## UNION

```sql
SELECT product_id, product_name
  FROM product
 UNION
SELECT product_id, product_name
  FROM product2;
```

**注意：** UNION 等集合运算符通常都会除去重复的记录。

* 同一个表中使用UNION，可用OR代替；

* 若想在使用UNION的同时保留重复行（比如为了方便计数），可以使用**UNION ALL**;

### 隐式数据类型转换

**问题：** 如何合并数据类型不同的列？

```sql
SELECT product_id, product_name, '1'
  FROM Product
 UNION
SELECT product_id, product_name,sale_price
  FROM Product2;
```

* "1" returns a list of 1's. 例如字符串和数值类型可以通过隐式类型转换。

* 时间日期类型和字符串,数值以及缺失值均能兼容。

## INTERSECT (MySQL 8.0 no longer supports)

```sql
SELECT product_id, product_name
  FROM Product
  
INTERSECT
SELECT product_id, product_name
  FROM Product2
```

* 可以用Inner join来求交集：

```sql
SELECT p1.product_id, p1.product_name
  FROM Product p1
INNER JOIN Product2 p2
ON p1.product_id=p2.product_id
```

* AND （在同一表内）

```sql
SELECT * 
  FROM Product
 WHERE sale_price > 1.5 * purchase_price 
   AND sale_price < 1500
```

## EXCEPT (MySQL 8.0 no longer supports)

可以用子查询和NOT IN实现：

```sql
SELECT * 
  FROM Product
 WHERE product_id NOT IN (SELECT product_id 
                            FROM Product2)
```

## 对称差

类似于集合中A\B union B\A：（可用UNION和NOT IN实现）

```sql
SELECT * 
  FROM Product
 WHERE product_id NOT IN (SELECT product_id FROM Product2)
UNION
SELECT * 
  FROM Product2
 WHERE product_id NOT IN (SELECT product_id FROM Product)
```

**观察：** INTERSECT 可以看作A, B 的UNION去掉两者的对称差。

# 连结（JOIN）

<img src="https://img-blog.csdnimg.cn/img_convert/bc6e967f1b27c2bd4debe418394c668a.png" width='450'>

## INNER　JOIN

```sql
FROM <tb_1> INNER JOIN <tb_2> ON <condition(s)>
```

关键:找出一个类似于"轴"或者"桥梁"的公共列, 将两张表用这个列连结起来。
(如：商品编号列是一个公共列, 因此可以用这个商品编号列来作为连接的“桥梁”，将Product和ShopProduct这两张表连接起来。)

```sql
SELECT SP....
       ,P....
  FROM ShopProduct AS SP
 INNER JOIN Product AS P
    ON SP.product_id = P.product_id;
 ```
 
 **注意：** 
 
 * 进行连结时需要在 FROM 子句中使用多张表;
 
 * 在进行内连结时 ON 子句是必不可少的;

 * SELECT 子句中的列最好按照 表名.列名 的格式来使用。

### 结合 WHERE 子句使用内连结
 
1. 通过**子查询**增加筛选条件：
 
 ```sql
 SELECT *
  FROM (-- 第一步查询的结果
        SELECT SP.shop_id
               ,SP.shop_name
               ,SP.product_id
               ,P.product_name
               ,P.product_type
               ,P.sale_price
               ,SP.quantity
          FROM ShopProduct AS SP
         INNER JOIN Product AS P
            ON SP.product_id = P.product_id) AS STEP1
 WHERE shop_name = '东京'
   AND product_type = '衣服' ;
```

**2. 标准写法**

**执行顺序:**

**FROM 子句->WHERE 子句->SELECT 子句**

```sql
SELECT  SP.shop_id
       ,SP.shop_name
       ,SP.product_id
       ,P.product_name
       ,P.product_type
       ,P.sale_price
       ,SP.quantity
  FROM ShopProduct AS SP
 INNER JOIN Product AS P
    ON SP.product_id = P.product_id
 WHERE SP.shop_name = '东京'
   AND P.product_type = '衣服' ;
```

3. 将 WHERE 子句中的条件直接添加在 ON 子句中:（不常见的用法）
 
 ```sql
 SELECT SP.shop_id
       ,SP.shop_name
       ,SP.product_id
       ,P.product_name
       ,P.product_type
       ,P.sale_price
       ,SP.quantity
  FROM ShopProduct AS SP
 INNER JOIN Product AS P
    ON (SP.product_id = P.product_id
   AND SP.shop_name = '东京'
   AND P.product_type = '衣服') ;
 ```
 
**注意：** 此方法不方便阅读, 不建议使用。
 
 先连结再筛选的标准写法的执行顺序是, 两张完整的表做了连结之后再做筛选,如果要连结多张表, 或者需要做的筛选比较复杂时, 在写 SQL 查询时会感觉比较吃力.
 在结合 WHERE 子句使用内连结的时候, 我们也可以更改任务顺序, 并采用任务分解的方法,先分别在两个表使用 WHERE 进行筛选,然后把上述两个子查询连结起来。
 
 ```sql
 SELECT SP.shop_id
       ,SP.shop_name
       ,SP.product_id
       ,P.product_name
       ,P.product_type
       ,P.sale_price
       ,SP.quantity
  FROM (-- 子查询 1:从 ShopProduct 表筛选出东京商店的信息
        SELECT *
          FROM ShopProduct
         WHERE shop_name = '东京' ) AS SP
 INNER JOIN -- 子查询 2:从 Product 表筛选出衣服类商品的信息
   (SELECT *
      FROM Product
     WHERE product_type = '衣服') AS P
    ON SP.product_id = P.product_id;
 ```
 
 ### INNER JOIN & GROUP BY
 
 **问题：** 每个商店中, 售价最高的商品的售价分别是多少?
 
 ```sql
 SELECT SP.shop_id
      ,SP.shop_name
      ,MAX(P.sale_price) AS max_price
FROM shopproduct AS SP
INNER JOIN product AS P
ON SP.product_id = P.product_id
GROUP BY SP.shop_id,SP.shop_name
 ```
 
 **思考题:**

上述查询得到了每个商品售价最高的商品, 但并不知道售价最高的商品是哪一个。
如何获取每个商店里售价最高的商品的名称和售价?
 
### SELF JOIN

一张表也可以与自身作连结, 这种连接称之为自连结。
需要注意, **自连结并不是区分于内连结和外连结的第三种连结**, 自连结可以是外连结也可以是内连结, 它是不同于内连结外连结的另一个连结的分类方法。

```sql
SELECT  P1.product_id
       ,P1.product_name
       ,P1.product_type
       ,P1.sale_price
       ,P2.avg_price
  FROM Product AS P1
 INNER JOIN 
   (SELECT product_type,AVG(sale_price) AS avg_price 
      FROM Product 
     GROUP BY product_type) AS P2 
    ON P1.product_type = P2.product_type
 WHERE P1.sale_price > P2.avg_price;
 ```
 
 ### NATURAL JOIN
 
 自然连结其实是**内连结的一种特例**--
 当两个表进行自然连结时, 会按照两个表中都包含的列名来进行等值内连结, 
 此时无需使用 ON 来指定连接条件。
 
 ```sql
 SELECT *  FROM shopproduct NATURAL JOIN Product
 ```
 
**注意：** 

* NATURAL JOIN还可以求出两张表或子查询的公共部分，功能类似于INTERSECT；

* 两个NULL值进行比较时，结果不为真，（需要用IS NULL谓词）所以最终返回的结果可能会有不希望的遗漏；


## OUTER JOIN

左连结会保存左表中无法按照 ON 子句匹配到的行, 此时对应右表的行均为缺失值;

右连结则会保存右表中无法按照 ON 子句匹配到的行, 此时对应左表的行均为缺失值;

全外连结则会同时保存两个表中无法按照 ON子句匹配到的行, 相应的另一张表中的行用缺失值填充。

```sql
-- 左连结     
FROM <tb_1> LEFT  OUTER JOIN <tb_2> ON <condition(s)>
-- 右连结     
FROM <tb_1> RIGHT OUTER JOIN <tb_2> ON <condition(s)>
-- 全外连结
FROM <tb_1> FULL  OUTER JOIN <tb_2> ON <condition(s)>
```

**注意：**
* 由于连结时可以交换左表和右表的位置, 因此左连结和右连结并没有本质区别。
* LEFT、RIGHT 可用来指定主表
* 注意NULL值的处理，如：

使用外连结从ShopProduct表和Product表中找出那些在某个商店库存少于50的商品及对应的商店.希望得到如下结果。

```sql
SELECT P.product_id
       ,P.product_name
       ,P.sale_price
       ,SP.shop_id
       ,SP.shop_name
       ,SP.quantity
  FROM Product AS P
  LEFT OUTER JOIN ShopProduct AS SP
    ON SP.product_id = P.product_id
 WHERE quantity< 50
 ```
 
**观察结果**：少了在所有商店都无货的高压锅和圆珠笔。在WHERE过滤条件中增加 OR quantity IS NULL 的条件, 便可以得到预期的结果。

**更干净、更直观的写法：**

```sql
SELECT P.product_id
      ,P.product_name
      ,P.sale_price
       ,SP.shop_id
      ,SP.shop_name
      ,SP.quantity 
  FROM Product AS P
  LEFT OUTER JOIN-- 先筛选quantity<50的商品
   (SELECT *
      FROM ShopProduct
     WHERE quantity < 50 ) AS SP
    ON SP.product_id = P.product_id
 ```
 
 ### MySQL8.0 目前还不支持全外连结
 
 不过我们可以对左连结和右连结的结果进行 UNION 来实现全外连结。
 
 ## 多表连结
 
 ```sql
 SELECT P.product_id
       ,P.product_name
       ,P.sale_price
       ,SP.shop_id
       ,SP.shop_name
       ,IP.inventory_quantity
  FROM Product AS P
  LEFT OUTER JOIN ShopProduct AS SP
ON SP.product_id = P.product_id
LEFT OUTER JOIN InventoryProduct AS IP
ON SP.product_id = IP.product_id
```

## ON 子句进阶--非等值连结

问题：希望对 Product 表中的商品按照售价赋予排名。

```sql
SELECT  product_id
       ,product_name
       ,sale_price
       ,COUNT(p2_id) AS my_rank
  FROM (--使用自左连结对每种商品找出价格不低于它的商品
        SELECT P1.product_id
               ,P1.product_name
               ,P1.sale_price
               ,P2.product_id AS P2_id
               ,P2.product_name AS P2_name
               ,P2.sale_price AS P2_price 
          FROM Product AS P1 
          LEFT OUTER JOIN Product AS P2 
            ON P1.sale_price <= P2.sale_price 
        ) AS X
 GROUP BY product_id, product_name, sale_price
 ORDER BY my_rank; 
 ```
 
* 中间X部分输出结果：
 
![lala](https://user-images.githubusercontent.com/107236740/191394451-4d32d332-a953-4874-bed3-79461fb074e7.png)

* GROUP BY 多个字段的理解：
1. 将多个字段整体视作一个key; 

2. 依次按照顺序分，先把第一个字段相同的划分为一组，再这些相同的字段中，再查找第二个字段相同的划分为一组...


## 交叉连结 CROSS JOIN

在连结去掉 ON 子句, 就是所谓的交叉连结(CROSS JOIN)。数据库表(或者子查询)的并,交和差都是在纵向上对表进行扩张或筛选限制等运算的, 这要求表的列数及对应位置的列的数据类型"相容", 因此这些运算并不会增加新的列, 而交叉连接(笛卡尔积)则是在**横向**上对表进行扩张, 即增加新的列, 这一点和连结的功能是一致的。但因为没有了ON子句的限制, **会对左表和右表的每一行进行组合**, 这经常会导致很多无意义的行出现在检索结果中. 当然, 在某些查询需求中, 交叉连结也有一些用处。

<img src='https://www.mathstopia.net/wp-content/uploads/2021/01/catesain-product.jpg' width='450'>

### 过时语法

```sql
SELECT SP.shop_id
      ,SP.shop_name
      ,SP.product_id
       ,P.product_name
       ,P.sale_price
  FROM shopproduct AS SP
 CROSS JOIN product AS P
 WHERE SP.product_id = P.product_id;
 ```
 
 输出同 inner join ... on SP.product_id = P.product_id.
 
 # Exercises
 
 
## 4.1

```sql
select product_id
,product_name
,sale_price
from product
where sale_price >= 500
union
select product_id
,product_name
,sale_price
from product2
where sale_price >= 500
```

![41](https://user-images.githubusercontent.com/107236740/191399370-2bc54a03-23e6-44ee-a2b2-d28461ceaf65.png)

**remark:** 如果MySQL8.0支持full outer join, 这也是一种方法，否则要用两次outer join，比较麻烦。

## 4.2

```sql
select *
from
(select * from product
union
select * from product2) as u
where product_id not in
(select product_id
from product
where product_id not in (select product_id from product2)
union
select product_id
from product2
where product_id not in (select product_id from product))
```

![42](https://user-images.githubusercontent.com/107236740/191400029-cd6072a0-9025-48d8-80cd-3f7d72b656b6.png)

## 4.3

```sql
select sp.shop_id, 
sp.shop_name,
p.product_name,
p.sale_price
from shopproduct as sp
inner join
(select * from product
union
select * from product2) as p
on sp.product_id = p.product_id,
(select product_type
, max(sale_price) as max_sale_price
from 
(select * from product
union
select * from product2) as all_p
group by product_type) as m
where p.product_type = m.product_type
and 
p.sale_price = m.max_sale_price
```

![43](https://user-images.githubusercontent.com/107236740/191407411-d967728a-6fdd-4d2d-a1bb-bc74cd92e051.png)

## 4.4
