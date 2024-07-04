```python
import pandas as pd
from datetime import datetime
import seaborn as sns
import matplotlib.pyplot as plt
```

### Fistly, lets read the file 


```python
df = pd.read_excel('/Users/vladimiregorov/Downloads/Online Retail 2.xlsx')
df.head(3)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>InvoiceNo</th>
      <th>StockCode</th>
      <th>Description</th>
      <th>Quantity</th>
      <th>InvoiceDate</th>
      <th>UnitPrice</th>
      <th>CustomerID</th>
      <th>Country</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>536365</td>
      <td>85123A</td>
      <td>WHITE HANGING HEART T-LIGHT HOLDER</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>2.55</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
    </tr>
    <tr>
      <th>1</th>
      <td>536365</td>
      <td>71053</td>
      <td>WHITE METAL LANTERN</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>3.39</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
    </tr>
    <tr>
      <th>2</th>
      <td>536365</td>
      <td>84406B</td>
      <td>CREAM CUPID HEARTS COAT HANGER</td>
      <td>8</td>
      <td>2010-12-01 08:26:00</td>
      <td>2.75</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
    </tr>
  </tbody>
</table>
</div>



### Here it is. The following info columns represent : 
- `InvoiceNo` - the id of the purchase
- `StockCode` - the id of the purchased good
- `Description` - the description of the purchased good
- `Quantity` - the quantity of the purchased good
- `InvoiceDate` - the date of the purchase
- `UnitPrice` - the price of the purchased good per unit
- `CustomerID` - the id of the Customer
- `Country` - the country from which the purchase was maid

I do think that there is no reason for us to grab the `Description` data with us. Lets get rid of it


```python
df = df.drop(columns=('Description'))
df.shape
```




    (541909, 7)



### Data Cleaning

Lets see how many `null` values do we have : 


```python
df.isna().sum()
```




    InvoiceNo           0
    StockCode           0
    Quantity            0
    InvoiceDate         0
    UnitPrice           0
    CustomerID     135080
    Country             0
    dtype: int64



Lets drop rows where `CustomerID` is null


```python
df.dropna(inplace=True)
df.shape
```




    (406829, 7)



### Let's find the retention 

Firstly, lets find out the types of our variables : 


```python
df.dtypes
```




    InvoiceNo              object
    StockCode              object
    Quantity                int64
    InvoiceDate    datetime64[ns]
    UnitPrice             float64
    CustomerID            float64
    Country                object
    dtype: object



Lets make the `date` column into the format `%Y-%m-%d`


```python
df['date'] = df['InvoiceDate'].apply(lambda x: datetime.strftime(x, '%Y-%m-%d'))
df['date'] = df['date'].apply(lambda x: datetime.strptime(x, '%Y-%m-%d'))
df['date'].head(2)
```




    0   2010-12-01
    1   2010-12-01
    Name: date, dtype: datetime64[ns]



Let's find the `first day` for each user : 


```python
first_sale = df.groupby('CustomerID').agg({'date' : 'min'}).reset_index()
first_sale = first_sale.rename(columns=({'date' : 'first'}))
first_sale.head(3)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>CustomerID</th>
      <th>first</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>12346.0</td>
      <td>2011-01-18</td>
    </tr>
    <tr>
      <th>1</th>
      <td>12347.0</td>
      <td>2010-12-07</td>
    </tr>
    <tr>
      <th>2</th>
      <td>12348.0</td>
      <td>2010-12-16</td>
    </tr>
  </tbody>
</table>
</div>



And merge it with the main table : 


```python
df = df.merge(first_sale, how='left', left_on='CustomerID', right_on='CustomerID')
```

Now we have the `following` table : 


```python
df.head(2)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>InvoiceNo</th>
      <th>StockCode</th>
      <th>Quantity</th>
      <th>InvoiceDate</th>
      <th>UnitPrice</th>
      <th>CustomerID</th>
      <th>Country</th>
      <th>date</th>
      <th>first</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>536365</td>
      <td>85123A</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>2.55</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
      <td>2010-12-01</td>
      <td>2010-12-01</td>
    </tr>
    <tr>
      <th>1</th>
      <td>536365</td>
      <td>71053</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>3.39</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
      <td>2010-12-01</td>
      <td>2010-12-01</td>
    </tr>
  </tbody>
</table>
</div>



With the `following columns` and `types` of the data : 


```python
df.dtypes
```




    InvoiceNo              object
    StockCode              object
    Quantity                int64
    InvoiceDate    datetime64[ns]
    UnitPrice             float64
    CustomerID            float64
    Country                object
    date           datetime64[ns]
    first          datetime64[ns]
    dtype: object




```python
df['date'] = pd.to_datetime(df['date'])
df['first'] = pd.to_datetime(df['first'])
```

Lets find the period for each user. It is needed for calculating the `retention` : 


```python
dif = ((df['date'].dt.year - df['first'].dt.year) * 12 + (df['date'].dt.month - df['first'].dt.month))
dif = dif.reset_index()
dif = dif.rename(columns=({0 : 'per'}))
dif.head(2)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>index</th>
      <th>per</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



Lets add the index columns for the main `df` to merge it with `dif` easily


```python
df = df.reset_index()
```


```python
df.head(3)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>index</th>
      <th>InvoiceNo</th>
      <th>StockCode</th>
      <th>Quantity</th>
      <th>InvoiceDate</th>
      <th>UnitPrice</th>
      <th>CustomerID</th>
      <th>Country</th>
      <th>date</th>
      <th>first</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>536365</td>
      <td>85123A</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>2.55</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
      <td>2010-12-01</td>
      <td>2010-12-01</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>536365</td>
      <td>71053</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>3.39</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
      <td>2010-12-01</td>
      <td>2010-12-01</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>536365</td>
      <td>84406B</td>
      <td>8</td>
      <td>2010-12-01 08:26:00</td>
      <td>2.75</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
      <td>2010-12-01</td>
      <td>2010-12-01</td>
    </tr>
  </tbody>
</table>
</div>



Finaly, lets merge them : 


```python
df = df.merge(dif, how='left', left_on='index', right_on='index')
df.head(3)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>index</th>
      <th>InvoiceNo</th>
      <th>StockCode</th>
      <th>Quantity</th>
      <th>InvoiceDate</th>
      <th>UnitPrice</th>
      <th>CustomerID</th>
      <th>Country</th>
      <th>date</th>
      <th>first</th>
      <th>per</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>536365</td>
      <td>85123A</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>2.55</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
      <td>2010-12-01</td>
      <td>2010-12-01</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>536365</td>
      <td>71053</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>3.39</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
      <td>2010-12-01</td>
      <td>2010-12-01</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>536365</td>
      <td>84406B</td>
      <td>8</td>
      <td>2010-12-01 08:26:00</td>
      <td>2.75</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
      <td>2010-12-01</td>
      <td>2010-12-01</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



Let's add the `first_month` and `month` to make it more convenient for us : 


```python
df['first_month'] = df['first'].apply(lambda x : datetime.strftime(x, '%Y-%m'))
df['month'] = df['date'].apply(lambda x : datetime.strftime(x, '%Y-%m'))
```

What we have : 


```python
df.head(3)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>index</th>
      <th>InvoiceNo</th>
      <th>StockCode</th>
      <th>Quantity</th>
      <th>InvoiceDate</th>
      <th>UnitPrice</th>
      <th>CustomerID</th>
      <th>Country</th>
      <th>date</th>
      <th>first</th>
      <th>per</th>
      <th>first_month</th>
      <th>month</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>536365</td>
      <td>85123A</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>2.55</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
      <td>2010-12-01</td>
      <td>2010-12-01</td>
      <td>0</td>
      <td>2010-12</td>
      <td>2010-12</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>536365</td>
      <td>71053</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>3.39</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
      <td>2010-12-01</td>
      <td>2010-12-01</td>
      <td>0</td>
      <td>2010-12</td>
      <td>2010-12</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>536365</td>
      <td>84406B</td>
      <td>8</td>
      <td>2010-12-01 08:26:00</td>
      <td>2.75</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
      <td>2010-12-01</td>
      <td>2010-12-01</td>
      <td>0</td>
      <td>2010-12</td>
      <td>2010-12</td>
    </tr>
  </tbody>
</table>
</div>



Lets creat the pre-final dataframe `result` and group our data by the fist month and the period : 


```python
result = df.groupby(['first_month','per']).agg({'InvoiceNo' : 'nunique'}).reset_index()
result.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>first_month</th>
      <th>per</th>
      <th>InvoiceNo</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2010-12</td>
      <td>0</td>
      <td>1708</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2010-12</td>
      <td>1</td>
      <td>689</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2010-12</td>
      <td>2</td>
      <td>579</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2010-12</td>
      <td>3</td>
      <td>753</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2010-12</td>
      <td>4</td>
      <td>611</td>
    </tr>
  </tbody>
</table>
</div>



Great, what we nee to do now is to find the `maximum number` for each `cohort` in order to calculate the `%` for `retention` : 


```python
max = result.groupby('first_month').agg({'InvoiceNo' : 'max'}).reset_index()
max = max.rename(columns=({'InvoiceNo' : 'max'}))
max.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>first_month</th>
      <th>max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2010-12</td>
      <td>1708</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2011-01</td>
      <td>547</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2011-02</td>
      <td>474</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2011-03</td>
      <td>548</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2011-04</td>
      <td>386</td>
    </tr>
  </tbody>
</table>
</div>



Awesome! Let's `merge` them together and calculate the `percentage`! 


```python
result = result.merge(max)
result['percent'] = result['InvoiceNo'] / result['max'] * 100
result['percent'] = result['percent'].round()
result.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>first_month</th>
      <th>per</th>
      <th>InvoiceNo</th>
      <th>max</th>
      <th>percent</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2010-12</td>
      <td>0</td>
      <td>1708</td>
      <td>1708</td>
      <td>100.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2010-12</td>
      <td>1</td>
      <td>689</td>
      <td>1708</td>
      <td>40.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2010-12</td>
      <td>2</td>
      <td>579</td>
      <td>1708</td>
      <td>34.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2010-12</td>
      <td>3</td>
      <td>753</td>
      <td>1708</td>
      <td>44.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2010-12</td>
      <td>4</td>
      <td>611</td>
      <td>1708</td>
      <td>36.0</td>
    </tr>
  </tbody>
</table>
</div>



