```python 

df.query("color == 'red' ").goupby('model').agg({'id' : 'count'})
```

## ДЛЯ ЧЕГО?

Позволяет делает фильтр перед группировкой.  По вмещаем л