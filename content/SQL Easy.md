---
tags:
  - datascience
date: " 2024-07-15"
subj: "[[SQL]]"
status: "#study"
---
SQL is a special porpuse programming language to work with [[relational databases]] (put/add data into a relational database). 

Done ❗

# SQL Statements and Syntax

There are three types of SQL statements:

**1. Data Definition Language(DDL):**  
The Data Definition Language contains commands that are less frequently used. DDL commands modify the actual structure of a database, rather than the database’s contents. example:

- Generating a table
- Modifying a structure of a table (altering)

**2. Data Control Language(DCL):**  
The Data Control Language allows you to manipulate and manage user access rights on database objects. It consists of two commands:

- the [[GRANT]] command, used to add database permissions for a user,
- and the [[REVOKE]] command, used to remove existing permissions.

These two commands form the core of the relational database security model.

**3. Data Manipulation Language (DML):**  
Data Manipulation Language contains the subset of SQL commands used most frequently. It is used for searching, inserting, updating, and deleting data that we will see in this notebook.

>[!hint] Some quick notes on the SQL syntax:
>
> - SQL is ==case insensitive==. You can type statements in upper case, lowercase or a mixture of both
> - Database names can be typed ==with or without backquotes== i.e. `classicmodels` or `` `classicmodels` ``
> - SQL statement can span over multiple lines and must end with a ==semicolon== `;`



# Work with database

We'll explore SQL statements for listing, creating and deleting databases along the way.

## Listing

### How to display available databases?

use the statement:

```R
show databases;
```

### How to create a database?

Use the `CREATE DATABASE` statement:

```R
create database `name_of_database`;
```

Once created, the database should appear in the list of databases

We can also ==conditionally== create a database if it doesn't already exist using:

```R
create database if not exists `name_of_database`;
```

## Select

A database ==must be selected== before it can be used. This is done with the `USE` statement.

```R
use  `name_of_database`;
```

## Delete 

To delete a database, use `DROP DATABASE`

```R
drop database `name_of_database`;
```

As soon as the command is run, the database is deleted. ❗watch out
# Working with tables

Let's create the tables for the `jacopo` database according to the given Entity Relationship Diagram. We will also insert some data along the way.

## How to create tables in a database?

Tables are created using the `CREATE TABLE` statement.
While creating tables, we specify a list of columns and the ==data type== for each column.


```
CREATE TABLE table_name (
    column1 datatype,
    column2 datatype,
    column3 datatype,
   ....
);
```

### Data type

In SQL, i tipi di dati specificano il tipo di dati che una colonna può contenere. 

#### Tipi di Dati Numerici

##### Numeri Interi
- **INT** o **INTEGER**: Un intero con una dimensione specificata dal sistema (può variare).
- **SMALLINT**: Un intero di dimensioni ridotte.
- **TINYINT**: Un piccolo intero (supportato in MySQL).
- **BIGINT**: Un intero di grandi dimensioni.
- **SERIAL**: Un intero che si auto-incrementa (spesso utilizzato come chiave primaria in PostgreSQL).

##### Numeri in Virgola Mobile
- **FLOAT**: Numero in virgola mobile a precisione singola.
- **DOUBLE** o **DOUBLE PRECISION**: Numero in virgola mobile a precisione doppia.
- **REAL**: Un altro tipo di numero in virgola mobile (può variare tra RDBMS).

##### Numeri Decimali
- **DECIMAL(p, s)** o **NUMERIC(p, s)**: Numero decimale con precisione `p` e scala `s`. Usato per valori che richiedono precisione esatta, come importi di denaro.

#### Tipi di Dati a Caratteri

- **CHAR(n)**: Stringa di lunghezza fissa `n`.
- **VARCHAR(n)**: Stringa di lunghezza variabile fino a `n` caratteri.
- **TEXT**: Stringa di lunghezza variabile di dimensioni molto grandi (supportato in molti RDBMS).

#### Tipi di Dati Data e Ora

- **DATE**: Data (anno, mese, giorno).
- **TIME**: Ora (ore, minuti, secondi).
- **TIMESTAMP**: Data e ora con precisione di frazioni di secondo.
- **DATETIME**: Combina data e ora (simile a TIMESTAMP, differenze minori tra RDBMS).
- **YEAR**: Anno (supportato in MySQL).

