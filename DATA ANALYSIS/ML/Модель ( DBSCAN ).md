<h6>DBSCAN</h6>
Density-based spatial clustering of applications with noise - плотностной алгоритм пространственной кластеризации с присутствием шума), как следует из названия, оперирует плотностью данных. На вход он просит уже знакомую матрицу близости и два параметра — радиус окрестности и количество соседей.

Освежить принцип работы алгоритма : https://www.youtube.com/watch?v=svAtnZ5XjSI

Перед тем, как начать тренировку модели. нужно : 
1)  Удалить выбросы [[Удаление выбросов ( LocalOutlierFactor )]]
2) Подготовить данные [[Подготовка данных]], в том числе и масштабировать их
3) Обработать категориальные данные 

После того, как мы сделали весь процесc... : 

```python
from sklearn.neighbors import LocalOutlierFactor
from sklearn.preprocessing import StandardScaler
from sklearn.neighbors import LocalOutlierFactor

#считаем, что данные были подготовлины и нужно только отскейлить и удалить выбросы

lof = LocalOutlierFactor(n_neighbors=20, contamination=0.1)
outliers = lof.fit_predict(data)
outliers_indices = data[outliers == -1].index
data_clean = data.drop(outliers_indices)


scaler = StandardScaler()
X_scaled = scaler.fit_transform(data_clean[['Recency', 'Frequency', 'Monetary']])
```

.... мы можем перейти к инициализации нашей модели : 

```python 
from sklearn.cluster import DBSCAN

dbscan = DBSCAN(eps=0.5, min_samples=5)
```

и к тренировке : 

```python
data_clean['Cluster'] = dbscan.fit_predict(X_scaled)
```

Далее, мы можем визуализировать как сработал наш алгоритм. Мы можем использовать как 2D графики, так и 3D. Как построить 3D смотри тут [[3D Визуализация]]. Если вкратце, то вот пример : 

```python

from mpl_toolkits.mplot3d import Axes3D
import seaborn as sns
import matplotlib.pyplot as plt



fig = plt.figure(figsize=(10,10))
ax = fig.add_subplot(111, projection='3d')

ax.scatter(data_clean['Recency'], data_clean['Monetary'], data_clean['Frequency'], c=data_clean['Cluster'])
ax.set_xlabel('Recency')
ax.set_ylabel('Monetary')
ax.set_zlabel('Frequency')

plt.show()

```

<h3>Как выбрать параметр eps ? </h3>
Для того, чтобы выбрать правильный параметр `eps`, воспользуемся `kkn`.

Давайте для начала в целом рассмотрим идею : 
-Импортируем нужные библиотеки: Импортируем `NearestNeighbors` для поиска ближайших соседей и `seaborn` для визуализации.
-Определяем `min_samples`: Устанавливаем значение `min_samples` для алгоритма DBSCAN.
-Обучаем модель `NearestNeighbors`: Создаём и обучаем модель на данных.
-Находим расстояния до ближайших соседей : Получаем массивы расстояний и индексов ближайших соседей.
-Вычисляем средние расстояния : Для каждой точки вычисляем среднее расстояние до её ближайших соседей (исключая саму точку).
-Сортируем средние расстояния : Сортируем массив средних расстояний по возрастанию.
-Визуализируем результаты: Строим график средних расстояний для визуального определения оптимального значения `eps`.


