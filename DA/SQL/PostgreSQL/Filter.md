
Конструкция выглядит следующим способом : 

```SQL 
aggregate_function(column) filter (where condition)
```

например : 

```SQL 
select
    s.user_id
  , round(count(action) filter (where action = 'confirmed') / count(*)::numeric, 2) as confirmation_rate
from
    signups as s
```

