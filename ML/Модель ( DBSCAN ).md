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
from sklearn.cluster import DBSCAN
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

.... мы можем перейти к тренировке нашей модели : 

