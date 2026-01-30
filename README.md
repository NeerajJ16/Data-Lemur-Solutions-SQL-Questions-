# Data-Lemur-Solutions-SQL-Questions-

The questions are linked directly, and the solutions are also provided, solved using my approach.
Some problems are in MySQL, and some are in PostgreSQL 14, since a few problems only have a PostgreSQL compiler available in the code editor.

## Level:Easy

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

#### Question : [Data Science Skills](https://datalemur.com/questions/matching-skills)
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

#### Question : [Cards Issued Difference](https://datalemur.com/questions/cards-issued-difference)
```sql
select 
  card_name,
  max(issued_amount) - min(issued_amount) as difference
from monthly_cards_issued 
group by card_name
order by difference DESC;
```

#### Question : [Compressed Mean](https://datalemur.com/questions/alibaba-compressed-mean)
```sql
WITH itemCalculations AS (SELECT 
SUM(item_count * order_occurrences) as total,
SUM(order_occurrences) as orderTotal
FROM items_per_order)

SELECT 
  ROUND(
    total::numeric / NULLIF(ordertotal, 0),
    1
  ) AS mean
FROM itemCalculations;
```

#### Question : [Pharmacy Analytics (Part 1)](https://datalemur.com/questions/top-profitable-drugs)
```sql
SELECT 
drug,
total_sales - cogs as total_sales
FROM pharmacy_sales
ORDER BY total_sales DESC
LIMIT 3
```

#### Question : [Pharmacy Analytics (Part 2)](https://datalemur.com/questions/non-profitable-drugs)
```sql
SELECT
  manufacturer,
  COUNT(*) AS drug_count,
  ABS(SUM(total_sales - cogs)) AS total_loss
FROM pharmacy_sales
WHERE total_sales - cogs < 0
GROUP BY manufacturer
ORDER BY total_loss DESC;
```

#### Question : [Pharmacy Analytics (Part 3)](https://datalemur.com/questions/total-drugs-sales)
```sql
SELECT
manufacturer,
CONCAT('$',ROUND(sum(total_sales)/1000000), ' million') as sales
from pharmacy_sales
GROUP BY manufacturer
ORDER BY sum(total_sales) DESC, manufacturer
```

#### Question : [Patient Support Analysis (Part 1)](https://datalemur.com/questions/frequent-callers)
```sql
WITH policy as (SELECT 
count(policy_holder_id) as user_count,
count(*) as call_count
FROM callers
GROUP BY policy_holder_id
HAVING count(*)>=3)

SELECT count(user_count) AS policy_holder_count from policy
```

## Level:Medium

#### Question : [User's Third Transaction](https://datalemur.com/questions/sql-third-transaction)
```sql
WITH transactionscount AS (SELECT 
  user_id, 
  spend, 
  transaction_date, 
  ROW_NUMBER() OVER (
    PARTITION BY user_id ORDER BY transaction_date) AS row_num
FROM transactions)

SELECT user_id, 
  spend, 
  transaction_date FROM transactionscount WHERE row_num = 3
```

#### Question : [Second Highest Salary](https://datalemur.com/questions/sql-second-highest-salary)
```sql
SELECT MAX(salary) AS second_highest_salary
FROM employee
WHERE salary < (
    SELECT MAX(salary)
    FROM employee
);
```

#### Question : [Sending vs. Opening Snaps](https://datalemur.com/questions/time-spent-snaps)
```sql
WITH calculations as (SELECT 
  user_id,
  SUM(CASE WHEN activity_type = 'open' THEN time_spent END) as open_time,
  SUM(CASE WHEN activity_type = 'send' THEN time_spent END) as send_time
FROM activities
GROUP BY user_id)

SELECT  
  a.age_bucket,
  ROUND((c.send_time / (c.send_time + c.open_time)*100.0),2) AS send_perc,
  ROUND((c.open_time / (c.send_time + c.open_time)*100.0),2) AS open_perc
FROM age_breakdown a
INNER JOIN calculations c
  ON a.user_id = c.user_id
```

#### Question : [Tweet's Rolling Average](https://datalemur.com/questions/rolling-average-tweets)
```sql
SELECT
 user_id,
 tweet_date,
 ROUND (AVG(tweet_count) OVER(
 PARTITION BY user_id
 ORDER BY tweet_date
 ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
 ), 2) as rolling_avg_3d
from tweets
```

#### Question : [Highest Grossing Items](https://datalemur.com/questions/sql-highest-grossing)
```sql
WITH rankingCategory AS (SELECT 
  category,
  product, 
  sum(spend) as total_spend,
  ROW_NUMBER() OVER(
  PARTITION BY category ORDER BY sum(spend) desc) AS ranking
FROM product_spend
WHERE YEAR(transaction_date) = 2022
GROUP BY  category, product)

SELECT 
  category,
  product,
  total_spend
from rankingCategory
WHERE ranking <=2
ORDER BY category, ranking;
```

#### Question : [Top Three Salaries](https://datalemur.com/questions/sql-top-three-salaries)
```sql
WITH department_ranking as (SELECT 
 department_id,
 name,
 salary,
 DENSE_RANK() OVER(
 PARTITION BY department_id
 ORDER BY salary DESC
 ) as rankings
FROM employee)

SELECT
  d.department_name,
  dr.name,
  dr.salary
from department_ranking dr
INNER JOIN department d
 ON dr.department_id = d.department_id
WHERE dr.rankings <=3
ORDER BY d.department_name,dr.rankings, dr.name
```

#### Question : [Spotify Streaming History](https://datalemur.com/questions/spotify-streaming-history)
```sql
WITH spotifyhistory AS (
  SELECT 
    user_id, 
    song_id, 
    song_plays
  FROM songs_history

  UNION ALL

  SELECT 
    user_id, 
    song_id, 
    COUNT(*) AS song_plays
  FROM songs_weekly
  WHERE listen_time <= '2022-08-04 23:59:59'
  GROUP BY user_id, song_id
)
SELECT 
  user_id, 
  song_id, 
  SUM(song_plays) AS song_plays
FROM spotifyhistory
GROUP BY user_id, song_id
ORDER BY song_plays DESC;
```
