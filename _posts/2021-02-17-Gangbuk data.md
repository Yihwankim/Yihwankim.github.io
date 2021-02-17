# WTS : The price change in Gangbuk district 


```python
import pandas as pd
import numpy as np
```

read the excel file. We use the __real estate data__ from KB LiiV .


```python
df_Gangbuk = pd.read_excel('data(95to07).xlsx', sheet_name='price', header=0, skipfooter=0, usecols="A,C")
```

We want to make the new variables such as 'p_rate' , 'p_dot'  
**'p_rate'**(; the growth-rate of Gangbuk area) will be used to make x   
**'p_dot'** (; the rate of change of condominium price = 100 means that the price is unchanged)


```python
df_Gangbuk['L1_Gangbuk'] = df_Gangbuk['Gangbuk'].shift(1)
```


```python
df_Gangbuk['p_rate'] = (df_Gangbuk['Gangbuk'] / df_Gangbuk['L1_Gangbuk'] - 1) * 100
```


```python
df_Gangbuk['p_dot'] = (df_Gangbuk['Gangbuk'] / df_Gangbuk['L1_Gangbuk']) * 100
```

we don't use 'L1_Gangbuk' column anymore. __To fill the empty data of 'p-dot' & 'p-rate'__ remove 'L1_Gangbuk'


```python
df_Gangbuk = df_Gangbuk.drop('L1_Gangbuk', axis=1)
```


```python
df_Gangbuk = df_Gangbuk.fillna(method='bfill')
```

using the 'for' , we can make the variable 'x'


```python
df_Gangbuk['x0'] = np.where(df_Gangbuk['p_rate'] < 0, -1, 1)
```


```python
df_Gangbuk['cum_x0'] = df_Gangbuk['x0']
```


```python
for i in range(len(df_Gangbuk)):
    if i == 0:
        if df_Gangbuk['x0'][i] == 1:
            df_Gangbuk['cum_x0'][i] = 1
        else:
            df_Gangbuk['cum_x0'][i] = 0
    else:
        if df_Gangbuk['x0'][i] == df_Gangbuk['x0'][i - 1]:
            df_Gangbuk['cum_x0'][i] = df_Gangbuk['cum_x0'][i - 1] + df_Gangbuk['x0'][i]
        else:
            df_Gangbuk['cum_x0'][i] = df_Gangbuk['x0'][i]
```

    C:\Users\a\anaconda3\lib\site-packages\ipykernel_launcher.py:4: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      after removing the cwd from sys.path.
    C:\Users\a\anaconda3\lib\site-packages\ipykernel_launcher.py:9: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      if __name__ == '__main__':
    C:\Users\a\anaconda3\lib\site-packages\ipykernel_launcher.py:11: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      # This is added back by InteractiveShellApp.init_path()
    

Make the variable 'x', 'x' means the number of periods in the current house price growth regime.


```python
df_Gangbuk['x'] = df_Gangbuk['cum_x0']
```


```python
df_Gangbuk = df_Gangbuk.drop(['x0', 'cum_x0'], axis=1)
```

**Chonsei**


```python
df_Gangbuk_ch = pd.read_excel('data(95to07).xlsx', sheet_name='chonsei', header=0, skipfooter=0, usecols="C")
```


```python
df_Gangbuk_ch = df_Gangbuk_ch.rename({'Gangbuk':'Gangbuk_ch'},axis=1)
```


```python
df_Gangbuk = pd.concat([df_Gangbuk, df_Gangbuk_ch], axis=1)
```

**Sovereign yield**


```python
df_sovereign = pd.read_excel('data(95to07).xlsx', sheet_name='sovereign yield', header=0, skipfooter=0, usecols="B")
```


```python
df_Gangbuk = pd.concat([df_Gangbuk, df_sovereign], axis=1)
```

Make the variable R(rent), C(rent-price ratio), b(chonesei-price ratio) , i(sovereign yield)


```python
df_Gangbuk['R'] = df_Gangbuk['Gangbuk_ch'] * df_Gangbuk['sovereign yield']
```


```python
df_Gangbuk['c'] = df_Gangbuk['R'] / df_Gangbuk['Gangbuk']
```


```python
df_Gangbuk['b'] = df_Gangbuk['Gangbuk_ch'] / df_Gangbuk['Gangbuk']
```


