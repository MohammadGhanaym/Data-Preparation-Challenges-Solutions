## 2024: Week 3 Performance Against Targets
The world is swimming in data and with so many sources everywhere, it's often on you to tie them together. Most data tools need to read from a single data so will use Unions, Joins and Logical Relationship models to tie them together. In this week's challenge we will introduce you to joining data sets together to prepare them for analysis. 

This week's challenge is to link together a Quarterly Sales Target data source (an Excel Workbook) with our original sales data (Week One output). Is Prep Air meeting its targets?

Input
Week One's output files and Week Three's new input: 
![image.png](eb800a34-b543-444e-bbb6-66f3efa581f0.png)

![image.png](ec560b71-28e3-4ff3-baf9-a13a9e2961d8.png)


```python
import pandas as pd
```


```python
# read the required files
df_flow = pd.read_csv('./data/PD 2024 Wk 1 Output Flow Card.csv')
df_non_flow = pd.read_csv('./data/PD 2024 Wk 1 Output Non-Flow Card.csv')
df_sales = pd.concat((df_flow, df_non_flow))
sales_target = pd.read_excel('./data/PD 2024 Wk 3 Input.xlsx', sheet_name=['Q1', 'Q2', 'Q3', 'Q4'])
```


```python
df_sales.head(5)
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
      <th>Date</th>
      <th>Flight Number</th>
      <th>From</th>
      <th>To</th>
      <th>Class</th>
      <th>Price</th>
      <th>Flow Card?</th>
      <th>Bags Checked</th>
      <th>Meal Type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>22/07/2024</td>
      <td>PA010</td>
      <td>Tokyo</td>
      <td>New York</td>
      <td>Economy</td>
      <td>2380.0</td>
      <td>Yes</td>
      <td>0</td>
      <td>Egg Free</td>
    </tr>
    <tr>
      <th>1</th>
      <td>20/04/2024</td>
      <td>PA002</td>
      <td>New York</td>
      <td>London</td>
      <td>Economy</td>
      <td>3490.0</td>
      <td>Yes</td>
      <td>1</td>
      <td>Vegan</td>
    </tr>
    <tr>
      <th>2</th>
      <td>23/01/2024</td>
      <td>PA010</td>
      <td>Tokyo</td>
      <td>New York</td>
      <td>Premium Economy</td>
      <td>825.0</td>
      <td>Yes</td>
      <td>1</td>
      <td>Vegetarian</td>
    </tr>
    <tr>
      <th>3</th>
      <td>05/06/2024</td>
      <td>PA006</td>
      <td>Tokyo</td>
      <td>London</td>
      <td>First Class</td>
      <td>618.0</td>
      <td>Yes</td>
      <td>3</td>
      <td>Vegan</td>
    </tr>
    <tr>
      <th>4</th>
      <td>30/03/2024</td>
      <td>PA004</td>
      <td>Perth</td>
      <td>London</td>
      <td>First Class</td>
      <td>446.0</td>
      <td>Yes</td>
      <td>1</td>
      <td>Nut Free</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Join the data in different sheets together
sales_target = pd.concat((sales_target['Q1'],
                          sales_target['Q2'],
                          sales_target['Q3'],
                          sales_target['Q4']))
sales_target.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Index: 48 entries, 0 to 11
    Data columns (total 3 columns):
     #   Column  Non-Null Count  Dtype 
    ---  ------  --------------  ----- 
     0   Month   48 non-null     int64 
     1   Class   48 non-null     object
     2   Target  48 non-null     int64 
    dtypes: int64(2), object(1)
    memory usage: 1.5+ KB
    


```python
# Correct the class names
class_mapping = {
    "Economy": "First",
    "First Class": "Economy",
    "Business Class": "Premium",
    "Premium Economy": "Business"
}
df_sales.Class = df_sales.Class.apply(lambda c:class_mapping.get(c, c))
```


```python
df_sales.head()
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
      <th>Date</th>
      <th>Flight Number</th>
      <th>From</th>
      <th>To</th>
      <th>Class</th>
      <th>Price</th>
      <th>Flow Card?</th>
      <th>Bags Checked</th>
      <th>Meal Type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>22/07/2024</td>
      <td>PA010</td>
      <td>Tokyo</td>
      <td>New York</td>
      <td>First</td>
      <td>2380.0</td>
      <td>Yes</td>
      <td>0</td>
      <td>Egg Free</td>
    </tr>
    <tr>
      <th>1</th>
      <td>20/04/2024</td>
      <td>PA002</td>
      <td>New York</td>
      <td>London</td>
      <td>First</td>
      <td>3490.0</td>
      <td>Yes</td>
      <td>1</td>
      <td>Vegan</td>
    </tr>
    <tr>
      <th>2</th>
      <td>23/01/2024</td>
      <td>PA010</td>
      <td>Tokyo</td>
      <td>New York</td>
      <td>Business</td>
      <td>825.0</td>
      <td>Yes</td>
      <td>1</td>
      <td>Vegetarian</td>
    </tr>
    <tr>
      <th>3</th>
      <td>05/06/2024</td>
      <td>PA006</td>
      <td>Tokyo</td>
      <td>London</td>
      <td>Economy</td>
      <td>618.0</td>
      <td>Yes</td>
      <td>3</td>
      <td>Vegan</td>
    </tr>
    <tr>
      <th>4</th>
      <td>30/03/2024</td>
      <td>PA004</td>
      <td>Perth</td>
      <td>London</td>
      <td>Economy</td>
      <td>446.0</td>
      <td>Yes</td>
      <td>1</td>
      <td>Nut Free</td>
    </tr>
  </tbody>
