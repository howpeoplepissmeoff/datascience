---
tags:
  - datascience
date: " 2025-02-15"
up: "[[SQL]]"
status: "#videos"
---
# Database
## Create a database

1. `drop database if exists`
2. `create database`
3. `use database`

```python
DROP DATABASE IF EXISTS `Parks_and_Recreation`;

CREATE DATABASE `Parks_and_Recreation`;

USE `Parks_and_Recreation`;

```

# Table
## How to add data into a Table

```sql
CREATE TABLE employee_demographics (
  employee_id INT NOT NULL,
  first_name VARCHAR(50),
  last_name VARCHAR(50),
  age INT,
  gender VARCHAR(10),
  birth_date DATE,
  PRIMARY KEY (employee_id)
);
```

we can choose the `type` of each column and the `name`

`name type default-values`


## How to insert data into a table

1. `Insert into name-of-table (columns) values `
2. respect the order


```python
INSERT INTO employee_demographics (employee_id, first_name, last_name, age, gender, birth_date)
VALUES
(1,'Leslie', 'Knope', 44, 'Female','1979-09-25'),
(3,'Tom', 'Haverford', 36, 'Male', '1987-03-04'),
(4, 'April', 'Ludgate', 29, 'Female', '1994-03-27'),
(5, 'Jerry', 'Gergich', 61, 'Male', '1962-08-28'),
(6, 'Donna', 'Meagle', 46, 'Female', '1977-07-30'),
(7, 'Ann', 'Perkins', 35, 'Female', '1988-12-01'),
(8, 'Chris', 'Traeger', 43, 'Male', '1980-11-11'),
(9, 'Ben', 'Wyatt', 38, 'Male', '1985-07-26'),
(10, 'Andy', 'Dwyer', 34, 'Male', '1989-03-25'),
(11, 'Mark', 'Brendanawicz', 40, 'Male', '1983-06-14'),
(12, 'Craig', 'Middlebrooks', 37, 'Male', '1986-07-27');

```


# Select 

>[!note] What is that?
>The `SELECT` statement is used to select data from a database.

>[!abstract] Syntax
>SELECT _column1_, _column2, ..._  
>FROM _table_name_;


```python
select * 
from employee_demographics;
```

```python
select first_name, last_name 
from employee_demographics;
```


- `*`: means select all columns and rows

# Where


>[!note] What is that?
>The `WHERE` clause is used to filter records. 
>It is used to extract only those records that fulfill a specified condition.

>[!abstract] Syntax
>SELECT _column1_, _column2, ..._  
>FROM _table_name_  
>WHERE _condition_;


- `comparison`
```python
select *
from employee_demographics 
where age > 40;
```

- `equal`
```python
select *
from employee_demographics 
where first_name = "Leslie";
```

- `not equal`

```python
select *
from employee_demographics 
where first_name <> "Leslie";
```

- `in`

```python
select *
from employee_demographics 
where first_name in ("Tom", "Anna"); 
```


- `between`

```python
select *
from employee_demographics 
where age between 30 and 40;
```

- `like`: Search for a pattern

```python
select *
from employee_demographics 
where first_name like "T%" ; 

# begins with T
```

## Special character 
- `%`: means anything
- `_`: means a specific value

```python
like "Jer%"
```

the name begins with `Jer` and doesn't matter after `%`

```python
like "%er%"
```

the name must contain `er` somewhere in the name

```python
like "a__"
```

the name starts with an `a` and has precisely 2 characters after that.

```python
like "a_n"
```

It starts with an `a`, it has a character after and it is followed by an `n`

```python
like "a___%" 
```

It starts with an `a`, followed by 3 characters and then it can have anything after it.

# Group by


>[!note] What is that?
>The `GROUP BY` statement groups rows that have the same values into summary rows, like "find the number of customers in each country".

>The `GROUP BY` statement is often used with aggregate functions (`COUNT()`, `MAX()`, `MIN()`, `SUM()`, `AVG()`) to group the result-set by one or more columns.

