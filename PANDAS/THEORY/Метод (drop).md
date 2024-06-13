Для того, что бы удалить колонку(и) , мы можем написать : 

```python 
df.drop(columns='column_name', inplace=True)
```

или 

```python 
df = df.drop(columns=['column_name'])
```

Для того, чтобы удалить 