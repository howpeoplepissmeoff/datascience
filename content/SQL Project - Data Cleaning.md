---
tags:
  - datascience
date: " 2025-02-18"
up: "[[SQL]]"
status: "#project"
---
# SQL Project - Data Cleaning



```python
-- create a intermidiate table
Create table layoff_staging
Like layoffs;

-- add all the records in layoffs
insert layoff_staging
select * 
from layoffs;

```


## 1. Remove duplicates


Check for duplicates:

1. create a [[SQL Theory#CTEs]]
2. use a [[SQL Theory#Window]]
```python
with example as (
select *, 
row_number() over(partition by company, location, industry, total_laid_off, percentage_laid_off,  `date`,  stage, country, funds_raised_millions ) as prova
from layoff_staging)
select * 
from example
where prova>=2;

```
![[Pasted image 20250218120727.png]]

Delete just on of these rows

```python
with example as (
select *, 
row_number() over(partition by company, location, industry, total_laid_off, percentage_laid_off,  `date`,  stage, country, funds_raised_millions ) as prova
from layoff_staging)
Delete  
from example
where prova>=2;
```

>[!error]
>Error Code: 1288. The target table example of the DELETE is not updatable

This is because it is a CTEs. 

So, we should put the inner table in a staging table and then update it.

```python
CREATE TABLE `layoff_staging2` (
  `company` text,
  `location` text,
  `industry` text,
  `total_laid_off` int DEFAULT NULL,
  `percentage_laid_off` text,
  `date` text,
  `stage` text,
  `country` text,
  `funds_raised_millions` int DEFAULT NULL,
  `prova` int 
  
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;

Insert into layoff_staging2
select *, 
row_number() over(partition by company, location, industry, total_laid_off, percentage_laid_off,  `date`,  stage, country, funds_raised_millions ) as prova
from layoff_staging;
```

And then we can delete

```python
Delete 
from layoff_staging2
where prova >=2;
```

Now we can delete the `prova` column as we don't need it anymore

### Delete column

```python
Alter table layoff_staging2
drop column prova;
```


- [ ] easier way to delete dupliacates





## 2. Standardize the data


- `trim`
```python
Update layoff_staging2
set company =  trim(company), location =  trim(location), industry =  trim(industry), stage =  trim(stage)  ;

```


- `group similar values toghether`

![[Pasted image 20250218122449.png]]

```python
Update layoff_staging2
Set industry = 'Crypto';
```

```python
update layoff_staging2
set country = 'United States'
where country like 'United States%';
```


- `time series`: change time from text to date col

```python
Update layoff_staging2
set  `date` = STR_TO_DATE(`date`, '%m/%d/%Y')
;

```
![[Pasted image 20250218123902.png]]

Still the type is a text. 
But now we can change it to a date column.

```python
Alter table layoff_staging2
modify column `date` DATE;
```

![[Pasted image 20250218124022.png]]


- [ ] update rows value
- [ ] change data type
- [ ] `STR-TO-DATE`

## 3. Null or Blank values

1. check for `null` values 

```python
select * 
from layoff_staging2
where total_laid_off is Null
and percentage_laid_off is Null; 
```

![[Pasted image 20250218124234.png]]

Or where it is also a `blank` record

```python
select *
from layoff_staging2
where industry is Null or industry = ''; 
```
![[Pasted image 20250218124719.png]]

2. convert `blank` to `Null`
```python
update layoff_staging2
set industry = Null 
where industry = '';

```


3. Search if one of this values have been populated
```python
WITH example AS (
    SELECT company
    FROM layoff_staging2
    WHERE industry IS NULL 
)
SELECT *
FROM layoff_staging2
WHERE company IN (SELECT company FROM example);
```

![[Pasted image 20250218125054.png]]

4. join same values on itselfe and update

```python
Update layoff_staging2 as lft 
join layoff_staging2 as rgt
on lft.company = rgt.company and lft.location = rgt.location
set lft.industry =  rgt.industry 
where lft.industry is Null 
and rgt.industry is Not null;
```

5. check for correctness
```python
select * 
from layoff_staging2 as lft
join layoff_staging2 as rgt
on lft.company = rgt.company and lft.location = rgt.location
where (lft.industry is Null or  lft.industry = '') and rgt.industry is Not null;
```
![[Pasted image 20250218130325.png]]
```python

```





- [ ] check for `null` values

## 4. Remove any cols and rows unnecessary

- Get rid of rows where `total_lay_off` and `perc` is null 

```python
Delete  
from layoff_staging2
where total_laid_off is Null and percentage_laid_off is Null;

```


# Related:

- [[SQL Project - Exploratory Data Analysis]]

---
# Reference

6. https://www.youtube.com/watch?v=4UltKCnnnTA
