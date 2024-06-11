```python 

df.query("color == 'red' ").groupby('model').agg({'id' : 'count'})
```

## ДЛЯ ЧЕГО?

Позволяет делает фильтр перед группировкой.  По факту мы вмещаем любой фильтр до группирровки и все это делаем в одну строку. По другому код бы выглядел так : 

```python 
df_only_red = df[df['color'] == 'red']
df_only_red.groupby('model').agg({'id' : 'count'})
```
