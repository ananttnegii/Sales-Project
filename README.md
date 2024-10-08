# Sales Project

### Contents
- [Description](#Description)
- [Data Source](#Data-Source)
- [Tools](#Tools)
- [Data Uploading in PostgreSQL](#Data-Uploading-in-PostgreSQL)
- [Data Cleaning](#Data-Cleaning)
- [Data Analysis](#Data-Analysis)
- [Recommendations / Findings](#Recommendations--Findings)

### Description

This project aims to do a basic analysis on sales of an ecommerce company.I have analysed its yearly revenue, top selling categories and products, banks and preferred payment methods. 

### Data Source
-  Kaggle [Link](https://www.kaggle.com/datasets/mmohaiminulislam/ecommerce-data-analysis)

### Tools
- SQL (PostgreSQL) - Updating, cleaning and analyzing data
- Google Sheets - Presenting report [Sheet Link](https://docs.google.com/spreadsheets/d/1RD8amgf98O40_lbTmLqcBZX6MZahbqNT_aFWPCENHL0/edit?gid=1779673857#gid=1779673857)

### Data Uploading in PostgreSQL

- Below are the queries used for creating 6 tables that we have used for analysis

```sql
-- creating and inserting data in tables

create table fact_table (
	payment_key varchar(10),
	customer_key varchar(10),
	time_key varchar(10),
	item_key varchar(10),
	store_key varchar(10),
	quantity varchar(10),
	unit varchar(10),
	unit_price numeric,
	total_price numeric);
 
select * from fact_table;

create table customer_dim (
	customer_key varchar(10),
	name varchar(50),
	contact_no varchar(20),
	nid varchar(20));
	
select * from customer_dim;

create table item_dim (
	item_key varchar(20),
	item_name varchar(200),
	descent varchar(200),
	unit_price varchar(200),
	man_country varchar(200),
	supplier varchar(200),
	unit varchar(200));
	
select * from item_dim;

create table store_dim (
	store_key varchar(10),
	division varchar(200),
	district varchar(200),
	upazila varchar(200));
	
select * from store_dim;


create table time_dim (
	time_key varchar(10),
	date timestamp,
	hour int,
	day int,
	week varchar(10),
	month int,
	quarter char(2),
	year int);
	
select * from time_dim;

create table transaction_dim (
	payment_key char(4),
	trans_type varchar(50),
	bank_name varchar(200));
	
select * from transaction_dim;
```

### Data Cleaning 

- Checking for null values

```sql
-- 1. check for null values in fact_table

select * from fact_table
where 
	payment_key is null
	or customer_key is null
	or time_key is null
	or quantity is null
	or unit_price is null

-- 2. check for null values in customer_dim

select * from customer_dim where customer_key is null;

-- 3. check for null values in item_key

select * from item_dim where item_key is null;

-- 4. check for null values in store_key

select * from store_dim where store_key is null;

-- 5. check for null values in time_key

select * from time_dim where time_key is null;

-- 6. check for null values in payment_key

select * from transaction_dim where payment_key is null;
```

- Checking for duplicate values, I have counted number of rows and number of values of primary key in that table to determine if there are any duplicate values.

```sql
-- 1. in fact_table

select count(*) from fact_table;

select count(*)
from
	(select distinct customer_key, time_key, store_key from fact_table) as x
 
-- 2. in customer_dim
select count(*), count(distinct customer_key) from customer_dim

-- 3. in item_dim
select count(*), count(distinct item_key) from item_dim

-- 4. in store_dim
select count(*), count(distinct store_key) from store_dim;

-- 5. in time_dim
					   
select count(*), count(distinct time_key) from time_dim;

-- 6. in transaction_dim

select count(*), count(distinct payment_key) from transaction_dim; 
```

### Data Analysis

- Below are the queries used for analysing data. 

```sql
-- Sales trend analysis over time (monthly, yearly) 

/* revenue, units, avg at year-month-day level */

-- sales 
select year, month, 
	sum(total_price) as revenue,
	sum(quantity::int) as units_sold,
	avg(total_price) as avg_revenue
from fact_table as ft join time_dim as td
	on ft.time_key = td.time_key
group by year, month

-- avg
select year, avg(total_price) as avg_revenue
from fact_table as ft join time_dim as td
	on ft.time_key = td.time_key
group by year

/* products and categories */

-- Top 5 categories and items by revenue, comparison with others

-- categories
with amt as ( 
	select descent,
		sum(total_price) as amount
	from fact_table as ft join item_dim as id
		on ft.item_key = id.item_key
	group by descent ),
dense as ( 
	select *, dense_rank() over(order by amount desc) as dr
	from amt )
select 
	case when dr <= 5 then descent else 'Others' end as descent,
	sum(amount)
from dense
group by (case when dr <= 5 then descent else 'Others' end)

-- items
with amt as ( 
	select item_name,
		sum(total_price) as amount
	from fact_table as ft join item_dim as id
		on ft.item_key = id.item_key
	group by item_name ),
dense as ( 
	select *, dense_rank() over(order by amount desc) as dr
	from amt )
select 
	case when dr <= 5 then item_name else 'Others' end as descent,
	sum(amount)
from dense
group by (case when dr <= 5 then item_name else 'Others' end)
```

### Recommendations / Findings

There is no growth, revenue, units sold and average has been a constant for the years 2014 to 2020.

![sales](https://github.com/user-attachments/assets/6223a6ea-9cb4-4d1e-b972-b5b93b9465d6)


Our top category is Beverage - Energy/Protein

![top_categories](https://github.com/user-attachments/assets/5e336ea6-7894-43d3-912c-bd1f0c9264e4)


Red Bull 12oz is our top poduct

![top_products](https://github.com/user-attachments/assets/c001d237-7ef1-4931-a2be-698dca82387e)


Our customers prefer to pay by card

![preferred payment](https://github.com/user-attachments/assets/b63a3cb7-719c-40b0-8c5f-a2c0a84d96c5)


There is very little diffrence in our top 5 banks, almost gives us a revenue of 0.5 crores

![banks](https://github.com/user-attachments/assets/18f54bff-de39-4b87-87ad-a712fdb36e7b)



