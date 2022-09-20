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

* SELF JOIN还可以求出两张表或子查询的公共部分，功能类似于INTERSECT；

* 两个NULL值进行比较时，结果不为真，（需要用IS NULL谓词）所以最终返回的结果可能会有不希望的遗漏；


## OUTER JOIN

