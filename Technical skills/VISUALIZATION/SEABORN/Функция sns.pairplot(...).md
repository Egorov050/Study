Такая функция позволяет создавать парные графики, которые позволяют могут помочь оценить корреляцию между переменными. Визуальное представление в виде диаграмм рассеяния на графиках вне диагонали позволяет оценить, как сильно и в каком направлении изменяются переменные друг относительно друга. Если точки на графике рассеяния вытянуты вдоль прямой линии, это может указывать на сильную положительную или отрицательную корреляцию между переменными. Если точки распределены случайно, это может указывать на отсутствие корреляции.

Также на диагонали парных графиков выводятся гистограммы или KDE-графики (ядерные оценки плотности), которые показывают распределение каждой отдельной переменной. Это помогает оценить форму и разброс значений каждой переменной.


Для того, чтобы построить такие графики, необходимо написать : 

```python 
`sns.pairplot(df[["mpg", "cylinders", "displacement", "weight"]], diag_kind="kde")`
```

Получим следующий результат : 

![[Pasted image 20240614172817.png]]

