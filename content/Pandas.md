---
tags:
  - "#python"
date: " 2024-10-18"
up: "[[Python]]"
status: library
---
# Dataframe Object

In python, a `dataframe` could be seen as a dictionary, where the dictionary represents a dataframe, the `key` represents a `col_name` and the `values` are the `rows`.

The official definition is *A Pandas DataFrame is a 2 dimensional data structure, like a 2 dimensional array, or a table with rows and columns.*

## Create a dataframe

Use a dictionary and `DataFrame(dict)`

```python
data = {  
  "calories": [420, 380, 390],  
  "duration": [50, 40, 45]  
}  
  
#load data into a DataFrame object:  
df = pd.DataFrame(data)  
  
print(df)
```

A dataframe has an `index`
## Series

It is a one-dimensional dataframe, i.e. row of a single column. So, a `dataframe` can be seen as a container of multiple `series` objects.

A `series` as a `index`

# Load data

For `.csv` file, use `read_csv` and it creates a `dataframe` objec

```python
df = pd.read_csv("/path")
```

we can also specify a list of values that we want to be treated as missing values

```python
missing_val = ["na", "NA", "missing", "Missing"]
df = pd.read_csv("path", na_values = missing_val )
```

# Indexes

Index gives a unique identifier for the row.

Set the DataFrame index using existing columns.

```python
df.set_index("email")
```

The changes are not made in place, unless we specify it. To do it, use `inplace=True`


```python
df.set_index("email", inplace = True)
```

To reset the index, use `reset_index()` and `inplace=True` to apply the modification

```python
df.reset_index(inplace=True)
```

## Set index when loading data

```python
df = pd.read_csv("/path", index_col = "name_column")
```

## Sort index

Use `sort_index()`

```python
# ascending orderind
df.sort_index()

# reverse order
df.sort_index(ascending = False)

# permanently sorted

df.sort_index(inplace = True)

```

## Rename index

```python
# renaming index  
df.index = ['c1', 'c2', 'c3', 'c4', 'c5']  

```


# Import file


## Read_CSV

Here are the most important parameters:

1. `filepath_or_buffer`: 
```python
# Basic usage
df = pd.read_csv('data.csv')
# URL usage
df = pd.read_csv('https://example.com/data.csv')

# File-like object
with open('data.csv', 'r') as file:
    df = pd.read_csv(file)
```

2. `sep` or `delimiter`: Specifies the separator/delimiter
```python
# Tab-separated file
df = pd.read_csv('data.tsv', sep='\t')
# Semicolon-separated file
df = pd.read_csv('data.csv', sep=';')
```

1. `header`: Specify row number(s) to use as column names
```python
# No header row
df = pd.read_csv('data.csv', header=None)
# Use specific row as header
df = pd.read_csv('data.csv', header=0)  # First row
# Multiple header rows
df = pd.read_csv('data.csv', header=[0, 1])  # Two-level header
```

2. `names`: Specify column names
```python
# Define custom column names
df = pd.read_csv('data.csv', names=['Name', 'Age', 'City'])
```

3. `index_col`: Specify column(s) to use as index
```python
# Use first column as index
df = pd.read_csv('data.csv', index_col=0)
# Use multiple columns as index
df = pd.read_csv('data.csv', index_col=[0, 1])
```

4. `usecols`: Select specific columns to import
```python
# Select by column name
df = pd.read_csv('data.csv', usecols=['Name', 'Age'])
# Select by column position
df = pd.read_csv('data.csv', usecols=[0, 1, 3])
# Select using callable
df = pd.read_csv('data.csv', usecols=lambda x: x.upper() in ['NAME', 'AGE'])
```

5. `dtype`: Specify data types for columns
```python
df = pd.read_csv('data.csv', dtype={
    'age': 'int32',
    'salary': 'float64',
    'name': 'string'
})
```

6. `parse_dates`: Handle date parsing
```python
# Parse single column
df = pd.read_csv('data.csv', parse_dates=['date'])
# Parse multiple columns
df = pd.read_csv('data.csv', parse_dates=['start_date', 'end_date'])
# Combine columns into a single date
df = pd.read_csv('data.csv', parse_dates={'date': ['year', 'month', 'day']})
```

7. `na_values`: Specify additional NA/NaN values
```python
df = pd.read_csv('data.csv', na_values=['NA', 'missing', '-999'])
```

8. `skiprows`: Skip specific rows
```python
# Skip first 2 rows
df = pd.read_csv('data.csv', skiprows=2)
# Skip specific rows
df = pd.read_csv('data.csv', skiprows=[0, 2, 3])
```

9. `nrows`: Read specific number of rows
```python
# Read only first 1000 rows
df = pd.read_csv('data.csv', nrows=1000)
```

10. `encoding`: Specify file encoding
```python
# Read UTF-8 encoded file
df = pd.read_csv('data.csv', encoding='utf-8')
# Read file with different encoding
df = pd.read_csv('data.csv', encoding='latin1')
```

Some useful combinations for common scenarios:

11. Reading a messy CSV file:
```python
df = pd.read_csv('messy_data.csv',
                 sep=';',  # semicolon separator
                 na_values=['NA', 'missing', '-999'],  # multiple NA values
                 skip_blank_lines=True,  # skip empty lines
                 encoding='utf-8')  # specify encoding
```

12. Reading a large CSV file efficiently:
```python
df = pd.read_csv('large_data.csv',
                 usecols=['important_col1', 'important_col2'],  # select only needed columns
                 dtype={'important_col1': 'int32'},  # specify efficient dtypes
                 chunksize=10000)  # read in chunks
```

13. Reading dates and categorical data:
```python
df = pd.read_csv('data.csv',
                 parse_dates=['date_column'],
                 dtype={'category_column': 'category'},
                 date_parser=lambda x: pd.to_datetime(x, format='%Y-%m-%d'))
```


## Read Txt



1. Basic Python reading:
```python
# Simple reading
with open('file.txt', 'r') as file:
    content = file.read()  # Read entire file

# Read line by line
with open('file.txt', 'r') as file:
    for line in file:
        print(line.strip())
```

2. Using pandas.read_csv() for text files:
```python
import pandas as pd

# Basic reading
df = pd.read_csv('file.txt', sep='\t')  # For tab-separated
df = pd.read_csv('file.txt', sep='|')   # For pipe-separated

# With more options
df = pd.read_csv('file.txt',
    delimiter='\t',           # Use tab as separator
    header=None,             # No header row
    names=['col1', 'col2'],  # Custom column names
    skiprows=2,              # Skip first 2 rows
    encoding='utf-8')        # Specify encoding
```

1. Reading large text files:
```python
# Read in chunks
chunks = pd.read_csv('large_file.txt', 
    chunksize=1000,    # Read 1000 rows at a time
    delimiter='\t')

for chunk in chunks:
    # Process each chunk
    process_data(chunk)
```

Key parameters for text files:

2. `sep` or `delimiter`:
- `'\t'` for tab-separated
- `'|'` for pipe-separated
- `' '` for space-separated
- Any custom delimiter

3. `header`:
- `None` if no header
- `0` for first row as header
- List of row numbers for multi-level headers

