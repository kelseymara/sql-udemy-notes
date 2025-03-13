# Overview
These notes are from this Udemy Course: udemy.com/course/the-complete-sql-bootcamp/learn/lecture/18192138#overview

## Aggregation Functions
```SQL
SELECT MIN(replacement_cost) FROM film; // returns lowest replacement cost
SELECT MAX(replacement_cost) FROM film; // returns highest replacement cost
SELECT MAX(replacement_cost), MIN(replacement_cost) FROM film; // returns both
SELECT COUNT(*) FROM film; // returns how many rows
SELECT AVG(replacement_cost) FROM film; // returns average replacement cost
SELECT ROUND(AVG(replacement_cost),2); // rounds average replacement cost to 2 decimal places
SELECT SUM(replacement_cost); // returns sum of replacement cost
```

## GROUP BY
- choose a **categorical** column to GROUP BY
- categorical columns are non-continuous
- **the GROUP BY clause must appear right after a FROM or WHERE statement**
```SQL
SELECT category_col, AGG(data_col) // AGG is placeholder for Aggregate Function
FROM table
GROUP BY category_col;

SELECT category_col, AGG(data_col) // AGG is placeholder for Aggregate Function
FROM table
WHERE category_col!='A' // you can filter before group by
GROUP BY category_col;
```
- in the SELECT statement, columns **must either have an aggergate function or be in the GROUP BY call** (look at code above)
- where statements should not refer to the aggregation result, later on we will learn to use HAVING to filter on those results
You can group by multiple columns:
```SQL
SELECT company, division, SUM(sales)
FROM finance_table
GROUP BY company, division;
```
If you want to sort results based on the aggregate, make sure to reference the entire function:
```SQL
SELECT company, SUM(sales)
FROM finance_table
GROUP BY company
ORDER BY SUM(sales)
LIMIT 5;
```
## GROUP BY (part 2)
```SQL
SELECT DATE(payment_date) FROM payment; // formats the date into a more readable and takes out timestamp

SELECT DATE(payment_date) FROM payment
GROUP BY DATE(payment_date); // want to group the dates to see which ones have same date
```
## GROUP BY - Challenge
- We have 2 staff members, with staff IDs 1 and 2. We want to give a bonus to the staff member that handled the most payments (most in terms of number of payments processed, not total dollar amount).
- How many payments did each staff member handle and who gets the bonus?
```SQL
SELECT staff_id, COUNT(amount)
FROM payment,
GROUP BY staff_id;
```
- Corporate HQ is conducting a study on the relationship between replacement cost and a movie MPAA rating (e.g. PG, R, etc...)
- What is the average replacement cost per MPAA rating? note: you many need to expand the AVG column to view correct results
```SQL
SELECT rating, ROUND(AVG(replacement_cost),2)
FROM film
GROUP BY rating;
```
- We are running a promotion to reward our top 5 customers with coupons
- What are the customer ids of the top 5 customers by total spend?
```SQL
SELECT customer_id, SUM(amount)
FROM payment
ORDER BY SUM(amount) DESC
LIMIT 5;
```

## HAVING
- The HAVING clause allows us to filter **after** an aggregation has already taken place
```SQL
SELECT company, SUM(sales)
FROM finance_table
WHERE company!='Google'
GROUP BY company
HAVING SUM(sales)>1000
```
- HAVING allows us to use the aggregate result as a filter along with a GROUP BY
``` SQL
// returns count of customers in each store
SELECT customer_id, COUNT(customer_id) FROM customer
GROUP BY store_id;
// this gives the same results
SELECT customer_id, COUNT(*) FROM customer
GROUP BY store_id;

SELECT store_id, COUNT(customer_id) FROM customer
GROUP BY store_id
HAVING COUNT(customer_id) > 300;
```
- What are the customer ids of customers who have spent more than $100 in payment transactions with our staff_id member 2?
``` SQL
SELECT customer_id, SUM(amount)
FROM payment
WHERE staff_id=2
GROUP BY customer_id
HAVING SUM(amount)>100;
```

## JOINS
- Joins allow us to combine multiple tables together

### AS Statement
```SQL
SELECT amount AS rental_price // instead of column returning amount it returns rental_price (readability) 
FROM payment;

SELECT customer_id, SUM(amount) AS net_revenue
FROM payment
GROUP BY customer_id;
```
- **the AS operator gets executed at the very end of a query, meaning that we can not use the ALIAS inside a WHERE operator**
- only get to use it in SELECT statement
You can NOT do this because AS operator is executed at the END:
```SQL
SELECT customer_id, SUM(amount) AS total_spent
FROM payment
GROUP BY customer_id
HAVING SUM(total_spent) > 100
```
You have to choose the original:
```SQL
SELECT customer_id, SUM(amount) AS total_spent
FROM payment
GROUP BY customer_id
HAVING SUM(amount) > 100
```
You can NOT do this either:
```SQL
SELECT customer_id, amount AS new_name
FROM payment
WHERE new_name > 2;
```

