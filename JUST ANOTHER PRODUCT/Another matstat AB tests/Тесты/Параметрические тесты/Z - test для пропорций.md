В контексте A/B-тестирования Z-тест часто используется для проверки, есть ли значимая разница в конверсии или другом ключевом показателе между контрольной группой (A) и экспериментальной группой (B).

Итак , пусть $P_a$ - пропорция успехов в группе А. $n_a$ - размер выборки.  $P_b$ - пропорция успехов в группе B , а $n_b$ - размер выборки B. 

Дак вот , `z - value` мы можем рассчитать по следующей формуле : 

$$
Z = \frac{p_A - p_B}{\sqrt{p(1 - p) \left(\frac{1}{n_A} + \frac{1}{n_B}\right)}}
$$
Где:

$$
p = \frac{x_A + x_B}{n_A + n_B}
$$

где $x_A​$ и  $x_B$​ — количество успехов (конверсий) в группах A и B соответственно.

<h6>Как это сделать на питоне? </h6>

Для этого импортируем `import statsmodels.api as sm` и проведем тест : 

```python
import statsmodels.api as sm

# Количество успехов
success_A = 30
success_B = 40

# Общее количество наблюдений
trials_A = 100
trials_B = 120

# Выполнение Z-теста
z_stat, p_value = sm.stats.proportions_ztest([success_A, success_B], [trials_A, trials_B])

# Вывод результатов
print(f"Z-statistic: {z_stat}")
print(f"P-value: {p_value}")

```

Для того , чтобы получить значения , мы можем воспользоваться : 

```python
contingency_table = pd.crosstab(df['version'], df['retention_1'])
contingency_table
```

И далее выдергивать значения из таблицы : 

```python
success_A = contingency_table[0][1]
success_B = contingency_table[1][1]

trials_A = contingency_table[0][1] + contingency_table[0][0]
trials_B = contingency_table[1][1] + contingency_table[1][0]
```

