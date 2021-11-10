```python
# Import the required libraries and dependencies
import requests
import json
import pandas as pd
import numpy as np
from pathlib import Path
from MCForecastTools import MCSimulation
%matplotlib inline
```


```python
# Import the data by reading in the CSV file and setting the DatetimeIndex 
# Review the first 5 rows of eadh DataFrame
TSLA_navs_f = Path('../Starter_Code/Project#1/TSLA.csv')
TSLA_navs_df = pd.read_csv(TSLA_navs_f, infer_datetime_format=True, index_col='Date', parse_dates=True)
```


```python
USO_navs_f = Path('../Starter_Code/Project#1/USO.csv')
USO_navs_df = pd.read_csv(USO_navs_f, infer_datetime_format=True, index_col='Date', parse_dates=True)
```


```python
SPY_navs_f = Path('../Starter_Code/Project#1/SPY.csv')
SPY_navs_df = pd.read_csv(SPY_navs_f, infer_datetime_format=True, index_col='Date', parse_dates=True)
```


```python
TSLA_navs_df.head(), USO_navs_df.head(), SPY_navs_df.head()
```




    (             Open   High    Low  Close  Adj Close    Volume
     Date                                                       
     2010-11-09  5.000  5.138  4.810  4.926      4.926   4782000
     2010-11-10  4.896  5.994  4.810  5.872      5.872  15302500
     2010-11-11  5.720  5.820  5.466  5.608      5.608   9726500
     2010-11-12  5.650  6.100  5.614  5.968      5.968  13645500
     2010-11-15  6.044  6.588  6.044  6.160      6.160  13114500,
                       Open        High         Low       Close   Adj Close  \
     Date                                                                     
     2010-11-09  302.160004  302.480011  295.359985  296.399994  296.399994   
     2010-11-10  300.559998  304.480011  298.880005  304.320007  304.320007   
     2010-11-11  303.200012  304.320007  302.079987  302.720001  302.720001   
     2010-11-12  298.079987  299.279999  291.760010  291.760010  291.760010   
     2010-11-15  294.799988  295.359985  291.440002  292.000000  292.000000   
     
                  Volume  
     Date                 
     2010-11-09  1341425  
     2010-11-10  1945975  
     2010-11-11  1106150  
     2010-11-12  1769275  
     2010-11-15   958938  ,
                       Open        High         Low       Close  Adj Close  \
     Date                                                                    
     2010-11-09  122.820000  122.949997  121.120003  121.610001  98.179329   
     2010-11-10  121.580002  122.160004  120.660004  122.099998  98.574905   
     2010-11-11  121.050003  121.820000  120.680000  121.639999  98.203522   
     2010-11-12  120.820000  121.349998  119.650002  120.199997  97.040970   
     2010-11-15  120.580002  121.050003  119.980003  120.029999  96.903740   
     
                    Volume  
     Date                   
     2010-11-09  186621600  
     2010-11-10  221387400  
     2010-11-11  158017600  
     2010-11-12  239068800  
     2010-11-15  163940800  )




```python
# The current volume of shares for each company.
TSLA_shares = 100
USO_shares = 100
SPY_shares = 100
```

### Evaluate the Stock and Bond Holdings 

In this section, you’ll determine the current value of a member’s stock and bond holdings. You’ll make an API call to Alpaca via the Alpaca SDK to get the current closing prices of the SPDR S&P 500 ETF Trust (ticker: SPY) and of the iShares Core US Aggregate Bond ETF (ticker: AGG). For the prototype, assume that the member holds 110 shares of SPY, which represents the stock portion of their portfolio, and 200 shares of AGG, which represents the bond portion. To do all this, complete the following steps:

1. In the `Starter_Code` folder, create an environment file (`.env`) to store the values of your Alpaca API key and Alpaca secret key.

2. Set the variables for the Alpaca API and secret keys. Using the Alpaca SDK, create the Alpaca `tradeapi.REST` object. In this object, include the parameters for the Alpaca API key, the secret key, and the version number.