```python
df_Gb = df_Gangbuk.rename({'sovereign yield': 'i'}, axis=1)
```


```python
df_Gb['p_dot-1'] = df_Gb['p_dot'].shift(3)
```


```python
df_Gb = df_Gb.fillna(method='bfill')
```

### In the df_GB, we can identify the variables to calculate the regression.


```python
df_Gb['lnP'] = np.log(df_Gb['p_dot'])
```


```python
df_Gb['lnP-1'] = np.log(df_Gb['p_dot-1'])
```


```python
df_Gb['lnc'] = np.log(df_Gb['c'])
```


```python
df_Gb['lni'] = np.log(df_Gb['i'])
```


```python
df_Gb['lnb'] = np.log(df_Gb['b'])
```


```python
df_GB = df_Gb.drop({'Gangbuk','Gangbuk_ch','R','p_dot','p_dot-1','i','c','b','p_rate'}, axis=1)
```

#### The way to arrange dataframe : df = df[['e','c','b','f','d','a']]


```python
df_GB = df_GB[['lnP','lnb','lnc','lnP-1','x','lni']]
```

#### export to excel


```python
df_GB.to_excel('Gangbuk_95to07.xlsx')
```

#### make the regression


```python
import statsmodels.api as sm
import statsmodels.formula.api as smf
import matplotlib as plt
import seaborn as sns
```

Set the dependent variable (Y), independent variable(X: vector)  
## regression 1)


```python
Y = df_GB['lnP']
```


```python
X1 = df_GB[['lnc', 'lnP-1', 'x']]
```


```python
X1m = sm.add_constant(X1)
```


```python
K1m = sm.OLS(Y, X1m).fit()
```


```python
K1m.summary()
```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>           <td>lnP</td>       <th>  R-squared:         </th> <td>   0.329</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.315</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   22.58</td>
</tr>
<tr>
  <th>Date:</th>             <td>Wed, 17 Feb 2021</td> <th>  Prob (F-statistic):</th> <td>5.89e-12</td>
</tr>
<tr>
  <th>Time:</th>                 <td>17:29:37</td>     <th>  Log-Likelihood:    </th> <td>  441.45</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>   142</td>      <th>  AIC:               </th> <td>  -874.9</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   138</td>      <th>  BIC:               </th> <td>  -863.1</td>
</tr>
<tr>
  <th>Df Model:</th>              <td>     3</td>      <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
    <td></td>       <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>const</th> <td>    4.8085</td> <td>    0.361</td> <td>   13.332</td> <td> 0.000</td> <td>    4.095</td> <td>    5.522</td>
</tr>
<tr>
  <th>lnc</th>   <td>   -0.0015</td> <td>    0.002</td> <td>   -0.631</td> <td> 0.529</td> <td>   -0.006</td> <td>    0.003</td>
</tr>
<tr>
  <th>lnP-1</th> <td>   -0.0435</td> <td>    0.078</td> <td>   -0.556</td> <td> 0.579</td> <td>   -0.198</td> <td>    0.111</td>
</tr>
<tr>
  <th>x</th>     <td>    0.0011</td> <td>    0.000</td> <td>    7.276</td> <td> 0.000</td> <td>    0.001</td> <td>    0.001</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td>15.568</td> <th>  Durbin-Watson:     </th> <td>   0.720</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.000</td> <th>  Jarque-Bera (JB):  </th> <td>  51.700</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 0.174</td> <th>  Prob(JB):          </th> <td>5.94e-12</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 5.935</td> <th>  Cond. No.          </th> <td>3.19e+03</td>
</tr>
</table><br/><br/>Warnings:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.<br/>[2] The condition number is large, 3.19e+03. This might indicate that there are<br/>strong multicollinearity or other numerical problems.



## regression 2)


```python
X2 = df_GB[['lnc','lnP-1','x','lni']]
```


```python
X2m = sm.add_constant(X2)
```


```python
K2m = sm.OLS(Y,X2m).fit()
```


```python
K2m.summary()
```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>           <td>lnP</td>       <th>  R-squared:         </th> <td>   0.468</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.453</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   30.17</td>
</tr>
<tr>
  <th>Date:</th>             <td>Wed, 17 Feb 2021</td> <th>  Prob (F-statistic):</th> <td>5.35e-18</td>
