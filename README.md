# Sales-Insights-Project

BUSINESS REQUEST

Business Demand Overview:

Value of Change: Visual report consists of dashboards presenting sales trends & sales amount and quantity based on location/ markets, customers and products for the years 2017, 2018, 2019, and 2020 (hindsight, descriptive analysis to answer what happened?)
Necessary Systems/ tools/ Platform: Sales Database, MySQL, Power BI Query, Power BI Desktop.
User Stories:

A user stories table was defined to specify what the delivery enable the user to do (from the user point of view)

No #	As a (role)	I want (request / demand)	So that I (user value)	Acceptance Criteria
1	Sales Manager	A visual dashboard overviews sales over the four years	Observe & compare sales over the years, which year/ month had a pick and which had a decline	A dashboard shows a time-line graph drawing sales trend over time 
2	Sales Manager	A visual dashboard overviews sales based on the location/ market	See the sales amount & quantity in each location/ market and filter sales based on the location and see which location has the highest profit and the largest sales quantity	A dashboard includes a map to visualize the sales amount and quantity in each location. Also, include slicers to filter sales based on location/market
4	Sales Manager	A visual dashboard overviews sales amount & quantity by customers	See customers and sales, which customers paid (sales amount) the most and which buy the most (sales quantity)	A bar chart to show sales by customers & slicers to filter for each customer type
5	Sales Manager	A visual dashboard overviews sales by products and product types	See which products achieved the highest profit and the largest sales quantity. Filter sales based on the product types	A bar chart to show sales by products & slicers to filter for each customer product type.
6	Sales Manager	A dashboard/ charts showing the top N for both sales amount & sales quantity by products, customers, and markets	See which customers paid the most and buy the largest. which market had the highest sales amount and had the largest quantity. Which products sold the most and which products achieved the highest.    	A dashboard presents the top 5 in bar charts
DATA EXPLORING

Before the data cleaning and data analysis, I needed to understand and explore the database (see what data the database presents, in which format, how it is presenting the data, what are the fact tables and what are the dimension tables) decide which data is useful for the data analysis goal to extract.

Below is showing the SQL queries run to explore the database:

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
DATA EXTRACTING

Extract the data that I decided to use in csv format. Below is showing the SQL scripts to extract the data from the sales database

select * from sales.customers;
select * from sales.markets
where sales.markets.markets_name not in ('New York', 'Paris');
select * from sales.date;
select * from sales.transactions 
inner join sales.markets 
on sales.transactions.market_code = sales.markets.markets_code
where sales.markets.markets_name != 'New York' or sales.markets.markets_name != 'Paris';
# The currency needs to fixed (transfered to doller), it will be done in Power Query in data cleaning stage

DATA LOADING, CLEANING & TRANSFORMING

After extracting the data to csv files, I load these csv files to Power BI Query to clean and transform the data. Below you can the steps that have been done in Power BI Query to clean and transform the data in M language.

= Table.SelectRows(#"Changed Type", each ([sales_amount] <> -1 and [sales_amount] <> 0)) 

= Table.RemoveColumns(#"Filtered Rows1",{"markets_code"}) 

= Table.AddColumn(#"Removed Columns", "norm_amount", each if [currency] = "INR" or [currency] = "INR#(cr)" then [sales_amount]*0.014 else [sales_amount])

= Table.ReorderColumns(#"Changed Type1",{"product_code", "customer_code", "order_date", "market_code", "markets_name", "zone", "sales_qty", "sales_amount", "currency", "norm_amount"})

= Table.TransformColumnTypes(#"Promoted Headers",{{"product_code", type text}, {"product_type", type text}})

= Table.TransformColumnTypes(#"Promoted Headers",{{"customer_code", type text}, {"custmer_name", type text}, {"customer_type", type text}})

= Table.TransformColumnTypes(#"Promoted Headers",{{"markets_code", type text}, {"markets_name", type text}, {"zone", type text}})
DATA MODELING

After ETL process, I connected the tables in Power BI Desktop, in Model View. A screen shot below shows the connect tables in Model View.

