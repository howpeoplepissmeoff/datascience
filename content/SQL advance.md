---
tags:
  - datascience
date: " 2024-07-16"
up: "[[SQL]]"
status: "#study"
---

# Aggregation, Grouping and Aliases

SQL provides several functions like `COUNT`, `AVERAGE`, `SUM`, `MIN` and `MAX` for aggregating the results of a query.

## COUNT

The `COUNT()` function returns the number of rows that matches a specified criterion.


For example, find the total number of rows in the `Products` table:

```R
SELECT COUNT(*)  
FROM Products;
```

Or, report the total number of payments received before October 28, 2004.

```R
select count(*)
from classicmodels.payments
where paymentDate < "2004-10-28";
```

## Distinct 

The `SELECT DISTINCT` statement is used to return only distinct (different) values.

- Retrieve the list of customer numbers for customer who have made a payment before October 28, 2004.

```R
SELECT DISTINCT customerNumber
FROM classicmodels.payments 
WHERE paymentDate<"2004-10-28";

```

## Count(Distinct colname)

 `count(distinct colname)`: By using the `DISTINCT` keyword in a function called `COUNT`, we can return the number of different countries.

For example, Report the number of customer who have made payments before October 28, 2004.

```R
SELECT COUNT(distinct customerNumber)  
from classicmodels.payments
where paymentDate < "2004-10-28";

```

>[!warning] 
>If we had not used distinct, the it would have sum up also duplicate values (i.e. customers that made more than 1 order at different time)

## Chaining Queries

Retrieve the details all customers who have made a payment before October 28, 2004.

```R
Select * from jacopo.customers
where customerNumber in (
SELECT DISTINCT customerNumber
FROM classicmodels.payments 
WHERE paymentDate<"2004-10-28"
);

```

## Group by AS

The `GROUP BY` statement groups ==rows== that have the same values into summary rows, like "find the number of customers in each country".




The `GROUP BY` statement is often used with aggregate functions (`COUNT()`, `MAX()`, `MIN()`, `SUM()`, `AVG()`) to group the result-set by one or more columns.


```R
SELECT column_name(s)  
FROM table_name  
WHERE condition  
GROUP BY column_name(s)  
ORDER BY column_name(s);
```

- Find the total number of payments made each customer before October 28, 2004.


```R
Select customerNumber, count(*) as merda
from classicmodels.payments
WHERE paymentDate<"2004-10-28"
group by customerNumber ;

```

While performing aggregation, we can specify a column to group rows by. We can also rename aggregate columns using the `AS` keyword.

## SUM

Apart from the count of rows, we can also compute the sum of values in a column. The `SUM()` function returns the total sum of a numeric column.

- Find the total amount paid by each customer payment before October 28, 2004.

```R
Select customerNumber, sum(amount) as merda
from classicmodels.payments
WHERE paymentDate<"2004-10-28"
group by customerNumber ;
```

-  Determine the total number of units sold for each product

```R
select productCode, sum(quantityOrdered) as merda 
from classicmodels.orderdetails
group by productCode;
```

# Sorting and Pagination

Sorting of query results in SQL is done using the `ORDER BY` keyword. For pagination, you can use the `LIMIT` and `OFFSET` keywords.

## ORDER BY

The `ORDER BY` keyword is used to sort the result-set in ascending or descending order.

```R
SELECT * FROM Products  
ORDER BY Price ASC|DESC;
```

- Retrieve the customer number for 10 customers who made the highest total payment in 2004.

```R
Select customerNumber,  sum(amount) as merdo
from classicmodels.payments
WHERE paymentDate<"2004-01-01"
group by customerNumber
order by merdo DESC 
limit 10;
```


## OFFSET

To get the next 10 results, we can simply add an `OFFSET` with the number of rows to skip.

```
SELECT customerNumber, SUM(amount) as totalPayment 
	FROM payments 
    WHERE paymentDate<"2004-10-28" 
    GROUP BY customerNumber 
    ORDER BY totalPayment DESC
    LIMIT 10 
    OFFSET 10;
```