Good job! To `visualize` it let's transform our data into `pivot table` : 


```python
piv = result.pivot(columns='per', index='first_month', values='percent')
piv
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>per</th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>10</th>
      <th>11</th>
      <th>12</th>
    </tr>
    <tr>
      <th>first_month</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2010-12</th>
      <td>100.0</td>
      <td>40.0</td>
      <td>34.0</td>
      <td>44.0</td>
      <td>36.0</td>
      <td>47.0</td>
      <td>43.0</td>
      <td>40.0</td>
      <td>39.0</td>
      <td>47.0</td>
      <td>45.0</td>
      <td>66.0</td>
      <td>23.0</td>
    </tr>
    <tr>
      <th>2011-01</th>
      <td>100.0</td>
      <td>27.0</td>
      <td>33.0</td>
      <td>28.0</td>
      <td>43.0</td>
      <td>36.0</td>
      <td>33.0</td>
      <td>31.0</td>
      <td>35.0</td>
      <td>43.0</td>
      <td>52.0</td>
      <td>16.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2011-02</th>
      <td>100.0</td>
      <td>29.0</td>
      <td>24.0</td>
      <td>34.0</td>
      <td>30.0</td>
      <td>28.0</td>
      <td>26.0</td>
      <td>35.0</td>
      <td>29.0</td>
      <td>39.0</td>
      <td>8.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2011-03</th>
      <td>100.0</td>
      <td>22.0</td>
      <td>32.0</td>
      <td>27.0</td>
      <td>26.0</td>
      <td>22.0</td>
      <td>29.0</td>
      <td>29.0</td>
      <td>41.0</td>
      <td>9.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2011-04</th>
      <td>100.0</td>
      <td>28.0</td>
      <td>24.0</td>
      <td>20.0</td>
      <td>22.0</td>
      <td>23.0</td>
      <td>24.0</td>
      <td>31.0</td>
      <td>8.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2011-05</th>
      <td>100.0</td>
      <td>25.0</td>
      <td>17.0</td>
      <td>19.0</td>
      <td>25.0</td>
      <td>24.0</td>
      <td>31.0</td>
      <td>10.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2011-06</th>
      <td>100.0</td>
      <td>24.0</td>
      <td>19.0</td>
      <td>31.0</td>
      <td>27.0</td>
      <td>43.0</td>
      <td>10.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2011-07</th>
      <td>100.0</td>
      <td>21.0</td>
      <td>25.0</td>
      <td>24.0</td>
      <td>36.0</td>
      <td>11.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2011-08</th>
      <td>100.0</td>
      <td>29.0</td>
      <td>34.0</td>
      <td>33.0</td>
      <td>13.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2011-09</th>
      <td>100.0</td>
      <td>34.0</td>
      <td>41.0</td>
      <td>11.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2011-10</th>
      <td>100.0</td>
      <td>37.0</td>
      <td>13.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2011-11</th>
      <td>100.0</td>
      <td>13.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2011-12</th>
      <td>100.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



Let's finally visualize it : 


```python
fig = plt.figure(figsize=(10,7))
ax = fig.add_subplot()
sns.heatmap(piv)
plt.title('Retention rates')
plt.show()
```


    
![png](output_45_0.png)
    


Let's move further and figure out another features.
Here we will use not modified initiall dataset. 


```python
df1 = pd.read_excel('/Users/vladimiregorov/Downloads/Online Retail 2.xlsx')
```

Let's see the distribution of `hour`


```python
df1['hour'] = df1['InvoiceDate'].dt.hour
df1['hour'].hist()
```




    <Axes: >




    
![png](output_49_1.png)
    


Now let's figure out the amount of orders per each hour : 


```python
df1.groupby('hour').agg({'InvoiceNo' : 'count'}).plot()
```




    <Axes: xlabel='hour'>




    
![png](output_51_1.png)
    


Lets calculate the total amount for each order an analyze the mean amount money people spend per each hour


```python
df1['total'] = df1['UnitPrice'] * df1['Quantity']
exp = df1.groupby('InvoiceNo').agg({'total' : 'sum'}).reset_index()
df1 = df1.merge(exp, how='left', left_on='InvoiceNo', right_on='InvoiceNo')
df1.head(3)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>InvoiceNo</th>
      <th>StockCode</th>
      <th>Description</th>
      <th>Quantity</th>
      <th>InvoiceDate</th>
      <th>UnitPrice</th>
      <th>CustomerID</th>
      <th>Country</th>
      <th>hour</th>
      <th>total_x</th>
      <th>total_y</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>536365</td>
      <td>85123A</td>
      <td>WHITE HANGING HEART T-LIGHT HOLDER</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>2.55</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
      <td>8</td>
      <td>15.30</td>
      <td>139.12</td>
    </tr>
    <tr>
      <th>1</th>
      <td>536365</td>
      <td>71053</td>
      <td>WHITE METAL LANTERN</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>3.39</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
      <td>8</td>
      <td>20.34</td>
      <td>139.12</td>
    </tr>
    <tr>
      <th>2</th>
      <td>536365</td>
      <td>84406B</td>
      <td>CREAM CUPID HEARTS COAT HANGER</td>
      <td>8</td>
      <td>2010-12-01 08:26:00</td>
      <td>2.75</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
      <td>8</td>
      <td>22.00</td>
      <td>139.12</td>
    </tr>
  </tbody>
</table>
</div>




```python
df1 = df1.rename(columns=({'total_x' : 'total', 'total_y' : 'total_ord'}))
df1.head(2)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>InvoiceNo</th>
      <th>StockCode</th>
      <th>Description</th>
      <th>Quantity</th>
      <th>InvoiceDate</th>
      <th>UnitPrice</th>
      <th>CustomerID</th>
      <th>Country</th>
      <th>hour</th>
      <th>total</th>
      <th>total_ord</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>536365</td>
      <td>85123A</td>
      <td>WHITE HANGING HEART T-LIGHT HOLDER</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>2.55</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
      <td>8</td>
      <td>15.30</td>
      <td>139.12</td>
    </tr>
    <tr>
      <th>1</th>
      <td>536365</td>
      <td>71053</td>
      <td>WHITE METAL LANTERN</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>3.39</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
      <td>8</td>
      <td>20.34</td>
      <td>139.12</td>
    </tr>
  </tbody>
</table>
</div>




```python
mean_rev = df1.groupby('hour').agg({'total_ord': 'mean'}).reset_index()
```


```python
fig = plt.figure(figsize=(20, 10))
ax = fig.add_subplot()

x = mean_rev['hour']
y = mean_rev['total_ord']

plt.bar(x, y)

plt.title('Mean Revenue by Hour')
plt.xlabel('Hour')
plt.ylabel('Mean Total Order')
plt.grid(True)

plt.show()
```


    
![png](output_56_0.png)
    


One more thing. We want to find out the most populat goods per each hour. Let's find out : 


```python
data = df1.groupby(['hour', 'StockCode']).agg({'StockCode' : 'count'})
data = data.rename(columns=({'StockCode' : 'quntity'})).reset_index()
data.head(3)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>hour</th>
      <th>StockCode</th>
      <th>quntity</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>6</td>
      <td>21216</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>6</td>
      <td>21218</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>6</td>
      <td>21314</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
def f(group):
    return group.nlargest(3, 'quntity')

data = data.groupby('hour', group_keys=False).apply(f)
data.head()
```

    /var/folders/t9/p_t5rsf92s38bb58_mfpk3rm0000gn/T/ipykernel_56800/1174805563.py:4: DeprecationWarning:
    
    DataFrameGroupBy.apply operated on the grouping columns. This behavior is deprecated, and in a future version of pandas the grouping columns will be excluded from the operation. Either pass `include_groups=False` to exclude the groupings or explicitly select the grouping columns after groupby to silence this warning.
    





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>hour</th>
      <th>StockCode</th>
      <th>quntity</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4</th>
      <td>6</td>
      <td>21845</td>
      <td>2</td>
    </tr>
    <tr>
      <th>17</th>
      <td>6</td>
      <td>22729</td>
      <td>2</td>
    </tr>
    <tr>
      <th>24</th>
      <td>6</td>
      <td>22847</td>
      <td>2</td>
    </tr>
    <tr>
      <th>147</th>
      <td>7</td>
      <td>22423</td>
      <td>4</td>
    </tr>
    <tr>
      <th>75</th>
      <td>7</td>
      <td>21523</td>
      <td>3</td>
    </tr>
  </tbody>
</table>
</div>




```python
fig = plt.figure(figsize=(15, 47))  

grouped_data = data.groupby('hour')

num_rows = len(grouped_data) // 2 + (1 if len(grouped_data) % 2 != 0 else 0) 

for i, (hour, group) in enumerate(grouped_data, 1):
    ax = fig.add_subplot(num_rows, 2, i)  
    sns.barplot(x='StockCode', y='quntity', data=group, ax=ax)
    ax.set_title(f'Hour {hour}')

fig.tight_layout()

plt.show()

```


    
![png](output_60_0.png)
    


Great, let's move to the `ml` part!

## RFM ANALYSIS


```python
from sklearn.neighbors import LocalOutlierFactor
from mpl_toolkits.mplot3d import Axes3D
from sklearn.cluster import DBSCAN
from sklearn.preprocessing import StandardScaler
from sklearn.cluster import KMeans
from sklearn.metrics import silhouette_score
from sklearn.neighbors import LocalOutlierFactor
import plotly.express as px
```


```python
data = df1.groupby('CustomerID').agg({'InvoiceNo' : 'count'}).reset_index()
data.head()
data = data.rename(columns=({'InvoiceNo' : 'Quantity'}))
data.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>CustomerID</th>
      <th>Quantity</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>12346.0</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>12347.0</td>
      <td>182</td>
    </tr>
    <tr>
      <th>2</th>
      <td>12348.0</td>
      <td>31</td>
    </tr>
    <tr>
      <th>3</th>
      <td>12349.0</td>
      <td>73</td>
    </tr>
    <tr>
      <th>4</th>
      <td>12350.0</td>
      <td>17</td>
    </tr>
  </tbody>
</table>
</div>




