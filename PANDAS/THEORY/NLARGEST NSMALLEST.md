```python 

# Sample DataFrame
df = pd.DataFrame({'A': [1, 2, 3, 4, 5],
                   'B': [10, 9, 8, 7, 6]})

# Using nlargest
top_3_largest = df['B'].nlargest(3)
print("Top 3 largest values in column 'B':")
print(top_3_largest)

# Using nsmallest
top_3_smallest = df['B'].nsmallest(3)
print("Top 3 smallest values in column 'B':")
print(top_3_smallest)
```

<span style="color:rgb(253, 165, 15)">nsmallest()</span>: Эта функция извлекает самые маленькие N значения из DataFrame или Series. соответственно <span style="color:rgb(253, 165, 15)">nlargest</span> , n самых больших значений