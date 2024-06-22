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

select column_A as c_A to rename a column in your returning table:
```sql
SELECT cust_id, cust_orders AS orders
FROM Customers
```

## WHERE

### (AND, OR, BETWEEN...) LOGIC OPERATORS

### (UPPER, CONCAC, SUBSTRING...) STRING OPERATIONS

### Other functions (time)

### LIKE

## ORDER BY

## Aggregate Functions (AVG, SUM, COUNT, ...)

## JOIN