4. `encoding`:
- `'utf-8'` most common
- `'latin1'` for special characters
- `'cp1252'` for Windows files

5. Error handling:
```python
try:
    df = pd.read_csv('file.txt', 
        error_bad_lines=False,     # Skip bad lines
        warn_bad_lines=True)       # Show warnings
except Exception as e:
    print(f"Error reading file: {e}")
```

Would you like me to explain any specific aspect in more detail or show how to handle a particular text file format?

## Read_json



1. `path_or_buf`: The file path or URL to your JSON file
```python
import pandas as pd

# From file
df = pd.read_json('data.json')

# From URL
df = pd.read_json('http://api.example.com/data.json')

# From string
json_string = '{"name":["John","Anna"], "age":[30,25]}'
df = pd.read_json(json_string)
```

2. `orient`: Specifies the JSON structure format
```python
# 'split' format
df = pd.read_json("""
{
    "columns":["name","age"],
    "index":[0,1],
    "data":[["John",30],["Anna",25]]
}
""", orient='split')

# 'records' format (list of dictionaries)
df = pd.read_json("""
[
    {"name":"John", "age":30},
    {"name":"Anna", "age":25}
]
""", orient='records')

# 'index' format
df = pd.read_json("""
{
    "0":{"name":"John", "age":30},
    "1":{"name":"Anna", "age":25}
}
""", orient='index')

# 'columns' format
df = pd.read_json("""
{
    "name":{"0":"John", "1":"Anna"},
    "age":{"0":30, "1":25}
}
""", orient='columns')
```

1. `typ`: Type of object to recover
```python
# Default 'frame' for DataFrame
df = pd.read_json('data.json', typ='frame')

# 'series' for pandas Series
series = pd.read_json('data.json', typ='series')
```

2. `dtype`: Specify data types for columns
```python
df = pd.read_json('data.json', dtype={
    'name': 'string',
    'age': 'int32',
    'salary': 'float64'
})
```

3. `convert_dates`: List of columns to parse as dates
```python
df = pd.read_json('data.json', convert_dates=['birth_date', 'join_date'])
```

4. `encoding`: Specify file encoding
```python
df = pd.read_json('data.json', encoding='utf-8')
```

5. `lines`: Read JSON Lines format (one JSON object per line)
```python
# Read JSON Lines format
df = pd.read_json('data.jsonl', lines=True)
```

6. `compression`: Handle compressed files
```python
# Read gzipped JSON file
df = pd.read_json('data.json.gz', compression='gzip')

# Read zipped JSON file
df = pd.read_json('data.json.zip', compression='zip')
```

Practical Examples:

7. Reading Nested JSON:
```python
# Nested JSON data
nested_json = '''
{
    "records": [
        {
            "id": 1,
            "user": {
                "name": "John",
                "details": {
                    "age": 30,
                    "city": "New York"
                }
            }
        }
    ]
}
'''

# Read and normalize nested JSON
df = pd.json_normalize(
    json.loads(nested_json)['records'],
    record_path=['user'],
    meta=['id']
)
```

8. Error Handling:
```python
try:
    df = pd.read_json('data.json',
                      orient='records',
                      encoding='utf-8',
                      convert_dates=True)
except ValueError as e:
    print(f"Error reading JSON: {e}")
```

9. Reading JSON with DateTime:
```python
df = pd.read_json('data.json',
                  convert_dates=['timestamp'],
                  date_unit='s')  # Specify unit for timestamps
```

10. Reading Large JSON Files:
```python
# Read JSON Lines in chunks
chunks = []
chunksize = 1000
for chunk in pd.read_json('large_file.jsonl', 
                         lines=True,
                         chunksize=chunksize):
    chunks.append(chunk)

# Combine all chunks
df = pd.concat(chunks)
```

Common Issues and Solutions:

11. Memory Issues with Large Files:
```python
# Use JSON Lines format for large files
df = pd.read_json('large_file.jsonl', 
                  lines=True,
                  chunksize=1000)  # Read in chunks
```

12. Handling Mixed Data Types:
```python
df = pd.read_json('data.json')
# Convert mixed-type columns to string
mixed_columns = ['column1', 'column2']
df[mixed_columns] = df[mixed_columns].astype(str)
```

## Read_excel



Basic Usage:
```python
import pandas as pd

# Basic reading
df = pd.read_excel('file.xlsx')

# Read specific sheet
df = pd.read_excel('file.xlsx', sheet_name='Sheet2')
```

Key Parameters:

1. `sheet_name`: Specify which sheet to read
```python
# Read specific sheet by name
df = pd.read_excel('file.xlsx', sheet_name='Sales')

# Read specific sheet by index (0-based)
df = pd.read_excel('file.xlsx', sheet_name=0)

# Read multiple sheets
sheets = pd.read_excel('file.xlsx', sheet_name=['Sheet1', 'Sheet2'])
# Returns dictionary with sheet names as keys

# Read all sheets
all_sheets = pd.read_excel('file.xlsx', sheet_name=None)
```

2. `usecols`: Select specific columns to import
```python
# Select by column letters
df = pd.read_excel('file.xlsx', usecols='A:C')

# Select by column numbers (0-based)
df = pd.read_excel('file.xlsx', usecols=[0, 1, 2])

# Select by column names
df = pd.read_excel('file.xlsx', usecols=['Name', 'Age'])

# Use callable
df = pd.read_excel('file.xlsx', usecols=lambda x: 'TOTAL' not in x)
```

1. `skiprows` and `nrows`: Control which rows to read
```python
# Skip first 2 rows
df = pd.read_excel('file.xlsx', skiprows=2)

# Read only first 10 rows
df = pd.read_excel('file.xlsx', nrows=10)

# Skip specific rows
df = pd.read_excel('file.xlsx', skiprows=[0, 2])
```

2. `header`: Specify row for column names
```python
# No header
df = pd.read_excel('file.xlsx', header=None)

# Header on specific row (0-based)
df = pd.read_excel('file.xlsx', header=1)

# Multiple header rows
df = pd.read_excel('file.xlsx', header=[0, 1])
```

3. `names`: Provide custom column names
```python
df = pd.read_excel('file.xlsx', 
                   names=['Name', 'Age', 'City'])
```

4. `index_col`: Set index column
```python
# Use first column as index
df = pd.read_excel('file.xlsx', index_col=0)

# Use multiple columns as index
df = pd.read_excel('file.xlsx', index_col=[0, 1])
```

5. `dtype`: Specify data types
```python
df = pd.read_excel('file.xlsx', dtype={
    'ID': int,
    'Name': str,
    'Salary': float
})
```

6. `na_values`: Define additional NA/NaN values
```python
df = pd.read_excel('file.xlsx', 
                   na_values=['NA', 'missing', '-'])
```

Practical Examples:

7. Reading a complex Excel file:
```python
df = pd.read_excel(
    'sales_data.xlsx',
    sheet_name='2023 Sales',
    usecols='B:F',
    skiprows=3,
    header=0,
    na_values=['N/A', 'Missing'],
    dtype={'Sales': float, 'Units': int}
)
```

