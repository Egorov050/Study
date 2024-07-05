Основное преймущество этой библиотеки э

### Основные моменты работы с Plotly Express

1. **Установка библиотеки**:
   ```python
   pip install plotly
   ```

2. **Импорт библиотеки**:
   ```python
   import plotly.express as px
   ```

3. **Загрузка данных**:
   Plotly Express работает с различными форматами данных, такими как Pandas DataFrame, списки и массивы NumPy.

4. **Создание графиков**:
   Plotly Express предоставляет различные типы графиков, такие как scatter, line, bar, box, histogram и другие. Каждый из них имеет свои параметры и настройки.

### Пример кода

Давайте разберем ваш пример кода:

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

### Основные параметры и функции

- **Типы графиков**: `scatter`, `line`, `bar`, `box`, `histogram`, `pie` и т.д.
- **Основные параметры**: `data_frame`, `x`, `y`, `color`, `size`, `hover_name`, `title`, `labels`, `template`.
- **Настройки макета**: `update_layout`, `update_traces`.

### Преимущества Plotly Express

- **Интерактивность**: Все графики интерактивны и позволяют пользователю наводить мышкой на элементы для получения дополнительной информации.
- **Простота**: Минимальное количество кода для создания сложных графиков.
- **Широкие возможности настройки**: Возможность детальной настройки графиков и макета.

### Заключение

Plotly Express является мощным инструментом для быстрой визуализации данных. Он сочетает в себе простоту использования и широкие возможности настройки, что делает его отличным выбором как для новичков, так и для опытных аналитиков данных.