
```python

cohort = df[df['eventName'] == 'register'].groupby('registration_week').agg(
    users=('userId', 'nunique')
).reset_index()

```