>[!abstract] Syntax
>SELECT _column_name(s)_  
>FROM _table_name_  
>WHERE _condition_  
>GROUP BY _column_name(s)  
>_ORDER BY _column_name(s);_

```python
select gender, AVG(age)
from employee_demographics 
group by gender; 
```

We can also group_by different conditions

```python
select occupation, salary
from employee_salary 
group by occupation, salary;
```

# Order By

>[!note] What is that?
>The `ORDER BY` keyword is used to sort the result-set in ascending or descending order.

>[!abstract] Syntax
>SELECT _column1_, _column2, ..._  
>FROM _table_name_  
>ORDER BY _column1, column2, ..._ ASC|DESC;


```python
select *
from employee_salary 
order by  salary;
```

In descending order

```python
select *
from employee_salary 
order by  salary DESC;
```

On multiple columns and multiple orders

```python
select *
from employee_salary 
order by  salary DESC, occupation;
```

# Having Vs Where


>[!note] What is that?
> The `HAVING` clause was added to SQL because the `WHERE` keyword cannot be used with aggregate functions.

>[!abstract] Syntax
> SELECT _column_name(s)_  
> FROM _table_name_  
> WHERE _condition_  
> GROUP BY _column_name(s)  
> _HAVING _condition  
> _ORDER BY _column_name(s);_


```python
select occupation, AVG(salary) as Prova
from employee_salary 
group by occupation
having Prova > 65000;
```

With and aggregate function, when filtering we need to specify the new `aggregate column `, otherwise sql won't be able to understand the filter


>[!fail] 
>If I wanted to use `where` statements i would have used it before the `group by`

```python
select occupation, AVG(salary) as Prova
from employee_salary 
where occupation Like "%manager%"
group by occupation
having Prova >65000;
```

1. Filter at the row level with the `Where` clause
2. Filter at the aggregate level with `Having` clause

With this query I am looking for the `manager` role that earns more the 65000


# Limit


>[!note] What is that?
>It limits the number of rows we want to display

>[!abstract] Syntax
>Select col,
>From table
>limit number


```python
select occupation, AVG(salary) as Prova
from employee_salary 
where occupation Like "%manager%"
group by occupation
limit 1;
```

