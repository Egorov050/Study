Для перевода строки в формат datetime, нам нужно : 

```python 

df['rowupdate'] = df['rowupdate'].apply(lambda x : datetime.strptime(x, "%Y-%m-%d"))

```

, где в `strptime` в скобках мы прописываем как сейчас выглядит запись, которую мы 