
# Pizza Sales Analysis

## Project Overview
The Pizza Sales Analysis project involves analyzing pizza sales data using SQL queries to answer a variety of questions ranging from basic to advanced. This project includes multiple datasets such as order details, orders, pizza types, and pizzas. The analysis aims to uncover insights into sales patterns, popular pizzas, revenue distribution, and more, providing valuable information for business decision-making and strategy development etc.

## Datasets
1. **order_details**
    - Contains details about each order.
    - Columns: `order_details_id`, `order_id`, `pizza_id`, `quantity`
    
2. **orders**
    - Contains information about each order placed.
    - Columns: `order_id`, `date`, `time`
    
3. **pizza_types**
    - Contains information about different pizza types.
    - Columns: `pizza_type_id`, `name`, `category`, `ingredients`
    
4. **pizzas**
    - Contains information about pizzas including their size and price.
    - Columns: `pizza_id`, `pizza_type_id`, `size`, `price`

## Questions and SQL Queries
### Basic
1. **Retrieve the total number of orders placed.**
    ```sql
    SELECT COUNT(order_id) AS totat_orderds FROM orders;
    ```

2. **Calculate the total revenue generated from pizza sales.**
    ```sql
    SELECT ROUND(SUM(order_details.quantity * pizzas.price), 2) AS total_revenue
    FROM order_details
    JOIN pizzas ON pizzas.pizza_id = order_details.pizza_id;
    ```

3. **Identify the highest-priced pizza.**
    ```sql
    SELECT pizza_types.name, pizzas.price
    FROM pizza_types
    JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
    ORDER BY pizzas.price DESC
    LIMIT 1;
    ```

4. **Identify the most common pizza size ordered.**
    ```sql
    SELECT pizzas.size, COUNT(order_details.order_details_id) AS order_count
    FROM pizzas
    JOIN order_details ON pizzas.pizza_id = order_details.pizza_id
    GROUP BY pizzas.size
    ORDER BY order_count DESC
    LIMIT 1;
    ```

5. **List the top 5 most ordered pizza types along with their quantities.**
    ```sql
    SELECT pizza_types.name, SUM(order_details.quantity) AS quantity
    FROM pizza_types
    JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
    JOIN order_details ON order_details.pizza_id = pizzas.pizza_id
    GROUP BY pizza_types.name
    ORDER BY quantity DESC
    LIMIT 5;
    ```

### Intermediate
1. **Join the necessary tables to find the total quantity of each pizza category ordered.**
    ```sql
    SELECT pizza_types.category, SUM(order_details.quantity) AS quantity
    FROM pizza_types
    JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
    JOIN order_details ON order_details.pizza_id = pizzas.pizza_id
    GROUP BY pizza_types.category
    ORDER BY quantity DESC;
    ```

2. **Determine the distribution of orders by hour of the day.**
    ```sql
    SELECT HOUR(order_time) AS hour, COUNT(order_id) AS order_count
    FROM orders
    GROUP BY HOUR(order_time);
    ```

3. **Join relevant tables to find the category-wise distribution of pizzas.**
    ```sql
    SELECT category, COUNT(name)
    FROM pizza_types
    GROUP BY category;
    ```

4. **Group the orders by date and calculate the average number of pizzas ordered per day.**
    ```sql
    SELECT ROUND(AVG(quantity), 0) AS avg_pizzas_ordered_per_day
    FROM (
        SELECT orders.order_date, SUM(order_details.quantity) AS quantity
        FROM orders
        JOIN order_details ON orders.order_id = order_details.order_id
        GROUP BY orders.order_date
    ) AS order_quantity;
    ```

5. **Determine the top 3 most ordered pizza types based on revenue.**
    ```sql
    SELECT pizza_types.name, SUM(order_details.quantity * pizzas.price) AS revenue
    FROM pizza_types
    JOIN pizzas ON pizzas.pizza_type_id = pizza_types.pizza_type_id
    JOIN order_details ON order_details.pizza_id = pizzas.pizza_id
    GROUP BY pizza_types.name
    ORDER BY revenue DESC
    LIMIT 3;
    ```

### Advanced
1. **Calculate the percentage contribution of each pizza type to total revenue.**
    ```sql
    SELECT pizza_types.category,
    ROUND((SUM(order_details.quantity * pizzas.price) / (SELECT 
                    ROUND(SUM(order_details.quantity * pizzas.price), 2) AS total_sales
                FROM order_details
                JOIN pizzas ON pizzas.pizza_id = order_details.pizza_id)) * 100, 2) AS revenue
    FROM pizza_types
    JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
    JOIN order_details ON order_details.pizza_id = pizzas.pizza_id
    GROUP BY pizza_types.category
    ORDER BY revenue DESC;
    ```

2. **Analyze the cumulative revenue generated over time.**
    ```sql
    SELECT order_date, ROUND(SUM(revenue) OVER(ORDER BY order_date), 2) AS cum_revenue
    FROM (
        SELECT orders.order_date, SUM(order_details.quantity * pizzas.price) AS revenue
        FROM order_details
        JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id 
        JOIN orders ON orders.order_id = order_details.order_id
        GROUP BY orders.order_date
    ) AS sales;
    ```

3. **Determine the top 3 most ordered pizza types based on revenue for each pizza category.**
    ```sql
    SELECT name, revenue FROM (
        SELECT category, name, revenue,
        RANK() OVER(PARTITION BY category ORDER BY revenue DESC) AS rn
        FROM (
            SELECT pizza_types.category, pizza_types.name, 
            SUM((order_details.quantity)*pizzas.price) AS revenue
            FROM pizza_types
            JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
            JOIN order_details ON order_details.pizza_id = pizzas.pizza_id
            GROUP BY pizza_types.category, pizza_types.name
        ) AS a
    ) AS b
    WHERE rn <= 3;
    ```

## PDF Documentation
The project also includes a PDF document containing the SQL queries and their outputs for reference.

## How to Use
1. Load the provided datasets into your SQL database.
2. Use the SQL queries provided in this document to analyze the pizza sales data.
3. Refer to the PDF document for example outputs and further clarification.

## Conclusion
The Pizza Sales Analysis project provides valuable insights into the sales patterns and trends of a pizza business. By leveraging SQL queries, we were able to:

1. Identify key metrics such as total orders, revenue, and most popular pizzas.
2. Analyze the distribution of pizza orders by size, category, and time.
3. Determine the financial performance of different pizza types and sizes.
4. Track cumulative revenue over time to understand growth patterns.
5. Provide actionable insights for optimizing inventory, marketing strategies, and sales tactics.

This analysis helps stakeholders make informed decisions to drive business growth and improve customer satisfaction. The project showcases the importance of data analytics in uncovering hidden patterns and enhancing overall business performance.
