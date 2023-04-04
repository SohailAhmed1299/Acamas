# SQL Project on Faaso's

## Creation of tables.

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

## Updating the table which to fix discrepancies in the data
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

## List of tables output:

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

## Questions with solutions.

--1 How many rolls were order ?

select count(roll_id) from customer_orders;

![1](https://user-images.githubusercontent.com/90980952/226140386-7f3456a2-a4eb-4ec9-b15d-225dbf0bee58.JPG)

--2 How many unique customer order were made?


Select COUNT(distinct customer_id) from customer_orders;

![2](https://user-images.githubusercontent.com/90980952/226140395-7716a43a-26e8-4cbc-9ae6-a719a9ef745b.JPG)

--3  How many succesful order were deliverd?


Select driver_id, COUNT(distinct order_id) from driver_order where cancellation not in ('cancellation' , 'customer cancellation') group by driver_id

![3](https://user-images.githubusercontent.com/90980952/226140398-c7ece4f7-5377-44dc-92f4-629fdc659fd8.JPG)

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

![4](https://user-images.githubusercontent.com/90980952/226140419-28746cbc-ee2f-438d-898f-bb2b67490f65.JPG)

--5 How many non veg and veg rolls were ordered by each customer?


select	a.*, b.roll_name from
(
Select customer_id,roll_id,COUNT(roll_id) cnt
from customer_orders
group by customer_id,roll_id)a inner join rolls b on a.roll_id = b.roll_id;

![5](https://user-images.githubusercontent.com/90980952/226140425-ebde2236-319f-4e35-bf50-7f70dbd2710d.JPG)

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

![6](https://user-images.githubusercontent.com/90980952/226140431-ffa3e264-0646-4e5b-ac36-a23942c1f9d6.JPG)

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

![7](https://user-images.githubusercontent.com/90980952/226140434-16e14810-6b84-4b87-8c60-3fd67bf702f4.JPG)

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

![8](https://user-images.githubusercontent.com/90980952/226140438-7c0f4f0b-4de4-4ea9-9d7f-8530acbf2b15.JPG)


--9 What is the total number of rolls order for each hour of the day?

select * from customer_orders;

Select 
hourly_range,count(Hourly_Range)  
from
(select *,concat(cast(DATEPART(HOUR,order_date) as varchar),'-',cast(DATEPART(HOUR,order_date)+1 as varchar)) Hourly_Range from customer_orders) a
group by 
hourly_range;

![9](https://user-images.githubusercontent.com/90980952/226140441-99570d2f-460f-4a62-8503-88e667a8f6f1.JPG)

--10 What is the total number of rolls order for each hour of the day by a customer ?

Select order_id,hourly_range,count(customer_id) customer from
(select *,concat(cast(DATEPART(HOUR,order_date) as varchar),'-',cast(DATEPART(HOUR,order_date)+1 as varchar)) Hourly_Range from customer_orders) a
group by hourly_range,Order_id;

![10](https://user-images.githubusercontent.com/90980952/226140448-caff5797-9ee0-494c-9f16-b974ab75a5bf.JPG)


--11 What was the number of orders for each day of week?

Select week_name,count(distinct order_id) 
from
(select *,datename(WEEKDAY, order_date) week_name from customer_orders) a
group by week_name;

![11](https://user-images.githubusercontent.com/90980952/226140456-926968a3-c57b-4d36-bba8-fb5a4513ff6c.JPG)



--12 What was the average time of minute it took for each driver to arrive at thepickup point the order?
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

![12](https://user-images.githubusercontent.com/90980952/226140464-98344860-d7a4-4a46-820d-d93116f36f64.JPG)



# SQL Project on Population Census

Data Sets

The following data set contains information reagarding District, State, Growth, Sex ratio, Literacy 

![dataset 1](https://user-images.githubusercontent.com/90980952/229322675-47e7f151-5dd1-42ac-8497-466452d2f39e.JPG)


The following data set contains information reagarding  District, State, Area Km2, Population


![Dataset2](https://user-images.githubusercontent.com/90980952/229322731-a4d0f586-5ce7-416c-b774-6cfa59580ca2.JPG)

After importing the both the data set, i joined both the table:

![Capture](https://user-images.githubusercontent.com/90980952/229322766-7ce2064b-b00b-4133-82fc-dda14400a4da.JPG)

Computing basic information on both of the data set 

![Capture1](https://user-images.githubusercontent.com/90980952/229322955-5700f806-511b-4b1c-88ff-1b7870c12d47.JPG)

## Question and Solutions of the following Project

1- Selecting all the data from the state Jharkand and Bihar.


![1](https://user-images.githubusercontent.com/90980952/229322987-d20d4f1d-ff41-44da-948f-9bee105d12f1.JPG)


2- Total population of India.


![2](https://user-images.githubusercontent.com/90980952/229323004-3eec5aa5-ee78-42a7-bdaa-51889736b7e5.JPG)


3-Total population of India by state.


![3](https://user-images.githubusercontent.com/90980952/229323014-4af6ac16-6bb3-4351-8540-318ff65350ce.JPG)


4- Total Average growth  of India.


![4](https://user-images.githubusercontent.com/90980952/229323028-54636c70-7cad-4618-8662-3b10febd65bf.JPG)


5-Total Average growth  of India by state.


![5](https://user-images.githubusercontent.com/90980952/229323050-61974f3a-a5c2-4edb-9695-c283d900f384.JPG)


6-Total average sex ratio of India by state.


![6](https://user-images.githubusercontent.com/90980952/229323066-d558f05c-7ec8-40a0-8d10-b3242050c86f.JPG)


7-Total average literacy o of India by state.


![7](https://user-images.githubusercontent.com/90980952/229323090-122d8830-3ab6-4d64-9a0f-25c44f2c8f61.JPG)


8-Top 3 states with growth sate in India.



![8](https://user-images.githubusercontent.com/90980952/229323101-14718d2d-4ae1-4f0d-9792-e9c008dfb23b.JPG)


9-Bottom 3 states with sex ratio sate in India.


![9](https://user-images.githubusercontent.com/90980952/229323138-90c7f9a5-6d07-4b29-87a8-9c321679dcd6.JPG)


10-Top 3 states with sex ratio sate in India.


![10](https://user-images.githubusercontent.com/90980952/229323165-c577f9b4-3eb3-46e7-b4b7-392c98b03043.JPG)


11- Top and bottom 3 states with literacy rate.


![11](https://user-images.githubusercontent.com/90980952/229391885-1cb893e7-6449-412e-8dfd-9c468d293550.JPG)


12- Calculation of Total Females and Males by District.


![12](https://user-images.githubusercontent.com/90980952/229391932-96bf8d49-e600-413c-a56a-116332132a8d.JPG)


13- Calculation of Total Females and Males by State.


![13](https://user-images.githubusercontent.com/90980952/229391975-4814ab51-34b5-417c-b59e-e96bfb33b073.JPG)


14- Calculation of total literate and non literate by District.


![14](https://user-images.githubusercontent.com/90980952/229392012-dd407eb2-4e85-4df3-98e2-3cd0388d92e8.JPG)


15- Calculation of total literate and non literate by State.


![15](https://user-images.githubusercontent.com/90980952/229392102-1cfa6848-3f85-462c-8f0c-76c00ea55eb8.JPG)


16- Population in the previous census population by district.


![16](https://user-images.githubusercontent.com/90980952/229392147-d2b98031-7fb8-4123-a671-bcc366b49366.JPG)


17- Population in the previous census population by state and total population of current and previous census.


![17](https://user-images.githubusercontent.com/90980952/229392183-2886679d-affe-4339-b188-f4ad68927fdd.JPG)


18- Population per area and curren and previous population per are.


![18](https://user-images.githubusercontent.com/90980952/229392229-8dafbddf-c534-4a47-af66-77e27f7ad786.JPG)


19- Calculation Top 3 district with highest literacy rate from each state.


![19](https://user-images.githubusercontent.com/90980952/229392272-86f62f2c-bab3-41fb-9960-c4747a0e80b4.JPG)


20- Calculation Bottom 3 district with highest literacy rate from each state.


![20](https://user-images.githubusercontent.com/90980952/229884072-c7baa5e6-6cc2-4f61-a617-d66762572c95.JPG)










