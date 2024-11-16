`DISTINCT ON`  — это расширение SQL, которое позволяет выбирать уникальные строки из таблицы, но с некоторыми условиями по сортировке. В отличие от стандартного DISTINCT, который просто удаляет дубликаты, DISTINCT ON позволяет выбрать первую строку из каждой группы строк, которые совпадают по заданному набору столбцов. Этот оператор используется в основном в PostgreSQL, так как не является частью стандартного SQL.

```SQL
SELECT DISTINCT ON (column1, column2, ...) column1, column2, ... 
FROM table_name
ORDER BY column1, column2, ...;
```

Здесь:
- DISTINCT ON (column1, column2, ...) указывает на столбцы, которые будут определять уникальность строк. Для каждой уникальной комбинации значений этих столбцов будет выбрана только одна строка.
- ORDER BY используется для сортировки строк. Это важно, потому что DISTINCT ON выбирает первую строку в каждой группе по порядку, установленному в ORDER BY.

Например : 

```SQL
SELECT DISTINCT ON (user_id) user_id, order_id, order_date
FROM orders
ORDER BY user_id, order_date DESC;
```

Этот запрос выбирает по одной строке для каждого user_id, причем для каждого пользователя будет выбрана строка с самым поздним order_date.

*Важно, что для корректной работы запроса с DISTINCT ON должна быть прописана сортировка по тем же столбцам, что и в DISTINCT ON, иначе запрос не будет работать корректно.*

Ну или вот возможное решение задачи (https://leetcode.com/problems/immediate-food-delivery-ii/solutions/5894953/simple-solution-using-distinct-on-and-cte/?envType=study-plan-v2&envId=top-sql-50) : 

```SQL

WITH temp AS 
(SELECT DISTINCT ON (customer_id) customer_id, 
 CASE WHEN order_date = customer_pref_delivery_date THEN 1 ELSE NULL END AS "immediate", 
 CASE WHEN order_date != customer_pref_delivery_date THEN 1 ELSE NULL END AS "scheduled" 
 FROM Delivery 
 ORDER BY customer_id, order_date ) SELECT ROUND(COUNT(immediate)::NUMERIC/COUNT(*) * 100 , 2) AS immediate_percentage FROM temp

```