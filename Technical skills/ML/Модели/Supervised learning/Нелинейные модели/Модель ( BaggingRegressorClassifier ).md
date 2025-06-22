Ансамбль регрессоров на основе **Bootstrap Aggregating** (bagging):

По сути алгоритм связан с Random forest и работает по тому же самому принципу, одна random forest на каждом сплите в дереве выбирает признак рандомно, в то время как в begging regressor обыно random forest также ищет лучший праметр для сплита, просто у нас таких деревье много. 

Используется также бутстрап, что позволяет после завершения считать OOB-error, так как 37% из выборки так и не попадут никуда. 

Ключевые параметры : 

| **Параметр**       | **Описание**                                                          | **По умолчанию**       |
| ------------------ | --------------------------------------------------------------------- | ---------------------- |
| n_estimators       | Число базовых моделей B                                               | 10                     |
| base_estimator     | Тип модели для каждого бутстрэп-сэмпла (обычно DecisionTreeRegressor) | DecisionTreeRegressor  |
| bootstrap          | Использовать ли бутстрэп (сэмплинг с возвращением)                    | True                   |
| max_samples        | Доля или число образцов в бутстрэп-выборке                            | 1.0                    |
| bootstrap_features | Сэмплировать ли признаки (для фантазийного “random bagging”)          | False                  |
| oob_score          | Вычислять ли OOB-оценку (см. ниже)                                    | False                  |
| n_jobs             | Число параллельных процессов                                          | None (последовательно) |

```python

from sklearn.ensemble import BaggingRegressor
from sklearn.tree import DecisionTreeRegressor
from sklearn.metrics import mean_squared_error

bag = BaggingRegressor(
    base_estimator=DecisionTreeRegressor(),
    n_estimators=20,
    oob_score=True,
    random_state=42,
    n_jobs=-1
)
bag.fit(X_train, y_train)

print("OOB R²:", bag.oob_score_)
print("OOB MSE:", mean_squared_error(y_train, bag.oob_prediction_))
print("Test MSE:", mean_squared_error(y_test,  bag.predict(X_test)))


```




Тоже самое имеется для классификации : 

```python
from sklearn.ensemble import BaggingClassifier
from sklearn.tree import DecisionTreeClassifier
from sklearn.datasets import make_classification
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score

# 1) Синтетические данные
X, y = make_classification(n_samples=500, n_features=10, random_state=42)
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.3, random_state=42
)

# 2) BaggingClassifier с 20 деревьями и OOB
bag_clf = BaggingClassifier(
    base_estimator=DecisionTreeClassifier(),
    n_estimators=20,
    oob_score=True,
    random_state=42,
    n_jobs=-1
)
bag_clf.fit(X_train, y_train)

# 3) Оценки
print("OOB accuracy:", bag_clf.oob_score_)
print("Test  accuracy:", accuracy_score(y_test, bag_clf.predict(X_test)))

```


