# Data-Lemur-Solutions-SQL-Questions-

Question are the link to questions directly and then there are solutions too solved (using my approach)

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