3. Set the following parameters for the Alpaca API call:

    - `tickers`: Use the tickers for the member’s stock and bond holdings.

    - `timeframe`: Use a time frame of one day.

    - `start_date` and `end_date`: Use the same date for these parameters, and format them with the date of the previous weekday (or `2020-08-07`). This is because you want the one closing price for the most-recent trading day.

4. Get the current closing prices for `SPY` and `AGG` by using the Alpaca `get_barset` function. Format the response as a Pandas DataFrame by including the `df` property at the end of the `get_barset` function.

5. Navigating the Alpaca response DataFrame, select the `SPY` and `AGG` closing prices, and store them as variables.

6. Calculate the value, in US dollars, of the current amount of shares in each of the stock and bond portions of the portfolio, and print the results.


#### Step 3: Set the following parameters for the Alpaca API call:

- `tickers`: Use the tickers for the member’s stock and bond holdings.

- `timeframe`: Use a time frame of one day.

- `start_date` and `end_date`: Use the same date for these parameters, and format them with the date of the previous weekday (or `2020-08-07`). This is because you want the one closing price for the most-recent trading day.



```python
# Set the tickers for both the bond and stock portion of the portfolio
tickers = ["TSLA", "USO", "SPY"]
# Set timeframe to 1D 
timeframe = "1D"

# Format current date as ISO format
# Set both the start and end date at the date of your prior weekday 
# This will give you the closing price of the previous trading day
# Alternatively you can use a start and end date of 2020-08-07
start_date = pd.Timestamp("2010-11-08", tz="America/New_York").isoformat()
end_date = pd.Timestamp("2021-11-08", tz="America/New_York").isoformat()


```

#### Step 4: Get the current closing prices for `SPY` and `AGG` by using the Alpaca `get_barset` function. Format the response as a Pandas DataFrame by including the `df` property at the end of the `get_barset` function.

#### Step 5: Navigating the Alpaca response DataFrame, select the `SPY` and `AGG` closing prices, and store them as variables.


```python
# Access the closing price for AGG from the Alpaca DataFrame
# Converting the value to a floating point number
TSLA_close_price = TSLA_navs_df["Close"]
TSLA_close_price

# Print the AGG closing price
print(TSLA_close_price)

```

    Date
    2010-11-09       4.926000
    2010-11-10       5.872000
    2010-11-11       5.608000
    2010-11-12       5.968000
    2010-11-15       6.160000
                     ...     
    2021-11-02    1172.000000
    2021-11-03    1213.859985
    2021-11-04    1229.910034
    2021-11-05    1222.089966
    2021-11-08    1162.939941
    Name: Close, Length: 2769, dtype: float64
    


```python
# Access the closing price for SPY from the Alpaca DataFrame
# Converting the value to a floating point number
USO_close_price = USO_navs_df["Close"]
USO_close_price

# Print the SPY closing price
print(USO_close_price)

```

    Date
    2010-11-09    296.399994
    2010-11-10    304.320007
    2010-11-11    302.720001
    2010-11-12    291.760010
    2010-11-15    292.000000
                     ...    
    2021-11-02     57.529999
    2021-11-03     55.340000
    2021-11-04     54.919998
    2021-11-05     56.549999
    2021-11-08     57.090000
    Name: Close, Length: 2769, dtype: float64
    


```python
# Access the closing price for SPY from the Alpaca DataFrame
# Converting the value to a floating point number
SPY_close_price = SPY_navs_df["Close"]
SPY_close_price

# Print the SPY closing price
print(SPY_close_price)

```

    Date
    2010-11-09    121.610001
    2010-11-10    122.099998
    2010-11-11    121.639999
    2010-11-12    120.199997
    2010-11-15    120.029999
                     ...    
    2021-11-02    461.899994
    2021-11-03    464.720001
    2021-11-04    466.910004
    2021-11-05    468.529999
    2021-11-08    468.929993
    Name: Close, Length: 2769, dtype: float64
    

#### Step 6: Calculate the value, in US dollars, of the current amount of shares in each of the stock and bond portions of the portfolio, and print the results.