8. Reading multiple sheets:
```python
excel_file = pd.ExcelFile('data.xlsx')

# Get sheet names
print(excel_file.sheet_names)

# Read each sheet into a dictionary
dataframes = {}
for sheet in excel_file.sheet_names:
    dataframes[sheet] = pd.read_excel(excel_file, sheet_name=sheet)
```

9. Reading with date parsing:
```python
df = pd.read_excel(
    'data.xlsx',
    parse_dates=['Date'],
    date_parser=lambda x: pd.to_datetime(x, format='%Y-%m-%d')
)
```

Error Handling:
```python
try:
    df = pd.read_excel('file.xlsx',
                       sheet_name='Sales',
                       na_values=['NA', 'missing'])
except FileNotFoundError:
    print("File not found")
except Exception as e:
    print(f"An error occurred: {e}")
```


# Info extraction

## DF Strucutre

| name         | type      | output                                                                              | output format      |
| ------------ | --------- | ----------------------------------------------------------------------------------- | ------------------ |
| `shape`      | attribute | outputs a `tuple` with the number of rows and columns `(r,c)`                       | `(r,c)`            |
| `info()`     | method    | outputs the number of rows and columns and the datatypes of the dataframe's columns |                    |
| `head(n)`    | method    | print the first n-th number of rows. n by default is 5                              | subseted dataframe |
| `tail(n)`    | method    | print the last n-th number of rows. n by default is 5                               | subseted dataframe |
| `describe()` | method    | returns statistical information of all numeric columns                              |                    |

```python
df.info()
df.shape
```

## Access column values 

We can use
- `["column"]` notation
- `.column` notation

```python
# access all the values of a single column
df["email"] ## column notation
df.email ## dot notation

# access multiple columns: pass a list 
df[["last","email"]] ## return a dataframe 

# return all the columns name
df.colums

  
# Describe - returns statistical information of all numeric columns  
df.describe()

```

When accessing values, the object that it returns is a `Series` object.

## Access row values

We use the following indexor
- `loc[]`: access the row by label, i.e. `index`. Default labels are integer. It takes as second argument the column name/ list of column names
- `iloc[i]`: access the row by integer location `i`. It takes as second argument the column index/ list of column indexes

```python
# iloc
df.iloc[0] # returns the first row as a series
df.iloc[[2,3]] # returns a dataframe of rows

# iloc and columns
df.iloc[[0,1],2 ] # specify the column index we want
df.iloc[[0,1],[2,1 ]] # specify the column index we want

```

```python
# loc 
df.loc[0] # returns the first row as a series
df.loc[[2,3]] # returns a dataframe of rows

# loc and columns
df.loc[[0,1],"email" ] # specify the column name we want
df.loc[[0,1],["email","last"] ] # specify the column name we want

```


*Note: `iloc` and `loc` returns a `series` where the `index` is the column names, so we know what the values referes to*


The main difference is that with `iloc` we can just pass integer or a list of integers for accessing both rows and columns, with `loc` we are not limited.

## Slicing
- Use `loc` or `iloc`
- Do not use the `[]`
- The last value is **included** when using `loc`

```python
# iloc slicing

df.iloc[1:40, 1:5]

# loc slicing

df.loc[1:40, "Q120"]
df.loc[1:40, "Q120":"SurveyEase"] # only in this case is inclusive
```

Use just `:` to retrive all the rows/colums
# Filtering

- filter by a **filter mask**
```python
filter_mask = df["last"] == "Doe" 
df[filter_mask]
df.loc[filter_mask, "column"]
```
Get back a series of `bool` values based on wether the value matches the fileter criteria 

*Note: passing a boolean series to `loc` provides a way to filter data out*


- `and: &` `or: |`

```python
# get all of the row

filter = (df["last"] == "Doe") & (df["first"] == "John")

df.loc(filter, "email")
```

```python
# get all of the row

filter = (df["last"] == "Schafer") | (df["first"] == "John")

df.loc(filter, "email")
```

Get the opposite of a filter with `~`. `~` returns everything that does not match the filter


```python
# get all of the row

filter = (df["last"] == "Schafer") | (df["first"] == "John")

df.loc(~filter, "email")
```

- `isin`
```python
countries =["America", "Nigeria"]
filter_mask = df["country"].isin(countries)

df[filter_mask, "email"]
```


- `str.cointains("", na=False)`
```python
filter = df["programming"].str.contains("python",na = False)

df[filter, "email"]
```

# Modifying data

## Columns
For renaming specific column/s, use the `rename(dict)` method passing a dictionary

```python

# ALL COLUMNS
# rename all columns 
df.columns = ["first_name", "last_name", "email"]

# lower/upper case all the columns
df.columns = [x.upper() for x in df.columns]
df.columns = [x.lower() for x in df.columns]

# remove space and replace with _
df.columns = df.columns.str.replce(" ", "_")

######################

# SPECIFIC COLUNM
# one column
df.rename(columns= {"old_name":"new_name"})
# multiple columns
df.rename(columns= {"old_name":"new_name","old_name":"new_name"})

# use inplace = True to apply the changes

df.rename(columns= {"old_name":"new_name","old_name":"new_name"}, inplace = True)
```

## Rows

Rows
```python
# Update Single row
df.loc[0, "first"] = "Bob" # single value
df.loc[0, ["first", "last"]] = ["Bob", "Smith"] # multiple values
df.loc[0] = ["Bob", "Smith", "gmail"] # all values

```

Assignment to a filter

```python
filt = (df["last"]=="john")
df[filt]["last"] = "Mark"
# error "setting with copy"

#### correct version
df.loc[filt, "last"] = "Mark"

```

Multiple rows
- `.apply(fun)`: use for calling a function on values. It can work both with `dataframe` and `series` object:
	- `series`: `apply` the function to **all** the values
	- `dataframe`: `apply` run the `fun` on each `series` (columns/rows) in the dataframe
		- by default `axis=0` (columns)
		- `axis=1`: apply the fun row-wise

- `.applymap(fun)`: it only works on `dataframe` and applies the `fun` element-wise

- `map(dict)`: it only works with `series`.  It is often used to map values from two series having one similar column.

- `replace(dict)`: it only works with a `series`. Replace the values by the mapping passed as argument 


```python
# lowercase/uppercase all the row values of a column
df["email"]= df["email"].str.lower()

########################## 
# apply
## Series
df["email"] = df["email"].apply(len)
df["email"] = df["email"].apply(lambda x : x.lower())

## Dataframe
df.apply(len) # rumber of rows in each col
df.apply(len, axis = 1) # rumber of cols in each row

df.apply(pd.Series.min) # get the minimum value for each series
df.apply(lambda x: x.min()) # get the minimum value for each series


##########################
# applymap
df.applymap(len) # len of each element

##########################
# map
s= pd.Series(['cat','cow','dog'])
s.map({'cat':'kitten','cow':'calf'})

Mapping: 
0    kitten
1      calf
2       NaN # the value that has not be substitute becomes NaN


s= pd.Series(['cat','cow','dog'])
s.map('This is a {}'.format)

0     This is a cat  
1     This is a cow  
2    This is a dog  
dtype: object


##########################
# replace

df["first"].replace({'cat':'kitten','cow':'calf'})

Mapping: 
0    kitten
1      calf
2       dog # no NaN anymore

```

