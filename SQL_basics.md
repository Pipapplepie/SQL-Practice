# Call A Table

## SELECT... FROM...

select cust_id from table Customers:
```sql
SELECT cust_id
FROM Customers
```

select all columns from table Customers using *:
```sql
SELECT *
FROM Customers
```

select DISTINCT values:
```sql
SELECT DISTINCT prod_name
FROM Products
```

### ... AS...

use AS to rename a column in your returning table;
```sql
SELECT cust_id, cust_orders AS orders
FROM Customers
```

Or create a column using some operations; (you will see more operations afterwards)
```sql
SELECT prod_id, prod_price, prod_price*0.8 AS sale_price
FROM Products
```

## WHERE

WHERE is the most important clause in SQL. It enables you to return a table based on your given conditions.

e.g.,

return results in table only when order_price is greater than or equal to 100:
```sql
SELECT *
FROM Customers
WHERE order_price >= 100
```

### LOGIC OPERATORS (AND, OR, BETWEEN...)

when order_price is no less than 100 and no greater than 500:
```sql
SELECT *
FROM Customers
WHERE order_price >= 100 AND order_price <= 500
```

Same as;
```sql
SELECT *
FROM Customers
WHERE order_price BETWEEN 100 AND 500
```

### String Conditions

```sql
SELECT vend_name
FROM Vendors
WHERE vend_country = "USA" and vend_state = "CA"
```

Another way to mean OR: **in() function**
```sql
SELECT order_num, prod_id, quantity
FROM OrderItems
WHERE prod_id in('BR01','BR02','BR03')
```

### LIKE

Want our string to contain some specific words; (or any string segments)
```sql
SELECT order_num, prod_desc
FROM Products
WHERE prod_desc LIKE '%toy%'
```
It will return results whose prod_desc contains 'toy'.

About %:

You can see % as a **place holder** for other string segments, that is;

- %toy% allows all strings containing 'toy';

- %toy only allows strings ending with 'toy'; (no string segments allowed after 'toy')

- toy% only allows strings starting with 'toy'; (no string segments allowed before 'toy')

```sql
SELECT order_num, prod_desc
FROM Products
WHERE prod_desc LIKE '%carrots%toy%'
```

Here, the results have to contain both 'carrot' and 'toy'. And note; the order is also specified. ('carrots' before 'toy')

Another symbol: _

_ is also a place holder, but it can only hold one character.

```sql
SELECT order_num, prod_desc
FROM Products
WHERE prod_desc LIKE '_t%'
```
returns results whose prod_desc's second character is 't'.

```sql
SELECT order_num, prod_desc
FROM Products
WHERE prod_desc NOT LIKE '%toy%'
```
NOT is another logic operator. It means the opposite of what you specified. It returns the complementary of the result after 'NOT'. 

Here, the result prod_desc won't contain 'toy'.

### Other STRING OPERATIONS (UPPER, CONCAC, SUBSTRING...)
```sql
SELECT cust_id, cust_name, 
       upper(concat(substring(cust_contact,1,2), substring(cust_city,1,3))) as user_login
FROM Customers
```
You can check how to use them by yourself.

### Other functions (time)
```sql
SELECT order_num, order_date
FROM Orders
WHERE year(order_date) = 2020 AND month(order_date) = 1
ORDER BY order_date
```
Note: These time functions can only work with time input of certain style. (e.g., 2020-01-01 00:00:00)

## ORDER BY

Order the returning table according to some columns. (Specify ASC, DESC as needed. ASC by default.)
```sql
SELECT cust_id, order_num
FROM Orders
ORDER BY cust_id, order_date desc;
```

## Aggregate Functions (AVG, SUM, COUNT, ...)
```sql
SELECT max(quantity)
FROM Orders
```
It returns the maximum quantity value of all orders.

```sql
SELECT sum(quantity) as items_ordered
FROM OrderItems
WHERE prod_id = 'BR01'
```
It returns the sum of ordered quantity of product 'BR01'.

## GROUP BY
Aggregate functions are often used with GROUP BY clause;
```sql
SELECT order_num, count(*) as order_lines
FROM OrderItems
GROUP BY order_num
ORDER BY order_lines
```

### HAVING
HAVING is ALWAYS used after GROUP BY! 
```sql
SELECT order_num
FROM OrderItems
GROUP BY order_num
HAVING sum(quantity) >= 100
```
HAVING clause almost always uses some aggregate functions. 

(bcz otherwise, why not just use WHERE clause?)

# Build A Table

There are two ways to build a table;

- CREATE a table from scratch; (specify all the column names, values by yourself)
- Bind several tables together in a certain way so that you have a bigger table;

## JOIN

### Before Join
There is a simpler, intuitive way to 'connect' two tables; that is to use a mutual column as the index. (等联结) To do this, Just use the equal sign (=) to bind the mutual columns in two tables.
```sql
SELECT c.cust_id, o.order_num
FROM Customers AS c, Orders AS o
WHERE c.cust_id = o.cust_id
```
You can omit the 'AS' before the rename; i.e.,
```sql
FROM Customers AS c, Orders AS o
```
is the same as
```sql
FROM Customers c, Orders o
```

### Even Simpler
Sometimes you can get what you want without binding the table;
```sql
SELECT cust_id, order_date
FROM Orders
WHERE order_num in (SELECT order_num FROM OrderItems WHERE prod_id = 'BR01')
ORDER BY order_date
```

However, JOIN allows more complex 'connections'.
```sql
SELECT cust_id, order_date
FROM Orders o JOIN OrderItems oi ON o.order_num = oi.order_num
WHERE oi.prod_id = 'BR01'
ORDER BY o.order_date
```
This works just the same with the last code.

Join more than two tables;
```sql
SELECT cust_email
FROM (Orders AS o JOIN OrderItems AS oi JOIN Customers AS c
ON o.order_num = oi.order_num AND o.cust_id = c.cust_id)
WHERE oi.prod_id = 'BR01'
```

Analyze below;
```sql
SELECT cust_email, SUM(item_price*quantity) total_ordered
FROM (Orders o LEFT JOIN OrderItems oi ON o.order_num = oi.order_num)
GROUP BY cust_id
ORDER BY total_ordered DESC
```

### INNER JOIN, LEFT JOIN, RIGHT JOIN, OUTER JOIN (FULL JOIN)

## UNION