We can combine `limit` with [[#Order By]] to display the top/bottom records

```python
select occupation, AVG(salary) as Prova
from employee_salary 
group by occupation
order by Prova Desc
limit 3
;
```

`Limit` has a second parameter


>[!abstract] Syntax
>Select col,
>From table
>limit begin, end


```python
select occupation, AVG(salary) as Prova
from employee_salary 
group by occupation
order by Prova Desc
limit 2, 3
;
```

I start from `pos` 2 and I take the 3 after it

# Joins


>[!note] What is that?
>A `JOIN` clause is used to combine rows from two or more tables, based on a related column between them.



>[!abstract] Syntax
>


```python
select *
from employee_demographics
Inner Join employee_salary
	on employee_id = employee_id
;
```

- `Inner Join`: I specify on which table i want to join the fisrt selected table `employee_demographics`
- `ON`: I specify mysql in which `cols` it is supposed to be joining on

With this configuration mysql won't be able to understand the join 

>[!fail]
>"Error Code: 1052. Column 'employee_id' in on clause is ambiguous"

Because it does not know from which table these columns are from 

```python
select *
from employee_demographics
Inner Join employee_salary
	on employee_demographics.employee_id = employee_salary.employee_id
;
```

- We have specified now the `cols` and it will work





Here are the different types of the JOINs in SQL:

- `(INNER) JOIN`: Returns records that have matching values in both tables
- `LEFT (OUTER) JOIN`: Returns all records from the left table, and the matched records from the right table
- `RIGHT (OUTER) JOIN`: Returns all records from the right table, and the matched records from the left table
- `FULL (OUTER) JOIN`: Returns all records when there is a match in either left or right table


![[Pasted image 20250217153518.png]]


- `left`

```python
select *
from employee_demographics as lft
left Join employee_salary as rgt
	on lft.employee_id = rgt.employee_id
;
```
![[Pasted image 20250217155005.png]]
We have included all the info from the `lft` table

- `right`

```python
select *
from employee_demographics as lft
right Join employee_salary as rgt
	on lft.employee_id = rgt.employee_id
;
```
![[Pasted image 20250217154818.png]]
In this case it will also include `Null` due to the fact that `employee_id=2` has no info in the `left_table` but has to be included in the final result due to the fact that we are doing a `right_join` 

- `self join`

```python
select lft.employee_id, lft.first_name, lft.last_name, rgt.employee_id, rgt.first_name, rgt.last_name
from employee_salary as lft
join employee_salary as rgt
	on lft.employee_id +1 = rgt.employee_id
;
```
![[Pasted image 20250217155359.png]]

We just cross empolyee by subs `employee_id`

## Joining multiple tables


```python
select lft.employee_id, lft.age, rgt.occupation, pd.department_id, pd.department_name
from employee_demographics as lft
inner join employee_salary as rgt
	on lft.employee_id = rgt.employee_id
inner join parks_departments as pd
	on rgt.dept_id = pd.department_id;




```


## Weird cases

The same problem with `ambiguos col value` will occur in the `Select` statement if we don't specify the table which data is coming from 

- ❌ This won't work
```python
select employee_id, age, occupation
from employee_demographics as dem
Left Join employee_salary as sl
	on dem.employee_id = sl.employee_id
;
```

- ✅This will work
```python
select dem.employee_id, dem.age, sl.occupation
from employee_demographics as dem
Left Join employee_salary as sl
	on dem.employee_id = sl.employee_id
;
```

>[!info]
>Notice that here I have used `aliases` for referencing to the tables

# Union


>[!note] What is that?
> The `UNION` operator is used to combine the result-==rows== of two or more `SELECT` statements.

- Every `SELECT` statement within `UNION` must have the same number of columns
- The columns must also have similar data types
- The columns in every `SELECT` statement must also be in the same order

>[!abstract] Syntax
>SELECT _column_name(s)_ FROM _table1_  
>UNION  
>SELECT _column_name(s)_ FROM _table2_;


```python
select age, gender
from employee_demographics
Union 
select salary, employee_id
from employee_salary;

```

![[Pasted image 20250217160713.png]]

Doesn't make much sense.

For example, we could have use it to pull all the name from the tables

```python

```
![[Pasted image 20250217160835.png]]

>[!tip] 
>By ==default== UNION is going to call `distinct` and removing all the duplicates

To show `ALL` the results from the tables just use `UNION ALL`

```python
select first_name, last_name
from employee_demographics
Union ALL
select first_name, last_name
from employee_salary;

```


## use cases

- with conditions

```python
select first_name, last_name, 'old' as Label
from employee_demographics
where age >50 
Union 
select first_name, last_name, 'highly paid'
from employee_salary
where salary > 70000;

```

![[Pasted image 20250217161309.png]]

# String functions


- `length()`

```python
select first_name, length(first_name) 
from employee_demographics;
```

![[Pasted image 20250217161544.png]]

- `Upper`: return all uppercase

```python
select first_name, upper(first_name) 
from employee_demographics;
```
![[Pasted image 20250217161659.png]]


- `lower`: return all lower

```python
select first_name, lower(first_name) 
from employee_demographics;
```

- `trim`: get rid of the trailing white spaces
	- `ltrim()`: just in the left handside
	- `rtrim()`: just in the right handside
	

```python
select first_name, rtrim(first_name) 
from employee_demographics;
```

- `substr(string, start, end)`
```python
select first_name, substr(first_name, 1, 4)
from employee_demographics;
```
![[Pasted image 20250217162100.png]]
```python
select substr(birth_date, 6, 2) as brt, avg(age)
from employee_demographics
group by brt;
```

- `replace(srt, pattern, new_patter)`: replace specific character with the one specified

```python
select first_name, replace(lower(first_name), "a", "z") as brt
from employee_demographics;
```

![[Pasted image 20250217162541.png]]

- `locate(target, srt)`: it returns the position of the `target` inside a `str`

```python
select first_name, locate( "an", lower(first_name)) as brt
from employee_demographics;
```

![[Pasted image 20250217162705.png]]

- `concat(col1,col2, ...)`: concatenate `str` cols

```python
select  concat( lower(first_name), ' ',  lower(last_name)) as brt
from employee_demographics;
```

# Case statement


>[!note] What is that?
> The `CASE` expression goes through conditions and returns a value when the first condition is met (like an if-then-else statement). So, once a condition is true, it will stop reading and return the result. If no conditions are true, it returns the value in the `ELSE` clause.

If there is no `ELSE` part and no conditions are true, it returns NULL.

>[!abstract] Syntax
> CASE  
    WHEN _condition1_ THEN _result1_  
    WHEN _condition2_ THEN _result2_  
    WHEN _conditionN_ THEN _resultN_  
    ELSE _result_  
END;


```python
select first_name, last_name,
case 
	when age <= 30 then "young"
    else "old"
end 
from employee_demographics;

```

![[Pasted image 20250217163213.png]]

```python
select first_name, last_name,
case 
	when age <= 30 then "young"
    when age between 30 and 40 then  "wow"
    else "old"
end 
from employee_demographics;

```
![[Pasted image 20250217163315.png]]

```python
select first_name, last_name,
case 
	when salary <= 50000 then salary * 1.07
    when salary > 50000  then  salary * 1.07
end  as new_salary,
case
	when dept_id = 6 then salary*0.1
    else 0
end as bonus
from employee_salary;

```

- salary < 50000 
- salary > 50000
- department is finance
![[Pasted image 20250217163923.png]]
# Subqueries


>[!note] What is that?
>It is essentialy a query within a query.


>[!abstract] Syntax
>![[Pasted image 20250217164536.png]]
>- The subquery (inner query) executes once before the main query (outer query) executes.
>- The main query (outer query) use the subquery result.



There are many cases where we can use a `subquery`


- A `SELECT` clause: Used to return a ==single value== or a set of values

```python
select first_name, (select  avg(salary)
from  employee_salary)
from employee_salary;
```


![[Pasted image 20250217164951.png]]

The query above would'nt have worked as sql would have required to use `Group BY`
```python
select first_name,avg(salary)

from employee_salary;
```


- A `FROM` clause: Treated as a derived table or inline view

```python
Select  avg(`Max(age)`)
From (select gender, AVG(age), Max(age), count(age)
from employee_demographics
group by gender
) as Agg_tabl
;
```

![[Pasted image 20250217165238.png]]
![[Pasted image 20250217165557.png]]

This allows us to compute aggregation to the groped by table

>[!tip]
>1. we have to name the table (`agg_tabl`)
>2. we used the backtick to ==reference the actual col name==. If instead we had used aliases we could have just used the alias name


- A `WHERE` clause: Used to filter the ==results==

Use the keyword `In`
```python
select * 
from employee_salary
where dept_id in ( select department_id
	from parks_departments
	where department_id = 1)

```


- A `HAVING` clause: Used to filter ==groups==.


```python
SELECT dept_id, AVG(salary)
FROM employee_salary
GROUP BY dept_id
HAVING AVG(salary) > (SELECT AVG(salary) FROM employee_salary);
```

![[Pasted image 20250217170030.png]]

# Window


>[!note] What is that?
>They enable calculations across a specific set of rows, known as a “window,” while retaining the individual rows in the dataset. Unlike traditional ****aggregate functions**** that summarize data for the entire group, window functions allow detailed calculations for specific partitions or ****subsets of data****.



>[!abstract] Syntax
>  
SELECT column_name1,  
window_function(column_name2)  
OVER([PARTITION BY column_name1] [ORDER BY column_name3]) AS new_column  
FROM table_name;

- ****window_function****= any aggregate or ranking function  
- ****column_name1****= column to be selected
- ****column_name2****= column on which window function is to be applied
- ****column_name3****= column on whose basis partition of rows is to be done
- ****new_column****= Name of new column
- ****table_name****= Name of table

Let's say we want to mimic the behaviour of this `Group_By` query

```python
select gender, AVG(salary) as avg_salary
from employee_demographics as dem 
join employee_salary as sal
on dem.employee_id = sal.employee_id
group by gender;


```
![[Pasted image 20250217170619.png]]

```python
select gender, AVG(salary) OVER()
from employee_demographics as dem 
join employee_salary as sal
on dem.employee_id = sal.employee_id
;

```

![[Pasted image 20250217170745.png]]
Like this `OVER()` is gonna look for the `AVG(salary)` over everybody. We need to `Partition by` gender


```python
select gender, AVG(salary) OVER(
partition by gender )
from employee_demographics as dem 
join employee_salary as sal
on dem.employee_id = sal.employee_id
;

```
![[Pasted image 20250217170923.png]]

We use the window function to add info and don't mess up the table 

```python
select dem.first_name, dem.last_name, AVG(salary) OVER(
partition by gender )
from employee_demographics as dem 
join employee_salary as sal
on dem.employee_id = sal.employee_id
;

```

![[Pasted image 20250217171046.png]]



## ROW_Number


[ROW_NUMBER()](https://www.geeksforgeeks.org/how-to-select-the-first-row-of-each-group-by-in-sql/) gives e­ach row a ****unique number****. It numbers rows from one­ to the total rows. The rows are put into ****groups**** base­d on their values. Each group is called a ****partition****. In e­ach partition, rows get numbers one afte­r another. No two rows have the same­ number in a partition.

```python
select dem.employee_id, dem.first_name, dem.last_name, gender, salary,  ROW_Number() OVER(
partition by gender)
from employee_demographics as dem 
join employee_salary as sal
on dem.employee_id = sal.employee_id
;


```
This assigns a new row number based on gender 

![[Pasted image 20250217171711.png]]

So numbers repete them self between groups but not within gorups (male-female)

With this we are ranking based on salry

```python
select dem.employee_id, dem.first_name, dem.last_name, gender, salary,  ROW_Number() OVER(
partition by gender order by salary Desc)
from employee_demographics as dem 
join employee_salary as sal
on dem.employee_id = sal.employee_id
;

```

![[Pasted image 20250217171826.png]]

## Rank

The [RANK()](https://www.geeksforgeeks.org/sql-query-to-add-ranking-positions-of-rows-in-a-database-with-rank/) function assigns ranks to rows within a partition, with the same rank given to rows with identical values. If two rows share the same rank, the next rank is skipped.

```python
select dem.employee_id, dem.first_name, dem.last_name, gender, salary,  
ROW_Number() OVER(partition by gender order by salary Desc) as Prova, Rank() OVER(partition by gender order by salary Desc) as Miao 
from employee_demographics as dem 
join employee_salary as sal
on dem.employee_id = sal.employee_id
;

```

![[Pasted image 20250217172055.png]]

It gives the ord_rank positionally and not numerically



## DENSE_RANK


It gives the next number numerccally and not positionally 


```python
select dem.employee_id, dem.first_name, dem.last_name, gender, salary,  
ROW_Number() OVER(partition by gender order by salary Desc) as Prova, Rank() OVER(partition by gender order by salary Desc) as Miao ,
Dense_Rank() OVER(partition by gender order by salary Desc) as uao 
from employee_demographics as dem 
join employee_salary as sal
on dem.employee_id = sal.employee_id
;

```

![[Pasted image 20250217172248.png]]
## Use cases

- rolling sum : i.e. the cumulative sum partitioned by gender
```python
select dem.first_name, dem.last_name, gender, salary,  sum(salary) OVER(
partition by gender order by dem.employee_id)
from employee_demographics as dem 
join employee_salary as sal
on dem.employee_id = sal.employee_id
;

```
![[Pasted image 20250217171310.png]]

# CTEs


>[!note] What is that?
> ****CTE in SQL**** allows developers to break down ****complicated logic**** into manageable parts. CTEs help with ****hierarchical data representation****, improve code reusability, and simplify maintenance.

A ****Common Table Expression (CTE)**** in [SQL](https://www.geeksforgeeks.org/what-is-sql/) is a ==temporary== result set that is defined and used within the execution scope of a ****SELECT****, ****INSERT****, ****UPDATE****, or ****DELETE**** statement. 

>[!tip]
>Since CTE is a temporary object, it cannot be referenced outside the query

CTEs are defined using the [WITH clause](https://www.geeksforgeeks.org/sql-with-clause/) and can be referenced multiple times within the main SQL query. This makes CTEs a great alternative to ****subqueries****, especially in cases where we need to perform the same operation multiple times or create ****recursive queries****.

>[!abstract] Syntax
>WITH cte_name AS (  
SELECT query  
)  
SELECT *  
FROM cte_name;


```python

with cte_exmaple as (
select gender, AVG(salary), max(salary), count(salary)
from employee_salary as sl
Inner join employee_demographics as dm 
on sl.employee_id = dm.employee_id
group by gender
)
```
This create a virtual table from where we can query from 

![[Pasted image 20250217183802.png]]

```python

with cte_exmaple as (
select gender, AVG(salary), max(salary), count(salary) as co
from employee_salary as sl
Inner join employee_demographics as dm 
on sl.employee_id = dm.employee_id
group by gender
)

select *
from cte_exmaple
where co >4;

```

![[Pasted image 20250217183859.png]]

## Concatenating CTEs


```python

with cte_exmaple as (
select gender, AVG(salary), max(salary)
from employee_salary as sl
Inner join employee_demographics as dm 
on sl.employee_id = dm.employee_id
group by gender
), 
cte_example2 as(
select   gender, count(gender)
from employee_demographics 
group by gender
)
select *
from cte_exmaple
join cte_example2
on cte_exmaple.gender = cte_example2.gender
;


```
![[Pasted image 20250217185254.png]]

## Aliasing

```python
 WITH cteReports (EmpID, FirstName, LastName, MgrID, EmpLevel)  
  AS
```

# Temp tables



>[!note] What is that?
> A temporary table in SQL is a ****special type of table**** that is created and stored in the system’s temporary database (such as TempDB in SQL Server). This table is primarily used to store and generate important mediation results when ****executing**** a query, stored procedure, or session.

Temporary tables are ****automatically deleted**** when the session or transaction that created them ends, making them perfect for temporary or intermediate data storage. They are particularly useful in situations where you need to perform ****calculations**** or data transformations without changing the permanent database structure.




>[!abstract] Syntax:  Create a Temporary Table
>CREATE TABLE #EmpDetails (id INT, name VARCHAR(25))  

>[!abstract] Syntax: Insert Values Into Temporary Table
>INSERT INTO #EmpDetails VALUES (01, 'Lalit'), (02, 'Atharva') 

>[!abstract] Syntax: Select Values from the Temporary Table
> SELECT * FROM #EmpDetails


```python
create temporary table temp_50K
select *
from employee_salary
where salary >= 50000
;



```


```python
CREATE temporary Table Temp_table (
first_name varchar(50),
last_name varchar(50),
favorite varchar(50)
);

insert into Temp_table
values("Alex", "Nom", "Sborra");

```


```python
select * 
from temp_50K ;
```

# Stored Procedures

>[!note] What is that?
> A stored procedure is a prepared SQL code that you can save, so the code can be reused over and over again.


You can also pass parameters to a stored procedure, so that the stored procedure can act based on the parameter value(s) that is passed

>[!abstract] Syntax
> CREATE PROCEDURE _procedure_name_  
AS  
_sql_statement_  
GO;

>[!abstract] Execute
>CALL _procedure_name_;


```python
create procedure large_salsry ()
select * 
from employee_salary
where salary >= 50000;
```

![[Pasted image 20250217190814.png]]

```python
CAll large_salsry();
```

## Delimiter

1. modify the delimiter
2. create the stored procedure
3. `begin` the statement
4. put the logic
5. `End *delimiter*` the procedure
6. change back the delimiter

```python
Delimiter //
create procedure large_salsry2 ()
Begin
select * 
from employee_salary
where salary >= 50000;
select * 
from employee_salary
where salary >= 10000;
End //
DElimiter ;
```

![[Pasted image 20250217191630.png]]
It returns two results

## Parameters

>[!abstract] Syntax
> CREATE PROCEDURE _procedure_name_ (`param_name` data_type) 
AS  
_sql_statement_  
GO;


```python


Delimiter //

create procedure large_salsry3 (id int)
Begin
select * 
from employee_salary
where employee_id = id;
End //
Delimiter ;

CAll large_salsry3(1);


```
![[Pasted image 20250217192114.png]]

# Triggers


>[!note] What is that?
> A [trigger](https://www.geeksforgeeks.org/sql-trigger-student-database/) is a stored procedure in a ****database**** that automatically invokes whenever a special event in the database occurs. 

For example, a trigger can be invoked when a row is inserted into a specified table or when specific table columns are updated.



- ****Automatic Execution****: Triggers fire automatically when the defined event occurs (e.g., INSERT, UPDATE, DELETE).
- ****Event-Driven****: Triggers are tied to specific events that take place within the database.
- ****Table Association****: A trigger is linked to a specific table or view, and operates whenever changes are made to the table’s data.

>[!abstract] Syntax
> create trigger [trigger_name]   
[before | after]    
{insert | update | delete}    
on [table_name]    
FOR EACH ROW  
BEGIN  
END;


```python
DELIMITER $$
create trigger update_table
	After Insert on employee_salary
    for each row 
Begin 

	Insert into employee_demographics (employee_id,first_name,last_name)
    values( New.employee_id,  New.first_name,New.last_name);
End $$
```

- trigger is called after a new record is added in `employee_salary`
- it will take the new record and add it into `employee_demographics (employee_id,first_name,last_name)`

![[Pasted image 20250217193104.png]]

![[Pasted image 20250217193211.png]]

![[Pasted image 20250217193305.png]]

# Events

An event takes place when it is schedule

>[!note] What is that?
> Events in MySQL are tasks that run according to a defined Schedule. events are executed based on schedule so it is also called a Scheduled event. 

The event can be used for various optimization over collected data on a frequent basis like weekly, or monthly.

>[!abstract] Syntax
>CREATE EVENT  event_name  
ON SCHEDULE schedule  
DO  
event_body

	
```python
Delimiter $$
create event delete_retired
on schedule every 10 second
do 
begin 
	Delete 
	from employee_demographics
	where age>=60
    ;
end $$
Delimiter ;
```

To show all the events

```python
SHOW EVENTS;
```

To check the status
```python
 SHOW VARIABLES
 like "event%";
```

![[Pasted image 20250217194040.png]]

# Related:
- [ ] primary key
- [ ] external key
- [ ] datetime
- [ ] finance

---
# Reference
- https://www.youtube.com/@AlexTheAnalyst/playlists
- https://www.youtube.com/watch?v=1aybOgni7lI&list=PLavw5C92dz9Ef4E-1Zi9KfCTXS_IN8gXZ&index=3
- https://www.youtube.com/watch?v=q_JsgpiuY98&list=PL9ooVrP1hQOG6DQnOD6ujdCEchaqADfCU


- ✅[beginner level](https://www.youtube.com/watch?v=wgRwITQHszU&list=PLUaB-1hjhk8Fq6RBY-3MQ5MCXB5qxb8VA)
- ✅[intermidiete level](https://www.youtube.com/watch?v=lXQzD09BOH0&list=PLUaB-1hjhk8G5zci4HA8E21x2BJS3jzNm)
- ✅[advance level](https://www.youtube.com/watch?v=UC7uvOqcUTs&list=PLUaB-1hjhk8GjfgvWlreA6BvTvazz8RHG)
- [free course](https://www.analystbuilder.com/courses/mysql-for-data-analytics)
- https://www.w3schools.com/sql/sql_intro.asp
