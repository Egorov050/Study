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




Тоже самое имеется для классификации 