# Mapping

Apart from aggregation functions, SQL also provides mapping functions like `UCASE`, `LCASE`, `SUBSTRING`, `LEN`, `ROUND`, `CONCAT` that are applied to individual values. Let's look at some examples.

##  `UCASE` 

The UCASE() function converts a string to upper-case.

```R
SELECT UCASE("SQL Tutorial is FUN!");

```

## `CONCAT`

The CONCAT() function adds two or more expressions together.

```R
CONCAT(_expression1_, _expression2_, _expression3_,...)
```

```R
SELECT CONCAT(Address, " ", PostalCode, " ", City) AS Address  
FROM Customers;
```

- Display the full name of point of contact each customer in the United States in upper case, along with their phone number, sorted by alphabetical order of customer name.

```R
SELECT concat(ucase(contactFirstName), " ", ucase(contactLastName)) as `full name`, 
phone
from classicmodels.customers
where country = "USA"
order by `full name` ASC;
```

## `LCASE`


The LCASE() function converts a string to lower-case.

```R
SELECT LCASE("SQL Tutorial is FUN!");

```

## `SUBSTRING` 

The SUBSTRING() function extracts a substring from a string (starting at any position).

```R
SUBSTRING(_string_, _start_, _length_)
```
OR
```R
SUBSTRING(_string_ FROM _start_ FOR _length_)
```

![[Pasted image 20240716155501.png]]

- Display a paginated list of customers (sorted by customer name), with a country code column. The country is simply the first 3 letters in the country name, in lower case.

```R
SELECT LCASE(substring(country, 1, 3)) as countryCode
from classicmodels.customers
order by customerName ASC;
```

## Round

The ROUND() function rounds a number to a specified number of decimal places.

```R
ROUND(number, decimals)
```

- Display the list of the 5 most expensive products in the "Motorcycles" product line with their price (MSRP) rounded to dollars.

```R
SELECT productName, round(MSRP, 0) as price
from classicmodels.products
where productLine = "Motorcycles"
order by buyPrice desc
Limit 5
;
```

# Arithmetic Operations

Columns can also be combined using arithmetic operations.

- Display the product code, product name, buy price, sale price and profit margin percentage (`(MSRP - buyPrice)*100/buyPrice`) for the 10 products with the highest profit margin. Round the profit margin to 2 decimals.


```R
SELECT productCode, productName, buyPrice, round((MSRP - buyPrice)*100/buyprice,2)  as margin
from classicmodels.products
order by margin desc
Limit 10
;
```

# DATES

SQL provides functions for extracting information like year, month etc. out of date columns.

## Year

The YEAR() function returns the year part for a given date (a number from 1000 to 9999).

```R
YEAR(_date_)
```

- List the largest single payment done by every customer in the year 2004, ordered by the transaction value (highest to lowest).

```R
SELECT customerNumber, max(amount)  as wow
from classicmodels.payments
where year(paymentDate) = 2004
group by customerNumber 
order by wow DESC
;
```


#### `MONTH`

The MONTH() function returns the month part for a given date (a number from 1 to 12).


```R
month(_date_)
```

- Show the total payments received month by month for every year.

```R
SELECT year(paymentDate) as `year`
, month(paymentDate) as `month`,
sum(amount)  as total
from classicmodels.payments
group by `year`, `month`
order by `year`, `month` 
;
```

## `DATE_FORMAT` and `FORMAT`

The DATE_FORMAT() function formats a date as specified.

```R
DATE_FORMAT(_date_, _format_)
```

The FORMAT() function formats a number to a format like "#,###,###.##", rounded to a specified number of decimal places, then it returns the result as a string.

```R
DATE_FORMAT(number, _decimal_places_)
```



- For the above query, format the amount properly with a dollar symbol and comma separation (e.g `$26,267.62`), and also show the month as a string.

```R
SELECT year(paymentDate) as `year`
, date_format((paymentDate), "%b") as `month`,
concat("$", format(sum(amount),2))  as total
from classicmodels.payments
group by `year`, `month`
order by `year`, `month` 
;
```