## Add/Remove Rows 

- add: `append(dict)`
```python
# single row
df = df.append({"first":"Tony"}, ingore_index = True) # missing column's values will have NaN

# Adding a new row  
df.loc['c7'] = ['Cuba', 11485, 109884, 87206]  

# append a dataframe

df = df.append(df2, ignore_index = True, sort = False)
```
*Note: ignore the index = True or the series has an index*

- remove: by index
```python
df.drop(index = 4, inplace = True)


# deleting a row using explicit index  
df = df.drop('c7', axis=0)  


# by condition (pass the index)

filter = df["last"] == "Doe"
df.drop(index = df[filter].index , inplace = True)

```


## Add/Remove Columns

- add
```python
df["new_col"] =  ['SouthernAsia', 'NorthernAmerica', 'EasternAsia',   
                'WesternEurope', 'NorthernAmerica', 'NorthernAfrica',   
                'Caribbean']  

# from a combiantion of columns

new= df["first"] +" "+ df["last"]
df["full_name"] = new
```

- remove
```python
df.drop("full_name", inplace = True)
df.drop(columns = ["full_name", "last_name"], inplace = True)


df = df.drop('Region', axis=1)  

```

- split up in 2 columns
```python
df[["last","first"]]=df["full_name"].str.split(" ",expand=True)
```

## Duplicates

```python
# Duplicates and dropping duplicates  
###########################################################################  
# duplicate the last row for testing purpose  
df.loc['c6'] = ['Egypt', 97553, 1002000, 315917]  
df.loc['c7'] = ['Egypt', 38275, 9600000, 164234]  
print(df)  
>>    Country  Pop(in K)     Area       GDP  
  c1    India    1339180  3287263   2116239  
  c2      USA     324460  9833517  18036648  
  c3    China    1409517  9600000  11158457  
  c4  Germany      82114   357376   3363600  
  c5    Egypt      97553  1002000    315917  
  c6    Egypt      97553  1002000    315917  
  c7    Egypt      38275  9600000    164234  
  
# check if a row is duplicated across all columns  
print(df.duplicated())  
>> c1    False  
   c2    False  
   c3    False  
   c4    False  
   c5    False  
   c6     True  
   c7    False  
   dtype: bool  
  
# check if a row is duplicated for a specific column  
print(df.duplicated(subset=['Country']))  
>> c1    False  
   c2    False  
   c3    False  
   c4    False  
   c5    False  
   c6     True  
   c7     True  
   dtype: bool  
  
# drop duplicates  
df = df.drop_duplicates(subset=['Country'])  
print(df)  
>>    Country  Pop(in K)     Area       GDP  
  c1    India    1339180  3287263   2116239  
  c2      USA     324460  9833517  18036648  
  c3    China    1409517  9600000  11158457  
  c4  Germany      82114   357376   3363600  
  c5    Egypt      97553  1002000    31591  
###########################################################################
```

## Concatenate


- `concat([series], )`: concatenate two series in one

```python
group_country = df.groupby(["Country"])

py = group_country["LanguageHaveWorkedWith"].apply(lambda x : x.str.contains("Python").sum())

n = df["Country"].value_counts()

  

df2 = pd.concat([py,n], axis = columns, sort = False) # so that we match the axis
```


# Sorting

 `sort_values(_by_, _*_, _axis=0_, _ascending=True_, _inplace=False_, _kind='quicksort'_, _na_position='last'_, _ignore_index=False_, _key=None_)`
 
| args         | definition                                                                                                                                                                                                                                                                                                                                       | options                                                                                                                                                                                       | expected type                                    |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------ |
| by           | it is a ==str== or ==list of str== and defines how we want to sort the series/dataframe. <br><br>use the ==list of string==<br>when we want to sort by many columns according to the order of the list column names (see example n.5)<br><br>we can also use `by` = `column name` and we will sort according to column name values. <br>         | if axis is 0 or ‘index’ then `by` may contain index levels and/or column labels.<br><br>if axis is 1 or ‘columns’ then `by` may contain column levels and/or index labels.<br><br> `last`<br> | str or list of str                               |
| axis         | Defines the Axis to be sorted.                                                                                                                                                                                                                                                                                                                   | “{0 or ‘index’, 1 or ‘columns’}”, default 0.                                                                                                                                                  |                                                  |
| ascending    | list of bool follows the same reasoning of `by`<br><br>(see example n.7)                                                                                                                                                                                                                                                                         |                                                                                                                                                                                               | bool or list of bool                             |
| inplace      |                                                                                                                                                                                                                                                                                                                                                  |                                                                                                                                                                                               | bool                                             |
| kind         | Choice of sorting algorithm                                                                                                                                                                                                                                                                                                                      | ‘quicksort’, ‘mergesort’, ‘heapsort’, ‘stable’}                                                                                                                                               | {‘quicksort’, ‘mergesort’, ‘heapsort’, ‘stable’} |
| na_position  | Puts NaNs at the beginning if first; last puts NaNs at the end.                                                                                                                                                                                                                                                                                  | {‘first’, ‘last’}, default ‘last’                                                                                                                                                             |                                                  |
| ignore_index | default False. <br><br><br>If True, the resulting axis will be labeled 0, 1, …, n - 1.<br>                                                                                                                                                                                                                                                       |                                                                                                                                                                                               | bool                                             |
| key          | Apply the key function to the values before sorting. This is similar to the key argument in the builtin `sorted()` function, with the notable difference that this key function should be _vectorized_. It should expect a `Series` and return a Series with the same shape as the input. It will be applied to each column in by independently. |                                                                                                                                                                                               | callable (function/lambda)                       |






```python
# Sort a column
10. df.sort_values() # Asc
11. df.sort_values(ascending = False) # Desc


# sort by one column
12. df.sort_values(by = "last") # Asc
13. df.sort_values(by = "last",ascending = False) # Desc

# sort by multiple columns

14. df.sort_values(by = ["last","first"]) # Asc
15. df.sort_values(by = ["last","first"],ascending = False) # Asc

# sort by multiple columns, one asc one desc

7 .df.sort_values(by = ["last","first"], ascending = [False, True]) # Asc

```

*Note: when sorting by multiple columns, the sort is made by first the fist column and then others*


After the `sort_values`, and we didn't use `ignore_index`, indexes will be messed around. We can sort by index using `sort_index`

*Note: use `inplace` to mantain the changes*

```python
# sort by index
df.sort_index()

```

# Operations

```python
# Return a Series containing counts of unique values.
df["Employment"].value_counts()

index = pd.Index([3, 1, 2, 3, 4, np.nan])
>>> index.value_counts()
3.0    2
1.0    1
2.0    1
4.0    1


```

- `nlargest(n, /op "column name")`
```python

```

# Aggregation


![[Pasted image 20250207125351.png]]


>[!tip] Big gotcha
>When calling `agg` and passing a function, the values that are analized by this aggregation function are the elements in one groups (i.e a sub dataframe), not a single value