```python
# Calculate the current value of the bond portion of the portfolio
TSLA_value_recent = TSLA_close_price[-1] * TSLA_shares
TSLA_value_recent

# Print the current value of the bond portfolio
print(f"The current value of TSLA stock is ${TSLA_value_recent}")

```

    The current value of TSLA stock is $116293.99410000001
    


```python
# Calculate the current value of the bond portion of the portfolio
TSLA_value_middle = TSLA_close_price[-1384] * TSLA_shares
TSLA_value_middle

# Print the current value of the bond portfolio
print(f"The value of TSLA stock in the middle of 2015 is ${TSLA_value_middle}")

```

    The value of TSLA stock in the middle of 2015 is $4145.6001
    


```python
# Calculate the current value of the bond portion of the portfolio
TSLA_value_early = TSLA_close_price[-2700] * TSLA_shares
TSLA_value_early

# Print the current value of the bond portfolio
print(f"The early value of TSLA stock is ${TSLA_value_early}")

```

    The early value of TSLA stock is $472.0
    


```python
# Calculate the current value of the stock portion of the portfolio
USO_value_recent = USO_close_price[-1] * USO_shares
USO_value_recent

# Print the current value of the stock portfolio
print(f"The current value of the USO stock is ${USO_value_recent}")

```

    The current value of the USO stock is $5709.0
    


```python
# Calculate the current value of the stock portion of the portfolio
USO_value_middle = USO_close_price[-1384] * USO_shares
USO_value_middle

# Print the current value of the stock portfolio
print(f"The value of the USO stock in the middle of 2015 is ${USO_value_middle}")

```

    The value of the USO stock in the middle of 2015 is $9159.9998
    


```python
# Calculate the current value of the stock portion of the portfolio
USO_value_early = USO_close_price[-2700] * USO_shares
USO_value_early

# Print the current value of the stock portfolio
print(f"The early value of the USO stock is ${USO_value_early}")

```

    The early value of the USO stock is $28848.001099999998
    


```python
# Calculate the current value of the stock portion of the portfolio
SPY_value_recent = SPY_close_price[-1] * SPY_shares
SPY_value_recent

# Print the current value of the stock portfolio
print(f"The current vale of the SPY stock is ${SPY_value_recent}")

```

    The current vale of the SPY stock is $46892.9993
    


```python
# Calculate the current value of the stock portion of the portfolio
SPY_value_middle = SPY_close_price[-1384] * SPY_shares
SPY_value_middle

# Print the current value of the stock portfolio
print(f"The value of the SPY stock in the middle of 2015 is ${SPY_value_middle}")

```

    The value of the SPY stock in the middle of 2015 is $20655.9998
    


```python
# Calculate the current value of the stock portion of the portfolio
SPY_value_early = SPY_close_price[-2700] * SPY_shares
SPY_value_early

# Print the current value of the stock portfolio
print(f"The early value of the SPY stock is ${SPY_value_early}")

```

    The early value of the SPY stock is $13425.0
    

### Evaluate the Emergency Fund

In this section, you’ll use the valuations for the cryptocurrency wallet and for the stock and bond portions of the portfolio to determine if the credit union member has enough savings to build an emergency fund into their financial plan. To do this, complete the following steps:

1. Create a Python list named `savings_data` that has two elements. The first element contains the total value of the cryptocurrency wallet. The second element contains the total value of the stock and bond portions of the portfolio.

2. Use the `savings_data` list to create a Pandas DataFrame named `savings_df`, and then display this DataFrame. The function to create the DataFrame should take the following three parameters:

    - `savings_data`: Use the list that you just created.

    - `columns`: Set this parameter equal to a Python list with a single value called `amount`.

    - `index`: Set this parameter equal to a Python list with the values of `crypto` and `stock/bond`.

3. Use the `savings_df` DataFrame to plot a pie chart that visualizes the composition of the member’s portfolio. The y-axis of the pie chart uses `amount`. Be sure to add a title.

