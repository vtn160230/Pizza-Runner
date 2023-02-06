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

![entitydg2](https://user-images.githubusercontent.com/122754787/216842806-1545ba7f-8155-4efa-b514-fca62c533464.png)

## Schema
<details>
	<summary>
		Click to expand the Schema
	</summary>
	
````sql
CREATE SCHEMA dannys_diner;
SET search_path = dannys_diner;

CREATE TABLE sales (
  "customer_id" VARCHAR(1),
  "order_date" DATE,
  "product_id" INTEGER
);

INSERT INTO sales
  ("customer_id", "order_date", "product_id")
VALUES
  ('A', '2021-01-01', '1'),
  ('A', '2021-01-01', '2'),
  ('A', '2021-01-07', '2'),
  ('A', '2021-01-10', '3'),
  ('A', '2021-01-11', '3'),
  ('A', '2021-01-11', '3'),
  ('B', '2021-01-01', '2'),
  ('B', '2021-01-02', '2'),
  ('B', '2021-01-04', '1'),
  ('B', '2021-01-11', '1'),
  ('B', '2021-01-16', '3'),
  ('B', '2021-02-01', '3'),
  ('C', '2021-01-01', '3'),
  ('C', '2021-01-01', '3'),
  ('C', '2021-01-07', '3');
 

CREATE TABLE menu (
  "product_id" INTEGER,
  "product_name" VARCHAR(5),
  "price" INTEGER
);

INSERT INTO menu
  ("product_id", "product_name", "price")
VALUES
  ('1', 'sushi', '10'),
  ('2', 'curry', '15'),
  ('3', 'ramen', '12');
  

CREATE TABLE members (
  "customer_id" VARCHAR(1),
  "join_date" DATE
);

INSERT INTO members
  ("customer_id", "join_date")
VALUES
  ('A', '2021-01-07'),
  ('B', '2021-01-09');
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