```python
# on a column
df["ConvertedComp"].mean()
df["ConvertedComp"].median()

# on a dataframe will be applied only to columns that contains numerical values

df.median()
df.mean()

# describe

df.desccribe()
df["ConvertedComp"].describe() # count: is the number of non missing rows


# count the unique values of a column/s
df["Hobbyist"].value_counts()
df["Hobbyist"].value_counts(normalize = True) # in percentage

```

*Note: does not take into account NA values*

`agg()`: pass a list of aggregation function

```python
group_country = df.groupby(["Country", "Industry"])

group_country["ConvertedCompYearly"].agg(["mean", "median"]).loc[("Italy")].sort_values(by="mean", ascending=False)
```

	## How to transform values grouo into list for each column


- The code is using `lambda tdf: tdf.unique().tolist()` as an aggregation function. It works because:
    - For each unique 'X' value, it takes all corresponding Y and Z values
    - `unique()` gets the distinct values
    - `tolist()` converts the numpy array of unique values into a Python list
- In your example:
    - When X=10, it finds all Y values (10,15,11,14) and Z values (22,20,18) that correspond to X=10
    - When X=20, there's only one Y value (20) and Z value (20)
    - When X=30, it finds two Y values (21,12) and Z values (13,10)


```python
df = pd.DataFrame( {'X' : [10, 10, 10, 20, 30, 30, 10],

                    'Y' : [10, 15, 11, 20, 21, 12, 14],

                    'Z' : [22, 20, 18, 20, 13, 10, 0]})

  

df.groupby(by= 'X').agg(lambda tdf: tdf.unique().tolist())
```

## How to aggreagate on multiple columns

```python
df = pd.DataFrame({

    'school_code': ['s001','s002','s003','s001','s002','s001'],

    'class': ['V', 'V', 'VI', 'VI', 'V', 'VI'],

    'name': ['Alberto Franco','Gino Mcneill','Ryan Parkes', 'Eesha Hinton', 'Gino Mcneill', 'David Parkes'],

    'date_Of_Birth ': ['15/05/2002','17/05/2002','16/02/1999','25/09/1998','11/05/2002','15/09/1997'],

    'age': [12, 12, 13, 13, 14, 12],

    'height': [173, 192, 186, 167, 151, 159],

    'weight': [35, 32, 33, 30, 31, 32],

    'address': ['street1', 'street2', 'street3', 'street1', 'street2', 'street4']},

    index=['S1', 'S2', 'S3', 'S4', 'S5', 'S6'])

  

df.groupby(["school_code", "class"]).agg(

    {'height' : ['mean', 'min', "max"], 'age' : ['mean', 'min', "max"]}

)
```

## How to create custom names

```python
student_data.groupby("school_code").agg(Age_Mean = ('age','mean'),Age_Max=('age',max),Age_Min=('age',min) )
```


I don't need to subset for the `col name`, just put the name in the tuple
```python
result = df.groupby(['salesman_id'])\ .agg(customer_id_start_C = ('customer_id', customer_id_C), customer_id_list = ('customer_id', lambda x: ', '.join(x)), purchase_amt_gap = ('purch_amt', lambda x: x.max()-x.min()) )
```

## How to filter groups

```python
df = pd.DataFrame({

    'school_code': ['s001','s002','s003','s001','s002','s004'],

    'class': ['V', 'V', 'VI', 'VI', 'V', 'VI'],

    'name': ['Alberto Franco','Gino Mcneill','Ryan Parkes', 'Eesha Hinton', 'Gino Mcneill', 'David Parkes'],

    'date_Of_Birth ': ['15/05/2002','17/05/2002','16/02/1999','25/09/1998','11/05/2002','15/09/1997'],

    'age': [12, 12, 13, 13, 14, 12],

    'weight': [173, 192, 186, 167, 151, 159],

    'height': [35, None, 33, 30, None, 32]},

    index=['S1', 'S2', 'S3', 'S4', 'S5', 'S6'])

  

df.groupby("school_code").filter(lambda x: x['height'].notna().any())
```

![[Pasted image 20250207151201.png]]


>[!important] Why any/all?
>When filtering a group, the filter function needs to return a single True/False value for each group. Whitout `any/all`, the lambda function is comparing each value individually, returning a series of booleans instead of a single boolean.
>
>- We use `.any()` or `.all()` to reduce the series of boolean values to a single True/False value
>>- `.any()` will keep groups where at least one value meets the condition
>>- `.all()` will keep groups where all values meet the condition
## How to use a custom function

Recalling the ==gotcha==, `x` in this case is the sub dataframe (i.e. the grouped dataframe) and not a single value



```python
data = {'Category': ['A', 'A', 'B', 'B', 'C', 'C'],

        'Value': [5, 15, 25, 35, 45, 55]}

  

df = pd.DataFrame(data)

  

def custom_agg(x):

    return x.max() - x.min()

  
  

df.groupby('Category').agg(custom_agg

    )
```


![[Pasted image 20250208145558.png]]

# Transform 


Transform is powerful because it allows you to apply operations while preserving the original DataFrame's index and shape.


```python
df = pd.DataFrame({ 'group': ['A', 'A', 'B', 'B'], 'value': [1, 2, 3, 4] }) 

# Add mean of each group to every row 

df['group_mean'] = df.groupby('group')['value'].transform('mean')

# Custom transformation
df['scaled'] = df.groupby('group')['value'].transform(lambda x: (x - x.mean()) / x.std())

# Apply multiple aggregations
stats = df.groupby('group')['value'].transform(['mean', 'sum', 'count'])
```


Key differences from other group operations:

- `transform()` returns a DataFrame/Series with the same shape as the input
- Each group's result is broadcast to all rows in that group
- Useful for feature engineering and creating group-based statistics

![[Pasted image 20250208151628.png]]
# Grouping

- pass a list of columns that we want to group
	- return: `DataFrameGroupBy` object containing groups of rows by the grouping column
	- it is the same of creating a filter mask and filter out the df

```python
# Group by one column
## 1) group
country_group = df.groupby(["Country"])

### groups are created by country name

## 2) get a group
country_group.get_group("United States of America")

## 3) get counts 
group_country["SocialMedia"].value_counts()

## 4) grab a specific group
group_country["SocialMedia"].value_counts().loc["India"]
group_country["SocialMedia"].value_counts(normalized=True).loc["India"]

## Gropu by multiple columns

group_country = df.groupby(["Country","Industry"])

group_country.get_group(("United States of America","Financial Services"))


```

`apply` and `SeriesGroupBy`

```python
group_country = df.groupby(["Country"])

group_country["LanguageHaveWorkedWith"].apply(lambda x : x.str.contains("Python").sum()).loc["Italy"]
```

![[Pasted image 20241019144254.png]]