#### Tipi di Dati Booleani
- **BOOLEAN**: Valore booleano (TRUE o FALSE).

#### Tipi di Dati Binari

- **BINARY(n)**: Dati binari di lunghezza fissa `n`.
- **VARBINARY(n)**: Dati binari di lunghezza variabile fino a `n`.
- **BLOB**: Dati binari di grandi dimensioni. (64 KB).
- **MEDIUMBLOB** / **MEDIUMTEXT**: is a type of binary large object (BLOB) that can store binary data up to a medium size. Specifically, it can hold data up to 16,777,215 bytes (approximately 16 MB). This type is useful for storing various types of binary data, such as images, multimedia files, or other large binary files.
- **LONGBLOB**: Can store up to (4 GB).


#### Altri Tipi di Dati

- **ENUM**: Un elenco di valori predefiniti (supportato in MySQL).
- **SET**: Un insieme di valori predefiniti (supportato in MySQL).
- **JSON**: Dati formattati come JSON (supportato in MySQL, PostgreSQL).
- **XML**: Dati formattati come XML (supportato in SQL Server, PostgreSQL).

### Constraints

#### Not Null

If we specify the constrain `not null`, then the field cannot be empty

```
CREATE TABLE `offices` (
  `officeCode` varchar(10) NOT NULL,
  `city` varchar(50) NOT NULL,
  `phone` varchar(50) NOT NULL,
  `addressLine1` varchar(50) NOT NULL,
  `addressLine2` varchar(50),
  `state` varchar(50),
  `country` varchar(50) NOT NULL,
  `postalCode` varchar(15) NOT NULL,
  `territory` varchar(10) NOT NULL,
  PRIMARY KEY (`officeCode`)
);
```

#### Default

add a default value to a field

```R
CREATE TABLE `offices` (
  `officeCode` varchar(10) default `0000`,
  ...
);
```

#### Default

add a default value to a field

```R
CREATE TABLE `offices` (
  `officeCode` varchar(10) default `0000`,
  ...
);
```

#### Default Null


```R
CREATE TABLE `offices` (
  `officeCode` varchar(10) default NULL,
  ...
);
```

#### Null Default Null

add a default value to a field

```R
CREATE TABLE `offices` (
  `state` varchar(10) Null default NULL,
  ...
);
```



### Index
`INDEX` statement is used to create indexes in tables.

Indexes are used to retrieve data from the database more quickly than otherwise. The users cannot see the indexes, they are just used to speed up searches/queries.


- Creates an index **on a table**. Duplicate values are allowed:

```R
CREATE INDEX index_name 
ON table_name (_column1_, _column2_, ...);
```

- Creates an index **inside a table**.


```R
CREATE TABLE IF NOT EXISTS `jacopo`.`Payments` ( 
---
INDEX index_name (_column1_, _column2_, ...);
```



### Esempi

```sql
CREATE TABLE esempio (
    id INT PRIMARY KEY,                 -- Un intero usato come chiave primaria
    nome VARCHAR(50),                   -- Una stringa di lunghezza variabile fino a 50 caratteri
    descrizione TEXT,                   -- Una stringa di lunghezza variabile molto lunga
    prezzo DECIMAL(10, 2),              -- Un numero decimale con precisione 10 e scala 2
    data_creazione DATE,                -- Una data
    ultima_modifica TIMESTAMP,          -- Data e ora con frazioni di secondo
    attivo BOOLEAN,                     -- Un valore booleano
    immagine BLOB                       -- Dati binari di grandi dimensioni
    primary key id
);
```
## How to list tables in a database?

Tables in a database can be listed using the `SHOW TABLES` statement

```R
show tables;
```
## How to delete tables in a database?

Tables are deleted using the `DROP TABLE` statement.

```
DROP TABLE IF EXISTS `employees`;
```

## How to view the structure of a  tables in a database?

You can view the structure of a table using the `DESCRIBE` statement

```R
describe `name_table`;
```

## How to add data in a  table in a database?

Data can be inserted into a table using the `INSERT INTO` statement
The `INSERT` statement has the following general syntax:

1. specify the columns name
2. add the values