```python
data2 = df1.groupby('CustomerID').agg({'InvoiceDate' : 'max'}).reset_index()
data2 = data2.rename(columns=({'InvoiceDate' : 'lastdata'}))
data2.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>CustomerID</th>
      <th>lastdata</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>12346.0</td>
      <td>2011-01-18 10:17:00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>12347.0</td>
      <td>2011-12-07 15:52:00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>12348.0</td>
      <td>2011-09-25 13:13:00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>12349.0</td>
      <td>2011-11-21 09:51:00</td>
    </tr>
    <tr>
      <th>4</th>
      <td>12350.0</td>
      <td>2011-02-02 16:01:00</td>
    </tr>
  </tbody>
</table>
</div>




```python
data = data.merge(data2)
data.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>CustomerID</th>
      <th>Quantity</th>
      <th>lastdata</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>12346.0</td>
      <td>2</td>
      <td>2011-01-18 10:17:00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>12347.0</td>
      <td>182</td>
      <td>2011-12-07 15:52:00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>12348.0</td>
      <td>31</td>
      <td>2011-09-25 13:13:00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>12349.0</td>
      <td>73</td>
      <td>2011-11-21 09:51:00</td>
    </tr>
    <tr>
      <th>4</th>
      <td>12350.0</td>
      <td>17</td>
      <td>2011-02-02 16:01:00</td>
    </tr>
  </tbody>
</table>
</div>




```python
maxdata = data['lastdata'].max()
maxdata = pd.to_datetime(maxdata)
maxdata = datetime.strftime(maxdata, '%Y-%m-%d')
maxdata
```




    '2011-12-09'




```python
maxdata = datetime.strptime(maxdata, '%Y-%m-%d')
```


```python
data['lastdata'] = data['lastdata'].apply(lambda x: datetime.strftime(x, '%Y-%m-%d'))

```


```python
data['lastdata'] = data['lastdata'].apply(lambda x: datetime.strptime(x, '%Y-%m-%d'))
```


```python
data['diff'] = maxdata - data['lastdata']
```


```python
data
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>CustomerID</th>
      <th>Quantity</th>
      <th>lastdata</th>
      <th>diff</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>12346.0</td>
      <td>2</td>
      <td>2011-01-18</td>
      <td>325 days</td>
    </tr>
    <tr>
      <th>1</th>
      <td>12347.0</td>
      <td>182</td>
      <td>2011-12-07</td>
      <td>2 days</td>
    </tr>
    <tr>
      <th>2</th>
      <td>12348.0</td>
      <td>31</td>
      <td>2011-09-25</td>
      <td>75 days</td>
    </tr>
    <tr>
      <th>3</th>
      <td>12349.0</td>
      <td>73</td>
      <td>2011-11-21</td>
      <td>18 days</td>
    </tr>
    <tr>
      <th>4</th>
      <td>12350.0</td>
      <td>17</td>
      <td>2011-02-02</td>
      <td>310 days</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>4367</th>
      <td>18280.0</td>
      <td>10</td>
      <td>2011-03-07</td>
      <td>277 days</td>
    </tr>
    <tr>
      <th>4368</th>
      <td>18281.0</td>
      <td>7</td>
      <td>2011-06-12</td>
      <td>180 days</td>
    </tr>
    <tr>
      <th>4369</th>
      <td>18282.0</td>
      <td>13</td>
      <td>2011-12-02</td>
      <td>7 days</td>
    </tr>
    <tr>
      <th>4370</th>
      <td>18283.0</td>
      <td>756</td>
      <td>2011-12-06</td>
      <td>3 days</td>
    </tr>
    <tr>
      <th>4371</th>
      <td>18287.0</td>
      <td>70</td>
      <td>2011-10-28</td>
      <td>42 days</td>
    </tr>
  </tbody>
</table>
<p>4372 rows × 4 columns</p>
</div>




```python
df1.head(2)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>InvoiceNo</th>
      <th>StockCode</th>
      <th>Description</th>
      <th>Quantity</th>
      <th>InvoiceDate</th>
      <th>UnitPrice</th>
      <th>CustomerID</th>
      <th>Country</th>
      <th>hour</th>
      <th>total</th>
      <th>total_order</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>536365</td>
      <td>85123A</td>
      <td>WHITE HANGING HEART T-LIGHT HOLDER</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>2.55</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
      <td>8</td>
      <td>15.30</td>
      <td>139.12</td>
    </tr>
    <tr>
      <th>1</th>
      <td>536365</td>
      <td>71053</td>
      <td>WHITE METAL LANTERN</td>
      <td>6</td>
      <td>2010-12-01 08:26:00</td>
      <td>3.39</td>
      <td>17850.0</td>
      <td>United Kingdom</td>
      <td>8</td>
      <td>20.34</td>
      <td>139.12</td>
    </tr>
  </tbody>
</table>
</div>




```python
data3 = df1.groupby('CustomerID').agg({'total' : 'sum'}).reset_index()
data = data.merge(data3)
```


```python
data.head()
data = data.rename(columns=({'diff' : 'Recency', 'Quantity' : 'Frequency', 'total' : 'Monetary'}))
data = data.drop(columns=('lastdata'))
```


```python
data = data.drop(columns=('CustomerID'))
```


```python
data.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Frequency</th>
      <th>Recency</th>
      <th>Monetary</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>325 days</td>
      <td>0.00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>182</td>
      <td>2 days</td>
      <td>4310.00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>31</td>
      <td>75 days</td>
      <td>1797.24</td>
    </tr>
    <tr>
      <th>3</th>
      <td>73</td>
      <td>18 days</td>
      <td>1757.55</td>
    </tr>
    <tr>
      <th>4</th>
      <td>17</td>
      <td>310 days</td>
      <td>334.40</td>
    </tr>
  </tbody>
</table>
</div>




```python
data['Recency'] = data['Recency'].dt.days
```


```python
sns.pairplot(data[['Frequency','Recency','Monetary']])
```

    /opt/miniconda3/lib/python3.9/site-packages/seaborn/_oldcore.py:1119: FutureWarning: use_inf_as_na option is deprecated and will be removed in a future version. Convert inf values to NaN before operating instead.
      with pd.option_context('mode.use_inf_as_na', True):
    /opt/miniconda3/lib/python3.9/site-packages/seaborn/_oldcore.py:1119: FutureWarning: use_inf_as_na option is deprecated and will be removed in a future version. Convert inf values to NaN before operating instead.
      with pd.option_context('mode.use_inf_as_na', True):
    /opt/miniconda3/lib/python3.9/site-packages/seaborn/_oldcore.py:1119: FutureWarning: use_inf_as_na option is deprecated and will be removed in a future version. Convert inf values to NaN before operating instead.
      with pd.option_context('mode.use_inf_as_na', True):





    <seaborn.axisgrid.PairGrid at 0x325bb7c40>




    
![png](output_79_2.png)
    



```python
fig = plt.figure()
ax = fig.add_subplot(111, projection='3d')

ax.scatter(data['Recency'], data['Monetary'], data['Frequency'])
ax.set_xlabel('Recency')
ax.set_ylabel('Monetary')
ax.set_zlabel('Frequency')

plt.show()
```


    
![png](output_80_0.png)
    



```python
lof = LocalOutlierFactor(n_neighbors=20, contamination=0.1)
outliers = lof.fit_predict(data)
outliers_indices = data[outliers == -1].index
data_clean = data.drop(outliers_indices)
```


```python
sns.pairplot(data_clean[['Frequency','Recency','Monetary']])
```

    /opt/miniconda3/lib/python3.9/site-packages/seaborn/_oldcore.py:1119: FutureWarning: use_inf_as_na option is deprecated and will be removed in a future version. Convert inf values to NaN before operating instead.
      with pd.option_context('mode.use_inf_as_na', True):
    /opt/miniconda3/lib/python3.9/site-packages/seaborn/_oldcore.py:1119: FutureWarning: use_inf_as_na option is deprecated and will be removed in a future version. Convert inf values to NaN before operating instead.
      with pd.option_context('mode.use_inf_as_na', True):
    /opt/miniconda3/lib/python3.9/site-packages/seaborn/_oldcore.py:1119: FutureWarning: use_inf_as_na option is deprecated and will be removed in a future version. Convert inf values to NaN before operating instead.
      with pd.option_context('mode.use_inf_as_na', True):





    <seaborn.axisgrid.PairGrid at 0x325bc76d0>




    
![png](output_82_2.png)
    



```python
scaler = StandardScaler()
X_scaled = scaler.fit_transform(data_clean[['Recency', 'Frequency', 'Monetary']])
```


```python
dbscan = DBSCAN(eps=0.5, min_samples=5)
```


```python
data_clean['Cluster'] = dbscan.fit_predict(X_scaled)
```


```python
data_clean
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Frequency</th>
      <th>Recency</th>
      <th>Monetary</th>
      <th>Cluster</th>
      <th>Clusters</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>325</td>
      <td>0.00</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>182</td>
      <td>2</td>
      <td>4310.00</td>
      <td>0</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>31</td>
      <td>75</td>
      <td>1797.24</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>73</td>
      <td>18</td>
      <td>1757.55</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>17</td>
      <td>310</td>
      <td>334.40</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>4366</th>
      <td>9</td>
      <td>73</td>
      <td>173.90</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4367</th>
      <td>10</td>
      <td>277</td>
      <td>180.60</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4368</th>
      <td>7</td>
      <td>180</td>
      <td>80.82</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4369</th>
      <td>13</td>
      <td>7</td>
      <td>176.60</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4371</th>
      <td>70</td>
      <td>42</td>
      <td>1837.28</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>3934 rows × 5 columns</p>
</div>




```python
fig = plt.figure(figsize=(10,10))
ax = fig.add_subplot(111, projection='3d')

ax.scatter(data_clean['Recency'], data_clean['Monetary'], data_clean['Frequency'], c=data_clean['Cluster'])
ax.set_xlabel('Recency')
ax.set_ylabel('Monetary')
ax.set_zlabel('Frequency')

plt.show()
```


    
![png](output_87_0.png)
    



```python
fig = plt.figure(figsize=(8, 6))
ax = fig.add_subplot()
ax.scatter(data_clean['Recency'], data_clean['Monetary'],c=data_clean['Cluster'])
```




    <matplotlib.collections.PathCollection at 0x3393ec970>




    
![png](output_88_1.png)
    



```python
inertias = []
K_range = range(1, 10)
for k in K_range:
    kmeans = KMeans(n_clusters=k, random_state=42)
    kmeans.fit(data_clean)
    inertias.append(kmeans.inertia_)

plt.figure(figsize=(8, 6))
plt.plot(K_range, inertias, marker='o')
plt.xlabel('Number of clusters')
plt.ylabel('Inertia')
plt.title('Elbow Method for Optimal k')
plt.show()

```


    
![png](output_89_0.png)
    