```python
df = pd.DataFrame({'Name': ['A', 'A', 'A', 'B', 'B', 'C', 'C'],   
                   'Subject': ['Ph', 'Ch', 'Ma', 'Ph', 'Ch', 'Ph', 'Ch'],  
                   'Marks': [90, 80, 95, 60, 70, 40, 90]})  
print(df)  
>>  Name Subject  Marks  
  0    A      Ph     90  
  1    A      Ch     80  
  2    A      Ma     95  
  3    B      Ph     60  
  4    B      Ch     70  
  5    C      Ph     40  
  6    C      Ch     90  
  
# Grouping based on a column and get single aggregate  
print(df.groupby('Name')['Subject'].count()) # get number of 'Subject'   
                                             # for each name  
Name  
A    3  
B    2  
C    2  
Name: Subject, dtype: int6  
  
# Grouping based on a column and get multiple aggregates  
print(df.groupby('Name')['Marks'].aggregate(['min', 'max', 'mean']))  
>>      min  max       mean  
  Name                       
  A      80   95  88.333333  
  B      60   70  65.000000  
  C      40   90  65.000000  
  
# Group based filtering - show all the rows of a student whose minimum  
# marks, of all the subject, is greater than 50  
print(df.groupby('Name').filter(lambda x: x['Marks'].min() > 50))  
>>  Name Subject  Marks  
  0    A      Ph     90  
  1    A      Ch     80  
  2    A      Ma     95  
  3    B      Ph     60  
  4    B      Ch     70  
  
# Group based apply - apply a function which will create a new column   
# showing the grades for each student. The grade is calculated based on   
# the mean of all marks of a student  
def grade(x):  
  x['grade'] = 'A' if x['Marks'].mean() > 80 else 'B'  
  return x  
df = df.groupby('Name').apply(grade)  
print(df)  
>>  Name Subject  Marks grade  
  0    A      Ph     90     A  
  1    A      Ch     80     A  
  2    A      Ma     95     A  
  3    B      Ph     60     B  
  4    B      Ch     70     B  
  5    C      Ph     40     B  
  6    C      Ch     90     B
```

## Get groups and keys


- `groups`: dictionary form


- `groups.keys()`

```python
df = pd.DataFrame({

    'school_code': ['s001','s002','s003','s001','s002','s004'],

    'class': ['V', 'V', 'VI', 'VI', 'V', 'VI'],

    'name': ['Alberto Franco','Gino Mcneill','Ryan Parkes', 'Eesha Hinton', 'Gino Mcneill', 'David Parkes'],

    'date_Of_Birth ': ['15/05/2002','17/05/2002','16/02/1999','25/09/1998','11/05/2002','15/09/1997'],

    'age': [12, 12, 13, 13, 14, 12],

    'height': [173, 192, 186, 167, 151, 159],

    'weight': [35, 32, 33, 30, 31, 32],

    'address': ['street1', 'street2', 'street3', 'street1', 'street2', 'street4']},

    index=['S1', 'S2', 'S3', 'S4', 'S5', 'S6'])

  

df.groupby("school_code").groups.keys()
```

## How to group by bins

```python
res = df.groupby(pd.cut(df["sales_id"], bins=[5000, 5003, 5008]))

for i, j in res:

    print(i)

    print(j)
```

![[Pasted image 20250207141803.png]]

## Remove n rows from every group

```python
res = df.groupby(["salesman_id","customer_id"])

  

res.apply(lambda x: x.iloc[:-1])
```

## Grouper


A Grouper allows the user to specify a groupby instruction for an object.

- https://pandas.pydata.org/docs/reference/api/pandas.Grouper.html


### How to group by month


```python
orders_data["ord_date"] = pd.to_datetime(orders_data["ord_date"])

  

gr = pd.Grouper(key = "ord_date", freq="M")

  

a = orders_data.groupby(gr)["purch_amt"].agg(sum)

  

a
```

### Alternatives for Grouper

```python
gr = pd.Grouper(key = "ord_date", freq="Y")

a = orders_data.groupby([orders_data['ord_date'].dt.year, orders_data['ord_date'].dt.month]).agg({'purch_amt':sum})

a
```


# Missing values



```python
# Get number of null values per column  
print(df.isna().sum())  
>> Item        1  
   Price       1  
   Store       1  
   Quantity    2  
   dtype: int64  
  
# Get number of null values in the entire dataframe  
print(df.isna().sum().sum())

>>5
```

- `fillna(value)`

```python
# Filling null values  
# df.fillna(n) - fills null values with value 'n'  
###########################################################################  
print(df.fillna(-1))  
>>         Item  Price   Store  Quantity  
  0        Soap   25.0  Store1      14.0  
  1          -1  120.0  Store2      -1.0  
  2  Toothbrush   -1.0  Store3      15.0  
  3        Comb   30.0      -1      -1.0  
  4     Shampoo   80.0  Store2      20.0
```

- `dropna(axis=0/1, how= any, subset = [], inplace = )`
	- `axis`
		- `axis`= 0: drop by row
		- `axis`= : drop colum

	- `how`: the criteria used to drop. This depends on the contex.
		- `any` means *with any missing values*. 
		- `all` if *all of the values in the row/column are missing*. 
	- `subset=[column_name]`: the column where we check the criteria. (eg. I really need email for my analysiss, then if I do not have it the row is useless `subset = ["email"]`) 
```python
 
# Dropping null values  
# df.dropna() - axis=0 for rows and 1 for column. Drops rows/columns with  
 
print(df.dropna(axis=0))  
>>      Item  Price   Store  Quantity  
  0     Soap   25.0  Store1      14.0  
  4  Shampoo   80.0  Store2      20.0  

```
Use *`inplace = True`* to save changes.

Note that `dropna` deletes also the`index`. 

# Casting

```python
# check the type of each colum
df.dtypes

# one column
df["age"]=df["age"].astype(int)

```

*Note: if the column has a missing value, then it it a float type (np.nan is folat). Thus, if we are trying to casting into int, it will return an error. Instead, cast to float*


# Dates and timeseries

## Datetime python

### Parser

create a datetime from a given string 


```python
from dateutil import parser

  

date2 = parser.parse("1st of January, 2021")

date2
```


## Other

### Create a time series

```python
dates = [datetime.datetime(2011, 9, 1), datetime.datetime(2011, 9, 2)]

time_series = pd.Series(np.random.randn(2), dates)

time_series
```

### create Datetime index object

`pandas.DatetimeIndex`

```python
index = pd.DatetimeIndex(['2011-09-02', '2012-08-04',

                          '2015-09-03', '2010-08-04',

                          '2015-03-03', '2011-08-04',

                          '2015-04-03', '2012-08-04'])

  

s_dates = pd.Series([0, 1, 2, 3, 4, 5, 6, 7], index=index)
```

### how to select data based on time

- same year

```python
index = pd.DatetimeIndex(['2011-09-02', '2012-08-04',

                          '2015-09-03', '2010-08-04',

                          '2015-03-03', '2011-08-04',

                          '2015-04-03', '2012-08-04'])

  

s_dates = pd.Series([0, 1, 2, 3, 4, 5, 6, 7], index=index)

cond = s_dates['2011']
```


### Create a time range 

```python
pd.timedelta_range('1', periods= 30, freq="1H")
```

![[Pasted image 20250208155903.png]]

### Create a datetime column with year and day 

```python
data = {

"year": [2002, 2003, 2015, 2018],

"day_of_the_year": [250, 365, 1, 140]

}

df = pd.DataFrame(data)

prova = pd.to_datetime(df["year"].astype(str) + df["day_of_the_year"].astype(str), format="%Y%j")

  

prova
```

