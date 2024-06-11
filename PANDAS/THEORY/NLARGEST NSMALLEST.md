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
print("\nTop 3 smallest values in column 'B':")
print(top_3_smallest)
```

