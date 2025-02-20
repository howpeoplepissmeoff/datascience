---
tags:
  - datascience
date: " 2025-02-19"
up: "[[SQL]]"
status: "#study"
---
# Mysql connector
![[Pasted image 20250219183509.png]]
## Connector

1. Create a `connector`
```python
# create connection

connection = conn.connect(
    user = 'root',
    host="172.28.144.1",
    password = 'ciao1234',
    port=3306,
	database= "world_layoffs"
)

connection
```


### How to grant privilegies

```python
-- Create user that can connect from any host
CREATE USER 'root'@'%' IDENTIFIED BY 'ciao1234';

-- Grant privileges to this user
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;

-- Apply the changes
FLUSH PRIVILEGES;
```

## Cursor

A cursor in database programming is an object that allows you to traverse the result set of a query row by row. 

Key cursor methods:

1. `execute()`: Runs a SQL query
2. `fetchone()`: Gets next row
3. `fetchall()`: Gets all rows
4. `fetchmany(n)`: Gets next n rows

Important properties:

```python
# After executing a query:
cursor.rowcount      # Number of rows affected/returned
cursor.column_names  # Names of columns in result
cursor.statement    # Last executed statement
```

### How to query 

```python
# create a query
query = """
SELECT *
FROM layoff_staging2;
"""

  

# query database
cursor.execute(query)



# retreive iterating over cursor
result = []
for data in cursor:
    result.append(data)

  

df = pd.DataFrame(result)
```

Or with `fetchall()`

```python
# create a query
query = """
SELECT *
FROM layoff_staging2;
"""

  

# query database
cursor.execute(query)



# retreive iterating over cursor
result = cursor.fetchall()

  

df = pd.DataFrame(result)
```

### How to pull out col names

```python
column_names = cursor.column_names
```


## Best practisies

1. close the `connection`
2. close the `cursor`

```python
try:
    cursor = connection.cursor()
    cursor.execute("SELECT * FROM users")
    for row in cursor:
        print(row)
finally:
    cursor.close()    # Always close cursor
    connection.close()  # Always close connection
```


```python
with connection.cursor() as cursor:
    cursor.execute("SELECT * FROM users")
    for row in cursor:
        print(row)
    # Cursor automatically closes after with block
```


# SQLalchemy 

## Read_sql


>[!note] What is that?
>I'll explain pandas' `read_sql` function and its key parameters. This function is used to read SQL queries or database tables into pandas DataFrames.

>[!abstract] Syntax
>```python
>pd.read_sql(sql, 
>		con,
>		index_col=None, 
>		coerce_float=True, 
>		params=None, 
>		parse_dates=None, 
>		columns=None, 
>		chunksize=None)
>```

- `sql`: Can be either:
    - A SQL query string like `"SELECT * FROM table_name"`
    - A table name as a string
    - A SQLAlchemy Selectable object
- `con`: The database connection. This can be:
    - A SQLAlchemy engine instance
    - A database URL string
    - A sqlite3 connection
    - Other database connections (psycopg2, pymysql, etc.)

```python
import pandas as pd
from sqlalchemy import create_engine

# Create connection
engine = create_engine('postgresql://username:password@localhost:5432/dbname')

# Read entire table
df = pd.read_sql('table_name', con=engine)

# Read with SQL query
df = pd.read_sql('SELECT * FROM table_name WHERE column > 5', con=engine)
```

 - `index_col`: Specifies which column(s) to use as the DataFrame index:
```python
# Use 'id' column as index
df = pd.read_sql('table_name', con=engine, index_col='id')

# Use multiple columns as index
df = pd.read_sql('table_name', con=engine, index_col=['id', 'date'])
```


- `parse_dates`: Identifies which columns should be parsed as dates:

```python
# Parse a single column 
df = pd.read_sql('table_name', con=engine, parse_dates=['created_at']) 

# Parse multiple columns 
df = pd.read_sql('table_name', con=engine, parse_dates=['created_at', 'updated_at'])
```


- `chunksize`: Returns an iterator for processing large tables in chunks:

```python
# Read in chunks of 1000 rows for chunk in 
pd.read_sql('table_name', con=engine, chunksize=1000)    

```


- `params`: Allows passing parameters to SQL queries safely:

```python

query = "SELECT * FROM table_name WHERE value > %(min_value)s"

df = pd.read_sql(query, con=engine, params={'min_value': 100})

```