4. Using Python, determine if the current portfolio has enough to create an emergency fund as part of the member’s financial plan. Ideally, an emergency fund should equal to three times the member’s monthly income. To do this, implement the following steps:

    1. Create a variable named `emergency_fund_value`, and set it equal to three times the value of the member’s `monthly_income` of $12000. (You set this earlier in Part 1).

    2. Create a series of three if statements to determine if the member’s total portfolio is large enough to fund the emergency portfolio:

        1. If the total portfolio value is greater than the emergency fund value, display a message congratulating the member for having enough money in this fund.

        2. Else if the total portfolio value is equal to the emergency fund value, display a message congratulating the member on reaching this important financial goal.

        3. Else the total portfolio is less than the emergency fund value, so display a message showing how many dollars away the member is from reaching the goal. (Subtract the total portfolio value from the emergency fund value.)


#### Step 2: Use the `savings_data` list to create a Pandas DataFrame for each of the three stocks, and then display these DataFrames. The function to create the DataFrame should take the following three parameters:

- `savings_data`: Use the list that you just created.

- `columns`: Set this parameter equal to a Python list with a single value called `amount`.

- `index`: Set this parameter equal to a Python list with the values of `crypto` and `stock/bond`.



```python
# Create a Pandas DataFrame called savings_df 
display(type('amount'))
savings_df = pd.DataFrame(data=savings_data, index=['crypto', 'stock/bond'], columns=['amount'])

# Display the savings_df DataFrame
display(savings_df)

```


```python
display(type('Close'))
TSLA_df = pd.DataFrame(data= TSLA_navs_df, columns=['Close'])
display(TSLA_df)
```


    str



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
      <th>Close</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2010-11-09</th>
      <td>4.926000</td>
    </tr>
    <tr>
      <th>2010-11-10</th>
      <td>5.872000</td>
    </tr>
    <tr>
      <th>2010-11-11</th>
      <td>5.608000</td>
    </tr>
    <tr>
      <th>2010-11-12</th>
      <td>5.968000</td>
    </tr>
    <tr>
      <th>2010-11-15</th>
      <td>6.160000</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>2021-11-02</th>
      <td>1172.000000</td>
    </tr>
    <tr>
      <th>2021-11-03</th>
      <td>1213.859985</td>
    </tr>
    <tr>
      <th>2021-11-04</th>
      <td>1229.910034</td>
    </tr>
    <tr>
      <th>2021-11-05</th>
      <td>1222.089966</td>
    </tr>
    <tr>
      <th>2021-11-08</th>
      <td>1162.939941</td>
    </tr>
  </tbody>
</table>
<p>2769 rows × 1 columns</p>
</div>



```python
display(type('Close'))
USO_df = pd.DataFrame(data= USO_navs_df, columns=['Close'])
display(USO_df)
```


    str



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
      <th>Close</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2010-11-09</th>
      <td>296.399994</td>
    </tr>
    <tr>
      <th>2010-11-10</th>
      <td>304.320007</td>
    </tr>
    <tr>
      <th>2010-11-11</th>
      <td>302.720001</td>
    </tr>
    <tr>
      <th>2010-11-12</th>
      <td>291.760010</td>
    </tr>
    <tr>
      <th>2010-11-15</th>
      <td>292.000000</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>2021-11-02</th>
      <td>57.529999</td>
    </tr>
    <tr>
      <th>2021-11-03</th>
      <td>55.340000</td>
    </tr>
    <tr>
      <th>2021-11-04</th>
      <td>54.919998</td>
    </tr>
    <tr>
      <th>2021-11-05</th>
      <td>56.549999</td>
    </tr>
    <tr>
      <th>2021-11-08</th>
      <td>57.090000</td>
    </tr>
  </tbody>
</table>
<p>2769 rows × 1 columns</p>
</div>