```
INSERT INTO table_name(column_1, column_2,...) VALUES (value1_1,value1_2,...), (value2_1,value2_2,...), ...;
```

If you're inserting values for all the columns, you can skip the column names:

```
INSERT INTO table_name VALUES (value1_1, value1_2, ...), (value2_1,value2_2,...), ...;
```

To insert multiple rows, just separare `()` with `,`

## How to SELECT data out of a table?

The simplest way to view data from the table is using the `SELECT` statement. It has the following syntax:

```
SELECT column1, column2, ... FROM table_name;
```

You can also view data from all columns using

```
SELECT * FROM `offices`;
```


# Querying

## Where

>[!hit] 
>We can add a `WHERE` clause to a `SELECT` statement to select just the rows satisfying the given clause. 

Here's an example:

```
SELECT * FROM `employees` WHERE `jobTitle`="Sales Rep";
```

>[!important]
>Note that values with spaces must be placed within quotes e.g. `"Sales Rep"`.

### Operators

The `WHERE` clause supports the following operators:

- `>`
- `<`
- `>=`
- `<=`
- `<>` or `!=`
- `BETWEEN`
- [[LIKE]]
- `IN`
- \`=`


### AND / OR / NOT
Expressions within a `WHERE` clause can be combined using `AND` and `OR`.

```
SELECT column1, column2, ...
FROM table_name
WHERE condition1 AND condition2 AND condition3 ...;
```

An expression within a `WHERE` clause can be negated using `NOT`.

```
SELECT column1, column2, ...
FROM table_name
WHERE NOT condition;
```

