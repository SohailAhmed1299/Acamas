# SQL Project on Faaso's

# Creation of tables.

CREATE TABLE driver(driver_id integer,reg_date date); 

INSERT INTO driver(driver_id,reg_date) 
 VALUES (1,'01-01-2021'),
(2,'01-03-2021'),
(3,'01-08-2021'),
(4,'01-15-2021');


drop table if exists ingredients;
CREATE TABLE ingredients(ingredients_id integer,ingredients_name varchar(60)); 

INSERT INTO ingredients(ingredients_id ,ingredients_name) 
 VALUES (1,'BBQ Chicken'),
(2,'Chilli Sauce'),
(3,'Chicken'),
(4,'Cheese'),
(5,'Kebab'),
(6,'Mushrooms'),
(7,'Onions'),
(8,'Egg'),
(9,'Peppers'),
(10,'schezwan sauce'),
(11,'Tomatoes'),
(12,'Tomato Sauce');

drop table if exists rolls;
CREATE TABLE rolls(roll_id integer,roll_name varchar(30)); 

INSERT INTO rolls(roll_id ,roll_name) 
 VALUES (1	,'Non Veg Roll'),
(2	,'Veg Roll');

drop table if exists rolls_recipes;
CREATE TABLE rolls_recipes(roll_id integer,ingredients varchar(24)); 

INSERT INTO rolls_recipes(roll_id ,ingredients) 
 VALUES (1,'1,2,3,4,5,6,8,10'),
(2,'4,6,7,9,11,12');

drop table if exists driver_order;
Create TABLE driver_order(order_id integer,driver_id integer,pickup_time datetime,distance VARCHAR(7),duration VARCHAR(10),cancellation VARCHAR(23));
INSERT INTO driver_order(order_id,driver_id,pickup_time,distance,duration,cancellation) 
 VALUES(1,1,'01-01-2021 18:15:34','20km','32 minutes',''),
(2,1,'01-01-2021 19:10:54','20km','27 minutes',''),
(3,1,'01-03-2021 00:12:37','13.4km','20 mins','NaN'),
(4,2,'01-04-2021 13:53:03','23.4','40','NaN'),
(5,3,'01-08-2021 21:10:57','10','15','NaN'),
(6,3,null,null,null,'Cancellation'),
(7,2,'01-08-2021 21:30:45','25km','25mins',null),
(8,2,'01-10-2021 00:15:02','23.4 km','15 minute',null),
(9,2,null,null,null,'Customer Cancellation'),
(10,1,'01-11-2021 18:50:20','10km','10minutes',null);

# Updating the table which to fix discrepancies in the data
update driver_order
set pickup_time = '01-11-2021 18:50:20'
where order_id = 10;


drop table if exists customer_orders;
CREATE TABLE customer_orders(order_id integer,customer_id integer,roll_id integer,not_include_items VARCHAR(4),extra_items_included VARCHAR(4),order_date datetime);
INSERT INTO customer_orders(order_id,customer_id,roll_id,not_include_items,extra_items_included,order_date)
values (1,101,1,'','','01-01-2021  18:05:02'),
(2,101,1,'','','01-01-2021 19:00:52'),
(3,102,1,'','','01-02-2021 23:51:23'),
(3,102,2,'','NaN','01-02-2021 23:51:23'),
(4,103,1,'4','','01-04-2021 13:23:46'),
(4,103,1,'4','','01-04-2021 13:23:46'),
(4,103,2,'4','','01-04-2021 13:23:46'),
(5,104,1,null,'1','01-08-2021 21:00:29'),
(6,101,2,null,null,'01-08-2021 21:03:13'),
(7,105,2,null,'1','01-08-2021 21:20:29'),
(8,102,1,null,null,'01-09-2021 23:54:33'),
(9,103,1,'4','1,5','01-10-2021 11:22:59'),
(10,104,1,null,null,'01-11-2021 18:34:49'),
(10,104,1,'2,6','1,4','01-11-2021 18:34:49');


select * from customer