</table>
</div>




```python
sales_target.Class.unique()
```




    array(['FC', 'BC', 'PE', 'E'], dtype=object)




```python
# Create a class abbreviations column to use it when joining the two dataframes
class_abbrev = {
    'First': 'FC',
    'Economy': 'E',
    'Premium': 'BC',
    'Business': 'PE'
}
df_sales['Class'] = df_sales.Class.apply(lambda c:class_abbrev.get(c, c))
df_sales.head()
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
      <th>Date</th>
      <th>Flight Number</th>
      <th>From</th>
      <th>To</th>
      <th>Class</th>
      <th>Price</th>
      <th>Flow Card?</th>
      <th>Bags Checked</th>
      <th>Meal Type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>22/07/2024</td>
      <td>PA010</td>
      <td>Tokyo</td>
      <td>New York</td>
      <td>FC</td>
      <td>2380.0</td>
      <td>Yes</td>
      <td>0</td>
      <td>Egg Free</td>
    </tr>
    <tr>
      <th>1</th>
      <td>20/04/2024</td>
      <td>PA002</td>
      <td>New York</td>
      <td>London</td>
      <td>FC</td>
      <td>3490.0</td>
      <td>Yes</td>
      <td>1</td>
      <td>Vegan</td>
    </tr>
    <tr>
      <th>2</th>
      <td>23/01/2024</td>
      <td>PA010</td>
      <td>Tokyo</td>
      <td>New York</td>
      <td>PE</td>
      <td>825.0</td>
      <td>Yes</td>
      <td>1</td>
      <td>Vegetarian</td>
    </tr>
    <tr>
      <th>3</th>
      <td>05/06/2024</td>
      <td>PA006</td>
      <td>Tokyo</td>
      <td>London</td>
      <td>E</td>
      <td>618.0</td>
      <td>Yes</td>
      <td>3</td>
      <td>Vegan</td>
    </tr>
    <tr>
      <th>4</th>
      <td>30/03/2024</td>
      <td>PA004</td>
      <td>Perth</td>
      <td>London</td>
      <td>E</td>
      <td>446.0</td>
      <td>Yes</td>
      <td>1</td>
      <td>Nut Free</td>
    </tr>
  </tbody>
</table>
</div>




```python
# change the type of the Date column to datetime
df_sales.Date = pd.to_datetime(df_sales.Date, dayfirst=True)

# Change the date to a month number
df_sales.Date = df_sales.Date.dt.month

```


```python
# total up the sales at the level of : [Class, Month]
df_sales_total = df_sales.groupby(['Class', 'Date'])['Price'].sum().reset_index()
df_sales_total.head()
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
      <th>Class</th>
      <th>Date</th>
      <th>Price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>BC</td>
      <td>1</td>
      <td>48555.6</td>
    </tr>
    <tr>
      <th>1</th>
      <td>BC</td>
      <td>2</td>
      <td>46335.6</td>
    </tr>
    <tr>
      <th>2</th>
      <td>BC</td>
      <td>3</td>
      <td>47875.2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>BC</td>
      <td>4</td>
      <td>38233.2</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BC</td>
      <td>5</td>
      <td>43833.6</td>
    </tr>
  </tbody>
</table>
</div>




```python
df = pd.merge(df_sales_total, sales_target, how='inner', left_on=['Class', 'Date'], right_on=['Class', 'Month'])
df.drop('Month', axis=1, inplace=True)
```


```python
df['Difference to Target'] = df.Price - df.Target
```


```python
df.head()
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
      <th>Class</th>
      <th>Date</th>
      <th>Price</th>
      <th>Target</th>
      <th>Difference to Target</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>BC</td>
      <td>1</td>
      <td>48555.6</td>
      <td>85000</td>
      <td>-36444.4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>BC</td>
      <td>2</td>
      <td>46335.6</td>
      <td>86000</td>
      <td>-39664.4</td>
    </tr>
    <tr>
      <th>2</th>
      <td>BC</td>
      <td>3</td>
      <td>47875.2</td>
      <td>87000</td>
      <td>-39124.8</td>
    </tr>
    <tr>
      <th>3</th>
      <td>BC</td>
      <td>4</td>
      <td>38233.2</td>
      <td>88000</td>
      <td>-49766.8</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BC</td>
      <td>5</td>
      <td>43833.6</td>
      <td>89000</td>
      <td>-45166.4</td>
    </tr>
  </tbody>
</table>
</div>


