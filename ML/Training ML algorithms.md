1. <span style="color:rgb(253, 165, 15)">Обучающая выборка</span> (Training sample): это та часть данных, на которой модель машинного обучения обучается. Модель анализирует эти данные и на основе них вырабатывает правила и закономерности для предсказания результатов.
2. <span style="color:rgb(253, 165, 15)">Тестовая выборка</span>(Test sample): это та часть данных, на которой оценивается качество модели после ее обучения и настройки. Тестовая выборка не использовалась при обучении модели, поэтому она позволяет оценить, насколько хорошо модель обобщает на новые, ранее не виденные данные. Тестовая выборка используется для оценки производительности модели в реальных условиях.

![[Pasted image 20240613172626.png]]
Для начала мы должны разделить наш sample на <span style="color:rgb(253, 165, 15)">target</span> и <span style="color:rgb(253, 165, 15)">features</span>. 
<span style="color:rgb(253, 165, 15)">Target</span>- это переменная, которую мы хотим предсказать.
<span style="color:rgb(253, 165, 15)">Features</span> - это переменные, которые мы используем для предсказания target.

Обычно :
<span style="color:rgb(253, 165, 15)">y</span> -  <span style="color:rgb(253, 165, 15)">target</span>
<span style="color:rgb(253, 165, 15)">X</span> - <span style="color:rgb(253, 165, 15)">features</span>

Разделить мы можем простым способом :

```python 
X=df_t.drop(['TenYearCHD'], axis=1)
y=df_t['TenYearCHD']
```

Здесь у нам X - это <span style="color:rgb(253, 165, 15)">features</span>, y - это  <span style="color:rgb(253, 165, 15)">target</span>. 
В таблице, которая содержит features,  <span style="color:rgb(253, 165, 15)">НЕ ДОЛЖНО БЫТЬ </span> target

Далее мы должны разделить <span style="color:rgb(253, 165, 15)">target</span> и <span style="color:rgb(253, 165, 15)">features</span> на <span style="color:rgb(253, 165, 15)">training set</span> и <span style="color:rgb(253, 165, 15)">test set</span>. 
Для этого мы используем `train_test_split` из библиотеки `sklearn.model_selection` : 

```python 
from sklearn.model_selection import train_test_split
```

Далее , мы непосредственно делим. Обычно мы делим следующими пропорциями : 
80% - <span style="color:rgb(253, 165, 15)">training set</span>
20% - <span style="color:rgb(253, 165, 15)">testing set</span>

Делаем это вот таким способом : 
```python 
X_train, X_test, y_train, y_test = train_test_split(X,y,test_size=0.2 ,random_state=13)
```