![[Pasted image 20250208160427.png]]

### Time conversion and UTC

Use `pd.to_datetime`
- `unit`: The unit of the arg (D,s,ms,us,ns) denote the unit, which is an integer or float number. This will be based off the origin. Example, with `unit='ms'` and `origin='unix'`, this would calculate the number of milliseconds to the unix epoch start.

```python
epoch_t = 1621132355

time_stamp = pd.to_datetime(epoch_t, unit='s')

time_stamp
```
- `epoch_t = 1621132355` This is a Unix timestamp (also called epoch time)
- It represents the number of seconds that have elapsed since January 1, 1970, 00:00:00 UTC (the Unix epoch)
- The `unit='s'` parameter specifies that the input is in seconds (other options could be 'ms' for milliseconds, etc.)
- After this conversion, `time_stamp` is a pandas Timestamp object but without timezone information


use the built in function `tz.convert`

```python
epoch_t = 1621132355

time_stamp = pd.to_datetime(epoch_t, unit='s')

time_stamp.tz_localize(tz= 'UTC').tz_convert(tz = 'US/Pacific')
```

- First, `.tz_localize(tz='UTC')` adds timezone information, marking the time as UTC
- Then, `.tz_convert(tz='US/Pacific')` converts the time from UTC to US/Pacific time zone

### Create a timeseries with time zone information

use `timestamp`

```python
pd.Timestamp("2025-01-08", tz= "Europe/Rome")
```

### Intraday offset

```python
dateset1 = pd.date_range('2029-01-01 00:00:00', periods=20, freq='3h10min')

dateset1
```

### More regarding pandas business day

```python
# add more business day 
day + 2 *pd.tseries.offsets.BDay()

# add more last business day months

day + 2 *pd.tseries.offsets.BMonthEnd()
```

create start and end of a year

```python
# For Month Start dates

month_starts = pd.Series(pd.date_range(start='2024-02-01', periods=12, freq='MS'))

  

# For Month End dates

month_ends = pd.Series(pd.date_range(start='2024-02-01', periods=12, freq='M'))

  

# If you want to combine them into one series

all_dates = pd.concat([month_starts, month_ends]).sort_values()

all_dates
```


### To 

- `pd.to_datatime(df["Date"], format = "format string")`: convert a column to a `daytime` object

Before of doing this, better to format the values as pandas might strugle

We need to create a format string

```python
df["Date"] = pd.to_datetime(df["Date"], format = "%Y-%m-%d %I-%p")

```

We can also format the date direclty when uploading the data with `parse_dates = ["date"]` and `date_parser = fun`

```python
d_parser = lambda x: pd.datetime.strptime(x, "%Y-%m-%d %I-%p")

df = pd.read_csv("Bitcoin Historical Data.csv", parse_dates = ["Date"], date_parser = d_parser)
```


## Day name 

```python
# one day
df.loc[0, "Date"].day_name()
# all the days
df["Date"].dt.day_name()

```

## Explore data

```python
# earliest date
df["Date"].min()

# recent date
df["Date"].max()

# time delta
df["Date"].max() - df["Date"].min() ## returns a time delta object


```

## Filters

- strings format
- datetime objects

```python
# with string format
flt = df["Date"]>="2020"
df.loc[flt]


# with string format
flt = df["Date"]>=pd.to_datetime("2020-01-01")

df.loc[flt]


```

We can also use slicing

```python
df.set_index("Date", inplace=True)
df.sort_index(inplace = True)
df["2020"]
df['2020-01':'2021-02']


```

## Resamplying

Change timeframe. Look the documentation for all the formatting.


```python
df["High"].resample("ME") # monthly

# example
max_pd = df["High"].resample("ME").max()
max_pd["2020-01"]

# resample in all columns dataset
df.resample('W').mean()

# resample in all colums dataset and multiple aggregation functions

df.resample('W').agg({'Close': 'mean', 'High': 'max', 'Low': 'min', 'Volume': 'sum'})
```


# Merge

![[Pasted image 20250206175328.png]]


- It joins by ==index==

```python
student_data1 = pd.DataFrame({

        'student_id': ['S1', 'S2', 'S3', 'S4', 'S5'],

         'name': ['Danniella Fenton', 'Ryder Storey', 'Bryce Jensen', 'Ed Bernal', 'Kwame Morin'],

        'marks': [200, 210, 190, 222, 199]})

  

student_data2 = pd.DataFrame({

        'student_id': ['S4', 'S5', 'S6', 'S7', 'S8'],

        'name': ['Scarlette Fisher', 'Carla Williamson', 'Dante Morse', 'Kaiser William', 'Madeeha Preston'],

        'marks': [201, 200, 198, 219, 201]})

  

student_data1.join(student_data2, how='left', lsuffix='_1', rsuffix='_2')
```

![[Pasted image 20250206180536.png]]

it merges by ==columns==

```python
student_data1.merge(student_data2, how='inner', on= "student_id")
```

![[Pasted image 20250206180708.png]]

## Different columns name

Use left and right on

```python
df1 = pd.DataFrame({

    'Employee_ID': [1, 2, 3],

    'Name': ['Selena', 'Annabel', 'Caeso']

})

  

df2 = pd.DataFrame({

    'ID': [1, 2, 3],

    'Salary': [50000, 60000, 70000]

})

  

df1.merge(df2, left_on='Employee_ID', right_on='ID')
```

![[Pasted image 20250206184249.png]]


and more columns 

```python
import pandas as pd 

# Create two sample DataFrames with matching values 

df1 = pd.DataFrame({ 'Employee_ID': [1, 2, 3], 'Name': ['Selena', 'Annabel', 'Caeso'] 

# Matching names with df2 }) 

df2 = pd.DataFrame({ 'ID': [1, 2, 3], 'Employee_Name': ['Selena', 'Annabel', 'Caeso'], 

# Matching names with df1 'Salary': [50000, 60000, 70000] }) # Merge the DataFrames on different column names 

merged_df = pd.merge(df1, df2, left_on=['Employee_ID', 'Name'], right_on=['ID', 'Employee_Name']) 

# Output the result 

print(merged_df)
```

## Merge multiple dataframes

```python
df1 = pd.DataFrame({

    'ID': [1, 2, 3],

    'Name': ['Selena', 'Annabel', 'Caeso']

})

  

df2 = pd.DataFrame({

    'ID': [1, 2, 3],

    'Age': [25, 30, 22]

})

  

df3 = pd.DataFrame({

    'ID': [1, 2, 3],

    'Salary': [50000, 60000, 70000]

})

  

df1.merge(df3.merge(df2, on= "ID"), on="ID")
```

## Indicator

```python
# Create two sample DataFrames

df1 = pd.DataFrame({

    'ID': [1, 2, 3],

    'Name': ['Selena', 'Annabel', 'Caeso']

})

  

df2 = pd.DataFrame({

    'ID': [2, 3, 4],

    'Age': [25, 30, 22]

})

  

# Merge the DataFrames with an indicator

merged_df = pd.merge(df1, df2, on='ID', how='outer', indicator=True)

merged_df
```