![customer_orders](https://user-images.githubusercontent.com/90980952/226123365-531c330d-9da5-4ae3-9fdb-f14b19fdde8c.JPG)
_orders;

Select * from driver;

![Driver](https://user-images.githubusercontent.com/90980952/226123377-586e75a1-6d15-45b3-a47a-08551269bc88.JPG)

Select * from driver_order;

![Driver Order](https://user-images.githubusercontent.com/90980952/226123398-21887b99-6926-41c9-a432-919222093a42.JPG)

Select * from ingredients;

![Ingridients](https://user-images.githubusercontent.com/90980952/226123409-911a7dc1-2016-4ad8-bcd4-0e2dfb0ac4db.JPG)

select * from rolls;

![Rolls](https://user-images.githubusercontent.com/90980952/226123419-e3ea357b-1e81-4f0d-99c2-b637d4680229.JPG)

select * from rolls_recipes;

![rolls_recipes](https://user-images.githubusercontent.com/90980952/226123468-9e3503ae-57b1-434a-a33a-bed5265e5c11.JPG)

--1 How many rolls were order ?

select count(roll_id) from customer_orders;

--2 How many unique customer order were made?

Select COUNT(distinct customer_id) from customer_orders;


--3  How many succesful order were deliverd?

Select driver_id, COUNT(distinct order_id) from driver_order where cancellation not in ('cancellation' , 'customer cancellation') group by driver_id


--4 How many each type of roll were deliverd?


select roll_id, COUNT(roll_id) from customer_orders where order_id in (

select order_id from  
(select *, case when cancellation in ('cancellation' , 'customer cancellation') then 'c' else 'nc' end as order_cancel_details from driver_order) a 
where order_cancel_details = 'nc')
group by roll_id;

--Alternate solution 

Select a.roll_id , count(*) from
customer_orders a join driver_order b
on a.order_id=b.order_id
where b.cancellation not like '%Cancellation%' or b.cancellation is Null
group by a.roll_id;

--5 How many non veg and veg rolls were ordered by each customer?


select	a.*, b.roll_name from
(
Select customer_id,roll_id,COUNT(roll_id) cnt
from customer_orders
group by customer_id,roll_id)a inner join rolls b on a.roll_id = b.roll_id;


--6. What is the maximum number of rolls delivered in a single order?

select * from
(
select *, DENSE_RANK() over (order by cnt desc) rnk from(
select order_id, COUNT(roll_id) cnt
from(
select * from customer_orders where order_id in (

select order_id from  
(select *, case when cancellation in ('cancellation' , 'customer cancellation') then 'c' else 'nc' end as order_cancel_details from driver_order) a 
where order_cancel_details = 'nc'))b
group by order_id)c)d where rnk = 1;

--7 For each customer, how many delivered rolls had at least 1 chnages and how many had no changes?

with temp_customer_orders (order_id,customer_id,roll_id,not_include_items,extra_items_include,order_date) as
(
select order_id,customer_id,roll_id,
case when not_include_items is null or not_include_items = '' then '0' else not_include_items end as new_include_items,
case when extra_items_included is null or extra_items_included = '' or extra_items_included = 'NaN' then '0' else extra_items_included end as new_extra_items_included,
order_date from customer_orders
)
,

temp_drivers_order (order_id,driver_id,pickup_time,distance,duration,new_cancellation) as
(
select order_id,driver_id,distance,pickup_time,duration,
case when cancellation in('cancellation','customer cancellation') then  0  else 1 end as new_cancellation
from driver_order)

select customer_id,change_or_no_change,count(order_id) at_least_1_change from
(select * ,case when not_include_items = '0' And extra_items_include = '0' then 'no change ' else 'changes' end  change_or_no_change 
from temp_customer_orders where order_id in (
select order_id from temp_drivers_order where new_cancellation != 0))a
group by customer_id,change_or_no_change ;


--8 How many rolls were deliverd that had both exclusion and inclusinon?

with temp_customer_orders (order_id,customer_id,roll_id,not_include_items,extra_items_include,order_date) as
(
select order_id,customer_id,roll_id,
case when not_include_items is null or not_include_items = '' then '0' else not_include_items end as new_include_items,
case when extra_items_included is null or extra_items_included = '' or extra_items_included = 'NaN' then '0' else extra_items_included end as new_extra_items_included,
order_date from customer_orders
)
,

temp_drivers_order (order_id,driver_id,pickup_time,distance,duration,new_cancellation) as
(
select order_id,driver_id,distance,pickup_time,duration,
case when cancellation in('cancellation','customer cancellation') then  0  else 1 end as new_cancellation
from driver_order)

select change_or_no_change, count(change_or_no_change) cnt_Change  from
(select * ,case when not_include_items != '0' And extra_items_include != '0' then 'Both change' else 'eihter 1 chnage' end  change_or_no_change 
from temp_customer_orders where order_id in (
select order_id from temp_drivers_order where new_cancellation != 0))a
group by change_or_no_change

--9 What is the total number of rolls order for each hour of the day?

select * from customer_orders;

Select 
hourly_range,count(Hourly_Range)  
from
(select *,concat(cast(DATEPART(HOUR,order_date) as varchar),'-',cast(DATEPART(HOUR,order_date)+1 as varchar)) Hourly_Range from customer_orders) a
group by 
hourly_range;

--10 What is the total number of rolls order for each hour of the day by a customer ?

Select order_id,hourly_range,count(customer_id) customer from
(select *,concat(cast(DATEPART(HOUR,order_date) as varchar),'-',cast(DATEPART(HOUR,order_date)+1 as varchar)) Hourly_Range from customer_orders) a
group by hourly_range,Order_id;

--11 What was the number of orders for each day of week?

Select week_name,count(distinct order_id) 
from
(select *,datename(WEEKDAY, order_date) week_name from customer_orders) a
group by week_name;


--What was the average time of minute it took for each driver to arrive at thepickup point the order?
select * from customer_orders;

select driver_id,sum(Difference)/COUNT(order_id) avg_min from(
select * from(
select *,ROW_NUMBER() over (partition by order_id order by difference) rnk 
from
(select a.order_id,a.customer_id,a.roll_id,a.not_include_items,a.extra_items_included,a.order_date,
b.driver_id,b.pickup_time,b.distance,b.duration,b.cancellation, DATEDIFF(minute,a.order_date,b.pickup_time) Difference
from customer_orders a inner join driver_order b on a.order_id =b.order_id 
where b.pickup_time is not null) a )b where rnk = 1 )c
group by driver_id;