```python
display(type('Close'))
SPY_df = pd.DataFrame(data= SPY_navs_df, columns=['Close'])
display(SPY_df)
```


    str



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
      <th>Close</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2010-11-09</th>
      <td>121.610001</td>
    </tr>
    <tr>
      <th>2010-11-10</th>
      <td>122.099998</td>
    </tr>
    <tr>
      <th>2010-11-11</th>
      <td>121.639999</td>
    </tr>
    <tr>
      <th>2010-11-12</th>
      <td>120.199997</td>
    </tr>
    <tr>
      <th>2010-11-15</th>
      <td>120.029999</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>2021-11-02</th>
      <td>461.899994</td>
    </tr>
    <tr>
      <th>2021-11-03</th>
      <td>464.720001</td>
    </tr>
    <tr>
      <th>2021-11-04</th>
      <td>466.910004</td>
    </tr>
    <tr>
      <th>2021-11-05</th>
      <td>468.529999</td>
    </tr>
    <tr>
      <th>2021-11-08</th>
      <td>468.929993</td>
    </tr>
  </tbody>
</table>
<p>2769 rows × 1 columns</p>
</div>


#### Step 3: Use the `savings_df` DataFrame to plot a pie chart that visualizes the composition of the member’s portfolio. The y-axis of the pie chart uses `amount`. Be sure to add a title.


```python
# Plot the total value of the member's portfolio (crypto and stock/bond) in a pie chart
savings_df.plot.pie(y='amount', title="Total Savings made up of Crypto Wallet and Stock & Bonds")

```


```python
recent_price_df = [
    TSLA_value_recent,
    USO_value_recent, 
    SPY_value_recent
]
display(recent_price_df)
```


    [116293.99410000001, 5709.0, 46892.9993]



```python
recent_price_pddf = pd.DataFrame(
    data=recent_price_df,
    index=['TSLA','USO','SPY'],
    columns=['Close']
)
recent_price_pddf
display(recent_price_pddf)
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
      <th>Close</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>TSLA</th>
      <td>116293.9941</td>
    </tr>
    <tr>
      <th>USO</th>
      <td>5709.0000</td>
    </tr>
    <tr>
      <th>SPY</th>
      <td>46892.9993</td>
    </tr>
  </tbody>
</table>
</div>



```python
middle_price_df = [
    TSLA_value_middle, 
    USO_value_middle, 
    SPY_value_middle
]
display(middle_price_df)
```


    [4145.6001, 9159.9998, 20655.9998]



```python
middle_price_pddf = pd.DataFrame(
    data=middle_price_df,
    index=['TSLA', 'USO', 'SPY'],
    columns=['Close']
)
display(middle_price_pddf)
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
      <th>Close</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>TSLA</th>
      <td>4145.6001</td>
    </tr>
    <tr>
      <th>USO</th>
      <td>9159.9998</td>
    </tr>
    <tr>
      <th>SPY</th>
      <td>20655.9998</td>
    </tr>
  </tbody>
</table>
</div>



```python
early_price_df = [
    TSLA_value_early,
    USO_value_early,
    SPY_value_early
]
display(early_price_df)
```


    [472.0, 28848.001099999998, 13425.0]



```python
early_price_pddf = pd.DataFrame(
    data=early_price_df,
    index=['TSLA', 'USO', 'SPY'],
    columns=['Close']
)
display(early_price_pddf)
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
      <th>Close</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>TSLA</th>
      <td>472.0000</td>
    </tr>
    <tr>
      <th>USO</th>
      <td>28848.0011</td>
    </tr>
    <tr>
      <th>SPY</th>
      <td>13425.0000</td>
    </tr>
  </tbody>
</table>
</div>



```python
recent_price_pddf.plot.pie(y='Close', title='Most Recent value of TSLA, USO, and SPY Stocks')
```




    <AxesSubplot:title={'center':'Most Recent value of TSLA, USO, and SPY Stocks'}, ylabel='Close'>




    
![png](output_38_1.png)
    



```python

middle_price_pddf.plot.pie(y='Close', title='Value of TSLA, USO, and SPY stocks in the middle of 2015')
```




    <AxesSubplot:title={'center':'Value of TSLA, USO, and SPY stocks in the middle of 2015'}, ylabel='Close'>




    
![png](output_39_1.png)
    



```python

