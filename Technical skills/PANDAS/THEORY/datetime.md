Для перевода строки в формат datetime, нам нужно : 

```python 

df['rowupdate'] = df['rowupdate'].apply(lambda x : datetime.strptime(x, "%Y-%m-%d"))

```

, где в `strptime` в скобках мы прописываем как сейчас выглядит запись, которую мы хотим переделать. 



Затем, когда мы переделали это в datetime, мы уже используем `strftime`. Наприме, чтобы получить год, мы пишем : 

```python 

df['update_year'] = df['rowupdate'].apply(lambda x : datetime.strftime(x, "%Y")).astype('int64')

```