-  `columns`: Specifies which columns to select:

```python

df = pd.read_sql('table_name', con=engine, columns=['name', 'age', 'city'])

```


Here's an example

```python
query = """
SELECT *
FROM layoff_staging2;
"""

df = pd.read_sql(query, engine)

df
```

![[Pasted image 20250220120545.png]]




>[!important] Some important notes:
>- Always use parameterized queries (with `params`) instead of string formatting to prevent SQL injection
>- Close your database connections when you're done
>- For large datasets, consider using `chunksize` to manage memory usage
>- The function automatically handles data type conversion for common SQL types

## Write

use `DF.to_sql()`

>[!abstract] Syntax
>
>
>```python
>DataFrame.to_sql(name, 
>				con, 
>				schema=None, 
>				if_exists='fail', 
>				index=True, 
>				index_label=None, 
>               chunksize=None, 
>                dtype=None, 
>                method=None)
>```


-  `name`: (Required) The name of the SQL table
```python
df.to_sql('my_table_name', con=engine)
```

- `con`: (Required) SQLAlchemy engine or connection
```python
from sqlalchemy import create_engine
engine = create_engine('postgresql://user:password@localhost:5432/dbname')
df.to_sql('table_name', con=engine)
```

- `if_exists`: Controls behavior when table exists. Options are:
   - 'fail': Raise an error (default)
   - 'replace': Drop the table and create new
   - 'append': Add data to existing table
```python
# Replace existing table
df.to_sql('table_name', con=engine, if_exists='replace')

# Append to existing table
df.to_sql('table_name', con=engine, if_exists='append')
```

- `index`: Whether to write DataFrame index as a column
```python
# Don't include index
df.to_sql('table_name', con=engine, index=False)

# Include index with custom name
df.to_sql('table_name', con=engine, index=True, index_label='custom_id')
```

-  `dtype`: Specify SQL data types for columns
```python
from sqlalchemy.types import VARCHAR, INTEGER

dtype_dict = {
    'name': VARCHAR(50),
    'age': INTEGER,
    'date': DateTime
}
df.to_sql('table_name', con=engine, dtype=dtype_dict)
```

-  `chunksize`: Number of rows to send in each batch
```python
# Write in chunks of 1000 rows
df.to_sql('table_name', con=engine, chunksize=1000)
```

- `method`: How to write data. Options are:
   - None: Plain SQL INSERT statements
   - 'multi': Multiple VALUES lists in INSERT
   - callable: Custom function
```python
# Use multi-value insert
df.to_sql('table_name', con=engine, method='multi')
```

-  `schema`: Database schema to use
```python
# Write to specific schema
df.to_sql('table_name', con=engine, schema='my_schema')
```

Example putting it all together:
```python
import pandas as pd
from sqlalchemy import create_engine, VARCHAR, INTEGER

# Create engine
engine = create_engine('postgresql://user:password@localhost:5432/dbname')

# Define data types
dtypes = {
    'name': VARCHAR(100),
    'age': INTEGER,
    'city': VARCHAR(50)
}

# Write to database
df.to_sql(
    name='employees',
    con=engine,
    schema='hr',
    if_exists='replace',
    index=False,
    dtype=dtypes,
    chunksize=500,
    method='multi'
)
```

Important tips:
- Use `chunksize` for large DataFrames to manage memory
- Always specify `dtype` for important columns to ensure correct data types
- Consider using `if_exists='append'` for incremental updates
- Set `index=False` unless you specifically need the index as a column
- Remember to close your database connection when done


Here's an example

```python
from sqlalchemy import VARCHAR, INTEGER

new_type = {
    'industry':VARCHAR(50),
    'tot_lay_off':INTEGER,
}

df2.to_sql(
    name="prova",
    con = engine,
    index=False,
    dtype= new_type
)
```

## Append 


```python
from sqlalchemy import FLOAT

  

df2_plus = df2.copy()

  

df2_plus['tot_lay_off'] = df2_plus['tot_lay_off'] /100

  
df2_plus.to_sql(
    name="prova",
    if_exists='append',
    con = engine,
    index=False,
)
```

It will append rows 


# To do 

- [ ] how to alter a table
- [ ] 
# Related:

- [[Pandas]]
- [[SQLAlchemy]]

---
# Reference

3. https://www.youtube.com/watch?v=DiQ5Hni6oRI