# Combining Tables using Joins

A JOIN clause is used to combine ==rows== from two or more tables, based on a related column between them. There are four types of joins: 

- `(INNER) JOIN`: Returns records that have matching rows in both tables
- `LEFT (OUTER) JOIN`: All the rows in A + common rows in B
- `RIGHT (OUTER) JOIN`: All the rows in B + common rows in A
- `FULL (OUTER) JOIN`: Returns all records when there is a match in either left or right table


![[Pasted image 20240716163425.png]]
## Inner join


```R
SELECT columns
FROM table1
INNER JOIN table2
ON table1.common_column = table2.common_column;
```


- Show the 10 most recent payments with customer details (name & phone no.).

```
SELECT checkNumber, paymentDate, amount, customers.customerNumber, customerName, phone 
	FROM payments 
	JOIN customers 
    ON payments.customerNumber=customers.customerNumber 
    ORDER BY paymentDate DESC LIMIT 10;
```

## LEFT JOIN

Il LEFT JOIN restituisce tutte le righe dalla tabella di sinistra (table1) e le righe corrispondenti dalla tabella di destra (table2). Se non ci sono corrispondenze, i risultati della tabella di destra saranno NULL.

```R
SELECT columns
FROM table1
LEFT JOIN table2
ON table1.common_column = table2.common_column;

```

## RIGHT JOIN 

Il RIGHT JOIN restituisce tutte le righe dalla tabella di destra (table2) e le righe corrispondenti dalla tabella di sinistra (table1). Se non ci sono corrispondenze, i risultati della tabella di destra saranno NULL.

```R
SELECT columns
FROM table1
Right JOIN table2
ON table1.common_column = table2.common_column;

```

## FULL  JOIN

```R
SELECT columns
FROM table1
FULL outer JOIN table2
ON table1.common_column = table2.common_column;

```

## SELF JOIN

A table can also be joined with itself. Each instance of the table can be given a temporary alias.


Questa operazione è utile quando si vuole confrontare le righe della stessa tabella tra di loro. Per effettuare un self join, è necessario utilizzare alias per la tabella in modo da poter distinguere le due istanze della stessa tabella all'interno della query.


Supponiamo di avere una tabella `employees` con le seguenti colonne:

- `employee_id`: Identificatore unico per ogni dipendente.
- `employee_name`: Nome del dipendente.
- `manager_id`: Identificatore del manager del dipendente, che è un riferimento a `employee_id`.

Supponiamo di voler trovare coppie di dipendenti che lavorano nello stesso dipartimento e che abbiano lo stesso manager.

```R
SELECT e1.employee_name AS employee1, e2.employee_name AS employee2, e1.manager_id
FROM employees e1
JOIN employees e2 ON e1.manager_id = e2.manager_id AND e1.department_id = e2.department_id
WHERE e1.employee_id <> e2.employee_id;

```

- **Alias della Tabella**:
    
    - `employees e1`: La prima istanza della tabella `employees`.
    - `employees e2`: La seconda istanza della tabella `employees`.
- **JOIN Condizione**:
    
    - `ON e1.manager_id = e2.manager_id AND e1.department_id = e2.department_id`: Unisce le righe dove i dipendenti hanno lo stesso manager e appartengono allo stesso dipartimento.
- **Condizione WHERE**:
    
    - `WHERE e1.employee_id <> e2.employee_id`: Assicura che non vengano selezionate coppie di dipendenti identiche (evita di unirsi con se stessi).

- Show a list of employees with the name & employee number of their manager.
```R
select e.employeeNumber, e.firstName, e.lastName, concat(e2.firstName, " ", e2.lastName) as manager, e2.employeeNumber managernumber
from employees e
LEFT JOIN employees e2
ON e.reportsTO = e2.employeeNumber ; 
```

# Improving Query Performance
## Index

If you often search or order data by a particular column that's not a primary key, you can add an index to make query performance faster. The users cannot see the indexes, they are just used to speed up searches/queries.

- duplicate allowed
```R
CREATE INDEX _index_name_  
ON _table_name_ (_column1_, _column2_, ...);
```