early_price_pddf.plot.pie(y='Close', title='Value of TSLA, USO, and SPY stock prices at the beginning of 2010')
```




    <AxesSubplot:title={'center':'Value of TSLA, USO, and SPY stock prices at the beginning of 2010'}, ylabel='Close'>




    
![png](output_40_1.png)
    


#### Step 4: Using Python, determine if the current portfolio has enough to create an emergency fund as part of the member’s financial plan. Ideally, an emergency fund should equal to three times the member’s monthly income. To do this, implement the following steps:

Step 1. Create a variable named `emergency_fund_value`, and set it equal to three times the value of the member’s `monthly_income` of 12000. (You set this earlier in Part 1).

Step 2. Create a series of three if statements to determine if the member’s total portfolio is large enough to fund the emergency portfolio:

* If the total portfolio value is greater than the emergency fund value, display a message congratulating the member for having enough money in this fund.

* Else if the total portfolio value is equal to the emergency fund value, display a message congratulating the member on reaching this important financial goal.

* Else the total portfolio is less than the emergency fund value, so display a message showing how many dollars away the member is from reaching the goal. (Subtract the total portfolio value from the emergency fund value.)


##### Step 4-1: Create a variable named `emergency_fund_value`, and set it equal to three times the value of the member’s `monthly_income` of 12000. (You set this earlier in Part 1).


```python
# Create a variable named emergency_fund_value
emergency_fund_value = 3 * monthly_income
emergency_fund_value
```

##### Step 4-2: Create a series of three if statements to determine if the member’s total portfolio is large enough to fund the emergency portfolio:

* If the total portfolio value is greater than the emergency fund value, display a message congratulating the member for having enough money in this fund.

* Else if the total portfolio value is equal to the emergency fund value, display a message congratulating the member on reaching this important financial goal.

* Else the total portfolio is less than the emergency fund value, so display a message showing how many dollars away the member is from reaching the goal. (Subtract the total portfolio value from the emergency fund value.)


```python
# Evaluate the possibility of creating an emergency fund with 3 conditions:
if total_portfolio > emergency_fund_value:
    print("Congradulations for having enough money in your fund!")
elif total_portfolio == emergency_fund_value:
    print("Congradulations on reaching an important financial goal!")
elif total_portfolio < emergency_fund_value:
    print("You are currently ${emergency_fund_value - total_portfolio} away from your financial goal.  Keep working hard!")

```

## Part 2: Create a Financial Planner for Retirement

### Create the Monte Carlo Simulation

In this section, you’ll use the MCForecastTools library to create a Monte Carlo simulation for the member’s savings portfolio. To do this, complete the following steps:

1. Make an API call via the Alpaca SDK to get 3 years of historical closing prices for a traditional 60/40 portfolio split: 60% stocks (SPY) and 40% bonds (AGG).

2. Run a Monte Carlo simulation of 500 samples and 30 years for the 60/40 portfolio, and then plot the results.The following image shows the overlay line plot resulting from a simulation with these characteristics. However, because a random number generator is used to run each live Monte Carlo simulation, your image will differ slightly from this exact image:

![A screenshot depicts the resulting plot.](Images/5-4-monte-carlo-line-plot.png)

3. Plot the probability distribution of the Monte Carlo simulation. Plot the probability distribution of the Monte Carlo simulation. The following image shows the histogram plot resulting from a simulation with these characteristics. However, because a random number generator is used to run each live Monte Carlo simulation, your image will differ slightly from this exact image:

![A screenshot depicts the histogram plot.](Images/5-4-monte-carlo-histogram.png)

4. Generate the summary statistics for the Monte Carlo simulation.



#### Step 1: Make an API call via the Alpaca SDK to get 3 years of historical closing prices for a traditional 60/40 portfolio split: 60% stocks (SPY) and 40% bonds (AGG).


```python
# Set the tickers for both the bond and stock portion of the portfolio
tickers = ["TSLA", "USO", "SPY"]
# Set timeframe to 1D 
timeframe = "1D"

# Format current date as ISO format
# Set both the start and end date at the date of your prior weekday 
# This will give you the closing price of the previous trading day
# Alternatively you can use a start and end date of 2020-08-07
start_date = pd.Timestamp("2010-11-08", tz="America/New_York").isoformat()
end_date = pd.Timestamp("2021-11-08", tz="America/New_York").isoformat()

