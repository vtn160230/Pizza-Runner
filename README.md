# Pizza-Runner (Work In Progress)
Week 2 of the 8 Week SQL Challenge - Pizza Runner

##  Table of Contents
- [Business Task](#Business-Task)
- [Entity Relationship Diagram](#Entity-Relationship-Diagram)
- [Schema](#Schema)
- [Data Cleaning and Transformation](#Data Cleaning and Transformation)
- [Case Study Questions](#Case-Study-Questions)
- [Solutions](#Solutions)


## Business Task

Danny has prepared for us an entity relationship diagram of his database design but requires further assistance to clean his data and apply some basic calculations so he can better direct his runners and optimise Pizza Runner’s operations.


## Entity Relationship Diagram

![Capture](https://user-images.githubusercontent.com/122754787/217063607-37a496a9-4b12-4aee-b764-d3087d683f8a.PNG)

## Schema
<details>
	<summary>
		Click to expand the Schema
	</summary>
	
````sql
CREATE SCHEMA pizza_runner;
SET search_path = pizza_runner;

DROP TABLE IF EXISTS runners;
CREATE TABLE runners (
  "runner_id" INTEGER,
  "registration_date" DATE
);
INSERT INTO runners
  ("runner_id", "registration_date")
VALUES
  (1, '2021-01-01'),
  (2, '2021-01-03'),
  (3, '2021-01-08'),
  (4, '2021-01-15');


DROP TABLE IF EXISTS customer_orders;
CREATE TABLE customer_orders (
  "order_id" INTEGER,
  "customer_id" INTEGER,
  "pizza_id" INTEGER,
  "exclusions" VARCHAR(4),
  "extras" VARCHAR(4),
  "order_time" TIMESTAMP
);

INSERT INTO customer_orders
  ("order_id", "customer_id", "pizza_id", "exclusions", "extras", "order_time")
VALUES
  ('1', '101', '1', '', '', '2020-01-01 18:05:02'),
  ('2', '101', '1', '', '', '2020-01-01 19:00:52'),
  ('3', '102', '1', '', '', '2020-01-02 23:51:23'),
  ('3', '102', '2', '', NULL, '2020-01-02 23:51:23'),
  ('4', '103', '1', '4', '', '2020-01-04 13:23:46'),
  ('4', '103', '1', '4', '', '2020-01-04 13:23:46'),
  ('4', '103', '2', '4', '', '2020-01-04 13:23:46'),
  ('5', '104', '1', 'null', '1', '2020-01-08 21:00:29'),
  ('6', '101', '2', 'null', 'null', '2020-01-08 21:03:13'),
  ('7', '105', '2', 'null', '1', '2020-01-08 21:20:29'),
  ('8', '102', '1', 'null', 'null', '2020-01-09 23:54:33'),
  ('9', '103', '1', '4', '1, 5', '2020-01-10 11:22:59'),
  ('10', '104', '1', 'null', 'null', '2020-01-11 18:34:49'),
  ('10', '104', '1', '2, 6', '1, 4', '2020-01-11 18:34:49');


DROP TABLE IF EXISTS runner_orders;
CREATE TABLE runner_orders (
  "order_id" INTEGER,
  "runner_id" INTEGER,
  "pickup_time" VARCHAR(19),
  "distance" VARCHAR(7),
  "duration" VARCHAR(10),
  "cancellation" VARCHAR(23)
);

INSERT INTO runner_orders
  ("order_id", "runner_id", "pickup_time", "distance", "duration", "cancellation")
VALUES
  ('1', '1', '2020-01-01 18:15:34', '20km', '32 minutes', ''),
  ('2', '1', '2020-01-01 19:10:54', '20km', '27 minutes', ''),
  ('3', '1', '2020-01-03 00:12:37', '13.4km', '20 mins', NULL),
  ('4', '2', '2020-01-04 13:53:03', '23.4', '40', NULL),
  ('5', '3', '2020-01-08 21:10:57', '10', '15', NULL),
  ('6', '3', 'null', 'null', 'null', 'Restaurant Cancellation'),
  ('7', '2', '2020-01-08 21:30:45', '25km', '25mins', 'null'),
  ('8', '2', '2020-01-10 00:15:02', '23.4 km', '15 minute', 'null'),
  ('9', '2', 'null', 'null', 'null', 'Customer Cancellation'),
  ('10', '1', '2020-01-11 18:50:20', '10km', '10minutes', 'null');


DROP TABLE IF EXISTS pizza_names;
CREATE TABLE pizza_names (
  "pizza_id" INTEGER,
  "pizza_name" TEXT
);
INSERT INTO pizza_names
  ("pizza_id", "pizza_name")
VALUES
  (1, 'Meatlovers'),
  (2, 'Vegetarian');


DROP TABLE IF EXISTS pizza_recipes;
CREATE TABLE pizza_recipes (
  "pizza_id" INTEGER,
  "toppings" TEXT
);
INSERT INTO pizza_recipes
  ("pizza_id", "toppings")
VALUES
  (1, '1, 2, 3, 4, 5, 6, 8, 10'),
  (2, '4, 6, 7, 9, 11, 12');


DROP TABLE IF EXISTS pizza_toppings;
CREATE TABLE pizza_toppings (
  "topping_id" INTEGER,
  "topping_name" TEXT
);
INSERT INTO pizza_toppings
  ("topping_id", "topping_name")
VALUES
  (1, 'Bacon'),
  (2, 'BBQ Sauce'),
  (3, 'Beef'),
  (4, 'Cheese'),
  (5, 'Chicken'),
  (6, 'Mushrooms'),
  (7, 'Onions'),
  (8, 'Pepperoni'),
  (9, 'Peppers'),
  (10, 'Salami'),
  (11, 'Tomatoes'),
  (12, 'Tomato Sauce');
````
</details>

## Data Cleaning and Transformation

***

## Case Study Questions
  
- How many pizzas were ordered?
- How many unique customer orders were made?
- How many successful orders were delivered by each runner?
- How many of each type of pizza was delivered?
- How many Vegetarian and Meatlovers were ordered by each customer?
- What was the maximum number of pizzas delivered in a single order?
- For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
- How many pizzas were delivered that had both exclusions and extras?
- What was the total volume of pizzas ordered for each hour of the day?
- What was the volume of orders for each day of the week?


## Solutions 
### Q1: How many pizzas were ordered?

````sql
SELECT COUNT(order_id) AS order_count FROM customer_orders
````

***

### Q2: How many unique customer orders were made?

````sql
SELECT COUNT(DISTINCT order_id) AS unique_count FROM customer_orders 
````

***

### Q3: How many successful orders were delivered by each runner?

````sql
SELECT runner_id, COUNT(order_id) AS successful_orders FROM runner_orders
WHERE duration <> 'null'
GROUP BY runner_id
ORDER BY runner_id
````

***

### Q4: How many of each type of pizza was delivered?

````sql
SELECT p.pizza_name, COUNT(c.pizza_id) AS pizza_count FROM customer_orders c
JOIN pizza_names p ON c.pizza_id = p.pizza_id
JOIN runner_orders r ON c.order_id = r.order_id
WHERE r.distance <> 'null'
GROUP BY p.pizza_name
````

***

### Q5: How many Vegetarian and Meatlovers were ordered by each customer?

````sql
SELECT c.customer_id, p.pizza_name, COUNT(c.pizza_id) AS pizza_count FROM customer_orders c
JOIN pizza_names p ON c.pizza_id = p.pizza_id
GROUP BY c.customer_id, p.pizza_name
ORDER BY c.customer_id
````

***

### Q6: What was the maximum number of pizzas delivered in a single order?

````sql
with t1 as (
	SELECT c.order_id, COUNT(pizza_id) AS pizza_count FROM customer_orders c
	JOIN runner_orders r ON c.order_id = r.order_id
	WHERE duration <> 'null'
	GROUP BY c.order_id
	)
	
SELECT MAX(pizza_count) FROM t1;
````

***

### Q7: For each customer, how many delivered pizzas had at least 1 change and how many had no changes?

````sql
SELECT c.customer_id, 
	SUM(
	CASE WHEN c.exclusions <> ' ' OR c.extras <> ' ' THEN 1
		ELSE 0
		END) AS at_least_1_change,
	SUM(
		CASE WHEN c.exclusions = ' ' AND c.extras = ' ' THEN 1
		ELSE 0
		END) AS no_change
FROM customer_orders_temp c 
JOIN runner_orders_temp r ON c.order_id = r.order_id
WHERE r.duration <> 0 
GROUP BY c.customer_id
ORDER BY c.customer_id
````

***

### Q8: How many pizzas were delivered that had both exclusions and extras?

````sql
SELECT  
  SELECT  
  	SUM(
    	CASE WHEN exclusions IS NOT NULL AND extras IS NOT NULL THEN 1
    	ELSE 0
    	END) AS pizza_count_w_exclusions_extras
FROM customer_orders c
JOIN runner_orders r ON c.order_id = r.order_id
WHERE r.distance >= 1 AND exclusions <> ' ' AND extras <> ' ';
````

***

### Q9: What was the total volume of pizzas ordered for each hour of the day?


***

### Q10: What was the volume of orders for each day of the week?