### Inner Join
- will result with the set of records that **match in both** tables (the overlap in a venn diagram)
```SQL
SELECT * FROM Registrations // select all columns
INNER JOIN Logins // Logins is a table
ON Registrations.name = Logins.name; // column you're looking at to perform the inner join

// Selecting specific columns 
SELECT reg_id, Logins.name, log_id // could say Registrations.name instead if you want
FROM Registrations
INNER JOIN Logins
ON Registrations.name = Logins.name;
```
- remember that table order won't matter in an INNER JOIN
- if you see just JOIN without the INNER, it will be treated as an INNER JOIN

### Full Outer Join
Registrations table:
| reg_id | name | 
| ----- | -------- |
| 1 | Andrew | 
| 2 | Bob | 
| 3 | Charlie | 
| 4 | David |
Logins table:
| log_id | name | 
| ----- | -------- |
| 1 | Xavier | 
| 2 | Andrew| 
| 3 | Yolanda | 
| 4 | Bob |
Run this Query on those tables: 
```SQL
SELECT * FROM Registrations
FULL OUTER JOIN Logins
ON Registratoins.name=Logins.name
```
Result:

| log_id | name | log_id | name | 
| ----- | -------- | ------- | ------- |
| 1 | Andrew | 2 | Andrew | 
| 2 | Bob | 4 | Bob |
| 3 | Charlie | null | null | 
| 4 | David | null | null | 
| null | null | 1 | Xavier | 
| null | null | 3 | Yolanda |

#### Full Outer Join with WHERE: 
This gets the rows with no match:
```SQL
SELECT * FROM Registrations
FULL OUTER JOIN Logins
ON Registratoins.name=Logins.name
WHERE Registrations.reg_id IS null OR Logins.log_id IS null
```
So the outcome is: 
| log_id | name | log_id | name | 
| ----- | -------- | ------- | ------- |
| 3 | Charlie | null | null | 
| 4 | David | null | null | 
| null | null | 1 | Xavier | 
| null | null | 3 | Yolanda |

### Left Outer Join
- results in the set of records that are in the left table, if there is no match with the right table, the results are null
- can be notated LEFT OUTER JOIN or LEFT JOIN
- order MATTERS for left outer join
```SQL
// not returning anything exclusive to Table B
SELECT * FROM TableA
LEFT OUTER JOIN TableB
ON TableA.col_match=TableB.col_match
```
Registrations table:
| reg_id | name | 
| ----- | -------- |
| 1 | Andrew | 
| 2 | Bob | 
| 3 | Charlie | 
| 4 | David |

Logins table:

| log_id | name | 
| ----- | -------- |
| 1 | Xavier | 
| 2 | Andrew| 
| 3 | Yolanda | 
| 4 | Bob |

Run this Query on the tables:

```SQL
// not returning anything exclusive to Table B
SELECT * FROM Registrations
LEFT OUTER JOIN Logins
ON Registrations.name=Logins.name
```
Result:

| log_id | name | log_id | name | 
| ----- | -------- | ------- | ------- |
| 1 | Andrew | 2 | Andrew | 
| 2 | Bob | 4 | Bob |
| 3 | Charlie | null | null | 
| 4 | David | null | null | 
- notice that Yolanda and Bob from Logins is not included (what's exclusive to that right table)
- table order DOES MATTER when it comes to a left outer join (aka left join)
#### Left Outer Join with Where
- What if we only wanted entires unique to Table A? Those rows found in Table A and not found in Table B?
```SQL
// not returning anything exclusive to Table B
SELECT * FROM Registrations
LEFT OUTER JOIN Logins
ON Registrations.name=Logins.name
WHERE TableB.id IS null
```
- Grabs where login id and login name is NULL
Result:

| log_id | name | log_id | name | 
| ----- | -------- | ------- | ------- |
| 3 | Charlie | null | null | 
| 4 | David | null | null | 

### Right Outer Join
- essentially the same as a LEFT JOIN, except the tables are switched
  
### Union
- used to combine the result-set of two or mor SELECT statements
- basically serves to directly concatenate two results together, essentially "pasting" them together
```SQL
SELECT column_name(s) FROM table 1
UNION
SELECT column_name(s) from table2;
```
