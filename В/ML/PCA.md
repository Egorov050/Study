
```python
import numpy as np
import matplotlib.pyplot as plt
from sklearn.decomposition import PCA
from sklearn.datasets import load_iris
from sklearn.preprocessing import StandardScaler

# Загрузим пример данных - датасет Iris
data = load_iris()
X = data.data
y = data.target

# Стандартизация данных
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)

# Применение PCA
pca = PCA(n_components=2)  # Проецирование данных в двумерное пространство
X_pca = pca.fit_transform(X_scaled)

# Визуализация результатов
plt.figure(figsize=(8, 6))
for i in np.unique(y):
    plt.scatter(X_pca[y == i, 0], X_pca[y == i, 1], label=data.target_names[i])

plt.xlabel('Первая главная компонента')
plt.ylabel('Вторая главная компонента')
plt.legend()
plt.title('PCA на Iris датасете')
plt.show()

# Вывод доли объясненной дисперсии
print("Доли объясненной дисперсии:", pca.explained_variance_ratio_)

```

