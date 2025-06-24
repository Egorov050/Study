https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.factorize.html


test = test.groupby('Pl').apply(lambda x: x.sort_values(by = 'Posts Per Week')).reset_index(drop=True)

docs 
