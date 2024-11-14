
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

Но есть другое решение этой задачи : 

```SQL 
select s.user_id, round(avg(if(c.action="confirmed",1,0)),2) as confirmation_rate from Signups as s left join Confirmations as c on s.user_id= c.user_id group by user_id;

```

