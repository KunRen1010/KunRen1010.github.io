---
title: 'Common SQL syntax'
date: 2021-11-01 01:54:55
tags: [Study,Tech]
---
### Select
Select fleids and show the rows of table
```sql
SELECT CustomerName, City FROM Customers;
```

### Select Distinct
Select fleids and show the different value of the fleids
```sql
SELECT DISTINCT Country FROM Customers;
```

### Where Clause
Where Clause is used to filter records
```sql
SELECT * FROM Products
WHERE Price BETWEEN 50 AND 60;

SELECT * FROM Customers
WHERE City IN ('Paris','London');

SELECT * FROM Customers
WHERE City LIKE 's%';

SELECT * FROM Products
WHERE Price = 18;
```

### And, Or, Not
The WHERE clause can be combined with AND, OR, and NOT operators.
```sql
SELECT * FROM Customers
WHERE NOT City = 'Berlin'

SELECT * FROM Customers
WHERE Country='Germany' AND (City='Berlin' OR City='München');
```

### Order By
The following SQL statement selects all customers from the "Customers" table, sorted **ascending** by the "Country" and **descending** by the "CustomerName" column:
``` sql
SELECT * FROM Customers
ORDER BY Country ASC, CustomerName DESC;
```


### Insert Into
Insert new records in a table
```sql
INSERT INTO Customers (CustomerName, ContactName, Address, City, PostalCode, Country)
VALUES ('Cardinal', 'Tom B. Erichsen', 'Skagen 21', 'Stavanger', '4006', 'Norway');

INSERT INTO table_name
VALUES (value1, value2, value3, ...);
```

### Update
``` sql
UPDATE Customers
SET ContactName = 'Alfred Schmidt', City= 'Frankfurt'
WHERE CustomerID = 1;
```
