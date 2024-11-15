В SQL конструкция `HAVING` используется для фильтрации результатов, полученных после группировки данных с помощью `GROUP BY`. Она работает подобно `WHERE` , но применяется к агрегированным данным.

Например : 

```SQL
SELECT region, SUM(revenue) AS total_revenue
FROM sales
GROUP BY region
HAVING SUM(revenue) > 10000;
```