```python
k = 3
kmeans = KMeans(n_clusters=k, random_state=42)
data_clean['Cluster'] = kmeans.fit_predict(data_clean)
```


```python
fig = plt.figure(figsize=(8, 6))
ax = fig.add_subplot(111, projection='3d')

ax.scatter(data_clean['Recency'], data_clean['Monetary'], data_clean['Frequency'], c=data_clean['Cluster'], cmap='viridis')
ax.set_xlabel('Feature1')
ax.set_ylabel('Feature2')
ax.set_zlabel('Feature3')
plt.title('K-means Clustering')
plt.show()

```


    
![png](output_91_0.png)
    



```python
fig = plt.figure(figsize=(8, 6))
ax = fig.add_subplot()
ax.scatter(data_clean['Recency'], data_clean['Monetary'],c=data_clean['Cluster'])
```




    <matplotlib.collections.PathCollection at 0x33727ddc0>




    
![png](output_92_1.png)
    



```python
fig = plt.figure(figsize=(8, 6))
ax = fig.add_subplot()
ax.scatter( data_clean['Monetary'],data_clean['Frequency'],c=data_clean['Cluster'])
```




    <matplotlib.collections.PathCollection at 0x33796d310>




    
![png](output_93_1.png)
    



```python
results = []

for i in range(2, 10):
    k = i
    kmeans = KMeans(n_clusters=k, random_state=42)
    data_clean['Cluster'] = kmeans.fit_predict(data_clean)  
    silhouette_avg = silhouette_score(data_clean, data_clean['Cluster'])  
    results.append((silhouette_avg))

    
    
```


```python
fig = plt.figure(figsize=(20,10))
ax = fig.add_subplot()
x = [2,3,4,5,6,7,8,9]
y = results
plt.plot(x,y)
plt.show()
```


    
![png](output_95_0.png)
    



```python
data_clean['Clusters'].value_counts()
```




    Clusters
    0    3338
    2     501
    1      95
    Name: count, dtype: int64




```python
fig = px.box(data_clean, x='Clusters', y='Monetary',
             title="Cluster ID vs Amount Box Plot",
             labels={'Clusters': 'Cluster ID', 'Monetary': 'Amount'}, color = 'Clusters')


fig.update_layout(width=800, height=600)

fig.show()
```


