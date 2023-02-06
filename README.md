# Pizza-Runner
Week 2 of the 8 Week SQL Challenge - Pizza Runner

##  Table of Contents
- [Business Task](#Business-Task)
- [Entity Relationship Diagram](#Entity-Relationship-Diagram)
- [Schema](#Schema)
- [Case Study Questions](#Case-Study-Questions)
- [Solutions](#Solutions)


## Business Task

Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. Having this deeper connection with his customers will help him deliver a better and more personalised experience for his loyal customers.


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

## Case Study Questions
  
- What is the total amount each customer spent at the restaurant?
- How many days has each customer visited the restaurant?
- What was the first item from the menu purchased by each customer?
- What is the most purchased item on the menu and how many times was it purchased by all customers?
- Which item was the most popular for each customer?
- Which item was purchased first by the customer after they became a member?
- Which item was purchased just before the customer became a member?
- What is the total items and amount spent for each member before they became a member?
- If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
- In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?


## Solutions 
### Q1: What is the total amount each customer spent at the restaurant?
<details>

````sql
SELECT s.customer_id, SUM(price) AS total_amount_spent FROM sales s
JOIN menu ON s.product_id = m.product_id
GROUP BY customer_id
ORDER BY customer_id
````

- I used the SUM and GROUP BY to figure out the total_amount_spent that each customer spent
- Used a JOIN to combine the sales and menu table on product_id that are from both tables
- Ended with an ORDER BY on customer_id to get an ascending table

Answer: 
<br>
![q1answer](https://user-images.githubusercontent.com/122754787/216840816-1676169f-e90f-4528-abbd-03c240d7242d.png)
</details>

***

### Q2: How many days has each customer visited the restaurant?
<details>

````sql
SELECT customer_id, COUNT(DISTINCT(order_date)) as days_visited FROM sales s
GROUP BY customer_id
````
<br>

- I used COUNT on the order_date to get each entry for the order_date, and DISTINCT to remove the duplicates of the same dates
- Finished with GROUP BY to get the customers in ascending order

Answer: 
<br>
![q2answer](https://user-images.githubusercontent.com/122754787/216841374-7dcbccce-3a06-4093-a864-df90981651c3.png)
</details>

***

### Q3: What was the first item from the menu purchased by each customer?
<details>

````sql
  with t1 as(
	SELECT customer_id, order_date, product_name, 
	ROW_Number() OVER(PARTITION BY s.customer_id
	ORDER BY s.order_date) as rank
	FROM sales s
	JOIN menu m ON s.product_id = m.product_id
)

SELECT customer_id, product_name FROM t1
WHERE rank = 1
GROUP BY customer_id, product_name
````
  
- Use t1 as a temporary table and use ROW_Number to create column ranks that is partitioned by the customer_id and ORDERED BY order_date
- Write new query pulling the customer_id and product_name from t1 table WHERE the rank = 1, which will pull the rank 1 entry for each customer_id
- GROUP BY customer_id and product_name to fetch the customer_id and first item ever ordered by the customer
	
Answer: 
<br>
![q3answer](https://user-images.githubusercontent.com/122754787/216845143-43f7855d-4c28-4edf-9520-2316d43317c5.png)
  </details>

***

### Q4: What is the most purchased item on the menu and how many times was it purchased by all customers?
<details>

````sql
SELECT product_name, COUNT(s.product_id) AS most_purchased FROM sales s
JOIN menu m ON s.product_id = m.product_id
GROUP BY product_name, s.product_id
ORDER BY most_purchased DESC
````

- Use COUNT of product_id 
- GROUP BY the product_id and product_name to show all the products and the amount of times they were each purchased
- ORDER BY most_purchased DESC to get the highest to lowest

Answer:
<br>
![q4asnwer](https://user-images.githubusercontent.com/122754787/216847985-9d0cecf3-dd8f-4075-a61e-67e3c38c5db3.png)
</details>

***

### Q5: Which item was the most popular for each customer?
<details>

````sql
	with t1 as (
	SELECT customer_id, product_name, COUNT(m.product_id) AS order_count,
	DENSE_RANK() OVER(PARTITION BY s.customer_id 
					ORDER BY COUNT(s.customer_id) DESC) AS rank 
	FROM sales s
	JOIN menu m ON s.product_id = m.product_id
	GROUP BY customer_id, product_name
	ORDER BY customer_id, rank DESC
)

SELECT customer_id, product_name, order_count FROM t1
WHERE rank = 1
````
- Use t1 as a temp table and use DENSE_RANK so the tied numbers are not skipped over
- Fetch data WHERE the rank = 1 to show the most popular item for each customer

Answer:
<br>
![q5answer](https://user-images.githubusercontent.com/122754787/216849655-b547c715-ca65-4186-b32b-9e4b7e4bbea1.png)
</details>

***

### Q6: Which item was purchased first by the customer after they became a member?
<details>
	
````sql
with t1 as (
	SELECT s.customer_id, m.join_date, s.order_date, s.product_id,
	DENSE_RANK() OVER(PARTITION BY s.customer_id
					 ORDER BY s.order_date) AS rank
	FROM sales s
	JOIN members m ON s.customer_id = m.customer_id
	WHERE s.order_date >= m.join_date
)

SELECT s.customer_id, s.order_date, m.product_name FROM t1 s
JOIN menu m ON s.product_id = m.product_id
WHERE rank = 1
ORDER BY customer_id
````
- Use a temp table t1
- Use DENSE_RANK and partition by customer id, and then ORDER BY the order_date AS rank to get a ranking on the order dates
- JOIN and use WHERE on order date with a greater than or equal to the join_date to get only entries starting from the join_date
- Use a rank = 1 and to get the top rank for each customer_id
	
Answer: 
<br>
![q6 answer](https://user-images.githubusercontent.com/122754787/216884056-765e37b4-be00-4dee-9d60-0a57d9298db8.png)
</details>

***

### Q7: Which item was purchased just before the customer became a member?
<details>

````sql
with t1 as (
	SELECT s.customer_id, m.join_date, s.order_date, s.product_id,
	ROW_Number() OVER(PARTITION BY s.customer_id
					 ORDER BY s.order_date DESC) AS rank
	FROM sales s
	JOIN members m ON s.customer_id = m.customer_id
	WHERE s.order_date < m.join_date
)

SELECT s.customer_id, s.order_date, m.product_name FROM t1 s
JOIN menu m ON s.product_id = m.product_id
WHERE rank = 1
ORDER BY customer_id
````
- Very similar to Question 6, make sure to DESC the ORDER BY  on s.order_date so that it gives the most recent orders right before the join_date
- Flip the greater than equal sign to a less than sign on WHERE s.order_date < m.join_date so that you can fetch all the orders before the join_date

Answer:
<br>
![q7answer](https://user-images.githubusercontent.com/122754787/216886145-e1edb434-adc6-4eae-a564-be2e74d34c95.png)
</details>

***

### Q8: What is the total items and amount spent for each member before they became a member?
<details>

````sql
SELECT s.customer_id, COUNT(s.product_id) AS menu_item,
SUM(mm.price) as total_sales FROM sales s
JOIN members m ON s.customer_id = m.customer_id
JOIN menu mm ON s.product_id = mm.product_id
WHERE s.order_date < m.join_date
GROUP BY s.customer_id
ORDER BY s.customer_id
````

- Use a COUNT DISTINCT on product_id and SUM the total_sales before becoming a member
- Use a filter on order_date less than the join date to get all the entries before each customer became a member

Answer:
<br>
![Q8](https://user-images.githubusercontent.com/122754787/216927917-de59dbe2-0514-415b-9d1f-028bc26bd62a.png)
</details>

***

### Q9: If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have? (on going)
	


***

### Q10: In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January? (on going)
