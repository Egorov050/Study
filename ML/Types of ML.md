Machine learning is the science of finding patterns in data using a computer and mathematics

We can divide classical ML tasks into : 
- Supervised 
- Unsupervised
<h1> Supervised </h1>
Is about finding an unknown dependency
_X_ - the set of all objects in the feature space
_Y_ - target variable range of values
ƒ : X → Y - unknown pattern, function. 

![[Pasted image 20240613163143.png]]

**Обучение с учителем** предполагает использование размеченных данных, где каждому входному примеру соответствует известный выход (цель). Целью является построение модели, которая может предсказывать выходные значения для новых входных данных. Основные типы задач в этом подходе — это регрессия и классификация.

<h1> Unsupervised </h1>
In contrast to supervised learning problems, in classic unsupervised learning problems there is _X_, but there is no training sample (i.e. we do not know the correct answers).  
In such problems, we usually minimize the “entropy” of the system: we look for the most successful placement of labels.

Обучение без учителя (unsupervised learning) — это тип машинного обучения, при котором модели обучаются на неразмеченных данных, т.е. у нас есть только входные данные (X), но нет известных выходных значений (Y). Цель этого подхода — выявить скрытые структуры, паттерны или отношения в данных.

