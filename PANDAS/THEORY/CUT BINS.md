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

или мы можем просто указать на сколько колонок мы хотим поделить. 

```python

df['Возрастная группа'] = pd.cut(df['Возраст'], bins=30, labels=labels)
```

<h2>Пример</h2>
```python 
data = {
    'Name': ['Alice', 'Bob', 'Charlie', 'David', 'Eva'],
    'Age': [23, 45, 18, 34, 56]
}

df = pd.DataFrame(data)

# Определяем бины для возрастных групп
bins = [0, 18, 35, 50, 100]
labels = ['0-18', '19-35', '36-50', '51+']

# Разбиваем данные на группы
df['Age Group'] = pd.cut(df['Age'], bins=bins, labels=labels)

print(df)
```

Этот код создаст DataFrame и добавит новый столбец <span style="color:rgb(253, 165, 15)">Age Group</span>, в котором каждому возрасту будет присвоена соответствующая возрастная группа.

Вывод будет примерно таким:

```python 
      Name  Age Age Group
0    Alice   23     19-35
1      Bob   45     36-50
2  Charlie   18      0-18
3    David   34     19-35
4      Eva   56       51+
```

