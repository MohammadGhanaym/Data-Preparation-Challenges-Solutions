## 2024: Week 4 - Unpopular Seats

Last week you needed to use a Join technique to pair the flight data with the sales targets. This week you'll be using Joins again but this time in a different way. 

When using Joins, there are two things you need to set up:

- Join Condition - what logic will join similar rows of data together from each data set
- Join Type - determines what data you will bring back based on the Join Condition
  
This challenge will test using join types to return the data you require for the output.

This week we are trying to understand which seats aren't chosen on our planes as we're thinking of applying fees for customers to choose their seat when booking. 
![image.png](dd9777e3-ed12-44ce-bfec-d8b8250d52d5.png)

![image.png](c157e5f8-2174-46c5-bb85-6c6a61f3852f.png)


```python
import pandas as pd
import numpy
```


```python
# use sheet_name = None to get all existing sheets
df = pd.read_excel('PD 2024 Wk 4 Input.xlsx', sheet_name=None)
```


```python
df['Flow Card'].head()
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
      <th>Seat</th>
      <th>Row</th>
      <th>Class</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>654</td>
      <td>2</td>
      <td>2</td>
      <td>FC</td>
    </tr>
    <tr>
      <th>1</th>
      <td>466</td>
      <td>4</td>
      <td>5</td>
      <td>FC</td>
    </tr>
    <tr>
      <th>2</th>
      <td>27</td>
      <td>4</td>
      <td>3</td>
      <td>FC</td>
    </tr>
    <tr>
      <th>3</th>
      <td>519</td>
      <td>1</td>
      <td>4</td>
      <td>FC</td>
    </tr>
    <tr>
      <th>4</th>
      <td>933</td>
      <td>2</td>
      <td>3</td>
      <td>FC</td>
    </tr>
  </tbody>
</table>
</div>



#### Union the Flow Card and Non-Flow card data sets together
#### Create a data field to show whether the seat was booked by someone with the Flow Card or not
- Call this field 'Flow Card?'


```python
# take a look at the sheets names
df.keys()
```




    dict_keys(['Flow Card', 'Non_flow Card', 'Non_flow Card2', 'Seat Plan'])




```python
# First, we are going to create 'Flow Card' column
df['Flow Card']['Flow Card'] = 1
df['Non_flow Card']['Flow Card'] = 0
df['Non_flow Card2']['Flow Card'] = 0
```


```python
df_card = pd.concat((df['Flow Card'], df['Non_flow Card'], df['Non_flow Card2']), axis=0)
df_card.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Index: 29213 entries, 0 to 9739
    Data columns (total 5 columns):
     #   Column      Non-Null Count  Dtype 
    ---  ------      --------------  ----- 
     0   CustomerID  29213 non-null  int64 
     1   Seat        29213 non-null  int64 
     2   Row         29213 non-null  int64 
     3   Class       29213 non-null  object
     4   Flow Card   29213 non-null  int64 
    dtypes: int64(4), object(1)
    memory usage: 1.3+ MB
    


```python
df_card.head()
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
      <th>Seat</th>
      <th>Row</th>
      <th>Class</th>
      <th>Flow Card</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>654</td>
      <td>2</td>
      <td>2</td>
      <td>FC</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>466</td>
      <td>4</td>
      <td>5</td>
      <td>FC</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>27</td>
      <td>4</td>
      <td>3</td>
      <td>FC</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>519</td>
      <td>1</td>
      <td>4</td>
      <td>FC</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>933</td>
      <td>2</td>
      <td>3</td>
      <td>FC</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



#### Aggregate the Seat Bookings to count how many bookings there are for:
- Each Seat
- In each Row
- In each Class
- For Flow and Non-Flow Card holders


```python
bookings_count = df_card.groupby(['Seat', 'Row', 'Class', 'Flow Card']).count().reset_index()
```

#### Join on the Seating Plan data to ensure you have a data set for every seat on the plane, even if it hasn't been book
- Only return the records for the seats that haven't been booked


```python
unbooked_seats = pd.merge(left=df_card, right=df['Seat Plan'], on=['Seat', 'Row', 'Class'], how='outer', indicator=True)\
  .query('_merge=="right_only"')
unbooked_seats = unbooked_seats.drop(['CustomerID', 'Flow Card', '_merge'], axis=1)
```


```python
unbooked_seats
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
      <th>Seat</th>
      <th>Row</th>
      <th>Class</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>17197</th>
      <td>5</td>
      <td>28</td>
      <td>E</td>
    </tr>
    <tr>
      <th>17954</th>
      <td>5</td>
      <td>36</td>
      <td>E</td>
    </tr>
    <tr>
      <th>18276</th>
      <td>5</td>
      <td>40</td>
      <td>E</td>
    </tr>
    <tr>
      <th>18277</th>
      <td>5</td>
      <td>41</td>
      <td>E</td>
    </tr>
    <tr>
      <th>19810</th>
      <td>6</td>
      <td>32</td>
      <td>E</td>
    </tr>
    <tr>
      <th>20247</th>
      <td>6</td>
      <td>37</td>
      <td>E</td>
    </tr>
    <tr>
      <th>20457</th>
      <td>6</td>
      <td>40</td>
      <td>E</td>
    </tr>
  </tbody>
</table>
</div>


