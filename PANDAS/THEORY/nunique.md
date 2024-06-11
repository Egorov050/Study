
```python

cohort = cohort.groupby(['first', 'date']).agg({'uid' : 'nunique'}).reset_index()
```

Здесь nunique подсчитает количество уникальных значений в столбце uid.


