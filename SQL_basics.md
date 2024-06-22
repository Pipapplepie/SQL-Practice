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

use AS to rename a column in your returning table:
```sql
SELECT cust_id, cust_orders AS orders
FROM Customers
```

## WHERE

WHERE is the most important clause in SQL. It enables you to return a table based on your given conditions.

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

If the condition is about some strings:
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
Such that return with results whose prod_desc contains 'toy'.

You can see % as a **place holder** for other string segments, that is;

%toy% allows all strings containing 'toy';

%toy only allows strings ending with 'toy'; (no string segments allowed after 'toy')

toy% only allows strings starting with 'toy'; (no string segments allowed before 'toy')

### (UPPER, CONCAC, SUBSTRING...) Other STRING OPERATIONS

### Other functions (time)

## ORDER BY

Order the returning table according to chosen index columns. (Specify ASC, DESC. ASC by default.)
```sql
SELECT cust_id, order_num
FROM Orders
ORDER BY cust_id, order_date desc;
```

## GROUP BY

### HAVING

## Aggregate Functions (AVG, SUM, COUNT, ...)

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