<div>                            <div id="2515b3db-fac6-4aa4-a692-04bbb7ab4aeb" class="plotly-graph-div" style="height:600px; width:800px;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("2515b3db-fac6-4aa4-a692-04bbb7ab4aeb")) {                    Plotly.newPlot(                        "2515b3db-fac6-4aa4-a692-04bbb7ab4aeb",                        [{"alignmentgroup":"True","hovertemplate":"Cluster ID=%{x}\u003cbr\u003eAmount=%{y}\u003cextra\u003e\u003c\u002fextra\u003e","legendgroup":"0","marker":{"color":"#636efa"},"name":"0","notched":false,"offsetgroup":"0","orientation":"v","showlegend":true,"x":[0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0],"x0":" ","xaxis":"x","y":[0.0,1797.24,1757.55,334.4,1545.41,89.0,1079.4,459.4,1168.06,189.9,552.0,1313.1,320.69,168.9,1887.96,1298.04,364.6,742.9300000000001,455.42,850.29,1803.96,566.16,401.9,549.84,460.89,1582.6000000000001,1272.48,1635.6599999999999,1108.65,84.3,225.6,409.7,1710.39,1708.1200000000001,681.0799999999999,1226.88,694.15,562.41,668.58,600.39,806.27,803.5600000000001,1849.11,777.2,582.73,708.37,256.4,765.19,509.01,173.55,172.06,133.4,1002.3100000000001,476.49,449.45,197.88,428.57,707.09,5.684341886080802e-14,1917.78,947.4,826.88,1124.01,1303.63,1212.05,733.89,724.0400000000001,723.38,680.99,477.3,1298.66,334.93,460.40000000000003,263.21000000000004,416.79,1139.3500000000001,1043.1,403.70000000000005,114.0,-4.5,73.5,1258.06,398.27,176.5,982.5699999999999,138.03,586.79,1017.68,383.7,1312.06,2056.89,340.84,599.68,192.72,1745.57,1316.66,348.71000000000004,1922.28,1623.63,125.22,349.83000000000004,1089.18,716.35,363.95,981.18,355.7,1000.39,1141.5,207.79999999999998,95.19999999999999,964.83,168.0,437.82,603.71,1.0658141036401503e-14,536.21,2101.39,238.85,456.9,131.17,346.7,519.61,1004.72,160.54000000000002,218.45,1306.98,595.4,410.72,415.7,197.71,511.36,1893.95,179.94000000000003,144.0,214.89999999999998,646.68,452.84999999999997,437.6,418.09999999999997,618.27,1032.58,569.5600000000001,153.12,254.18,-7.5,1126.85,2057.99,1193.32,643.59,315.6,657.25,355.53,177.31,1339.41,180.1,305.1,641.13,769.61,1270.88,640.5,2140.63,1689.18,671.69,486.1,1174.58,185.9,1013.26,477.91,1755.38,1346.97,318.05,603.71,314.44,114.0,914.53,601.98,1152.06,686.05,91.68,245.1,61.75,620.13,1607.73,225.89999999999998,610.52,1919.31,1331.39,1058.67,862.81,2283.63,89.14,1247.61,389.01,1035.8,830.96,869.67,582.1,939.02,797.71,219.0,274.7,2220.84,603.42,1130.57,848.55,353.86,1053.49,980.98,238.25,608.1,904.33,725.07,1031.41,427.8,836.33,453.01,236.26000000000002,383.95,110.39999999999999,779.5699999999999,234.0,155.35,1013.04,427.6,275.88,112.07999999999998,1531.44,1898.52,494.08,1143.6,892.47,1351.24,804.23,707.72,2273.32,1603.87,574.42,305.71,463.66,91.85,324.92,192.60000000000002,400.53999999999996,545.48,860.41,430.48,194.18,412.58,483.65000000000003,484.36,514.85,229.64000000000001,125.92,1063.46,572.2,166.04,942.34,92.72,918.98,1759.5,397.12,1468.1200000000001,430.15000000000003,1018.71,253.05,215.05,383.03,417.38,312.38,134.1,683.13,1118.99,1680.76,354.09,871.54,1050.8899999999999,299.75,1881.7,1289.15,38.1,1106.4,504.2,147.12,1548.07,559.12,-14.899999999999999,380.64,599.97,374.0,343.2300000000001,2218.02,1535.77,841.64,552.5699999999999,298.0,1463.04,703.47,309.05,1158.27,1351.09,313.77,380.46999999999997,331.0,188.14,347.3,216.5,138.68,509.58,246.0,2128.83,1662.3,363.65,594.0,1301.44,164.23,405.38,176.97,929.95,694.05,2062.7000000000003,105.1,409.9,606.28,2156.45,1012.9,1504.27,114.14,876.29,683.9,-3.75,604.51,462.95,1407.29,2053.7,1042.07,1382.8400000000001,329.85,108.07,266.39,1841.03,160.18,1194.75,135.95,144.8,738.6,39.239999999999995,372.21999999999997,377.2,97.5,1215.6200000000001,545.0,535.32,409.5,503.76,414.76,902.22,200.72,1197.94,455.88,534.85,120.94999999999999,149.05,468.95,81.57,178.46999999999997,50.550000000000004,1311.6200000000001,789.89,204.0,689.9,936.15,659.68,1247.5,799.48,996.52,190.55,642.77,886.63,881.07,908.46,676.99,471.57,560.47,305.28000000000003,629.31,1805.91,656.6800000000001,348.15000000000003,1201.96,271.40000000000003,183.4,307.53,248.61,346.98,1119.49,205.86,430.9,115.46000000000001,344.0,228.8,177.15,919.61,729.27,220.10000000000002,58.08,303.75,272.38,319.82,459.83,1708.8600000000001,74.39999999999999,207.35999999999999,101.19,243.94,1098.48,59.5,1524.07,350.06,766.84,1452.8700000000001,2205.63,485.19,984.91,1125.0,30.599999999999998,254.73,922.39,2056.29,259.17,64.0,1039.3,77.3,109.8,1740.63,962.39,423.72,307.09,110.95,614.77,228.91,712.8000000000001,1676.06,486.22,971.76,1721.57,1201.06,1379.75,91.8,112.75,1644.99,1021.48,414.7,791.33,1310.14,712.88,116.00999999999999,426.81,353.65,492.84,282.45,121.18,1648.79,71.4,1539.69,236.02,114.60000000000001,260.68,911.94,117.88,60.7,1797.78,870.09,869.46,250.26000000000002,353.48,1197.42,780.58,1518.43,333.74,133.62,62.63999999999999,819.2900000000001,847.41,163.20000000000002,2150.85,108.21000000000001,257.7,358.0,160.95,1357.6,605.12,727.27,680.83,329.56,663.65,270.40000000000003,600.07,121.74000000000001,430.5,1922.1,501.12,465.68,711.41,1030.4,390.66,0.0,698.58,292.32,557.62,944.1,200.29,2107.66,590.0,276.56,138.98,590.9300000000001,258.4,542.73,126.30000000000001,1047.8400000000001,93.66,196.15,361.11,578.9399999999999,873.74,197.95000000000002,87.4,2089.85,360.0,142.5,365.29,63.75,300.42,2000.86,15.0,1367.6100000000001,1456.79,1083.63,598.57,1555.32,2140.07,640.76,1491.7,567.36,199.48000000000002,787.85,675.3,740.4,1140.93,350.9,795.12,431.82,860.12,775.85,303.93,377.6,341.36,233.76,247.32,910.72,373.94,389.28,743.53,674.7,768.24,439.03,431.26,992.5,0.0,2191.22,56.160000000000004,623.4,263.75,378.64,772.9,1019.76,572.56,222.99,534.57,627.15,797.96,544.08,59.8,307.72,1325.29,1539.18,457.34,621.23,75.0,252.49,572.451,988.91,213.55,1390.5,712.5,640.83,221.06,140.35,1848.98,1624.21,582.6,218.47,1006.8,648.6899999999999,1269.1,534.24,196.89,296.64,1104.23,291.88,2043.7199999999998,590.0,356.97999999999996,1007.35,1737.8200000000002,183.44,1445.0,143.25,674.68,297.95,1083.71,165.0,417.54,1431.78,1092.77,1441.53,291.0,1776.81,184.64,140.34,313.8,454.24,1122.41,575.4,449.52,1215.82,1269.84,618.63,316.26000000000005,1990.1200000000001,617.93,321.05,1146.96,1136.77,292.0,216.85,295.93,452.82,110.97,979.72,1785.16,251.21,205.1,152.20000000000002,891.12,1389.41,2109.07,659.4399999999999,110.80000000000001,125.80000000000001,680.1800000000001,628.78,307.76,483.8,210.31,1280.85,605.84,291.41,626.99,916.12,91.80000000000001,584.06,605.06,174.37,141.85999999999999,1799.33,777.53,240.22,874.71,192.85,373.75,239.49,1384.25,294.4,732.0699999999999,1658.3,467.04,118.0,781.03,142.4,347.0,379.65,1976.12,1117.13,247.95999999999998,396.97,697.04,167.42000000000002,219.52,693.32,428.89,164.34,280.53999999999996,678.01,1115.43,1158.2,399.99,544.18,177.34,213.85,672.44,257.23,1624.02,1995.68,2117.02,1575.16,1071.03,941.62,810.5,122.64,1033.34,519.44,252.1,1670.22,346.6,1809.34,94.2,1095.08,932.97,379.65,382.14,879.08,189.47,84.65,218.55,349.7,-71.46,396.92,486.82000000000005,232.6,827.83,59.5,64.23,72.0,910.0,172.25,1466.8700000000001,-32.0,151.29999999999998,308.58000000000004,2265.12,99.5,711.86,330.79,806.41,296.88,180.48,252.06,228.84,1053.94,138.28,1432.26,306.55,1041.68,799.9,524.14,199.85000000000002,1809.81,1079.6599999999999,333.48,1016.14,752.6,610.59,491.86,724.0,339.59999999999997,216.9,350.75,666.33,1340.1499999999999,1156.36,291.54,79.6,948.25,1647.26,296.25,1186.28,741.26,282.59999999999997,659.4100000000001,286.79,1070.65,1689.46,1521.76,345.0,178.7,360.01,127.84,912.0,230.39999999999998,309.04,348.8,1047.68,195.55,688.86,558.32,324.8,277.07,924.6,256.38,112.6,1747.66,155.05,899.51,591.75,539.0,1353.41,1562.12,555.04,382.98,1140.35,385.64,632.0,957.66,1698.94,174.65,412.05,209.49,2202.99,40.949999999999996,384.8,834.57,271.24,691.73,85.0,361.62,312.98,847.61,1255.0,1537.95,1586.81,1056.94,363.4,216.17,201.16,1240.0,2187.31,299.56,501.56,145.67000000000002,1015.29,381.7,540.45,1786.79,122.7,117.24,1281.53,1131.28,781.3000000000001,506.8,243.56,349.55,605.63,416.90000000000003,1860.8600000000001,632.97,821.1,535.97,311.16,155.93,388.52,740.95,332.64,613.95,272.04,1212.8400000000001,350.15999999999997,172.25,351.13,1682.08,616.65,223.85000000000002,348.99,464.57,802.85,337.34,1230.93,307.4,608.7900000000001,922.1,361.28,158.85,71.66,556.75,839.14,561.5699999999999,90.6,102.0,728.56,674.45,378.59,322.96,230.22,169.55,664.54,80.7,190.25,264.7,413.59000000000003,357.98,196.79000000000002,869.87,200.14,1861.11,488.03,320.46,1266.08,311.3,850.63,76.8,1383.49,633.71,2030.3300000000002,529.73,962.19,685.9300000000001,328.8,473.02,505.18,917.34,1398.96,642.8,631.9,440.0,584.07,327.7,104.82,467.66,512.02,1428.02,626.65,147.20000000000002,1554.48,152.2,141.41,652.82,1659.75,154.99,1290.01,746.36,225.36,260.25,92.46,725.6400000000001,183.6,1188.32,152.55,374.7,265.85,1307.41,139.54,942.26,122.47,499.41999999999996,1049.37,375.1,45.599999999999994,182.25,1018.43,161.0,76.32000000000001,442.85,147.35,194.9,1314.73,216.20999999999998,1260.33,594.16,80.2,688.6,1377.92,386.53,1382.74,90.0,-19.9,419.58000000000004,643.6300000000001,709.71,637.5,1123.8600000000001,483.86,590.3399999999999,982.3800000000001,415.82,920.05,879.0300000000001,311.81,115.8,468.4,157.2,259.43,658.64,611.09,118.75,400.43,507.66,1110.52,289.36,1631.77,122.53999999999999,574.84,450.84,134.46,574.0,554.46,719.78,452.89,197.65,2018.91,853.39,962.7,335.52,663.5,185.39999999999998,688.04,161.03,218.78,308.26,151.74,2067.13,1900.89,2208.3,1070.48,1000.23,1777.92,1839.3,1925.3600000000001,169.48000000000002,89.94,632.4,683.17,975.23,2201.64,1450.6,822.0600000000001,2043.93,1447.51,775.46,490.86,161.0,146.85,890.34,213.70000000000002,280.55,1474.06,136.76999999999998,302.58,1904.05,935.0600000000001,120.0,1163.45,319.47,530.18,1279.09,492.65000000000003,112.32,437.96,559.97,1019.39,134.04,578.45,1910.01,151.23000000000002,413.85,911.26,1046.56,154.70000000000002,1284.06,43.2,632.04,197.1,1803.76,2083.42,229.71,1611.59,120.0,508.58000000000004,343.11,1530.75,574.26,166.85,311.65,462.65999999999997,866.31,351.45,1614.91,554.22,289.90999999999997,134.7,2166.38,607.71,889.9300000000001,108.97999999999999,1021.58,409.57,133.5,258.7,51.0,1078.96,90.6,163.62,174.4,666.9399999999999,225.77,882.58,2128.23,161.2,155.0,100.5,76.55,617.05,348.1,202.68,626.0699999999999,1247.69,202.11,142.4,1770.56,999.72,67.95,559.7,1426.31,1434.72,156.75,2111.08,1908.25,1063.0,761.3299999999999,122.7,490.22,1937.25,1058.06,385.87,813.11,265.2,322.08,363.34000000000003,397.76,1372.8,89.06,131.9,1545.14,1544.62,1400.88,1163.23,375.35,483.25,662.59,264.44,119.93,113.64,383.34,1965.9,529.62,1058.6200000000001,2272.4,598.76,461.19,1936.84,234.34000000000003,537.71,799.82,215.7,1307.81,316.25,279.05,636.51,2068.15,129.2,321.92,165.75,1047.43,463.38,340.93,139.56,123.8,538.8100000000001,457.47,1957.32,815.32,356.85,190.31,673.26,2012.17,1368.18,276.42,1267.4099999999999,275.28,438.49,1055.35,2102.09,330.45,174.37,270.6,974.54,241.09,1867.68,364.21999999999997,932.85,1812.6200000000001,171.8,355.56,388.25,537.48,996.26,1461.65,103.25,2024.45,652.8,765.62,186.23,859.27,324.14,0.0,2174.18,1080.76,1432.0,1524.46,227.39,218.06,35.400000000000006,859.71,168.63,138.6,114.11,1029.26,157.05,134.16,39.75,1447.14,570.46,617.99,379.83,1740.6,883.15,221.97,393.33000000000004,213.96,660.0,1785.15,601.56,406.08000000000004,538.59,379.51,393.28000000000003,1765.76,320.58000000000004,869.35,225.85,848.48,-21.85,671.96,939.21,1006.98,114.56,344.90000000000003,609.85,443.6,2042.15,110.46,96.12,668.61,780.46,597.09,243.22,1417.29,654.6,114.18,1947.55,1930.15,838.27,247.15,285.74,107.95,771.17,637.89,1258.38,195.25,765.55,1736.4,604.75,-2.55,498.95,52.0,628.38,112.8,358.61,2114.33,582.05,172.9,2070.97,188.60999999999999,109.13000000000001,2235.3,318.17,179.0,1096.67,606.13,917.07,160.19,867.38,329.68,663.81,187.92000000000002,1158.3,718.08,268.58,313.49,380.02,1164.55,1289.94,1423.21,1400.34,1088.53,1167.16,294.3,1311.79,389.64,563.15,638.05,738.96,420.5,1484.06,1625.97,53.8,255.9,300.52,250.55,254.11,139.5,876.42,139.26,1011.9,2195.58,730.86,386.15,447.68,200.10000000000002,289.99,77.4,679.4399999999999,197.64,745.06,0.0,214.14,1272.11,131.45000000000002,173.74,157.7,193.14,845.55,353.27,554.65,193.39000000000001,155.43,778.5,271.85,1110.34,657.8,272.07,507.29,48.800000000000004,1127.71,2226.91,644.65,197.13,1698.35,102.44999999999999,1641.2,62.85,309.4,278.15,511.35,463.05,645.65,1311.3799999999999,1165.65,1352.04,168.62,125.79,841.1999999999999,266.76,52.2,701.33,721.95,327.25,522.38,519.68,1448.72,1485.54,706.19,255.4,589.7,756.64,765.3199999999999,364.8,369.2,135.9,125.65,554.38,1237.8500000000001,655.75,468.77000000000004,1366.38,1228.32,1414.99,314.11,1025.44,599.9,277.8,389.86,841.52,1227.43,258.83,1264.84,1610.15,977.77,1316.07,749.1,310.05,223.88,964.6800000000001,363.53,1888.84,1617.63,100.9,290.82,1066.57,361.25,311.69,289.82,336.17,58.49,499.68,684.97,221.27,126.7,1487.8,206.21,191.32,463.8,147.59,1592.06,114.77,1314.45,114.39,279.94,765.86,1931.42,102.12,213.27,326.4,334.54,233.23000000000002,540.98,429.87,490.52,1641.76,502.09,156.91,308.94,260.17,170.04,125.58,456.26,905.04,1661.33,377.01,469.0,1051.95,627.13,518.28,668.14,252.73000000000002,665.59,135.93,537.0,633.66,343.62,380.57,121.17000000000002,1727.17,1391.61,215.78,697.79,1325.82,987.86,370.79999999999995,760.59,1744.76,553.36,1109.53,106.2,982.07,612.43,748.87,773.03,880.85,893.66,487.35,88.2,306.94,1140.27,381.32,476.96,457.81,1364.7,219.39000000000001,248.08,649.5,410.48,635.1,310.03,703.36,1002.54,1407.73,247.66,1291.75,463.75,970.25,1567.48,340.11,1352.75,1550.13,244.8,266.4,343.82,457.72,1612.13,2199.31,184.19,406.53,1419.54,330.11,977.9300000000001,982.42,1387.99,127.15,1282.45,178.96,498.74,259.45,1194.73,1313.66,608.65,301.29,520.8000000000001,329.25,961.49,1869.5,401.40000000000003,158.16,304.47,843.05,487.75,163.0,106.56,1624.05,135.75,224.14,40.5,2186.2,87.5,680.28,622.85,406.76,310.62,411.41,1044.32,499.32,656.44,193.64,1268.7,1827.8,316.58,648.8299999999999,420.29999999999995,867.62,331.18,894.4100000000001,88.94999999999999,325.05,1661.44,615.99,96.6,305.75,1758.92,114.1,585.0,454.90000000000003,310.48,409.40000000000003,154.41,1219.4,429.6,612.06,197.0,321.05,1412.32,1056.54,764.34,1685.57,1738.59,316.68,631.57,514.15,356.0,740.78,915.3,300.36,890.85,98.45,232.14999999999998,623.16,1780.77,254.82,178.95000000000002,156.51,393.74,1506.1200000000001,119.3,1717.36,408.8,721.94,716.57,609.19,255.89999999999998,277.40999999999997,113.12,2239.9500000000003,240.55,485.24,935.1,180.94,635.04,496.44,574.02,680.3,1174.35,205.3,302.27,921.1,52.0,327.97,551.54,165.0,312.62,204.5,267.13,602.97,311.71,208.75,1005.5,714.96,1028.56,173.97,2020.52,391.76,364.5,476.58,367.69,252.42000000000002,201.60000000000002,158.04,473.81,115.65,1862.14,777.02,277.05,427.93,418.92,318.05,913.19,602.51,1867.69,446.94000000000005,2213.11,168.0,1233.3600000000001,1682.3600000000001,596.85,635.76,316.88,394.14,140.54,500.0,1515.73,231.86,2183.54,107.45,288.18,94.0,1339.63,815.77,666.7,626.77,349.1,426.61,267.18,1346.38,190.53,774.9000000000001,572.8100000000001,508.8,135.86,950.36,97.7,43.5,383.34,1429.9,93.78,364.90000000000003,171.19,417.87,997.48,149.29,200.16,156.57999999999998,383.68,594.0,1418.26,113.5,155.17,494.64,971.22,429.84,538.73,1388.37,179.1,447.22,237.07,161.64,234.6,126.14,511.88,438.46,504.5,469.48,1465.2,454.10999999999996,1441.42,905.5,91.32000000000001,1090.03,659.4599999999999,1967.25,1299.23,1259.7,1131.56,52.83,979.26,492.36,1000.27,250.0,909.23,121.0,62.43,267.03,1911.89,343.5,398.84,412.01000000000005,440.0,811.02,148.44,229.33,1502.35,2007.44,1517.08,746.92,459.9,110.92,538.46,213.3,85.13000000000001,127.86,292.71999999999997,1655.76,575.63,436.06,90.12,437.23,217.2,279.77,473.65000000000003,722.74,134.55,1319.69,173.16,186.01,758.29,1375.71,650.4300000000001,607.45,940.6700000000001,258.88,127.1,348.6,682.9100000000001,722.61,455.3,913.6800000000001,456.58,-30.19,388.58,199.8,1767.53,1343.34,315.71999999999997,235.68,761.74,1083.63,48.480000000000004,216.39,1198.23,104.76,1093.68,270.03000000000003,112.46000000000001,1568.39,195.15,747.031,336.40000000000003,1524.4,1158.77,301.03,588.13,1504.56,675.8,1180.4,243.55,724.76,223.36,425.32,647.4,612.01,425.14000000000004,599.9,816.0,646.3,337.74,877.4399999999999,922.53,511.32,748.5,30.0,958.9,306.45,1552.01,309.8,138.14,358.63,301.32,76.32000000000001,677.69,173.05,1744.02,323.69,657.23,352.7,781.28,1333.68,946.36,193.75,254.1,239.76,136.8,191.04,409.98,309.65,664.03,1542.14,211.5,143.1,1001.8100000000001,945.58,133.25,1072.3600000000001,346.75,330.88,1118.81,150.3,598.73,507.08,2181.21,203.1,212.3,415.25,62.0,1108.38,294.12,162.3,1339.45,1121.21,449.12,154.88,784.46,34.8,1071.24,182.96,974.04,149.31,79.2,455.67,714.42,205.25,253.04,583.75,1048.67,315.06,581.8299999999999,635.6800000000001,270.8,104.9,241.62,1021.61,475.28000000000003,333.56,246.3,948.05,351.3,220.85,1604.44,948.96,610.01,884.14,1042.19,415.82000000000005,784.58,903.9,399.21,1145.43,2171.32,1801.14,1303.9099999999999,1842.73,2100.6,1905.45,206.98,570.96,885.47,532.94,1649.3600000000001,1641.48,636.14,547.7,747.76,836.79,2084.9,954.09,117.47,391.98,1020.74,345.66,1209.1,118.95000000000002,130.73999999999998,389.27,160.71,110.8,712.24,297.0,1438.85,832.88,68.84,1769.7800000000002,895.13,149.64,354.09000000000003,438.24,495.36,396.48,379.35,181.09,141.27,524.52,263.55,179.17,222.05,1384.68,56.25,351.11,363.88,164.68,1521.79,967.45,191.94,1232.18,264.62,1491.24,123.1,334.23,639.89,336.03000000000003,363.6,127.08,207.15,426.63,1107.46,416.86,145.35,404.58,31.799999999999997,337.44,325.1,181.0,1708.24,955.24,364.72,1705.26,669.57,806.57,1634.53,428.89,104.35,470.76,845.64,418.83,582.9,314.1,99.77000000000002,371.01,1054.73,1098.78,1475.02,1037.42,168.12,1451.28,158.03,792.11,41.989999999999995,427.07,101.4,478.78,992.71,1415.29,653.13,690.54,1483.31,211.88,408.90000000000003,851.01,628.28,719.81,251.24,439.65999999999997,236.09,852.12,331.24,437.7,758.92,855.02,454.33000000000004,1313.55,1147.37,773.99,1340.7,243.69000000000003,1111.79,137.9,362.06,1511.95,783.9,134.8,664.85,2140.2200000000003,1459.8600000000001,-29.95,1153.62,335.32,1521.87,852.15,326.24,584.3199999999999,94.35,2173.53,79.2,1352.0,176.6,1317.62,436.18,981.51,1119.31,315.06,779.36,17.0,1041.83,320.62,185.04999999999998,2005.63,731.9,1245.96,1842.14,1429.64,302.36,336.96,108.38,816.1,611.88,195.73999999999998,161.38,135.75000000000003,592.88,232.21,423.36,1294.14,867.11,684.4200000000001,307.78,187.15,289.35,656.25,1880.22,603.69,517.55,709.38,-7.95,635.41,703.77,620.4,535.34,175.2,734.13,375.0,76.32000000000001,280.52,1906.07,1824.23,644.24,348.2,1009.4499999999999,37.4,441.59999999999997,802.53,1822.97,1880.4199999999998,73.2,1140.21,1406.57,1009.5500000000001,1037.59,331.95,197.9,215.83,305.54,338.71000000000004,1155.54,529.41,981.86,215.48,215.08,314.45,400.68100000000004,1821.52,365.27,589.15,405.71,761.38,537.0600000000001,394.38,664.26,542.77,681.19,159.0,1062.75,242.35,914.9300000000001,259.39000000000004,864.86,1295.3,255.12,308.32000000000005,1010.6,542.03,983.23,422.13,858.41,432.2,372.9,409.1,1522.75,1352.3600000000001,114.56,152.94,1800.07,389.44,732.63,46.95,1035.45,746.83,-31.65,861.89,667.65,1423.29,411.7,559.87,810.6000000000001,537.83,1496.68,2062.06,168.3,1531.51,823.97,142.5,280.9,2236.7400000000002,1328.55,275.4,598.2,212.82,361.21999999999997,602.27,686.67,616.26,274.15000000000003,226.37,1341.65,328.15,404.40000000000003,1038.46,230.70000000000002,326.75,207.50000000000003,510.54,242.21,1583.2,151.05,214.79999999999998,109.95,1959.38,322.41,370.47999999999996,101.10000000000001,575.41,887.65,1469.45,53.5,1100.47,153.9,494.44,395.8,210.32000000000002,658.88,2024.95,1460.3700000000001,896.66,652.07,109.36000000000001,707.04,1540.42,327.3,804.49,497.43,1553.66,367.93,473.03000000000003,676.2,362.95,2131.9,1780.59,668.36,584.5,555.89,317.2,94.36,1362.27,221.19,1412.06,1486.9,1631.15,408.57,303.93,161.04999999999998,443.05,167.25,163.07,2126.93,2028.88,214.65,151.83,262.2,126.25,210.35,1026.99,675.27,959.48,346.12,82.56,302.7,241.06,-2.95,284.55,300.85,642.81,287.4,184.26,1522.25,798.35,381.2,616.79,638.77,230.51,2.8999999999941792,259.01,188.89000000000001,674.69,311.92,266.97,-8.15,1841.18,2158.37,208.36,814.72,151.42,102.0,1383.7,1603.46,74.9,650.43,757.44,223.95000000000002,314.86,544.3,331.0,300.83,769.29,220.62,139.35,379.4,363.65,492.2,731.5,1329.66,826.53,684.41,1562.22,86.12,319.1,400.86,1421.43,484.38,90.30000000000001,363.7,248.1,950.26,874.59,1627.13,101.69999999999999,162.7,480.92,215.07999999999998,290.36,228.06,244.41,455.25,1244.59,1026.94,593.75,539.02,1388.81,119.62,339.3,922.12,379.73,641.55,285.89,281.85,816.51,511.12,1077.74,173.7,614.01,865.6,124.2,1285.22,999.7,648.41,451.44,-30.599999999999998,70.44,313.62,233.45,908.03,463.95,225.15,459.24,213.5,330.03,181.09,360.16,250.15,90.04,140.7,813.52,244.79999999999998,494.72999999999996,482.47,75.66,120.99000000000001,1187.66,618.59,506.19,500.42,151.85,204.41,178.96,813.48,129.45,810.5699999999999,333.08,322.8,2183.04,217.9,1124.59,1676.47,1169.38,231.10000000000002,679.83,487.51,833.78,214.06,270.85999999999996,518.42,1166.92,594.97,933.62,123.24,39.12,810.4499999999999,135.39999999999998,118.98,570.13,306.71999999999997,1692.27,319.61,1558.72,129.75,183.7,1770.34,1028.0,324.24,1260.86,303.26,208.63,482.01,657.9000000000001,112.75,531.35,656.63,1651.03,911.53,301.71999999999997,319.8000000000001,155.24,292.47,589.15,1382.02,272.82,1054.67,702.79,603.75,650.51,2269.21,417.59999999999997,3.75,312.55,0.0,2191.84,826.96,1515.9,623.93,1764.72,207.5,223.92000000000002,239.4,106.83000000000001,369.34000000000003,772.84,316.62,1501.38,594.9,34.0,1145.6,458.04,380.39,744.9,171.59,654.92,388.28,245.94,294.65,2242.25,107.6,80.8,1520.1200000000001,110.38000000000001,243.84,1201.51,414.88,305.62,332.3,536.99,1121.59,288.42,912.74,275.64,547.07,479.35,417.06,1446.95,497.61,358.38,460.64,775.1800000000001,341.7,128.5,197.22,325.91,199.15,1106.53,413.46,180.85,251.87,582.9,1774.62,1260.44,214.32,193.44,281.42,124.57,332.41,342.45,144.91000000000003,328.4,35.400000000000006,345.89000000000004,151.65,481.35,207.73999999999998,805.62,680.02,2023.47,853.72,0.0,1519.08,432.0,705.12,276.73,429.84000000000003,471.51,194.88,496.84000000000003,145.93,219.14,213.03,1281.56,1706.88,373.24,981.65,364.14,113.44,208.46,621.6800000000001,576.26,391.52,169.79,739.5,230.25,543.05,1295.39,380.55,161.94,530.08,950.9300000000001,262.68,1903.13,1894.11,816.73,406.48,435.02,1161.27,1178.56,1262.75,167.61,20.8,2085.08,493.67,292.34,416.64,117.35000000000001,507.05,234.17,110.55000000000001,201.19,2148.47,319.02,439.43,650.41,232.20999999999998,251.51999999999998,344.33,1809.05,1078.96,384.06,1920.2,1625.05,126.12,179.0,1601.8600000000001,-1.25,1427.46,702.0,287.02000000000004,484.82,1487.78,1412.94,781.78,877.4300000000001,185.46,270.9,565.58,920.1,71.0,95.85,357.77,154.82999999999998,716.0,146.87,380.35,1030.14,734.5699999999999,388.33,1253.22,112.4,449.73,1065.77,1735.18,897.4300000000001,339.44,642.02,1100.9,864.32,247.14000000000001,413.75,496.38,1353.83,1179.89,234.24,534.47,1421.59,859.35,1446.09,813.77,841.0799999999999,378.2,118.80000000000001,285.5,290.16,2003.85,1405.78,2050.08,1991.01,2041.8500000000001,1777.78,270.20000000000005,1088.2,992.6800000000001,937.92,971.74,1621.73,25.5,665.78,869.6800000000001,163.29999999999998,248.61,747.22,806.9000000000001,116.19999999999999,157.02,593.61,307.46,138.32,345.29999999999995,298.89,259.35,157.09,425.0,1139.73,380.5,465.32,579.0,984.57,749.78,1679.64,124.88,213.78,329.34,251.70000000000002,306.55,1266.97,1534.73,1707.21,293.1,158.68,189.49,816.88,224.91,313.26,59.9,1430.94,306.13,711.79,780.38,239.92000000000002,144.0,203.95,2275.58,249.74,1674.46,262.62,1013.01,692.22,342.63,1352.91,384.08,204.24,226.85,113.5,1960.72,114.23,335.13,354.15,426.79,393.49,939.32,1906.09,421.52,190.88,149.89000000000001,550.06,916.13,314.69,379.95,1135.04,954.9,171.45,384.52,283.28000000000003,178.26,298.41,240.0,1253.3600000000001,63.440000000000005,317.62,763.64,2144.53,111.97999999999999,1065.3500000000001,246.04,187.82999999999998,1703.41,153.92,1419.73,936.16,525.24,550.8000000000001,1875.1100000000001,556.95,498.42,669.11,197.63,1119.93,250.04,82.75,1384.85,644.3,2288.13,1200.6200000000001,700.86,908.99,990.23,96.8,523.11,363.9,175.2,494.25,306.6,243.36,193.64,160.57,537.74,228.96,1159.52,110.75,1256.19,587.15,1740.0,1393.06,178.22,388.14,365.85,127.86,585.79,488.2,312.23,168.4,1349.52,446.18,423.89,1342.68,646.92,495.77,203.2,404.66,65.4,199.29,336.4,261.11,784.11,1259.56,508.8,457.91,203.86999999999998,696.41,653.38,281.62,32.65,771.85,1214.72,748.94,1258.06,778.0,507.84,463.46000000000004,598.83,1853.1299999999999,663.63,759.17,100.8,512.12,265.76,403.3,365.76,216.29999999999998,378.3,177.1,2210.2599999999998,534.24,120.9,541.53,1648.95,486.71999999999997,517.53,131.7,249.86,1737.7,657.3,452.63,757.46,662.94,1881.36,289.96000000000004,763.28,137.0,1843.06,1125.07,194.37,506.51,1249.34,414.04,396.87,376.64,1926.8799999999999,374.57,497.56,271.05,272.22,622.88,288.6,689.1,558.96,1145.84,294.29,605.58,387.31,308.48,513.2,208.65,639.02,768.08,153.94,1839.07,546.81,721.9599999999999,1070.71,652.74,145.0,422.70000000000005,706.7,888.86,293.45,223.36,164.4,76.5,897.62,98.42,570.4,170.75,645.96,-11.8,148.86,267.16,306.84,909.96,1952.45,483.26,157.9,541.46,854.95,167.62,1107.38,454.01,901.21,344.14,226.75000000000003,362.9,185.65,124.74,440.2,215.72,175.27,1227.3,1133.07,1240.2,1479.22,2215.02,993.18,388.79,161.67000000000002,116.00999999999999,193.42,1103.92,1992.76,390.07,329.6,571.1999999999999,971.58,81.44,1336.1,527.0500000000001,843.48,2047.0,75.75,1784.12,503.59999999999997,528.33,1242.34,265.93,547.0600000000001,108.5,621.66,712.29,101.55,995.6600000000001,133.06,1789.55,861.74,166.08,1175.88,117.55,1674.69,485.88,196.0,1788.27,604.64,728.16,2055.51,347.75,205.7,1284.08,551.04,1383.27,298.11,1992.1100000000001,184.4,239.41,290.40000000000003,733.4399999999999,187.02,283.12,765.34,2201.05,338.95,154.95,417.95,1719.72,798.74,885.19,1781.12,152.4,267.08,137.45,385.1,522.91,153.0,326.4,139.35,1195.94,995.6500000000001,1042.55,878.2,303.97,692.19,700.63,337.49,168.24,1863.35,355.11,98.8,64.65,674.52,80.64,1739.92,834.99,287.59,712.78,15.0,818.8199999999999,371.88,271.2,2273.1800000000003,1143.27,245.81,182.77,56.4,665.86,642.17,188.02,131.94,225.9,1965.55,106.1,323.04,350.39,697.9,780.43,1199.01,512.31,546.04,732.16,36.56,387.68,298.4,1995.46,1599.97,889.24,398.7,35.400000000000006,155.37,350.64,110.21000000000001,338.8,205.48,51.559999999999995,901.1999999999999,605.8000000000001,114.34,311.26,208.97,1393.79,148.21,252.8,330.9,597.5,325.7,622.75,428.18,1889.4,155.9,1764.3600000000001,658.26,237.61,379.45,178.51999999999998,458.92,133.35000000000002,671.77,717.45,190.19,899.61,111.35,525.78,203.87,602.09,203.85999999999999,244.1,112.25,1119.81,98.0,256.32,152.06,155.8,110.38,178.92,800.24,72.9,243.28,366.23,295.61,411.6,336.43,341.28,1304.04,826.52,207.50000000000003,244.08,397.29,212.54,834.5,1740.48,408.36,1331.57,704.25,145.79,389.84,99.14,1983.5800000000002,304.56,77.50000000000001,149.57999999999998,176.68,358.56000000000006,990.8400000000001,557.3,12.75,111.68,508.4599999999999,73.78,102.83,471.7,1098.43,123.07,228.2,582.59,136.04,337.04,740.79,353.19,288.14,300.91999999999996,737.81,811.45,634.95,20.8,198.57,191.17000000000002,630.84,93.35,629.6899999999999,76.1,1511.3,382.06,417.23,169.47,249.72,444.68,2116.19,252.24,174.8,102.78999999999999,187.29,166.42,1483.56,523.0,122.14,38.45,668.11,236.77999999999997,115.31,220.7,73.5,97.97,422.88,730.4,405.62,70.02,357.20000000000005,165.04999999999998,559.51,2085.65,189.4,172.29000000000002,74.4,297.07,363.65,170.16,296.71,2119.41,720.52,814.0,318.0,396.33,101.69999999999999,453.0,489.6,336.13,343.45,703.37,87.52999999999999,101.34,167.67000000000002,2083.42,580.0500000000001,911.15,168.31,223.65,754.64,113.48000000000002,430.7,264.65,206.05,352.69,76.32000000000001,220.1,320.72,181.12,111.15,673.1,589.4399999999999,1141.34,206.82999999999998,348.73,112.55000000000001,1045.74,715.4999999999999,681.9100000000001,761.83,152.65,-35.400000000000006,1014.9,1000.16,160.35000000000002,179.33999999999997,612.0799999999999,566.58,653.35,399.19,347.82,734.94,451.75,530.15,638.0600000000001,1612.79,394.7,302.46000000000004,1355.51,202.65,980.31,1208.51,2106.84,104.0,448.65,1314.17,1573.41,1793.17,1843.75,382.8,600.52,49.8,304.25,1998.49,1084.34,274.51,207.79999999999998,957.97,212.96,896.0,163.8,1396.99,160.42000000000002,1979.3,517.36,139.10000000000002,600.07,248.42,232.48000000000002,299.34000000000003,481.75,621.75,626.38,2019.77,297.78000000000003,1033.31,443.0,158.95000000000002,93.75,769.2,582.47,440.0,1796.48,882.24,987.1,422.58,2058.09,2232.4900000000002,596.1,783.02,95.34,342.92,526.67,103.3,-50.1,2265.38,324.24,149.48,1211.08,801.51,138.9,238.95000000000002,204.0,1.7763568394002505e-15,323.36,97.63,173.9,180.60000000000002,80.82,176.6,1837.28],"y0":" ","yaxis":"y","type":"box"},{"alignmentgroup":"True","hovertemplate":"Cluster ID=%{x}\u003cbr\u003eAmount=%{y}\u003cextra\u003e\u003c\u002fextra\u003e","legendgroup":"2","marker":{"color":"#EF553B"},"name":"2","notched":false,"offsetgroup":"2","orientation":"v","showlegend":true,"x":[2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2],"x0":" ","xaxis":"x","y":[4310.0,2811.43,6207.67,6245.53,2662.06,5154.58,3545.69,4008.62,2720.56,2780.66,2998.28,2409.9,3280.75,2842.57,3578.8,3750.4,6416.39,5059.32,4896.66,2906.85,5005.46,4067.29,2466.86,3153.89,6229.48,2848.23,7116.47,6546.58,3281.63,5590.86,2327.13,4495.27,5417.93,4206.27,3723.87,2582.51,4485.72,4106.82,3852.5,3652.25,3781.7400000000002,4064.9900000000002,2753.36,7187.34,3320.2200000000003,3683.13,3002.68,2581.04,3218.98,2291.19,3214.81,6388.35,4478.53,5953.25,3669.92,3982.72,3817.08,4863.88,3743.18,2986.15,2863.08,4873.8099999999995,4867.99,6814.24,2616.32,4241.63,4708.86,5713.28,2628.98,2609.1,3947.7,4196.01,3868.2,4366.78,4227.7,6989.63,3868.7,6977.04,6748.4,2612.86,5583.62,2919.81,3075.04,2483.63,2982.35,4143.02,4734.26,3979.04,7092.06,6854.51,5613.43,4786.99,3365.43,3346.28,7272.75,2584.4,6912.0,3079.1,5684.61,3595.48,5893.32,6336.29,3866.33,2545.38,5055.79,2385.8,5725.47,3957.78,3384.09,5820.16,6083.04,3717.67,4404.44,5315.92,2709.12,5815.49,7072.8,3617.57,3536.82,2543.72,3085.4900000000002,3312.25,3626.81,4122.28,2312.8,5845.44,2601.55,3550.7000000000003,3794.4900000000002,3408.06,3465.67,5656.75,4282.88,2479.16,3487.75,3755.38,5613.08,3448.29,3539.53,4149.28,6703.3,2445.12,5030.6,2597.52,2707.33,2421.47,6216.07,3119.44,2504.13,2295.31,6249.110000000001,2369.16,2766.18,5145.12,3190.55,2771.87,2631.7,2462.79,2635.79,4014.91,3385.62,3192.54,2751.25,6389.8,3219.77,2538.75,2588.12,2867.2,2347.05,2580.91,4912.41,2427.32,3515.68,2702.2200000000003,2503.23,2590.46,7024.53,4098.8,3409.66,2313.14,3865.26,4582.64,2874.72,4328.46,2360.09,2929.84,3542.19,5639.15,6147.4,6135.38,4435.79,4063.1,3092.9700000000003,5976.79,2688.48,5669.65,2774.17,2740.3,2740.43,3580.13,4690.31,3148.2,2949.57,2445.91,3108.06,4640.77,4604.31,4660.01,3427.85,3315.71,3093.86,2323.5,2535.88,2879.7000000000003,3087.27,2618.23,3957.0,3729.34,4595.81,2307.4500000000003,3873.3,4819.33,2855.35,2995.72,2319.68,2456.98,4928.74,2539.46,3717.35,3172.04,4592.47,2556.68,4039.98,2606.53,4263.64,3918.9,2357.6800000000003,2362.84,3543.26,4221.37,2925.91,2916.17,2487.02,3986.7400000000002,2381.04,4709.22,3073.77,2757.07,2952.34,2456.53,2543.2400000000002,5107.38,4911.14,2686.34,5807.6900000000005,3899.1,4965.38,2423.07,4236.34,3295.76,2456.8,3189.81,2836.69,2749.89,2939.64,3478.85,2372.86,5842.95,7006.58,6763.16,4277.66,5064.01,3135.98,2420.84,4410.14,2298.93,3439.41,2760.56,3447.4,2350.0,6530.04,5303.97,4684.24,4670.08,7521.17,5756.89,3376.96,6657.36,2298.2,2485.82,2306.52,4596.51,3610.31,3774.1,3787.09,5028.12,2650.73,2360.55,5927.86,3450.84,3974.37,6945.0199999999995,6568.72,3563.11,2575.0,3090.85,5541.46,3497.14,2963.19,2574.65,2348.82,3542.51,4109.97,2552.86,3429.9700000000003,6745.36,2422.64,3224.53,2865.64,3147.31,2635.4900000000002,2881.02,3324.09,2981.5,6423.6,2644.46,3329.7200000000003,5178.96,4788.77,2318.88,3245.47,2593.94,2303.75,4834.2,2666.61,3848.55,5452.17,3650.87,2974.65,4330.67,5126.34,2461.85,6102.26,4389.81,4199.85,3429.55,2398.86,3408.48,2816.97,2735.37,3362.71,3450.2400000000002,3166.4,2806.9,3204.1600000000003,6287.77,3506.54,2419.84,3741.98,3024.62,5352.97,3770.6,6617.65,3991.94,6841.24,4913.32,3054.87,3693.81,3598.26,3665.62,5203.51,5423.15,5305.83,2755.56,3844.22,6675.71,4061.24,3640.67,3482.7400000000002,2494.46,2965.69,6748.8,5664.57,3313.79,3622.95,4073.28,3038.67,5450.96,2630.64,4379.65,2878.15,6654.26,3794.52,4022.46,3013.83,5040.0,5150.2699999999995,3029.87,2483.04,6840.2300000000005,3278.36,4711.21,7076.28,7170.7,6694.31,5580.6,6500.6,3317.97,3167.73,2761.4900000000002,7066.31,3085.48,2528.67,2781.5,4345.5,4392.42,5461.62,3989.57,5594.78,5116.13,4531.34,2655.2200000000003,2385.48,2949.75,3802.81,2463.17,4892.240000000001,3518.03,2466.4,3755.65,3286.9100000000003,3563.85,2299.67,2741.08,2514.76,6204.46,4455.73,7330.8,2633.85,6838.34,4200.97,5022.26,2333.42,3541.92,6100.74,2920.04,3540.1,4012.56,3911.56,2827.93,4509.37,2768.98,3886.67,4737.13,2571.7400000000002,2954.75,3406.7200000000003,3106.62,2459.33,3191.53,5739.46,4748.45,2508.89,5505.79,5367.8,2380.64,3371.13,2515.68,5288.83,2786.05,2291.08,3960.63,4627.62,3661.0699999999997,2312.7,5155.66,3160.0,2894.33,4301.22,4393.61,6752.14,2380.08,3864.44,2538.7200000000003,3651.97,3017.3,2692.85,2888.75,2861.55,7490.17,5083.06,2621.38,6315.2300000000005,5361.02,5192.1,7276.900000000001,2716.6000000000004,2507.56,4314.72,2338.6000000000004,3064.78],"y0":" ","yaxis":"y","type":"box"},{"alignmentgroup":"True","hovertemplate":"Cluster ID=%{x}\u003cbr\u003eAmount=%{y}\u003cextra\u003e\u003c\u002fextra\u003e","legendgroup":"1","marker":{"color":"#00cc96"},"name":"1","notched":false,"offsetgroup":"1","orientation":"v","showlegend":true,"x":[1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],"x0":" ","xaxis":"x","y":[11056.93,7877.2,13375.87,7829.89,8933.52,18740.92,13117.01,13309.039999999999,11990.960000000001,9114.94,9861.38,13612.07,17588.26,13677.59,12288.22,8221.09,9294.1,18793.41,9120.39,16293.1,16389.74,11581.8,10930.26,9797.92,12432.28,8689.39,7741.47,10510.0,9803.96,10664.34,10736.48,9537.92,8910.61,7792.36,16945.71,8025.0199999999995,16989.52,8986.69,14634.640000000001,15477.34,10464.85,11216.75,12650.08,9167.82,11341.1,9231.2,9451.54,10641.6,7904.28,14197.45,11020.26,8117.42,7912.79,7629.38,9623.35,11117.05,18573.68,15802.14,10327.11,7803.7699999999995,8234.8,12396.46,14520.08,10360.04,12433.34,16173.67,12393.7,8727.61,14305.66,10217.48,9295.67,11895.57,8257.04,8568.24,8093.59,13946.130000000001,10454.05,15357.78,10183.82,8574.11,10380.43,10585.57,8628.55,8600.73,11891.18,17078.45,10736.11,16219.22,10097.37,13110.02,7677.71,10158.54,9065.76,7925.32,8393.22],"y0":" ","yaxis":"y","type":"box"}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"xaxis":{"anchor":"y","domain":[0.0,1.0],"title":{"text":"Cluster ID"},"categoryorder":"array","categoryarray":[0,2,1]},"yaxis":{"anchor":"x","domain":[0.0,1.0],"title":{"text":"Amount"}},"legend":{"title":{"text":"Cluster ID"},"tracegroupgap":0},"title":{"text":"Cluster ID vs Amount Box Plot"},"boxmode":"overlay","width":800,"height":600},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('2515b3db-fac6-4aa4-a692-04bbb7ab4aeb');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })                };                });            </script>        </div>



```python
inertias = []
K_range = range(1, 10)
for k in K_range:
    kmeans = KMeans(n_clusters=k, random_state=42)
    kmeans.fit(data_clean)
    inertias.append(kmeans.inertia_)



```


```python

```
