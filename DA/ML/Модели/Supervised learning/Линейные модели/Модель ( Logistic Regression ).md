`LogisticRegression` — это метод машинного обучения, используемый для решения задач классификации. Он принадлежит к классу линейных моделей и особенно хорошо подходит для бинарной классификации (когда целевая переменная имеет два возможных значения).

### Где и когда используется LogisticRegression

Бинарная классификация : Логистическая регрессия применяется для задач, где необходимо классифицировать данные на две категории. Примеры включают:
-  Определение, является ли электронное письмо спамом или нет.
- Прогнозирование, одобрит ли банк кредит.
- Предсказание, выживет ли пассажир на Титанике.

<h2>Как мы это делаем?</h2>
Импортируем библиотеку, которая нам нужна?

```python 
from sklearn.linear_model import LogisticRegression
```

Мы как обычно выделяем ключевую метрику, делим наш датасет на тренировочнный и тестовый. Затем мы его обязательно скейлим : 

```python 
from sklearn.preprocessing import StandardScaler
scaler = StandardScaler()
scaler.fit(X_train)

X_train = scaler.transform(X_train)
X_test = scaler.transform(X_test)
```

Отлично, теперь мы можем приступить к тренировке нашей модели : 

```python 

model_LR = LogisticRegression()
model_LR.fit(X_train, y_train)

y_pred_test_LR = model_LR.predict(X_test)
y_pred_train_LR = model_LR.predict(X_train)
```



