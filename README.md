# Sales Project

### Description

This project aims to .....

### Data Source
-  Kaggle [Link](https://www.kaggle.com/datasets/mmohaiminulislam/ecommerce-data-analysis)

### Tools
- SQL (PostgreSQL) - Updating, cleaning and analyzing data
- Google Sheets - Presenting report [Sheet Link](https://docs.google.com/spreadsheets/d/1RD8amgf98O40_lbTmLqcBZX6MZahbqNT_aFWPCENHL0/edit?gid=0#gid=0)

### Data Uploading in PostgreSQL

```sql
-- creating and inserting data in tables

create table fact_table (
	payment_key varchar(10),
	coustomer_key varchar(10),
	time_key varchar(10),
	item_key varchar(10),
	store_key varchar(10),
	quantity varchar(10),
	unit varchar(10),
	unit_price numeric,
	total_price numeric);
 
select * from fact_table;

create table customer_dim (
	coustomer_key varchar(10),
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

- Checking for duplicate values

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

### Recommendations / Findings
There is no Growth

![sales](https://github.com/user-attachments/assets/6223a6ea-9cb4-4d1e-b972-b5b93b9465d6)



