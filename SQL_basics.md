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

### (AND, OR, BETWEEN...) LOGIC OPERATORS

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

If the condition is about strings:
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

### (UPPER, CONCAC, SUBSTRING...) Other STRING OPERATIONS
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

## GROUP BY
Aggregate functions are almost always used with GROUP BY clause;


### HAVING

## JOIN

### Before Join
There is a simpler, intuitive way to 'connect' two tables; that is to use a mutual column as the index. (等联结) To do this, Just use the equal sign (=) to bind the mutual columns in two tables.
```sql
SELECT c.cust_id, o.order_num
FROM Customers c, Orders o
WHERE c.cust_id = o.cust_id
```

However, JOIN allows more complex 'connections'.

## UNION