- duplicate not allowed
```R
CREATE UNIQUE INDEX _index_name_  
ON _table_name_ (_column1_, _column2_, ...);
```

-  Add an index on the `lastName` column of the `customers` table.

```R
create index index_lastName
on customers(contactLastName);
```

This index will ==speed up queries== like:

```
SELECT * FROM customers WHERE contactLastName="Lee";
```

and

```
SELECT * FROM customers ORDER BY contactLastName LIMIT 10;
```


To view the indexes on a table, run:

```
SHOW INDEX FROM table_name
```

### Drop Index

The `DROP INDEX` statement is used to delete an index in a table.

```R
DROP INDEX _index_name_ ON _table_name_;
```
## View

If you perform a query often or frequently join two tables for querying, you can create a _virtual table_ called a "view" to make it easier to write queries.

Here's how a view is created:

```R
CREATE VIEW _view_name_ AS  
SELECT _column1_, _column2_, ...  
FROM _table_name_  
WHERE _condition_;
```

```R
CREATE VIEW usaCustomers AS 
SELECT * 
FROM customers 
WHERE country='USA';
```

A view can be queried just like a table:

```
select * from usaCustomers 
WHERE state="CA";
```

The term `usaCustomers` is replaced with the query used to create the view.



# Exercise

- Find the total no. of payments and total payment amount for each customer for payments made before October 28, 2004.

```R
Select customerNumber, count(*) as merda, sum(amount) as merdo
from classicmodels.payments
WHERE paymentDate<"2004-10-28"
group by customerNumber ;

```

- Modify the above query to also show the minimum, maximum and average payment value for each customer.

```R
Select customerNumber, count(*) as merda, sum(amount) as merdo, min(amount) as merd, max(amount) as merdz, avg(amount) as mer
from classicmodels.payments
WHERE paymentDate<"2004-10-28"
group by customerNumber ;


```

-  Show the full office address and phone number for each employee.

```R
select concat(addressLine1, ", ", state, ", ", country, ", ", postalCode) as fullAddres, phone , employees.employeeNumber
from classicmodels.offices
inner join classicmodels.employees
on employees.officeCode = offices.officeCode; 
```

- Show the full order information and product details for order no. 10100.

```R

select concat(orderNumber, ", ", quantityOrdered, ", ", priceEach) as fullOrder, products.productDescription
from classicmodels.orderdetails
inner join classicmodels.products
on orderdetails.productCode = products.productCode
where orderdetails.orderNumber = 10100; 
```

- Retrieve details of all the customers in the United States who have made payments between April 1st 2003 and March 31st 2004.

```R
select *
from classicmodels.customers
where customerNumber in(
	select distinct customerNumber 
	from classicmodels.payments
	where paymentDate > "2003-04-01" AND paymentDate < "2004-03-31")
    and country = "USA";
```

- Determine the total number of units sold for each product
```R
select productCode, sum(quantityOrdered) as `totalUnitSold`
from classicmodels.orderdetails
group by productCode;

```

- Modify the above query to also show the minimum, maximum and average payment value for each customer.

```R
select customerNumber, min(amount) as `min`, max(amount) as `max`, avg(amount) as `avg`
from classicmodels.payments
group by customerNumber;

```

- Show the full office address and phone number for each employee.

```R
select employees.employeeNumber, concat(city, ", ", addressLine1, ", ", state, ", ", country, ", ", postalCode) as fullAddress, employees.employeeNumber
from classicmodels.offices
join classicmodels.employees
on employees.officeCode = offices.officeCode ;
```

- Show the full order information and product details for order no. 10100.

```R
select orderNumber, quantityOrdered, priceEach, products.productDescription
from classicmodels.orderdetails
Join classicmodels.products
on orderdetails.productCode = products.productCode
where orderNumber = 10100;
```
# Reference

1. https://jovian.com/aakashns/advanced-sql-aggregation-and-joins


# TIP

>[!hit] TIP
>- understand input/output
>- explain the query verbally
>- query step by step