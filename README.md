# Sales Insights Project

BUSINESS REQUEST

Business Demand Overview:

Value of Change: Visual report consists of dashboards presenting sales trends & sales amount and quantity based on location/ markets, customers and products for the years 2017, 2018, 2019, and 2020 (hindsight, descriptive analysis to answer what happened?)
Necessary Systems/ tools/ Platform: Sales Database, MySQL, Power BI Query, Power BI Desktop.
User Stories:

A user stories table was defined to specify what the delivery enable the user to do (from the user point of view)

![User Request Table](https://user-images.githubusercontent.com/71211875/126686882-ba8e88a4-e0a7-4c54-b2d6-4e250b57f476.GIF)

DATA EXPLORING

Before the data cleaning and data analysis, I needed to understand and explore the database (see what data the database presents, in which format, how it is presenting the data, what are the fact tables and what are the dimension tables) decide which data is useful for the data analysis goal to extract.

Below is showing the SQL queries run to explore the database:

```
select * from sales.customers limit 100;
desc sales.customers;
select count(distinct customer_code), count(distinct custmer_name), count(distinct customer_type) from sales.customers;
select distinct customer_type from sales.customers;
# There are 38 different customers divided into two types: Brick & Mortar, E-commerce

select * from sales.date limit 100;
desc sales.date;
select count(distinct date), count(distinct cy_date), count(distinct year), count(distinct month_name), count(distinct date_yy_mmm)
from sales.date;
select distinct year from sales.date;
# This database shows the sales for 4 years: 2017, 2018, 2019, 2020

select * from sales.markets limit 100;
desc sales.markets;
select count(distinct markets_code), count(distinct markets_name), count(distinct zone) from sales.markets;
select distinct markets_code from sales.markets;
select distinct markets_name from sales.markets;
select distinct zone from sales.markets;
# There are 17 different markets/location (called by the city name) 15 cities/ markets in india and their codes are a sequence of 001 to 015,
# and New York (Market Code 097) and Paris (Market Code 999). 
# There are 4 zones: North, South, Center (applies for the indian markets) and Blank for New York and Paris

select * from sales.products limit 100;
desc sales.products;
select count(distinct product_code), count(distinct product_type) from sales.products;
select distinct product_type from sales.products;
#There are 279 different products falls into two type: Own Brand, Distribution

select * from sales.transactions limit 100;
desc sales.transactions;
select distinct currency from sales.transactions;
# Transaction tables shows sales amount in two different currencies. This should be fixed and shows sales amount only in one currency.
# All sales amount will be transfered to USD currency.

# Let's explore the data of Paris and New York
select * from sales.transactions 
inner join sales.markets on sales.transactions.market_code = sales.markets.markets_code 
where sales.markets.markets_code = 'New York' or sales.markets.markets_code  = 'Paris';
# It shows no data for New York or Paris market, only the indian markets are shown.
# So the sales insight will be done only for indian markets. 
```

DATA EXTRACTING

Extract the data that I decided to use in csv format. Below is showing the SQL scripts to extract the data from the sales database

```
select * from sales.customers;
select * from sales.markets
where sales.markets.markets_name not in ('New York', 'Paris');
select * from sales.date;
select * from sales.transactions 
inner join sales.markets 
on sales.transactions.market_code = sales.markets.markets_code
where sales.markets.markets_name != 'New York' or sales.markets.markets_name != 'Paris';
# The currency needs to fixed (transfered to doller), it will be done in Power Query in data cleaning stage
```

DATA LOADING, CLEANING & TRANSFORMING

After extracting the data to csv files, I load these csv files to Power BI Query to clean and transform the data. Below you can the steps that have been done in Power BI Query to clean and transform the data in M language.

```
= Table.SelectRows(#"Changed Type", each ([sales_amount] <> -1 and [sales_amount] <> 0)) 

= Table.RemoveColumns(#"Filtered Rows1",{"markets_code"}) 

= Table.AddColumn(#"Removed Columns", "norm_amount", each if [currency] = "INR" or [currency] = "INR#(cr)" then [sales_amount]*0.014 else [sales_amount])

= Table.ReorderColumns(#"Changed Type1",{"product_code", "customer_code", "order_date", "market_code", "markets_name", "zone", "sales_qty", "sales_amount", "currency", "norm_amount"})

= Table.TransformColumnTypes(#"Promoted Headers",{{"product_code", type text}, {"product_type", type text}})

= Table.TransformColumnTypes(#"Promoted Headers",{{"customer_code", type text}, {"custmer_name", type text}, {"customer_type", type text}})

= Table.TransformColumnTypes(#"Promoted Headers",{{"markets_code", type text}, {"markets_name", type text}, {"zone", type text}})
```


DATA MODELING

After ETL process, I connected the tables in Power BI Desktop, in Model View. A screen shot below shows the connect tables in Model View.

![DATA MODELLING](https://user-images.githubusercontent.com/71211875/126687531-9d0425f9-311b-47d0-bd6d-1d243a9246be.GIF)

Sales Insight Dashboard

The final dashboard with 3 pages (Sales Amount Overview, Sales Quantity Overview, Top 5 Overview) presents sales amount and quantity by markets, customers, and products and sales trends over the four past years (2017, 2018, 2019, 2020) with slicers to filter by year, month, market/ location, customer types, and product types.

![FEATURE IMAGE](https://user-images.githubusercontent.com/71211875/126687585-a700091e-d0be-4ed5-aa44-e86cf38e67df.GIF)

Special thanks to Ali Ahmad and Dhaval Patel

For the final report: https://app.powerbi.com/reportEmbed?reportId=77844c20-4aa5-451c-a417-5e063c438557&autoAuth=true&ctid=766ae0a8-2dd1-4de3-9241-97090c9c8d3d&config=eyJjbHVzdGVyVXJsIjoiaHR0cHM6Ly93YWJpLXVhZS1ub3J0aC1hLXByaW1hcnktcmVkaXJlY3QuYW5hbHlzaXMud2luZG93cy5uZXQvIn0%3D 

For the project description: https://reemalraeai.wordpress.com/portfolio/sales-insights-project/

