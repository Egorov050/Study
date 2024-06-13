1. <span style="color:rgb(253, 165, 15)">Обучающая выборка</span> (Training sample): это та часть данных, на которой модель машинного обучения обучается. Модель анализирует эти данные и на основе них вырабатывает правила и закономерности для предсказания результатов.
2. <span style="color:rgb(253, 165, 15)">Тестовая выборка</span>(Test sample): это та часть данных, на которой оценивается качество модели после ее обучения и настройки. Тестовая выборка не использовалась при обучении модели, поэтому она позволяет оценить, насколько хорошо модель обобщает на новые, ранее не виденные данные. Тестовая выборка используется для оценки производительности модели в реальных условиях.

![[Pasted image 20240613172626.png]]
Для начала мы должны разделить наш sample на <span style="color:rgb(253, 165, 15)">target</span> и <span style="color:rgb(253, 165, 15)">features</span>. 
<span style="color:rgb(253, 165, 15)">Target</span>- это переменная, которую мы хотим предсказать.
<span style="color:rgb(253, 165, 15)">Features</span> - это переменные, которые мы используем для предсказания target.

Обычно :
	<span style="color:rgb(253, 165, 15)">y</spa - это target
	X - features

Разделить мы можем простым способом :

```python 
X=df_t.drop(['TenYearCHD'], axis=1)
y=df_t['TenYearCHD']
```

Здесь у нам X - это features, y - это  target. 
В таблице, которая содержит features,  НЕ ДОЛЖНО БЫТЬ target

Далее мы должны разделить target и features на training set и test set. 
Для этого мы используем train_test_split из библиотеки sklearn.model_selection : 

```python 
from sklearn.model_selection import train_test_split
```

