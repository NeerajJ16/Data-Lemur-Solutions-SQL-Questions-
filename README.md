# Data-Lemur-Solutions-SQL-Questions-

The questions are linked directly, and the solutions are also provided, solved using my approach.
Some problems are in MySQL, and some are in PostgreSQL 14, since a few problems only have a PostgreSQL compiler available in the code editor.

#### Question : [Histogram of Tweets](https://datalemur.com/questions/sql-histogram-tweets)
```sql
WITH tweets2022 AS(
  SELECT user_id, count(tweet_id) as tweet_bucket
  FROM tweets
  WHERE YEAR(tweet_date) = 2022
  GROUP BY user_id
)

SELECT tweet_bucket, count(user_id) as users_num
FROM tweets2022
GROUP BY tweet_bucket
```

#### Question : [Write a query to list the candidates who possess all of the required skills for the job. Sort the output by candidate ID in ascending order.](https://datalemur.com/questions/matching-skills)
```sql
SELECT candidate_id
FROM candidates
WHERE skill IN ('Python', 'Tableau', 'PostgreSQL')
GROUP BY candidate_id
HAVING COUNT(DISTINCT skill) = 3
```

#### Question : [Page With No Likes](https://datalemur.com/questions/sql-page-with-no-likes)
```sql
WITH likecount as (select p.page_id, pl.user_id from pages p
LEFT join page_likes pl 
ON p.page_id=pl.page_id)
SELECT DISTINCT page_id from likecount WHERE user_id IS NULL
```

#### Question : [Unfinished Parts](https://datalemur.com/questions/tesla-unfinished-parts)
```sql
SELECT part, assembly_step FROM parts_assembly 
WHERE finish_date IS NULL
```

#### Question : [Laptop vs. Mobile Viewership](https://datalemur.com/questions/laptop-mobile-viewership)
```sql
SELECT
  COUNT(CASE WHEN device_type = 'laptop' THEN 1 END) AS laptop_views,
  COUNT(CASE WHEN device_type IN ('phone', 'tablet') THEN 1 END) AS mobile_views
FROM viewership;
```

#### Question : [Average Post Hiatus (Part 1)](https://datalemur.com/questions/sql-average-post-hiatus-1)
```sql
SELECT
  user_id,
  DATEDIFF(MAX(post_date), MIN(post_date)) AS days_between
FROM posts
WHERE post_date >= '2021-01-01'
  AND post_date < '2022-01-01'
GROUP BY user_id
HAVING COUNT(post_id) >= 2;
```

#### Question : [Teams Power Users](https://datalemur.com/questions/teams-power-users)
```sql
SELECT sender_id, count(message_id) as message_count FROM messages 
WHERE sent_date >= '08/01/2022'
AND sent_date < '09/01/2022'
Group  BY sender_id 
ORDER BY message_count DESC
LIMIT 2
```

#### Question : [Duplicate Job Lisitings](https://datalemur.com/questions/duplicate-job-listings)
```sql
SELECT COUNT(DISTINCT company_id) AS duplicate_companies
FROM (
  SELECT 
    company_id, 
    title, 
    description, 
    COUNT(job_id) AS job_count
  FROM job_listings
  GROUP BY company_id, title, description
) AS job_count_cte
WHERE job_count > 1;
```

#### Question : [Cities With Completed Trades](https://datalemur.com/questions/completed-trades)
```sql
select u.city, count(*) as total_orders from users as u
inner join trades t 
on u.user_id = t.user_id
WHere t.status = 'Completed'
GROUP BY u.city
ORDER by total_orders DESC
LIMIT 3
```

#### Question : [Average Review Ratings](https://datalemur.com/questions/sql-avg-review-ratings)
```sql
SELECT 
  EXTRACT(MONTH FROM submit_date) as mth, 
  product_id as product,
  ROUND(AVG(stars),2) as avg_stars
FROM reviews
GROUP BY product, mth
ORDER BY mth ASC
```

#### Question : [Well Paid Employees](https://datalemur.com/questions/sql-well-paid-employees)
```sql
WITH companydetails AS (SELECT
  emp.employee_id as employee_id,
  emp.name as employee_name,
  emp.salary as employee_salary,
  mgr.employee_id as manager_id,
  mgr.name as manager_name,
  mgr.salary as manager_salary
FROM employee as emp
INNER JOIN employee as mgr
  ON mgr.employee_id = emp.manager_id)
  
SELECT employee_id, employee_name from companydetails 
WHERE employee_salary>manager_salary
```

#### Question : [App Click through Rate](https://datalemur.com/questions/click-through-rate)
```sql
WITH CTR_RATE as (SELECT
  app_id,
  COUNT(CASE WHEN event_type = 'impression' THEN 1 END) AS impressions,
  COUNT(CASE WHEN event_type = 'click' THEN 1 END) AS clicks
FROM events
WHERE timestamp >= '2022-01-01' 
  AND timestamp < '2023-01-01'
GROUP BY app_id)

select app_id, ROUND((100.0* clicks/NULLIF(impressions,0)),2) from CTR_RATE
```

#### Question : [Final Account Balance](https://datalemur.com/questions/final-account-balance)
```sql
with banktransaction as (select 
  account_id,
  SUM(CASE WHEN transaction_type = 'Deposit' THEN amount END) as deposit_amount,
  SUM(CASE WHEN transaction_type = 'Withdrawal' THEN amount END) as Withdrawal_amount
FROM transactions
GROUP BY account_id)

select account_id, (deposit_amount-withdrawal_amount) as final_balance from banktransaction
```

#### Question : [Second Data Confirmation](https://datalemur.com/questions/second-day-confirmation)
```sql
WITH tiktokDates as (SELECT
  e.email_id,
  e.user_id,
  e.signup_date,
  t.action_date
FROM emails e
INNER JOIN texts t
ON e.email_id = t.email_id
WHERE t.signup_action = 'Confirmed')

SELECT user_id from tiktokDates
WHERE DATEDIFF(action_date, signup_date) = 1
```

#### Question : [IBM db2 Product Analytics](https://datalemur.com/questions/sql-ibm-db2-product-analytics)
```sql
WITH employee_queries AS (
  SELECT 
    e.employee_id,
    COALESCE(COUNT(DISTINCT q.query_id), 0) AS unique_queries
  FROM employees AS e
  LEFT JOIN queries AS q
    ON e.employee_id = q.employee_id
      AND q.query_starttime >= '2023-07-01T00:00:00Z'
      AND q.query_starttime < '2023-10-01T00:00:00Z'
  GROUP BY e.employee_id
)

SELECT
  unique_queries,
  COUNT(employee_id) AS employee_count
FROM employee_queries
GROUP BY unique_queries
ORDER BY unique_queries;
```
