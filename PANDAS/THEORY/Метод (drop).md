Для того, что бы удалить колонку(и) , мы можем : 

```python 
df.drop(columns='column_name', inplace=True)
```

или 

```python 
df = df.drop(columns=['column_name'])
```

Для того, чтобы удалить строки с определенными значениями, мы можем : 

```python 
df = df.drop(df[df['Tm'] == 'TOT'].index)
```

