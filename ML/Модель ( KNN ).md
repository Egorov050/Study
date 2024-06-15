K-Nearest Neighbors (KNN) — это один из простых и понятных алгоритмов машинного обучения, используемый как для задач классификации, так и для задач регрессии. Основная идея KNN заключается в том, что объект классифицируется на основе большинства его k ближайших соседей в пространстве признаков. Вот основные аспекты KNN:
<h6>Принцип работы :</h6>
При классификации новый объект относится к тому классу, который является наиболее частым среди k ближайших соседей.

При регрессии значение для нового объекта вычисляется как среднее значение его k ближайших соседей.

![[Pasted image 20240615131901.png]]


<h2>Регрессия</h2>
Итак, импортируем нужные библиотеки 

```python 
import pandas as pd
import seaborn as sns
import matplotlib as plt
from sklearn.model_selection import train_test_split
from sklearn.neighbors import KNeighborsRegressor
```

Далее мы подготавливаем наши данные см ( [[Подготовка данных]] )
Как только мы почистили , привели данные в порядок, обработали категориальные данные [[Обработка категориальных данных]],  приступим к сплитованию :
см ( [[Сплит данных перед моделью]] )

Как только наши данные подготовлены, можем приступить к обучению модели : 

```python 
kn = KNeighborsRegressor()
kn.fit(X_train, y_train)
```

Модель наша обучилась, далее мы засовываем `X_test` в нашу модель , мы получим `y_predict` и затем сравниваем `y_predict` с `y_test` : 

```python 
y_pred = kn.predict(X_test)
```

Затем, мы можем смотреть результат используя метрики [[Метрики]]


<h2>Классификация</h2>
```python 

import pandas as pd
import seaborn as sns
import matplotlib as plt
from sklearn.model_selection import train_test_split
from sklearn.neighbors import KNeighborsRegressor
```