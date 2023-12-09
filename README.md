# PIZZAA ANALYSIS

## 1. Ask

## Objectives

The objective is to perform an analysis of our pizza sales data to gain insights into our business performance. We will focus on the following key points:

- Total Revenue Generated
- Average Order Value
- Total Pizzas Sold
- Total Orders
- Average Pizzas Per Order

## Key Questions

We will be questioning our data in four different ways:

- Sales Performance Analysis
  - What is the revenue of pizza across different categories?
  - What is the revenue of pizza across different sizes?

- Seasonal Analysis
  - Which days of the week have the highest number of orders?
  - At what time do most orders occur?
  - Which month has the highest revenue?
 
- Customer behavior analysis
    - Which pizza is the favorite of customers?
    - top 5 pizzas by revenue
    - Which pizzas have had the most orders over the past year of business at the Pizza Restraunt?
 
- Pizza Analysis
    - The pizza with the least price and the highest price
    - Number of pizzas per category
    - Number of pizza per size
    - Which ingredients does the restaurant need to keep handy to make the ordered pizzas?



## 2. Prepare

### Data Sources
- **Origin:** Publicly available datasets related to pizza sales
- **Collection Method:** The datasets were acquired for analysis purposes.
- **Data Quality Considerations:** Careful validation and cleaning were performed to address any issues present in the original datasets.


### Data Cleaning
1. **Handling Missing Values:** Imputed or removed entries with missing values.
2. **Outlier Detection:** Identified and addressed outliers in the dataset.
3. **Data Standardization:** Ensured consistency by removing duplicates, processing categories, and addressing inconsistencies.
      

## 3. Process 

### SQL Queries
The processing phase involved specific transformations to enhance the data for analysis:

1. **Date and Time Format Change:**
   - Modified the date and time formats to ensure consistency.

### Additional Notes:
- No duplicates were found in the dataset during this processing phase.
- Provide specific details or examples related to the date and time format changes.
### Transformations
- Document specific transformations applied to the data, such as aggregations, joins, or calculations.

## 4. Analyze

## SQL Script used in analysis

```mysql
-- 
create database pizza_sales;
use pizza_sales;

create table orders(
	order_id int primary key,
    date text,
    time text
);

show variables like 'local_infile';

set global local_infile=1 ;

load data local infile "C:\\Users\\Dell\\Documents\\rohan\\Pizza Project\\orders.csv"
into table orders
fields terminated by ','
ignore 1 rows;

create table order_details( 
	order_details int primary key,
    order_id int,
    pizza_id text,
    quantity int
);
load data local infile "C:\\Users\\Dell\\Documents\\rohan\\Pizza Project\\order_details.csv"
into table order_details
fields terminated by ','
ignore 1 rows;


select * from orders;
select * from order_details;
select * from pizzas;
select * from pizza_types;

create view pizza_details as 
select p.pizza_id, p.pizza_type_id, pt.name, pt.category, p.size, p.price, pt.ingredients
from pizzas p
join pizza_types pt
on pt.pizza_type_id = p.pizza_type_id;

select * from pizza_details;

#---------Processing---------------
alter table orders modify date DATE;
alter table orders modify time TIME;


#---------Analysis-----------------

# total revenue #

SELECT round(sum(od.quantity * p.price),2) AS total_revenue
FROM order_details od
JOIN pizza_details p ON od.pizza_id = p.pizza_id;

#total pizza sold
select sum(od.quantity) as pizza_sold
from order_details od;

# total orders
select count(distinct(order_id)) as total_orders
from order_details;

# average order value
select round(sum(od.quantity * p.price) / count(distinct(od.order_id) ),2) as average_order_value
from order_details od
join pizza_details p
on od.pizza_id = p.pizza_id;


#average number of pizza per order
select round(sum(od.quantity) / count(distinct(od.order_id)),0) as average_pizza_perorder
from order_details od;

# total revenue and no of orders per category
select p.category, SUM(od.quantity * p.price) as total_revenue, count(distinct(od.order_id)) as total_orders
from order_details od
join pizza_details p
on od.pizza_id = p.pizza_id
group by p.category;

# total revenue and number of orders per size
select p.size, SUM(od.quantity * p.price) as total_revenue, count(distinct(od.order_id)) as total_orders
from order_details od
join pizza_details p
on od.pizza_id = p.pizza_id
group by p.size;


# hourly, daily and monthly trend in orders and revenue of pizza
select 
	case
		when hour(o.time) between 9 and 12 then 'late morning'
        when hour(o.time) between 12 and 15 then 'lunch time'
        when hour(o.time) between 15 and 18 then 'noon'
        when hour(o.time) between 18 and 21 then 'dinner time'
        when hour(o.time) between 21 and 23 then 'late night'
        else 'others'
        end as meal_time, count(distinct(od.order_id)) as total_orders
from order_details od
join orders o on o.order_id = od.order_id
group by meal_time
order by total_orders desc;

#


# weekdays trend
select dayname(o.date) as day_name, count(distinct(od.order_id)) as total_orders
from order_details od
join orders o
on o.order_id = od.order_id
group by dayname(o.date)
order by total_orders desc;

# month wise trend 
select monthname(o.date) as day_name, count(distinct(od.order_id)) as total_orders
from order_details od
join orders o
on o.order_id = od.order_id
group by monthname(o.date)
order by total_orders desc;


# customer's favorite pizza (most ordered)
select p.name, p.size, count(od.order_id) as count_pizza
from order_details od
join pizza_details p
on od.pizza_id = p.pizza_id
group by p.name, p.size
order by count_pizza desc limit 1;

# top 5 pizza by revenue
select p.name, sum(od.quantity * p.price) as total_revenue
from order_details od
join pizza_details p
on od.pizza_id = p.pizza_id
group by p.name
order by total_revenue desc
limit 5;


# top pizzas by sale
select p.name, sum(od.quantity) as pizzas_sold
from order_details od
join pizza_details p
on od.pizza_id = p.pizza_id
group by p.name
order by pizzas_sold desc
limit 5;

```

### PowerBI Visualizations
- Include screenshots or embed PowerBI visualizations in your documentation. Label each visualization appropriately.

### Insights
- Summarize key insights gained from the analysis, highlighting patterns or trends observed.

### Statistical Methods
- If applicable, mention any statistical methods or machine learning techniques used, along with a brief explanation.

## 5. Share

### PowerBI Report
- Share the link to the PowerBI report or upload the report file for easy access.

### Narrative Summary
- Write a narrative that summarizes the main findings, connecting insights to the initial objectives.

## 6. Act

### Recommendations
- List actionable recommendations based on your analysis. For example: "Introduce promotional offers for top-selling pizzas during peak hours."

### Implementation Plan
- Provide a brief plan for implementing the recommendations, outlining steps and timelines.

### Follow-Up
- Outline a follow-up plan to assess the impact of implemented recommendations. Specify KPIs to monitor and evaluate success.

## Additional Tips

### Documentation Standards
- Maintain a consistent format for documentation, using headings, bullet points, and formatting for clarity.

### References
- Include references to external sources, methodologies used, or any relevant documentation.

### Versioning
- If applicable, mention the version of the dataset or analysis. Consider version control for code and documents.

