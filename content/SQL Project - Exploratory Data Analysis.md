---
tags:
  - datascience
date: " 2025-02-18"
up: "[[SQL]]"
status: "#project"
---
# SQL Project - Exploratory Data Analysis


- total lay off by company

```python
with example as (
select company, sum(total_laid_off) as total_lay_off, sum(funds_raised_millions) as tot
from layoff_staging2
group by company)
select distinct(lft.company), lft.tot, lft.total_lay_off, rgt.industry
from example as lft
left join layoff_staging2 as rgt
on lft.company = rgt.company
order by total_lay_off desc;
```

- date range

```python
select min(`date`), max(`date`)
from layoff_staging2;
```

- Industry hit the most

```python
select 
	industry, 
    sum(total_laid_off) as total_lay_offs, 
    sum(funds_raised_millions) as tot, 
	ROUND((SUM(total_laid_off) * 100.0) / (SELECT SUM(total_laid_off) FROM layoff_staging2), 2) as percentage
from layoff_staging2
group by industry
order by percentage Desc;

```

- By year and country

```python
select 
	Year(`date`),
    country,
    sum(total_laid_off) as total_lay_offs, 
	ROUND((SUM(total_laid_off) * 100.0) / (SELECT SUM(total_laid_off) FROM layoff_staging2), 2) as percentage
from layoff_staging2
group by Year(`date`), country
order by percentage Desc;

```


- rolling sum 

```python

WITH stage_totals AS (
    SELECT 
        stage,
        SUM(total_laid_off) as total_layoffs
    FROM layoff_staging2
    GROUP BY stage
)
SELECT 
    stage,
    total_layoffs,
    ROW_NUMBER() OVER(ORDER BY total_layoffs Desc) as row_num
FROM stage_totals;
```

- rolling sum by month

```python
with example as (
select Month(`date`) as mnt, sum(total_laid_off) as tot
from layoff_staging2
where Month(`date`)  is not Null 
group by Month(`date`))
select mnt, tot, sum(tot) over(order by tot ) as row_n
from example;


```


- company laid offs by year rank

```python


with example as ( 
select company , Year(`date`) as y, sum(total_laid_off) as tot
from layoff_staging2
group by company, y
order by 3 desc)
select company, y, tot, dense_rank() over(partition by y order by tot desc)
from example
where y is not null;
```

- top 5 layd off companies for each year

```python
with example as ( 
select company , Year(`date`) as y, sum(total_laid_off) as tot
from layoff_staging2
group by company, y
order by 3 desc), company_rank as(
select company, y, tot, dense_rank() over(partition by y order by tot desc) as rr
from example
where y is not null)
select * 
from company_rank
where rr <=5;


```

# Related:

- [[SQL Project - Data Cleaning]]

---
# Reference

1. https://www.youtube.com/watch?v=QYd-RtK58VQ
