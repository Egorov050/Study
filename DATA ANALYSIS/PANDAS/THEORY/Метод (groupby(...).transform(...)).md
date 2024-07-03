```python 
df['Total'] = df.groupby('Group', as_index=False)['Value'].transform('sum')
```

<h2>ДЛЯ ЧЕГО?</h2>
Метод<span style="color:rgb(253, 165, 15)"> transform() </span>позволяет применять произвольные функции к каждой группе данных. Это может быть любая функция, которая может быть применена к серии данных, например, встроенные функции pandas (<span style="color:rgb(253, 165, 15)"> sum()</span>, <span style="color:rgb(253, 165, 15)"> count()</span>, <span style="color:rgb(253, 165, 15)"> mean()</span>, <span style="color:rgb(253, 165, 15)"> max()</span>, <span style="color:rgb(253, 165, 15)"> min()</span> и т. д.), пользовательские функции или анонимные функции (lambda-функции)

```python 
data = {
    'Группа': ['A', 'A', 'B', 'B', 'B'],
    'Значение': [1, 2, 3, 4, 5]
}
df = pd.DataFrame(data)

def subtract_mean(x):
    return x - x.mean()
df['Отклонение от среднего'] = df.groupby('Группа')['Значение'].transform(subtract_mean)

```

По итогу, у нас выведится : 

```python 
  Группа  Значение  Отклонение от среднего
0      A         1                    -0.5
1      A         2                     0.5
2      B         3                    -1.0
3      B         4                     0.0
4      B         5                     1.0
```

Функций считает среднее по каждой группе, а потом для каждого значения, принадлежащего этой группе высчитывает отклонение от среднего значения.

