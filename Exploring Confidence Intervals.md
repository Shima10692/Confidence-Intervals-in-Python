# Activity: Explore confidence intervals

## Introduction

The Air Quality Index (AQI) is the Environmental Protection Agency's index for reporting air quality. A value close to 0 signals little to no public health concern, while higher values are associated with increased risk to public health. The United States is considering a new federal policy that would create a subsidy for renewable energy in states observing an average AQI of 10 or above. <br>

You've just started your new role as a data analyst in the Strategy division of Ripple Renewable Energy (RRE). **RRE operates in the following U.S. states: `California`, `Florida`, `Michigan`, `Ohio`, `Pennsylvania`, `Texas`.** You've been tasked with constructing an analysis which identifies which of these states are most likely to be affected, should the new federal policy be enacted.

Your manager has requested that you do the following for your analysis:
1. Provide a summary of the mean AQI for the states in which RRE operates.
2. Construct a boxplot visualization for AQI of these states using `seaborn`.
3. Evaluate which state(s) may be most affected by this policy, based on the data and your boxplot visualization.
4. Construct a confidence interval for the RRE state with the highest mean AQI.

## Step 1: Imports

### Import packages

Import `pandas` and `numpy`.


```python
# Import relevant packages

import pandas as pd
import numpy as np
from scipy import stats
```

### Load the dataset

The dataset provided gives national Air Quality Index (AQI) measurements by state over time.  `Pandas` is used to import the file `c4_epa_air_quality.csv` as a DataFrame named `aqi`. As shown in this cell, the dataset has been automatically loaded in for you. You do not need to download the .csv file, or provide more code, in order to access the dataset and proceed with this lab. Please continue with this activity by completing the following instructions.

*Note: For the purposes of your analysis, you can assume this data is randomly sampled from a larger population.*


```python
# RUN THIS CELL TO IMPORT YOUR DATA

### YOUR CODE HERE ###
aqi = pd.read_csv('c4_epa_air_quality.csv')
```

## Step 2: Data exploration

### Explore your dataset

Before proceeding to your deliverables, spend some time exploring the `aqi` DataFrame. 


```python
# Explore your DataFrame `aqi`.

print(aqi.describe(include = "all"))

print("For a more thorough examination of observations by state use values_counts()")
print(aqi['state_name'].value_counts())

### YOUR CODE HERE ###
```

            Unnamed: 0  date_local  state_name  county_name      city_name  \
    count   260.000000         260         260          260            260   
    unique         NaN           1          52          149            190   
    top            NaN  2018-01-01  California  Los Angeles  Not in a city   
    freq           NaN         260          66           14             21   
    mean    129.500000         NaN         NaN          NaN            NaN   
    std      75.199734         NaN         NaN          NaN            NaN   
    min       0.000000         NaN         NaN          NaN            NaN   
    25%      64.750000         NaN         NaN          NaN            NaN   
    50%     129.500000         NaN         NaN          NaN            NaN   
    75%     194.250000         NaN         NaN          NaN            NaN   
    max     259.000000         NaN         NaN          NaN            NaN   
    
           local_site_name   parameter_name   units_of_measure  arithmetic_mean  \
    count              257              260                260       260.000000   
    unique             253                1                  1              NaN   
    top            Kapolei  Carbon monoxide  Parts per million              NaN   
    freq                 2              260                260              NaN   
    mean               NaN              NaN                NaN         0.403169   
    std                NaN              NaN                NaN         0.317902   
    min                NaN              NaN                NaN         0.000000   
    25%                NaN              NaN                NaN         0.200000   
    50%                NaN              NaN                NaN         0.276315   
    75%                NaN              NaN                NaN         0.516009   
    max                NaN              NaN                NaN         1.921053   
    
                   aqi  
    count   260.000000  
    unique         NaN  
    top            NaN  
    freq           NaN  
    mean      6.757692  
    std       7.061707  
    min       0.000000  
    25%       2.000000  
    50%       5.000000  
    75%       9.000000  
    max      50.000000  
    For a more thorough examination of observations by state use values_counts()
    California              66
    Arizona                 14
    Ohio                    12
    Florida                 12
    Texas                   10
    New York                10
    Pennsylvania            10
    Michigan                 9
    Colorado                 9
    Minnesota                7
    New Jersey               6
    Indiana                  5
    North Carolina           4
    Massachusetts            4
    Maryland                 4
    Oklahoma                 4
    Virginia                 4
    Nevada                   4
    Connecticut              4
    Kentucky                 3
    Missouri                 3
    Wyoming                  3
    Iowa                     3
    Hawaii                   3
    Utah                     3
    Vermont                  3
    Illinois                 3
    New Hampshire            2
    District Of Columbia     2
    New Mexico               2
    Montana                  2
    Oregon                   2
    Alaska                   2
    Georgia                  2
    Washington               2
    Idaho                    2
    Nebraska                 2
    Rhode Island             2
    Tennessee                2
    Maine                    2
    South Carolina           1
    Puerto Rico              1
    Arkansas                 1
    Kansas                   1
    Mississippi              1
    Alabama                  1
    Louisiana                1
    Delaware                 1
    South Dakota             1
    West Virginia            1
    North Dakota             1
    Wisconsin                1
    Name: state_name, dtype: int64


**Question:** What time range does this data cover?

1 day (2018-01-01)


**Question:** What are the minimum and maximum AQI values observed in the dataset?

Min = 0
Max = 50

**Question:** Are all states equally represented in the dataset?