# Set number of rows to 1000 to retrieve the maximum amount of rows
limit_rows = 1000
```

#### Step 2: Run a Monte Carlo simulation of 500 samples and 30 years for the 60/40 portfolio, and then plot the results.


```python
# Configure the Monte Carlo simulation to forecast 30 years cumulative returns
# The weights should be split 40% to AGG and 60% to SPY.
# Run 500 samples.
MC_recent = MCSimulation(
    portfolio_data= recent_price_pddf,
    weights= [.33, .33, .33], 
    num_simulation= 500,
    num_trading_days= 252 * 11
)

# Review the simulation input data
MC_recent.portfolio_data.head()
```


    ---------------------------------------------------------------------------

    IndexError                                Traceback (most recent call last)

    <ipython-input-59-4eb0b52ea56e> in <module>
          2 # The weights should be split 40% to AGG and 60% to SPY.
          3 # Run 500 samples.
    ----> 4 MC_recent = MCSimulation(
          5     portfolio_data= recent_price_pddf,
          6     weights= [.33, .33, .33],
    

    ~\Downloads\FAstartercode(5)\Starter_Code\MCForecastTools.py in __init__(self, portfolio_data, weights, num_simulation, num_trading_days)
         59 
         60         # Calculate daily return if not within dataframe
    ---> 61         if not "daily_return" in portfolio_data.columns.get_level_values(1).unique():
         62             close_df = portfolio_data.xs('close',level=1,axis=1).pct_change()
         63             tickers = portfolio_data.columns.get_level_values(0).unique()
    

    ~\anaconda3\lib\site-packages\pandas\core\indexes\base.py in _get_level_values(self, level)
       1554         Index(['a', 'b', 'c'], dtype='object')
       1555         """
    -> 1556         self._validate_index_level(level)
       1557         return self
       1558 
    

    ~\anaconda3\lib\site-packages\pandas\core\indexes\base.py in _validate_index_level(self, level)
       1473                 )
       1474             elif level > 0:
    -> 1475                 raise IndexError(
       1476                     f"Too many levels: Index has only 1 level, not {level + 1}"
       1477                 )
    

    IndexError: Too many levels: Index has only 1 level, not 2



```python
# Run the Monte Carlo simulation to forecast 30 years cumulative returns
MC_thiryyear.calc_cumulative_return()

```


```python
# Visualize the 30-year Monte Carlo simulation by creating an
# overlay line plot
MC_sim__line_plot = MC_thiryyear.plot_simulation()

MC_sim__line_plot.get_figure().savefig("MC_thirtyyear_sim_plot.png", bbox_inches="tight")

```

#### Step 3: Plot the probability distribution of the Monte Carlo simulation.


```python
# Visualize the probability distribution of the 30-year Monte Carlo simulation 
# by plotting a histogram
MC_sim__line_plot = MC_thiryyear.plot_distribution()


```

#### Step 4: Generate the summary statistics for the Monte Carlo simulation.


```python
# Generate summary statistics from the 30-year Monte Carlo simulation results
# Save the results as a variable
MC_summary_statistics = MC_thiryyear.summarize_cumulative_return()
MC_summary_statistics


# Review the 30-year Monte Carlo summary statistics
MC_summary_statistics
```

### Analyze the Retirement Portfolio Forecasts

Using the current value of only the stock and bond portion of the member's portfolio and the summary statistics that you generated from the Monte Carlo simulation, answer the following question in your Jupyter notebook:

-  What are the lower and upper bounds for the expected value of the portfolio with a 95% confidence interval?



```python
# Print the current balance of the stock and bond portion of the members portfolio
print(f"The current balance of the stock and bond portion of the members portfolio is ${total_portfolio}")

```


```python
# Use the lower and upper `95%` confidence intervals to calculate the range of the possible outcomes for the current stock/bond portfolio
ci_lower_thirty_cumulative_return = MC_summary_statistics[8] * 10000
ci_upper_thirty_cumulative_return = MC_summary_statistics[9] * 10000