</tr>
<tr>
  <th>Time:</th>                 <td>17:30:30</td>     <th>  Log-Likelihood:    </th> <td>  457.94</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>   142</td>      <th>  AIC:               </th> <td>  -905.9</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   137</td>      <th>  BIC:               </th> <td>  -891.1</td>
</tr>
<tr>
  <th>Df Model:</th>              <td>     4</td>      <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
    <td></td>       <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>const</th> <td>    5.7961</td> <td>    0.362</td> <td>   16.008</td> <td> 0.000</td> <td>    5.080</td> <td>    6.512</td>
</tr>
<tr>
  <th>lnc</th>   <td>    0.0540</td> <td>    0.009</td> <td>    5.684</td> <td> 0.000</td> <td>    0.035</td> <td>    0.073</td>
</tr>
<tr>
  <th>lnP-1</th> <td>   -0.2564</td> <td>    0.078</td> <td>   -3.268</td> <td> 0.001</td> <td>   -0.412</td> <td>   -0.101</td>
</tr>
<tr>
  <th>x</th>     <td>    0.0012</td> <td>    0.000</td> <td>    8.529</td> <td> 0.000</td> <td>    0.001</td> <td>    0.001</td>
</tr>
<tr>
  <th>lni</th>   <td>   -0.0548</td> <td>    0.009</td> <td>   -5.985</td> <td> 0.000</td> <td>   -0.073</td> <td>   -0.037</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td>20.327</td> <th>  Durbin-Watson:     </th> <td>   0.838</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.000</td> <th>  Jarque-Bera (JB):  </th> <td>  59.856</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 0.458</td> <th>  Prob(JB):          </th> <td>1.01e-13</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 6.046</td> <th>  Cond. No.          </th> <td>3.63e+03</td>
</tr>
</table><br/><br/>Warnings:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.<br/>[2] The condition number is large, 3.63e+03. This might indicate that there are<br/>strong multicollinearity or other numerical problems.



## regression 3)


```python
X3 = df_GB[['lnb', 'lnP-1', 'x', 'lni']]
```


```python
X3m = sm.add_constant(X3)
```


```python
K3m = sm.OLS(Y,X3m).fit()
```


```python
K3m.summary()
```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>           <td>lnP</td>       <th>  R-squared:         </th> <td>   0.468</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.453</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   30.17</td>
</tr>
<tr>
  <th>Date:</th>             <td>Wed, 17 Feb 2021</td> <th>  Prob (F-statistic):</th> <td>5.35e-18</td>
</tr>
<tr>
  <th>Time:</th>                 <td>17:30:34</td>     <th>  Log-Likelihood:    </th> <td>  457.94</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>   142</td>      <th>  AIC:               </th> <td>  -905.9</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   137</td>      <th>  BIC:               </th> <td>  -891.1</td>
</tr>
<tr>
  <th>Df Model:</th>              <td>     4</td>      <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
    <td></td>       <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>const</th> <td>    5.7961</td> <td>    0.362</td> <td>   16.008</td> <td> 0.000</td> <td>    5.080</td> <td>    6.512</td>
</tr>
<tr>
  <th>lnb</th>   <td>    0.0540</td> <td>    0.009</td> <td>    5.684</td> <td> 0.000</td> <td>    0.035</td> <td>    0.073</td>
</tr>
<tr>
  <th>lnP-1</th> <td>   -0.2564</td> <td>    0.078</td> <td>   -3.268</td> <td> 0.001</td> <td>   -0.412</td> <td>   -0.101</td>
</tr>
<tr>
  <th>x</th>     <td>    0.0012</td> <td>    0.000</td> <td>    8.529</td> <td> 0.000</td> <td>    0.001</td> <td>    0.001</td>
</tr>
<tr>
  <th>lni</th>   <td>   -0.0008</td> <td>    0.002</td> <td>   -0.392</td> <td> 0.696</td> <td>   -0.005</td> <td>    0.003</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td>20.327</td> <th>  Durbin-Watson:     </th> <td>   0.838</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.000</td> <th>  Jarque-Bera (JB):  </th> <td>  59.856</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 0.458</td> <th>  Prob(JB):          </th> <td>1.01e-13</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 6.046</td> <th>  Cond. No.          </th> <td>3.59e+03</td>
</tr>
</table><br/><br/>Warnings:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.<br/>[2] The condition number is large, 3.59e+03. This might indicate that there are<br/>strong multicollinearity or other numerical problems.


