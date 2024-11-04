
```python 

df['flag'] = df.apply(lambda row: 1 if row['eventName'] == 'download' and row['first_reg'] < row['LdayPer'] else 0, axis=1)

```

Как мы используем  `lambda x`