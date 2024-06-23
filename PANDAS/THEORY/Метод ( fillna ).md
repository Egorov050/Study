Метод `fillna()` в библиотеке Pandas используется для замены пропущенных значений (NaN) на заданное значение. Простой пример использования метода `fillna()` для замены одного значения на другое выглядит так:

```python 
import pandas as pd
import numpy as np

# Создаем пример DataFrame с NaN значениями
data = {'A': [1, 2, np.nan, 4, 5],
        'B': [np.nan, 10, 20, np.nan, 50]}
df = pd.DataFrame(data)

# Заменяем NaN значение в столбце 'A' на 0
df['A'].fillna(0, inplace=True)

# Выводим DataFrame после замены
print(df)
```