You can use a `WHERE` clause with SELECT, [UPDATE](https://jovian.com/outlink?url=https%3A%2F%2Fwww.w3schools.com%2Fmysql%2Fmysql_update.asp), and [DELETE](https://jovian.com/outlink?url=https%3A%2F%2Fwww.w3schools.com%2Fmysql%2Fmysql_delete.asp) statements.

## Delete

>[!hit]
>The `DELETE` statement is used to delete existing records in a table.


multiple data
```
Delet *
FROM table_name
WHERE NOT condition;
```

one data
```
Delet 
FROM table_name
WHERE NOT condition;
```
## Update

>[!hit]
>The `UPDATE` statement is used to modify the existing records in a table.

```R
UPDATE table_name_  
SET column1 = value1, column2 = value2, ...  
WHERE condition;
```


multiple data
```
Update 
FROM table_name
WHERE NOT condition;
```

one data

```
Update 
FROM table_name
WHERE NOT condition;
```




## Order By
The results of a Select statement can be ordered using the `ORDER BY` clause:

```
SELECT column1, column2, ...
FROM table_name
ORDER BY column1, column2, ... ASC|DESC;
```

```R
select customerName, phone, country 
from jacopo.customers
Order By country;

```

## Limit
To limit the number of results, use the `LIMIT` clause:

```
SELECT column_name(s)
FROM table_name
WHERE condition
LIMIT number;
```

# Concepts


## Primary Key

By using `primary key "name_of_the_column"` we specify that column to become the primary key. The primary key:

- The PRIMARY KEY constraint ==uniquely identifies each record== in a table.    
- Primary keys must contain UNIQUE values, and cannot contain NULL values.
- A table can have only ==ONE primary key constraint==; and in the table, this primary key can consist of single or multiple columns (fields).
- However, a primary key can consists of multiple attributes, just pu a `,` inside the `()`

## Foreing Key

A **FOREIGN KEY** is a field (or collection of fields) in one table, that refers to the PRIMARY KEY in another table.

For example:

```
  ---
  PRIMARY KEY (`employeeNumber`),
  1)FOREIGN KEY (`reportsTo`) REFERENCES `employees` (`employeeNumber`),
  2) FOREIGN KEY (`officeCode`) REFERENCES `offices` (`officeCode`)
);
```

- `employeeNumber` uniquely identifies an employee
- `reportTo` can be null as somebody in the company might not report to someone.
- `reportTo` must be related to a valid `employeeNumber` 
- line `1)` refers to the fact that `reportsTo` referces to the `employeeNumber` in the `employees` table. This ensures that `employeeNumber` ==is valid==
- line `2)` refers to the fact that `officeCode` referces to the `officeCode` in the `offices` table, ==thus they must match==

>[!important] REMEMBER
>The value that an attribute references to must be the **primary key** of that table

## CONSTRAINT

un constraint (vincolo) è una regola applicata ai dati in una tabella per garantire l'integrità, la correttezza e la coerenza dei dati stessi. I constraint possono limitare i valori che possono essere inseriti in una colonna, impedire valori duplicati, mantenere relazioni tra tabelle, ecc.


```
CREATE TABLE `products` (
  --
  PRIMARY KEY (`productCode`),
  CONSTRAINT `products_ibfk_1` FOREIGN KEY (`productLine`) REFERENCES `productlines` (`productLine`)
);
```

- La parola chiave `CONSTRAINT` viene utilizzata per dare un nome specifico al vincolo.
- **FOREIGN KEY (`productLine`)** specifica che il campo `productLine` nella tabella `products` è una chiave esterna. 
- Il valore di `productLine` deve esistere come ==chiave primaria== o come chiave unica nella tabella referenziata.
- **REFERENCES `productlines` (`productLine`)** specifica la tabella e la colonna a cui la chiave esterna fa riferimento. In questo caso, la chiave esterna `productLine` nella tabella `products` fa riferimento alla colonna `productLine` nella tabella `productlines`.
- garantisce l'integrità referenziale tra le due tabelle, impedendo che si possa inserire un valore di `productLine` in `products` che non esista in `productlines`.


# Modifying Table Structure

The structure of the table can be modified using `ALTER TABLE`

## How to add a new column to a table?

`alter table` and `add` ``

```
ALTER TABLE table_name ADD column_name datatype;
```

For example

```R
Alter table jacopo.employees add dateStart DATE;
```
## How to remove a column from a table?

`alter table` and `drop column`

```
ALTER TABLE table_name DROP COLUMN column_name;
```

For example

```R
Alter table jacopo.employees drop column dateStart;
```

## How to change the data type of a column?

`alter table` and `modify column`

```
ALTER TABLE table_name MODIFY COLUMN column_name datatype;
```


## How to Rename a column?

`alter table` `rename column to`

```
ALTER TABLE table_name RENAME COLUMN old_column_name TO new_column_name;
```


For example

```R
Alter table jacopo.employees Rename column dateStart to sborra;
```



# Esercizi

## #1
1. Try inserting an entry into `employees` with an invalid value for `officeCode`.
2. Try inserting an entry into `employees` with an invalid value for `reportsTo`.
3. Create a new office location and add some employees for the new location.


## #2 
 1. Try inserting an entry into `employees` with an invalid value for `officeCode`.
 2. Try inserting an entry into `employees` with an invalid value for `reportsTo`.
 3. Create a new office location and add some employees for the new location.


## #3

- notnull

1. List the customers in the United States with a credit limit higher than \$1000.
2. List the employee codes for sales representatives of customers in Spain, France and Italy. Make another query to list the names and email addresses of those employees.

```R
select number from customers where Country in ("","","")
```



3. Change the job title "Sales Rep" to "Sales Representative"

```R
update employees set jobTitle = "";
```

4. Delete the entries for Sales Representatives working in London.

```R
DELETE FROM employees WHERE jobTitle = 'Sales Representative' AND officeCode = ( SELECT officeCode FROM offices WHERE city = 'London' );
```

5. Show a list of employees who are not sales representatives
```R
Select * from employees where Not jobTitle = "Sales Department";
```

6. Show a list of customers with "Toys" in their name (hint, use [[LIKE]])

```R
select * from customers where customerName LIKE "%Toy%";
```


## 4

1. List the 5 most expensive products from the "Planes" product line
```R
select * from jacopo.products 
where productLine = "Planes" 
Order By "ASC" 
limit 5;
```

2. Identify the products that are about to run out of stock (quantity in stock < 100)

```R
select * from jacopo.products 
where quantityInStock < 100 ;
```

3. List 10 products in the "Motorcycles" category with the lowest buy price and more than 1000 units in stock

```R
select * from jacopo.products 
where productLine = "Motorcycles" and quantityInStock >  1000 
Order By "DES" 
limit 10;

```

## challenge


# References

https://www.youtube.com/watch?v=-fW2X7fh7Yg
https://jovian.com/aakashns/relational-databases-and-sql