Yes, there are 52 states covered, however California is covered more than most

## Step 3: Statistical tests

### Summarize the mean AQI for RRE states

Start with your first deliverable. Summarize the mean AQI for the states in which RRE operates (California, Florida, Michigan, Ohio, Pennsylvania, and Texas).


```python
# Summarize the mean AQI for RRE states.

# Create a list of RRE states.
RRE_states = ["California", "Florida", "Michigan", "Ohio", "Pennsylvania", "Texas"]

# Subset `aqi` to only consider these states.
aqi_rre = aqi[aqi["state_name"].isin(RRE_states)]

# Find the mean aqi for each of the RRE states.
aqi_rre.groupby("state_name").agg({"aqi" : "mean", "state_name":"count"})
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
      <th>aqi</th>
      <th>state_name</th>
    </tr>
    <tr>
      <th>state_name</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>California</th>
      <td>12.121212</td>
      <td>66</td>
    </tr>
    <tr>
      <th>Florida</th>
      <td>5.500000</td>
      <td>12</td>
    </tr>
    <tr>
      <th>Michigan</th>
      <td>8.111111</td>
      <td>9</td>
    </tr>
    <tr>
      <th>Ohio</th>
      <td>3.333333</td>
      <td>12</td>
    </tr>
    <tr>
      <th>Pennsylvania</th>
      <td>2.900000</td>
      <td>10</td>
    </tr>
    <tr>
      <th>Texas</th>
      <td>2.700000</td>
      <td>10</td>
    </tr>
  </tbody>
</table>
</div>



### Construct a boxplot visualization for the AQI of these states

Seaborn is a simple visualization library, commonly imported as `sns`. Import `seaborn`. Then utilize a boxplot visualization from this library to compare the distributions of AQI scores by state.


```python
# Import seaborn as sns.

import seaborn as sns
```

### Create an in-line visualization showing the distribution of `aqi` by `state_name`

Now, create an in-line visualization showing the distribution of `aqi` by `state_name`.


```python
sns.boxplot(data = aqi_rre, x = "state_name", y = "aqi")
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7e49f4788a50>




![png](output_19_1.png)


**Question:** Based on the data and your visualizations, which state(s) do you suspect will be most affected by this policy?

California

<details>
  <summary><h4><strong>Hint 1</strong></h4></summary>

Consider the mean AQI for the RRE states, as well as the distribution in the boxplots relative to the policy limit (10).
    
</details>

### Construct a confidence interval for the RRE state with the highest mean AQI

Recall the 4-step process in constructing a confidence interval:

1.   Identify a sample statistic.
2.   Choose a confidence level.
3.   Find the margin of error. 
4.   Calculate the interval.

### Construct your sample statistic

To contruct your sample statistic, find the mean AQI for your state.


```python
# Find the mean aqi for your state.

california_aqi_mean = aqi_rre[aqi_rre["state_name"] == "California"].mean()["aqi"]
```

### Choose your confidence level

Choose your confidence level for your analysis. The most typical confidence level chosen is 95%; however, you can choose 90% or 99% if you want decrease or increase (respectively) your level of confidence about your result.


```python
# Input your confidence level here:

confidence_level = 0.95
```

### Find your margin of error (ME)

Recall **margin of error = z * standard error**, where z is the appropriate z-value for the given confidence level. To calculate your margin of error:

- Find your z-value. 
- Find the approximate z for common confidence levels.
- Calculate your **standard error** estimate. 

| Confidence Level | Z Score |
| --- | --- |
| 90% | 1.65 |
| 95% | 1.96 |
| 99% | 2.58 |



```python
# Calculate your margin of error.

# Begin by identifying the z associated with your chosen confidence level.

aqi_z_score = 1.96

# Next, calculate your standard error.

std_error = np.std(aqi_rre[aqi_rre["state_name"] == "California"]["aqi"]
                  )/np.sqrt(aqi_rre[aqi_rre["state_name"] == "California"]["aqi"].count())
print("std_error: ", std_error)

# Lastly, use the preceding result to calculate your margin of error.
margin_of_error = aqi_z_score * std_error
print("margin_of_error: ", margin_of_error)
```

    std_error:  0.8918864849705216
    margin_of_error:  1.7480975105422223


### Calculate your interval

Calculate both a lower and upper limit surrounding your sample mean to create your interval.


```python
# Calculate your confidence interval (upper and lower limits).

upper_ci_limit = california_aqi_mean + margin_of_error
lower_ci_limit = california_aqi_mean - margin_of_error
```

### Alternative: Construct the interval using `scipy.stats.norm.interval()`

`scipy` presents a simpler solution to developing a confidence interval. To use this, first import the `stats` module from `scipy`.


```python
# Import stats from scipy.

import scipy.stats
```

## Step 4: Results and evaluation

### Recalculate your confidence interval

Provide your chosen `confidence_level`, `sample_mean`, and `standard_error` to `stats.norm.interval()` and recalculate your confidence interval.


```python
scipy.stats.norm.interval(alpha = confidence_level, loc = california_aqi_mean, scale = std_error)
```




    (10.373146732371875, 13.869277510052367)



# Considerations

**What findings would you share with others?**

With a confidence level of 95% a confidence interval of between 10.37 and 13.87 was observed for the AQI in Californina (the RRE state with the highest AQI mean)

**What would you convey to external stakeholders?**

It is very likey that California will benefit from a new federal policy that would create a subsidy for renewable energy
