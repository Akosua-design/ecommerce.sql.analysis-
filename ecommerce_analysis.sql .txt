-- SECTION 1: WARM UP 
-- how many orders are there 
SELECT COUNT(*)
FROM orders;
-- orders never delivered to the customer
SELECT COUNT(*)
FROM orders
WHERE order_delivered_customer_date IS NULL;
-- number of orders in each status 
SELECT order_status, COUNT(*) AS total
FROM orders
GROUP BY order_status
ORDER BY total DESC;
-- delivered orders with no delivery data (data quality check)
SELECT COUNT(*)
FROM orders
WHERE order_status = 'delivered'
 AND order_delivered_customer_date IS NULL;
 -- SECTION 2: BUISNESS QUESTIONS 
 -- number of orders per month
 SELECT strftime('%Y-%m', order_purchase_timestamp) AS month, COUNT(*) AS total_orders
 FROM orders 
 GROUP BY strftime('%Y-%m', order_purchase_timestamp)
 ORDER BY month;
 -- Q1b: busiest days in November 2017
SELECT strftime('%Y-%m-%d', order_purchase_timestamp) AS day, COUNT(*) AS total_orders
FROM orders
WHERE strftime('%Y-%m', order_purchase_timestamp) = '2017-11'
GROUP BY day
ORDER BY total_orders DESC 
LIMIT 5;
-- Q1c: compare January to August in 2017 and 2018
SELECT strftime('%Y', order_purchase_timestamp) AS year, COUNT(*) AS total_orders
FROM orders
WHERE strftime('%m', order_purchase_timestamp) BETWEEN '01' AND '08'
GROUP BY year;
-- Q2: top 10 product categories by revenue
SELECT p.product_category_name, ROUND(SUM(i.price), 2) AS revenue
FROM items i 
JOIN products p ON i.product_id = p.product_id
GROUP BY p.product_category_name
ORDER BY revenue DESC
LIMIT 10;
-- FIXING DUPLICATE ROWS 
SELECT COUNT (*) FROM ITEMS;
SELECT COUNT(*) FROM PRODUCTS;
CREATE TABLE FIXED AS SELECT DISTINCT * FROM ITEMS;
DROP TABLE ITEMS;
ALTER TABLE FIXED RENAME TO ITEMS;
SELECT COUNT(*) FROM ITEMS;
SELECT p.product_category_name, ROUND(SUM(i.price), 2) AS revenue
FROM items i 
JOIN products p ON i.product_id = p.product_id
GROUP BY p.product_category_name
ORDER BY revenue DESC
LIMIT 10;
-- Q2B: TOP 10 CATEGORIES BY REVENUE, IN ENGLISH
SELECT T.product_category_name_english AS CATEGORY, ROUND(SUM(I.PRICE), 2) AS REVENUE
FROM ITEMS I 
JOIN PRODUCTS P ON I.PRODUCT_ID = P.product_id
JOIN TRANSLATION T ON P.PRODUCT_CATEGORY_NAME = T.product_category_name
GROUP BY CATEGORY
ORDER BY REVENUE DESC
LIMIT 10;
-- Q3: TOP 10 STATES BY NUMBER OF orders
SELECT C.customer_state AS STATE, COUNT(*) AS TOTAL_ORDERS
FROM ORDERS O
JOIN CUSTOMERS C ON O.CUSTOMER_ID = C.customer_id
GROUP BY STATE
ORDER BY TOTAL_ORDERS DESC
LIMIT 10;
--Q4: 10 LOWEST RATE CATEGORES (MINIMUM 100 REVIEWS) 
SELECT T.product_category_name_english AS CATEGORY, ROUND(avg (R.review_score), 2) AS AVG_SCORE, COUNT(*) AS TOTAL_REVIEWS
FROM REVIEWS R
JOIN ITEMS I ON R.ORDER_ID = I.ORDER_ID 
JOIN PRODUCTS P ON I.PRODUCT_ID = P.product_id
JOIN translation T ON P.product_category_name = T.product_category_name
GROUP BY CATEGORY 
HAVING COUNT(*) >=100
ORDER BY AVG_SCORE ASC
LIMIT 10;
-- Q4B: AVERAGE REVIEW SCORE ACROSS ALL orders
SELECT ROUND(AVG(review_score), 2) FROM reviews;
-- Q5: NUMBER OF PAYMENTS AND AVERGE PAYMENTS BY PAYMENT type
SELECT payment_type, COUNT(*) AS TOTAL_PAYMENTS, ROUND(AVG(PAYMENT_VALUE), 2) AS AVG_PAYMENT
FROM payments
GROUP BY payment_type
ORDER BY TOTAL_PAYMENTS DESC;
--Q6: AVERAGE REVIEW SCORE FOR LATE VS ON TIME DELIVERIES
SELECT CASE
        WHEN O.order_delivered_customer_date > O.order_estimated_delivery_date THEN 'LATE'
		ELSE 'ON TIME'
		END AS DELIVERY_STATUS,
		COUNT(*) AS TOTAL_ORDERS,
		ROUND(AVG(R.REVIEW_SCORE), 2) AS AVG_SCORE
FROM ORDERS O 
JOIN REVIEWS R ON O.ORDER_ID = R.order_id
WHERE O.ORDER_STATUS = 'delivered'
AND O.order_delivered_customer_date IS NOT NULL
GROUP BY DELIVERY_STATUS;
