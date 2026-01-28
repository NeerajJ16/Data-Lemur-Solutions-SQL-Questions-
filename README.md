# Data-Lemur-Solutions-SQL-Questions-

Questions are the link to question directly and then there are solutions too solved (using my approach)

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
