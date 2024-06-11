```python 
# Разделяем данные на интервалы
bins = [0, 30, 50, 100]  # Границы интервалов
labels = ['Молодежь', 'Взрослые', 'Пожилые']  # Метки категорий
df['Возрастная группа'] = pd.cut(df['Возраст'], bins=bins, labels=labels)
```

или 

```python 

goods_df['LandAreaCategory'] = pd.cut(goods_df['Total Land Area (acres)'], bins=[0, 50, 100, 200, float('inf')], 
labels=['Small', 'Medium', 'Large', 'Extra Large'])
```

или мы можем просто указать

```python

labels = ['Молодежь', 'Взрослые', 'Пожилые']  # Метки категорий
df['Возрастная группа'] = pd.cut(df['Возраст'], bins=30, labels=labels)
```