# Print the result of your calculations
print(f"There is a 95% chance that an interval investment of $10,000 in the portfolio"
f" over the next five years will end within the range of "
f" ${ci_lower_thirty_cumulative_return: .2f} and ${ci_upper_thirty_cumulative_return: .2f}.")

```

### Forecast Cumulative Returns in 10 Years

The CTO of the credit union is impressed with your work on these planning tools but wonders if 30 years is a long time to wait until retirement. So, your next task is to adjust the retirement portfolio and run a new Monte Carlo simulation to find out if the changes will allow members to retire earlier.

For this new Monte Carlo simulation, do the following: 

- Forecast the cumulative returns for 10 years from now. Because of the shortened investment horizon (30 years to 10 years), the portfolio needs to invest more heavily in the riskier asset&mdash;that is, stock&mdash;to help accumulate wealth for retirement. 

- Adjust the weights of the retirement portfolio so that the composition for the Monte Carlo simulation consists of 20% bonds and 80% stocks. 

- Run the simulation over 500 samples, and use the same data that the API call to Alpaca generated.

- Based on the new Monte Carlo simulation, answer the following questions in your Jupyter notebook:

    - Using the current value of only the stock and bond portion of the member's portfolio and the summary statistics that you generated from the new Monte Carlo simulation, what are the lower and upper bounds for the expected value of the portfolio (with the new weights) with a 95% confidence interval?

    - Will weighting the portfolio more heavily toward stocks allow the credit union members to retire after only 10 years?



```python
# Configure a Monte Carlo simulation to forecast 10 years cumulative returns
# The weights should be split 20% to AGG and 80% to SPY.
# Run 500 samples.
MC_tenyear = MCSimulation(
    portfolio_data=prices_df,
    weights= [.20, .80],
    num_simulation=500,
    num_trading_days=252*10
)

# Review the simulation input data
MC_tenyear.portfolio_data.head()

```


```python
# Run the Monte Carlo simulation to forecast 10 years cumulative returns
MC_tenyear.calc_cumulative_return()

```


```python
# Visualize the 10-year Monte Carlo simulation by creating an
# overlay line plot
MC_sim__line_plot = MC_tenyear.plot_simulation()
MC_sim__line_plot.get_figure().savefig("MC_tenyear_sim_plot_line.png", bbox_inches="tight")

```


```python
# Visualize the probability distribution of the 10-year Monte Carlo simulation 
# by plotting a histogram
MC_sim__line_plot = MC_tenyear.plot_distribution()

```


```python
# Generate summary statistics from the 10-year Monte Carlo simulation results
# Save the results as a variable
MC_summary_statistics_ten = MC_tenyear.summarize_cumulative_return()
MC_summary_statistics_ten


# Review the 10-year Monte Carlo summary statistics
MC_summary_statistics_ten
```

### Answer the following questions:

#### Question: Using the current value of only the stock and bond portion of the member's portfolio and the summary statistics that you generated from the new Monte Carlo simulation, what are the lower and upper bounds for the expected value of the portfolio (with the new weights) with a 95% confidence interval?


```python
# Print the current balance of the stock and bond portion of the members portfolio
print(f"The current balance of the stock and bond portion of the members portfolio is ${total_portfolio}")

```


```python
# Use the lower and upper `95%` confidence intervals to calculate the range of the possible outcomes for the current stock/bond portfolio
ci_lower_ten_cumulative_return = MC_summary_statistics_ten[8] * 10000
ci_upper_ten_cumulative_return = MC_summary_statistics_ten[9] * 10000

# Print the result of your calculations
print(f"There is a 95% chance that an interval investment of $10,000 in the portfolio"
f" over the next five years will end within the range of "
f" ${ci_lower_ten_cumulative_return: .2f} and ${ci_upper_ten_cumulative_return: .2f}.")

```

#### Question: Will weighting the portfolio more heavily to stocks allow the credit union members to retire after only 10 years?
No the credit union members won't be able to retire after 10 years.