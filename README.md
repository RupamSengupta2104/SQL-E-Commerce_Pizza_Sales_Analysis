# E-Commerce Pizza Sales Analysis SQL Project

## Project Overview

**Project Title**: E-Commerce Pizza Sales Analysis  

This project demonstrates SQL skills for analyzing pizza sales data. The objective is to create a structured database, perform exploratory data analysis (EDA), and answer specific business questions using SQL queries.

## Objectives

1. **Set up the pizza sales database**: Create and populate a database named `pizzahut`.
2. **Data Cleaning**: Ensure data integrity by defining appropriate constraints.
3. **Exploratory Data Analysis (EDA)**: Retrieve, filter, and aggregate data.
4. **Business Analysis**: Answer key business questions using SQL queries.

## Project Structure

### 1. Database Setup

- **Database Creation**: `pizzahut`
- **Table Creation**: The following tables are created:

```sql
CREATE TABLE pizzas (
    pizza_id TEXT PRIMARY KEY,
    pizza_type_id TEXT,
    size VARCHAR(10),
    price NUMERIC(5,2)
);

CREATE TABLE pizza_types (
    pizza_type_id TEXT PRIMARY KEY,
    name TEXT NOT NULL,
    category TEXT NOT NULL,
    ingredients TEXT
);

CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    order_date DATE NOT NULL,
    order_time TIME NOT NULL
);

CREATE TABLE order_details (
    order_details_id SERIAL PRIMARY KEY,
    order_id INT NOT NULL,
    pizza_id TEXT NOT NULL,
    quantity INT NOT NULL CHECK (quantity > 0),
    FOREIGN KEY (order_id) REFERENCES orders(order_id) ON DELETE CASCADE,
    FOREIGN KEY (pizza_id) REFERENCES pizzas(pizza_id) ON DELETE CASCADE
);
```

### 2. Data Exploration & Queries

1. **Retrieve all records from tables**:
```sql
SELECT * FROM pizzas;
SELECT * FROM pizza_types;
SELECT * FROM orders;
SELECT * FROM order_details;
```

2. **Retrieve total number of orders placed**:
```sql
SELECT COUNT(order_id) AS total_orders FROM orders;
```

3. **Calculate the total revenue generated from pizza sales**:
```sql
SELECT 
ROUND(SUM(order_details.quantity * pizzas.price),2) AS total_revenue
FROM order_details 
JOIN pizzas
ON pizzas.pizza_id = order_details.pizza_id;
```

4. **Identify the highest-priced pizza**:
```sql
SELECT pizza_types.name, pizzas.price
FROM pizza_types
JOIN pizzas
ON pizza_types.pizza_type_id = pizzas.pizza_type_id
ORDER BY pizzas.price DESC LIMIT 1;
```

5. **Identify the most common pizza size ordered**:
```sql
SELECT pizzas.size, COUNT(order_details.order_details_id) AS order_count
FROM pizzas JOIN order_details
ON pizzas.pizza_id = order_details.pizza_id
GROUP BY pizzas.size
ORDER BY order_count DESC
LIMIT 1;
```

6. **List the top 5 most ordered pizza types along with their quantities**:
```sql

```


## Intermediate Queries

1. **Find the most common pizza size ordered**:
```sql
SELECT size, COUNT(*) AS order_count
FROM pizzas 
JOIN order_details ON pizzas.pizza_id = order_details.pizza_id
GROUP BY size
ORDER BY order_count DESC
LIMIT 1;
```

2. **List the top 5 most ordered pizza types along with their quantities**:
```sql
SELECT pizza_types.name, SUM(order_details.quantity) AS total_quantity
FROM order_details
JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id
JOIN pizza_types ON pizzas.pizza_type_id = pizza_types.pizza_type_id
GROUP BY pizza_types.name
ORDER BY total_quantity DESC
LIMIT 5;
```

3. **Find the total quantity of each pizza category ordered**:
```sql
SELECT pizza_types.category, SUM(order_details.quantity) AS total_quantity
FROM order_details
JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id
JOIN pizza_types ON pizzas.pizza_type_id = pizza_types.pizza_type_id
GROUP BY pizza_types.category
ORDER BY total_quantity DESC;
```

4. **Determine the distribution of orders by hour of the day**:
```sql
SELECT EXTRACT(HOUR FROM order_time) AS order_hour, COUNT(*) AS total_orders
FROM orders
GROUP BY order_hour
ORDER BY order_hour;
```

## Advanced Queries

1. **Calculate the percentage contribution of each pizza type to total revenue**:
```sql
SELECT pizza_types.name, 
       ROUND(SUM(order_details.quantity * pizzas.price) * 100 / 
       (SELECT SUM(order_details.quantity * pizzas.price) FROM order_details JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id), 2) AS revenue_percentage
FROM order_details
JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id
JOIN pizza_types ON pizzas.pizza_type_id = pizza_types.pizza_type_id
GROUP BY pizza_types.name
ORDER BY revenue_percentage DESC;
```

2. **Analyze the cumulative revenue generated over time**:
```sql
SELECT order_date, 
       SUM(order_details.quantity * pizzas.price) OVER (ORDER BY order_date) AS cumulative_revenue
FROM orders
JOIN order_details ON orders.order_id = order_details.order_id
JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id;
```

3. **Determine the top 3 most ordered pizza types based on revenue for each pizza category**:
```sql
SELECT category, name, total_revenue FROM (
    SELECT pizza_types.category, pizza_types.name, 
           SUM(order_details.quantity * pizzas.price) AS total_revenue,
           RANK() OVER(PARTITION BY pizza_types.category ORDER BY SUM(order_details.quantity * pizzas.price) DESC) AS rank
    FROM order_details
    JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id
    JOIN pizza_types ON pizzas.pizza_type_id = pizza_types.pizza_type_id
    GROUP BY pizza_types.category, pizza_types.name
) ranked_pizzas WHERE rank <= 3;
```

## Findings

- **Top-selling pizza**: Identified based on revenue and quantity.
- **Popular size**: Determined based on order frequency.
- **Revenue distribution**: Calculated for each pizza type.
- **Sales trends**: Analyzed order distribution by time.

## Conclusion

This project showcases SQL capabilities in database management, data retrieval, and business insights. The queries help understand sales trends, revenue distribution, and customer preferences, enabling data-driven decision-making.

## Author
This project is part of my portfolio, demonstrating SQL skills for data analysis. If you have any questions, feedback, or collaboration ideas, feel free to connect!
https://www.youtube.com/watch?v=zZpMvAedh_E&list=LL&index=1&t=1250s


