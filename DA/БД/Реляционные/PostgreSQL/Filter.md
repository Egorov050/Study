
Конструкция выглядит следующим способом : 

```SQL 
aggregate_function(column) filter (where condition)
```

например : 

```SQL 
SELECT s.user_id , 
ROUND(count(c.action) filter (where c.action = 'confirmed') / count(s.time_stamp)::numeric ,2) as confirmation_rate 
from Signups s
left join Confirmations c 
on s.user_id = c.user_id
group by s.user_id
```

Ссылка на задачу где это использовалось : https://leetcode.com/problems/confirmation-rate/submissions/1452653627/?envType=study-plan-v2&envId=top-sql-50





структура использования фильтра : 

```SQL
select query_name , ROUND(AVG(rating*1.0 / position),2) AS quality ,

ROUND(count(rating) filter (where rating < 3) *1.0 / count(*) , 4) * 100 AS poor_query_percentage

from Queries

where query_name is not null

GROUP BY query_name

```