## Combine_first

`DataFrame.combine_first(_other_)`

Update null elements with value in the same location in other.

```python
df1 = pd.DataFrame({'A': [None, 0, None], 'B': [3, 4, 5]})

df2 = pd.DataFrame({'A': [1, 1, 3], 'B': [3, None, 3]})

  

df1.combine_first(df2)
```

![[Pasted image 20250206182637.png]]

![[Pasted image 20250206182620.png]]


# Plot
# Others

```python
pd.set_option()


```

# Gotcha
```python
df.replace("value",np.na, inplace=True)
```

# To do 
- [ ] others
- [ ] string methods
- [ ] view/copy
- [ ] fix index

- [ ] Casting 
	- [ ] to numeric
- [ ] Flipping data
	- [ ] Stack
	- [ ] reset_index
- [ ] sort
	- [ ] nlargest
	- [ ] nsmallest

- [ ] Index
	- [ ] pd.Index
	- [ ] get loc
	- [ ] reset_index

- [ ] datetime
	- [ ] pd.date_range
	- [ ] parser

- [ ] df.query
# Reference

16. https://www.youtube.com/watch?v=ZyhVh-qRZPA&list=PL-osiE80TeTsWmV9i9c58mdDCSskIFdDS&ab_channel=CoreySchafer
17. https://medium.com/@shetanantheshnlsv/pandas-crash-course-44d02b2f41ee
18. https://tomaugspurger.net/posts/modern-1-intro/

# To be sistemati

## Add rows to a series

Use `pd.concat`:

```python
pd.concat([sr1, pd.Series([500,'php'])], ignore_index=True)
```


## Index change

use `reindex()` to create a new index with optional filling logic


## Elements not in series

use `seris.isin(series)`. the tilda mean `!`


Find elements that are not in both seires
```python
sr11 = pd.Series(np.union1d(sr1, sr2))

sr22 = pd.Series(np.intersect1d(sr1, sr2))

sr11[~sr11.isin(sr22)]
```


## Match values in different series

```python
# Create the series 
sr1 = pd.Series([1, 2, 3, 4, 5, 6, 7, 8, 9, 10]) 
sr2 = pd.Series([1, 3, 5, 7, 10]) 
# Method 
1: Using numpy where 
positions = np.where(sr1.isin(sr2))[0].tolist() 
# Method 2: Using index and isin 
positions = sr1[sr1.isin(sr2)].index.tolist()
```

## Modify datetime componets in palce

```python
sr = pd.Series(["Jan 2015","Feb 2016","Mar 2017","Apr 2018","May 2019"])

sr1 = sr.map(lambda x:parse(x))

sr1.apply(lambda x: x.replace(day=11))
```

## Convert continuos values to categorical

```python
pd.cut(x= df['age'], bins = [0,18,65,99] ,labels=['kids', 'adult', 'elderly'])
```


## Check memory usage

```python
print("\nGlobal usage of memory of the DataFrame:")

print(df.info(memory_usage = "deep"))

print("\nThe usage of memory of every column of the said DataFrame:")

print(df.memory_usage(deep = True))
```


## Create multi index Series

first create the index using `MultiIndex.from_`

```python
sales_arrays = [['sale1', 'sale1', 'sale2', 'sale2', 'sale3', 'sale3', 'sale4', 'sale4'], ['city1', 'city2', 'city1', 'city2', 'city1', 'city2', 'city1', 'city2']] 

sales_tuples = list(zip(*sales_arrays)) 

sales_index = pd.MultiIndex.from_tuples(sales_tuples, names=['sale', 'city']) print(sales_tuples) 

s = pd.Series(np.random.randn(8), index = sales_index) print(s)
```


## Create multi index Dataframe


```python
sales_arrays = [['sale1', 'sale1', 'sale2', 'sale2', 'sale3', 'sale3', 'sale4', 'sale4'], ['city1', 'city2', 'city1', 'city2', 'city1', 'city2', 'city1', 'city2']]

  

sales_tuples = list(zip(*sales_arrays))

  

multi_index = pd.MultiIndex.from_tuples(sales_tuples, names=["sale", "city"])

  

df = pd.DataFrame(data={

    "col1":np.random.randn(8),

    "col2": np.random.randn(8)

}, index=multi_index)

  

df
```



## Create interval index

```python
df_interval = pd.DataFrame({"X":[1, 2, 3, 4, 5, 6, 7]},

                            index = pd.IntervalIndex.from_breaks(

                            [0, 0.5, 1.0, 1.5, 2.0, 2.5, 3, 3.5]))
```


## Create datetime index

```python
dt_range = pd.date_range(start="2025-01-05", end="2025-01-15", periods=10)

df_dt = pd.DataFrame({"Sale_amt":[100, 110, 117, 150, 112, 99, 129, 135, 140, 150]},

                            index = dt_range)

  

df_dt
```


## Slice by different level of index level

use `IndexSlice`

```python
idx = pd.IndexSlice

df.loc[idx[:,'city1'],:]
```

## Sort by multiindex

```python
df.sort_index(level=[0,1], ascending=[True, False])
```


## Get index of an element 
```python
ds = pd.Series([1,3,5,7,9,11,13,15], index=[0,1,2,3,4,5,7,8])

ds[ds == 11].index[0]
```

## Create a dataframe with multiple column levels

```python
cols = pd.MultiIndex.from_tuples([("a", "x"), ("a", "y"), ("a", "z")])

print("\nConstruct a Dataframe using the said MultiIndex levels: ")

df = pd.DataFrame([[1,2,3], [3,4,5], [5,6,7]], columns=cols)

df
```

![[Pasted image 20250205151759.png]]

## Drop level from multilevel columns

```python
df_dropped = df.droplevel(level=0, axis=1)

df_dropped
```

## Insert a column in a multiindex columns


```python
date_of_birth = ['15/05/2002','17/05/2002','16/02/1999']  

df.insert(loc = 3, value=date_of_birth, column=('a',"ciao"), )
```


## Drop a column in multiindex columns

```python
df = df.drop(axis=1, level=0 , columns='ciao')
```

## select data based on conditions with multiindex

use `df.index.get_level_values('name_of_level'/ number)` to create a bool mask. This is importat because the dim of the level mask and cond mask must match

```python
idxs = pd.MultiIndex.from_tuples([('A',1), ('A',2),('B',1),('A',2),('C',1),], names=['letters', 'numbers'])

df = pd.DataFrame({
    'X': [1, 6, 8, 3, 7],
    'Y': [5, 2, 9, 4, 1],
    'Z': [3, 8, 6, 2, 9]
}, index=idxs)

  

mask_idx = df.index.get_level_values('letters') == 'A'

mask_value = df['X'] > 5

df[mask_idx & mask_value]
```


## find which years have all non-zero values and which years have any non-zero values from world alcohol consumption dataset.


```python
years_all_na = df.groupby('Year').apply(lambda x: x.isna().all().any())

years_all_na

  
  

years_any_na = df.groupby('Year').apply(lambda x: x.isna().any().any())

years_any_na
```
