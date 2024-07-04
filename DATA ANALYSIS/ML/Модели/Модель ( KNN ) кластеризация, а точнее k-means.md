Прикол, `knn` может использоваться не только для классификации и  регрессии но и для кластеризации. 

K-means лучше работает с шарообразными структурами данных. 
В отличие от `DBSCAN` , в `k-means` мы должны указать сколько кластеров будем искать. Как выбрать оптимальное количество кластеров мы рассмотрим чуть позже. 

*Как работает алгоритм хорошо описано здесь : https://www.youtube.com/watch?v=EHZJMz6zyFE&t=453s*

Перед тем, как тренировать модель, мы должны провести `некоторые манипуляции` с данными, а именно : 
1)  Удалить выбросы [[Удаление выбросов ( LocalOutlierFactor )]]
2) Подготовить данные [[Подготовка данных]], в том числе и масштабировать их
3) Обработать категориальные данные 
*( База )*

Далее, `инициализируем и тренируем` нашу модель : 

```python 
from sklearn.cluster import KMeans

k = 3
kmeans = KMeans(n_clusters=k, random_state=42)
data_clean['Cluster'] = kmeans.fit_predict(data_clean)
```


Далее, можем визуализировать данные : 

```python

fig = plt.figure(figsize=(8, 6))
ax = fig.add_subplot(111, projection='3d')

ax.scatter(data_clean['Recency'], data_clean['Monetary'], data_clean['Frequency'], c=data_clean['Cluster'], cmap='viridis')
ax.set_xlabel('Feature1')
ax.set_ylabel('Feature2')
ax.set_zlabel('Feature3')
plt.title('K-means Clustering')
plt.show()

```

И получить вот такое : 

![[Screenshot 2024-07-04 at 18.01.07.png]]

Теперь самое интересное, как нам выбрать количество кластеров.

Самое простое, это взглянуть на данные и оценочкой глаза самому подобрать количество кластеров. 
