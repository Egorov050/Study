Основное преймущество этой библиотеки это интерективность!

Итак, импортируем : 

   ```python
   import plotly.express as px
   ```

<h6>Пример кода</h6>

Давайте разберем :

```python
fig = px.box(data_clean, x='Cluster', y='Monetary',
             title="Cluster ID vs Amount Box Plot",
             labels={'Clusters': 'Cluster ID', 'Monetary': 'Amount'}, color = 'Cluster')

fig.update_layout(width=800, height=600)

fig.show()
```

1. **`px.box`**: Создает box plot (коробчатую диаграмму), которая показывает распределение данных и помогает выявить выбросы.
   - **`data_clean`**: DataFrame с данными.
   - **`x='Cluster'`**: Ось X, представляющая кластеры.
   - **`y='Monetary'`**: Ось Y, представляющая монетарные значения.
   - **`title`**: Заголовок графика.
   - **`labels`**: Словарь для замены меток осей.
   - **`color`**: Цветовая кодировка по кластеру.

2. **`fig.update_layout`**: Обновляет макет графика.
   - **`width` и `height`**: Устанавливают ширину и высоту графика.

3. **`fig.show()`**: Отображает график в интерактивном окне.

### Пример другого графика

Например, создание scatter plot:

```python
import plotly.express as px

# Пример данных
df = px.data.iris()

# Создание scatter plot
fig = px.scatter(df, x='sepal_width', y='sepal_length', color='species',
                 title="Iris Sepal Width vs Length",
                 labels={'sepal_width': 'Sepal Width (cm)', 'sepal_length': 'Sepal Length (cm)'})

fig.show()
```

<h6> Основные параметры и функции</h6>
- **Типы графиков**: `scatter`, `line`, `bar`, `box`, `histogram`, `pie` и т.д.
- **Основные параметры**: `data_frame`, `x`, `y`, `color`, `size`, `hover_name`, `title`, `labels`, `template`.
- **Настройки макета**: `update_layout`, `update_traces`.

Для того, чтобы построить `3D` график, нужно просто дополнительно прописать третью ось и все : 

```python
import plotly.express as px

# Пример данных
df = px.data.iris()

# Создание 3D scatter plot
fig = px.scatter_3d(df, x='sepal_length', y='sepal_width', z='petal_width', color='species',
                    title="3D Scatter Plot of Iris Dataset")

fig.update_layout(width=800, height=600)

fig.show()

```

