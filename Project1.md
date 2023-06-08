# Business understanding
## Real world problem: How does Microsoft studio maximize Worldwide Gross? 
Who are the stakeholders? Microsoft CEO, board and shareholders, project managers on team for Microsoft Studio, CFO.

This notebook should show the value of genre, runtime and budget on the worldwide gross.


```python

#importing pandas, numpy, matplotlib, seaborn, glob, sql and imdb, budget datasets
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from glob import glob
import os
from matplotlib.ticker import MultipleLocator
from matplotlib.ticker import FuncFormatter

import sqlite3
connection = sqlite3.connect("imdb")

%matplotlib inline


df = pd.read_csv('tn.movie_budgets.csv')
```

# Reviewing the data from the tn.movie_budget.csv
Important data used for this project comes from the Movie, production_budget, and worldwide_gross columns


```python
#displaying data from tn.movie_budgets
# This dataset displays the release date, movie name, production budget and the domestic, ww gross. This project heavily was influenced on the budget and gross.
print(df.head())
```

       id  release_date                                        movie  \
    0   1  Dec 18, 2009                                       Avatar   
    1   2  May 20, 2011  Pirates of the Caribbean: On Stranger Tides   
    2   3   Jun 7, 2019                                 Dark Phoenix   
    3   4   May 1, 2015                      Avengers: Age of Ultron   
    4   5  Dec 15, 2017            Star Wars Ep. VIII: The Last Jedi   
    
      production_budget domestic_gross worldwide_gross  
    0      $425,000,000   $760,507,625  $2,776,345,279  
    1      $410,600,000   $241,063,875  $1,045,663,875  
    2      $350,000,000    $42,762,350    $149,762,350  
    3      $330,600,000   $459,005,868  $1,403,013,963  
    4      $317,000,000   $620,181,382  $1,316,721,747  



```python
#displaying the diffrent columns in the tn.movie_budgets/info about the dataset
for c in df.columns:
    print(c)
```

    id
    release_date
    movie
    production_budget
    domestic_gross
    worldwide_gross


# Reviewing data 
There are no missing values. There are 5 object types and one interger type columns.


```python
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 5782 entries, 0 to 5781
    Data columns (total 6 columns):
     #   Column             Non-Null Count  Dtype 
    ---  ------             --------------  ----- 
     0   id                 5782 non-null   int64 
     1   release_date       5782 non-null   object
     2   movie              5782 non-null   object
     3   production_budget  5782 non-null   object
     4   domestic_gross     5782 non-null   object
     5   worldwide_gross    5782 non-null   object
    dtypes: int64(1), object(5)
    memory usage: 271.2+ KB



```python
df.describe()
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
      <th>id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>5782.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>50.372363</td>
    </tr>
    <tr>
      <th>std</th>
      <td>28.821076</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>25.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>50.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>75.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>100.000000</td>
    </tr>
  </tbody>
</table>
</div>



# Displaying column info from IMDB 
Using SQL to show IMDB movie basics, directors, known for, movie akas, movie ratings, persons, principal, writers and movie.

Subcategory of movie basics had very important info including runtime and genre. These were used to make dec of what type of movie should be produced.


```python

cursor = connection.cursor()
sql_query = """SELECT name FROM sqlite_master WHERE type='table';"""
cursor.execute(sql_query)
print(cursor.fetchall())
```

    [('movie_basics',), ('directors',), ('known_for',), ('movie_akas',), ('movie_ratings',), ('persons',), ('principals',), ('writers',), ('movie',)]



```python
#displaying movie_basics
movie_basics_df = pd.read_sql("""SELECT * FROM movie_basics""",connection)
movie_basics_df.head(100)


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
      <th>movie_id</th>
      <th>primary_title</th>
      <th>original_title</th>
      <th>start_year</th>
      <th>runtime_minutes</th>
      <th>genres</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>tt0063540</td>
      <td>Sunghursh</td>
      <td>Sunghursh</td>
      <td>2013</td>
      <td>175.0</td>
      <td>Action,Crime,Drama</td>
    </tr>
    <tr>
      <th>1</th>
      <td>tt0066787</td>
      <td>One Day Before the Rainy Season</td>
      <td>Ashad Ka Ek Din</td>
      <td>2019</td>
      <td>114.0</td>
      <td>Biography,Drama</td>
    </tr>
    <tr>
      <th>2</th>
      <td>tt0069049</td>
      <td>The Other Side of the Wind</td>
      <td>The Other Side of the Wind</td>
      <td>2018</td>
      <td>122.0</td>
      <td>Drama</td>
    </tr>
    <tr>
      <th>3</th>
      <td>tt0069204</td>
      <td>Sabse Bada Sukh</td>
      <td>Sabse Bada Sukh</td>
      <td>2018</td>
      <td>NaN</td>
      <td>Comedy,Drama</td>
    </tr>
    <tr>
      <th>4</th>
      <td>tt0100275</td>
      <td>The Wandering Soap Opera</td>
      <td>La Telenovela Errante</td>
      <td>2017</td>
      <td>80.0</td>
      <td>Comedy,Drama,Fantasy</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>95</th>
      <td>tt0429493</td>
      <td>The A-Team</td>
      <td>The A-Team</td>
      <td>2010</td>
      <td>117.0</td>
      <td>Action,Adventure,Thriller</td>
    </tr>
    <tr>
      <th>96</th>
      <td>tt0430524</td>
      <td>The Rescuer</td>
      <td>The Rescuer</td>
      <td>2011</td>
      <td>84.0</td>
      <td>Documentary</td>
    </tr>
    <tr>
      <th>97</th>
      <td>tt0431021</td>
      <td>The Possession</td>
      <td>The Possession</td>
      <td>2012</td>
      <td>92.0</td>
      <td>Horror,Mystery,Thriller</td>
    </tr>
    <tr>
      <th>98</th>
      <td>tt0432010</td>
      <td>The Queen of Sheba Meets the Atom Man</td>
      <td>The Queen of Sheba Meets the Atom Man</td>
      <td>2018</td>
      <td>110.0</td>
      <td>Comedy</td>
    </tr>
    <tr>
      <th>99</th>
      <td>tt0433035</td>
      <td>Real Steel</td>
      <td>Real Steel</td>
      <td>2011</td>
      <td>127.0</td>
      <td>Action,Drama,Family</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 6 columns</p>
</div>



# Cleaning data

Running to see if there are any duplicated items in the data set. I also checked to see if there were any placeholder such as null value. In both cases there was no problematic data.



```python
#cleaning duplicates
print(movie_basics_df.duplicated().any())
```

    False



```python

#Checking for Placeholders
movie_basics_df.isin(['?', '#', 'NaN', 'null', 'N/A', '-', 0]).sum()


```




    movie_id           0
    primary_title      0
    original_title     0
    start_year         0
    runtime_minutes    0
    genres             0
    dtype: int64



# Merging two diffrent types of data sets

In order to merge an SQL and PANDAS data set (IMDB, tn.movie_budget.csv), there has to be a similar name. I adjusted primary_title to movie on the IMDB dataset. This will allow for a future merge of the datasets.


```python
#changing primary title to movie
movie_basics_df.rename(columns={"primary_title": "movie"}, inplace=True)
print(movie_basics_df.head())


```

        movie_id                            movie              original_title  \
    0  tt0063540                        Sunghursh                   Sunghursh   
    1  tt0066787  One Day Before the Rainy Season             Ashad Ka Ek Din   
    2  tt0069049       The Other Side of the Wind  The Other Side of the Wind   
    3  tt0069204                  Sabse Bada Sukh             Sabse Bada Sukh   
    4  tt0100275         The Wandering Soap Opera       La Telenovela Errante   
    
       start_year  runtime_minutes                genres  
    0        2013            175.0    Action,Crime,Drama  
    1        2019            114.0       Biography,Drama  
    2        2018            122.0                 Drama  
    3        2018              NaN          Comedy,Drama  
    4        2017             80.0  Comedy,Drama,Fantasy  


# Combined merge of IMDB and tn.movie_budget
This data set will specfically combine just the movie basics info from the IMDB data set with the full tn.movie_budget dataset.



```python
#full combined merge of IMDB and tn.movie _budget
imdb_basics = movie_basics_df

full_df = pd.merge(left=df, right=imdb_basics, on="movie")
full_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 3815 entries, 0 to 3814
    Data columns (total 11 columns):
     #   Column             Non-Null Count  Dtype  
    ---  ------             --------------  -----  
     0   id                 3815 non-null   int64  
     1   release_date       3815 non-null   object 
     2   movie              3815 non-null   object 
     3   production_budget  3815 non-null   object 
     4   domestic_gross     3815 non-null   object 
     5   worldwide_gross    3815 non-null   object 
     6   movie_id           3815 non-null   object 
     7   original_title     3814 non-null   object 
     8   start_year         3815 non-null   int64  
     9   runtime_minutes    3328 non-null   float64
     10  genres             3743 non-null   object 
    dtypes: float64(1), int64(2), object(8)
    memory usage: 357.7+ KB



```python
full_df.head()
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
      <th>id</th>
      <th>release_date</th>
      <th>movie</th>
      <th>production_budget</th>
      <th>domestic_gross</th>
      <th>worldwide_gross</th>
      <th>movie_id</th>
      <th>original_title</th>
      <th>start_year</th>
      <th>runtime_minutes</th>
      <th>genres</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Dec 18, 2009</td>
      <td>Avatar</td>
      <td>$425,000,000</td>
      <td>$760,507,625</td>
      <td>$2,776,345,279</td>
      <td>tt1775309</td>
      <td>Abatâ</td>
      <td>2011</td>
      <td>93.0</td>
      <td>Horror</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>May 20, 2011</td>
      <td>Pirates of the Caribbean: On Stranger Tides</td>
      <td>$410,600,000</td>
      <td>$241,063,875</td>
      <td>$1,045,663,875</td>
      <td>tt1298650</td>
      <td>Pirates of the Caribbean: On Stranger Tides</td>
      <td>2011</td>
      <td>136.0</td>
      <td>Action,Adventure,Fantasy</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Jun 7, 2019</td>
      <td>Dark Phoenix</td>
      <td>$350,000,000</td>
      <td>$42,762,350</td>
      <td>$149,762,350</td>
      <td>tt6565702</td>
      <td>Dark Phoenix</td>
      <td>2019</td>
      <td>113.0</td>
      <td>Action,Adventure,Sci-Fi</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>May 1, 2015</td>
      <td>Avengers: Age of Ultron</td>
      <td>$330,600,000</td>
      <td>$459,005,868</td>
      <td>$1,403,013,963</td>
      <td>tt2395427</td>
      <td>Avengers: Age of Ultron</td>
      <td>2015</td>
      <td>141.0</td>
      <td>Action,Adventure,Sci-Fi</td>
    </tr>
    <tr>
      <th>4</th>
      <td>7</td>
      <td>Apr 27, 2018</td>
      <td>Avengers: Infinity War</td>
      <td>$300,000,000</td>
      <td>$678,815,482</td>
      <td>$2,048,134,200</td>
      <td>tt4154756</td>
      <td>Avengers: Infinity War</td>
      <td>2018</td>
      <td>149.0</td>
      <td>Action,Adventure,Sci-Fi</td>
    </tr>
  </tbody>
</table>
</div>



# Compressing data into readable format

I removed data that will not be needed for this project. Data removed includes id, release_date, domestic_gross, movie_id, original_title, and start_year. I then checked to ensure all data was removed. 


```python
#removing unneeded data =orignial_title, start_year, Movie_id, id
full_df.drop(["original_title", "movie_id", "start_year","id","domestic_gross","release_date"], axis=1, inplace=True)
```


```python
#checking to see if data was removed
full_df.head()
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
      <th>movie</th>
      <th>production_budget</th>
      <th>worldwide_gross</th>
      <th>runtime_minutes</th>
      <th>genres</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Avatar</td>
      <td>$425,000,000</td>
      <td>$2,776,345,279</td>
      <td>93.0</td>
      <td>Horror</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Pirates of the Caribbean: On Stranger Tides</td>
      <td>$410,600,000</td>
      <td>$1,045,663,875</td>
      <td>136.0</td>
      <td>Action,Adventure,Fantasy</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Dark Phoenix</td>
      <td>$350,000,000</td>
      <td>$149,762,350</td>
      <td>113.0</td>
      <td>Action,Adventure,Sci-Fi</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Avengers: Age of Ultron</td>
      <td>$330,600,000</td>
      <td>$1,403,013,963</td>
      <td>141.0</td>
      <td>Action,Adventure,Sci-Fi</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Avengers: Infinity War</td>
      <td>$300,000,000</td>
      <td>$2,048,134,200</td>
      <td>149.0</td>
      <td>Action,Adventure,Sci-Fi</td>
    </tr>
  </tbody>
</table>
</div>



# Cleaning full dataset

I checked to see if there was any missing data in the combined dataset.
There were missing values in the runtime_minutes and genre columns. This data was removed from the cleaned dataset.


```python
#review for missing data/remove
cleaned_df = full_df.dropna(subset=['production_budget'])
cleaned_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 3815 entries, 0 to 3814
    Data columns (total 5 columns):
     #   Column             Non-Null Count  Dtype  
    ---  ------             --------------  -----  
     0   movie              3815 non-null   object 
     1   production_budget  3815 non-null   object 
     2   worldwide_gross    3815 non-null   object 
     3   runtime_minutes    3328 non-null   float64
     4   genres             3743 non-null   object 
    dtypes: float64(1), object(4)
    memory usage: 178.8+ KB



```python
cleaned_df = full_df.dropna(subset=['production_budget', 'worldwide_gross', 'runtime_minutes', 'genres'])
cleaned_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 3309 entries, 0 to 3814
    Data columns (total 5 columns):
     #   Column             Non-Null Count  Dtype  
    ---  ------             --------------  -----  
     0   movie              3309 non-null   object 
     1   production_budget  3309 non-null   object 
     2   worldwide_gross    3309 non-null   object 
     3   runtime_minutes    3309 non-null   float64
     4   genres             3309 non-null   object 
    dtypes: float64(1), object(4)
    memory usage: 155.1+ KB



```python
cleaned_df = cleaned_df.dropna(subset=['worldwide_gross'])
cleaned_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 3309 entries, 0 to 3814
    Data columns (total 5 columns):
     #   Column             Non-Null Count  Dtype  
    ---  ------             --------------  -----  
     0   movie              3309 non-null   object 
     1   production_budget  3309 non-null   object 
     2   worldwide_gross    3309 non-null   object 
     3   runtime_minutes    3309 non-null   float64
     4   genres             3309 non-null   object 
    dtypes: float64(1), object(4)
    memory usage: 155.1+ KB



```python
cleaned_df = cleaned_df.dropna(subset=['runtime_minutes'])
cleaned_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 3309 entries, 0 to 3814
    Data columns (total 5 columns):
     #   Column             Non-Null Count  Dtype  
    ---  ------             --------------  -----  
     0   movie              3309 non-null   object 
     1   production_budget  3309 non-null   object 
     2   worldwide_gross    3309 non-null   object 
     3   runtime_minutes    3309 non-null   float64
     4   genres             3309 non-null   object 
    dtypes: float64(1), object(4)
    memory usage: 155.1+ KB


# Adjusting data type for cleaned dataset

In order to fully review the data we had to change the datatype for production_budget, and worldwide_gross. This data had to change from a string to an integer. To do this we had to drop the $. 


```python
# Adjusting type of data for production_budget, worldwide_gross 
#From object to int

cleaned_df['production_budget'] = cleaned_df['production_budget'].str.replace(',', '')
cleaned_df['production_budget'] = cleaned_df['production_budget'].str.replace('$', '')
cleaned_df['production_budget'] = cleaned_df['production_budget'].astype(int)
```


```python
cleaned_df.head()
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
      <th>movie</th>
      <th>production_budget</th>
      <th>worldwide_gross</th>
      <th>runtime_minutes</th>
      <th>genres</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Avatar</td>
      <td>425000000</td>
      <td>$2,776,345,279</td>
      <td>93.0</td>
      <td>Horror</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Pirates of the Caribbean: On Stranger Tides</td>
      <td>410600000</td>
      <td>$1,045,663,875</td>
      <td>136.0</td>
      <td>Action,Adventure,Fantasy</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Dark Phoenix</td>
      <td>350000000</td>
      <td>$149,762,350</td>
      <td>113.0</td>
      <td>Action,Adventure,Sci-Fi</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Avengers: Age of Ultron</td>
      <td>330600000</td>
      <td>$1,403,013,963</td>
      <td>141.0</td>
      <td>Action,Adventure,Sci-Fi</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Avengers: Infinity War</td>
      <td>300000000</td>
      <td>$2,048,134,200</td>
      <td>149.0</td>
      <td>Action,Adventure,Sci-Fi</td>
    </tr>
  </tbody>
</table>
</div>




```python
cleaned_df['worldwide_gross'] = cleaned_df['worldwide_gross'].str.replace(',', '')
cleaned_df['worldwide_gross'] = cleaned_df['worldwide_gross'].str.replace('$', '')
cleaned_df['worldwide_gross'] = cleaned_df['worldwide_gross'].astype(int)
```


```python
cleaned_df.head()
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
      <th>movie</th>
      <th>production_budget</th>
      <th>worldwide_gross</th>
      <th>runtime_minutes</th>
      <th>genres</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Avatar</td>
      <td>425000000</td>
      <td>2776345279</td>
      <td>93.0</td>
      <td>Horror</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Pirates of the Caribbean: On Stranger Tides</td>
      <td>410600000</td>
      <td>1045663875</td>
      <td>136.0</td>
      <td>Action,Adventure,Fantasy</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Dark Phoenix</td>
      <td>350000000</td>
      <td>149762350</td>
      <td>113.0</td>
      <td>Action,Adventure,Sci-Fi</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Avengers: Age of Ultron</td>
      <td>330600000</td>
      <td>1403013963</td>
      <td>141.0</td>
      <td>Action,Adventure,Sci-Fi</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Avengers: Infinity War</td>
      <td>300000000</td>
      <td>2048134200</td>
      <td>149.0</td>
      <td>Action,Adventure,Sci-Fi</td>
    </tr>
  </tbody>
</table>
</div>



# Reviewing cleaned data

Ensuring there are no missing values. Full count of 3,309 non-null meaning there is no missing data.


```python
cleaned_df = cleaned_df.dropna(subset=['genres'])
cleaned_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 3309 entries, 0 to 3814
    Data columns (total 5 columns):
     #   Column             Non-Null Count  Dtype  
    ---  ------             --------------  -----  
     0   movie              3309 non-null   object 
     1   production_budget  3309 non-null   int64  
     2   worldwide_gross    3309 non-null   int64  
     3   runtime_minutes    3309 non-null   float64
     4   genres             3309 non-null   object 
    dtypes: float64(1), int64(2), object(2)
    memory usage: 155.1+ KB


# Plots

These initial plots show the coorelation between production budget, worldwide gorss and runtime.


```python
#plots
sns.pairplot(cleaned_df)
```




    <seaborn.axisgrid.PairGrid at 0x7f83aa832d60>




    
![png](output_35_1.png)
    



```python
#Review columns of cleaned data
c = list(cleaned_df.columns)
c = c[1:]
```


```python
c
```




    ['production_budget', 'worldwide_gross', 'runtime_minutes', 'genres']



# Movie Worldwide Gross data

The count indicates there are 3,309 movies in this dataset. The mean or average movie grosses 98,220,550.00. The standard deviaton however shows there is a large spread of data. For instance if we look at the first quartline 25 percent of movies gross below 2,179,623.00. This number is well below the mean value. Also the largest grossing movie is well above the mean. Avatar grossed over 2.7 Billion dollars. In order to analyze what type of movie grosses well, we must look at other variables.



```python
cleaned_df['worldwide_gross'].describe()

```




    count    3.309000e+03
    mean     9.822055e+07
    std      1.965430e+08
    min      0.000000e+00
    25%      2.179623e+06
    50%      2.685381e+07
    75%      9.765154e+07
    max      2.776345e+09
    Name: worldwide_gross, dtype: float64




```python
import seaborn as sns
import matplotlib.pyplot as plt

# Create a scatter plot of worldwide gross
plt.figure(figsize=(12, 6))
sns.scatterplot(data=cleaned_df.reset_index(), x=cleaned_df.reset_index().index, y='worldwide_gross')
plt.xlabel('Movie Count')
plt.ylabel('Worldwide Gross (USD billions)')
plt.title('Distribution of Worldwide Gross')
plt.ylim(0, 3e9)  # Set the y-axis limits to 0 to 3 billion
plt.show()





```


    
![png](output_40_0.png)
    



```python
#worldwide gross by budget
total_gross = cleaned_df.sort_values(by="worldwide_gross", ascending=False)
total_gross.head(10)
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
      <th>movie</th>
      <th>production_budget</th>
      <th>worldwide_gross</th>
      <th>runtime_minutes</th>
      <th>genres</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Avatar</td>
      <td>425000000</td>
      <td>2776345279</td>
      <td>93.0</td>
      <td>Horror</td>
    </tr>
    <tr>
      <th>43</th>
      <td>Titanic</td>
      <td>200000000</td>
      <td>2208208395</td>
      <td>115.0</td>
      <td>Family</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Avengers: Infinity War</td>
      <td>300000000</td>
      <td>2048134200</td>
      <td>149.0</td>
      <td>Action,Adventure,Sci-Fi</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Jurassic World</td>
      <td>215000000</td>
      <td>1648854864</td>
      <td>124.0</td>
      <td>Action,Adventure,Sci-Fi</td>
    </tr>
    <tr>
      <th>67</th>
      <td>Furious 7</td>
      <td>190000000</td>
      <td>1518722794</td>
      <td>137.0</td>
      <td>Action,Crime,Thriller</td>
    </tr>
    <tr>
      <th>19</th>
      <td>The Avengers</td>
      <td>225000000</td>
      <td>1517935897</td>
      <td>143.0</td>
      <td>Action,Adventure,Sci-Fi</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Avengers: Age of Ultron</td>
      <td>330600000</td>
      <td>1403013963</td>
      <td>141.0</td>
      <td>Action,Adventure,Sci-Fi</td>
    </tr>
    <tr>
      <th>41</th>
      <td>Black Panther</td>
      <td>200000000</td>
      <td>1348258224</td>
      <td>134.0</td>
      <td>Action,Adventure,Sci-Fi</td>
    </tr>
    <tr>
      <th>116</th>
      <td>Jurassic World: Fallen Kingdom</td>
      <td>170000000</td>
      <td>1305772799</td>
      <td>128.0</td>
      <td>Action,Adventure,Sci-Fi</td>
    </tr>
    <tr>
      <th>156</th>
      <td>Frozen</td>
      <td>150000000</td>
      <td>1272469910</td>
      <td>93.0</td>
      <td>Adventure,Drama,Sport</td>
    </tr>
  </tbody>
</table>
</div>




```python
#What is the longest movie?
total_gross["runtime_minutes"].max()
```




    360.0



# List of all the Genres



```python
#Genres
print(cleaned_df["genres"].unique())
```

    ['Horror' 'Action,Adventure,Fantasy' 'Action,Adventure,Sci-Fi'
     'Action,Adventure,Thriller' 'Action,Thriller' 'Action,Adventure,Western'
     'Adventure,Animation,Comedy' 'Adventure,Family,Fantasy'
     'Adventure,Fantasy' 'Action,Crime,Thriller' 'Action,Adventure,Comedy'
     'Action,Adventure,Drama' 'Action,Drama' 'Action,Adventure,History'
     'Family' 'Action,Adventure,Animation' 'Documentary' 'Fantasy,Musical'
     'Action,Adventure,Horror' 'Drama,Romance' 'Comedy,Drama,Family'
     'Drama,Mystery,Sci-Fi' 'Adventure,Comedy,Family'
     'Action,Adventure,Family' 'Adventure,Drama,Family' 'Action,Horror,Sci-Fi'
     'Action,Sci-Fi' 'Animation' 'Crime,Drama' 'Biography,Documentary,History'
     'Adventure,Drama,Sci-Fi' 'Drama,Fantasy,Romance' 'Family,Fantasy,Musical'
     'Action,Drama,History' 'Sci-Fi' 'Documentary,Drama,Sport'
     'Adventure,Drama,Sport' 'Fantasy,Romance' 'Action,Drama,Fantasy'
     'Comedy,Fantasy,Horror' 'Action,Drama,Thriller' 'Drama,Fantasy,Horror'
     'Adventure,Animation,Family' 'Adventure,Animation,Drama'
     'Action,Comedy,Fantasy' 'Action,Comedy' 'Action,Adventure,Biography'
     'Thriller' 'Drama' 'Documentary,Drama,Family' 'Drama,Horror,Thriller'
     'Biography,Documentary' 'Documentary,History' 'Documentary,Family'
     'Animation,Documentary' 'Drama,Romance,Thriller' 'Comedy,Family,Fantasy'
     'Action,Animation,Comedy' 'Action,Mystery,Thriller' 'Drama,Thriller'
     'Animation,Documentary,Family' 'Action,Drama,Sci-Fi'
     'Action,Adventure,Crime' 'Adventure,Mystery,Sci-Fi'
     'Action,Adventure,Mystery' 'Adventure,Drama,Fantasy'
     'Action,Crime,Sci-Fi' 'Documentary,News' 'Documentary,Drama' 'Comedy'
     'Comedy,Drama' 'Action,Sci-Fi,Thriller' 'Drama,Sci-Fi,Thriller'
     'Action,Fantasy,War' 'Drama,Romance,Sci-Fi' 'Action,Comedy,Crime'
     'Action,Drama,Family' 'Adventure,Sport' 'Action,Drama,Romance'
     'Documentary,Thriller' 'Action,Drama,Mystery' 'Drama,Western'
     'Comedy,Romance' 'Biography,Crime,Drama' 'Comedy,Documentary,Drama'
     'Action,Crime,Sport' 'Horror,Sci-Fi,Thriller' 'Musical'
     'Drama,Family,Fantasy' 'Music' 'Fantasy' 'Comedy,Drama,Romance'
     'Action,Crime' 'Adventure,Comedy,Drama' 'Adventure' 'Crime,Drama,Mystery'
     'Action,Comedy,Sci-Fi' 'Action,Adventure,Romance' 'Drama,History'
     'Action,Family,Fantasy' 'Action,Crime,Fantasy' 'Adventure,Drama'
     'Animation,Comedy,Family' 'Action,Comedy,Family' 'Drama,Fantasy,Musical'
     'Documentary,Sport' 'Action,Comedy,Horror' 'Biography,Drama,Musical'
     'Drama,Horror,Mystery' 'Biography,Documentary,Drama' 'Comedy,Mystery'
     'Mystery,Thriller' 'Action,Drama,War' 'Comedy,Family,Romance'
     'Horror,Thriller' 'Crime,Horror,Thriller' 'Drama,Mystery,Thriller'
     'Romance' 'Action' 'Crime,Mystery,Thriller' 'Biography,Drama'
     'Action,Comedy,Romance' 'Action,Crime,Drama' 'Comedy,Drama,History'
     'Adventure,Biography,Documentary' 'Biography,Drama,Thriller'
     'Drama,History,War' 'Drama,Family' 'Crime,Drama,Thriller'
     'Action,Thriller,War' 'Action,Mystery,Sci-Fi' 'Action,Animation,Fantasy'
     'Comedy,Drama,Musical' 'Action,Crime,Mystery' 'Crime,Documentary,Drama'
     'Drama,Sci-Fi' 'Action,Fantasy,Horror' 'Biography,Crime,Documentary'
     'Comedy,Romance,Sci-Fi' 'Biography,Drama,History' 'Crime,Drama,Family'
     'Biography,Drama,Sport' 'Adventure,Comedy' 'Biography,Drama,Western'
     'Comedy,Crime,Drama' 'Mystery' 'Biography,Drama,Romance' 'Crime,Thriller'
     'Biography,Comedy,Drama' 'Action,Romance' 'Drama,Sport' 'Comedy,Western'
     'Action,Biography,Drama' 'Action,Fantasy,Thriller'
     'Biography,Drama,Music' 'Comedy,Crime,Romance' 'Adventure,Comedy,Crime'
     'Drama,Music,Musical' 'Horror,Mystery,Thriller' 'Adventure,Drama,Horror'
     'Drama,Musical,Romance' 'Comedy,Fantasy' 'Crime' 'Crime,Drama,Fantasy'
     'Romance,Sci-Fi,Thriller' 'Mystery,Sci-Fi,Thriller' 'Action,Comedy,Drama'
     'Action,Biography,Comedy' 'Comedy,Family' 'Adventure,Comedy,Fantasy'
     'Crime,Drama,History' 'Biography,Documentary,Family'
     'Adventure,Drama,Thriller' 'Biography,Documentary,Sport'
     'Comedy,Drama,Thriller' 'Documentary,Fantasy,Mystery'
     'Adventure,Drama,History' 'Drama,War' 'Comedy,Music' 'Drama,Horror'
     'Animation,Family,Fantasy' 'Animation,Drama,Fantasy'
     'Action,Comedy,Sport' 'Fantasy,Horror,Mystery' 'Action,Fantasy,Western'
     'Comedy,Crime' 'Drama,History,Thriller' 'Adventure,Comedy,Music'
     'Horror,Mystery' 'Adventure,Comedy,Sci-Fi' 'War' 'Comedy,Drama,Sport'
     'Comedy,Horror,Thriller' 'Biography,Drama,Family' 'Drama,Horror,Sci-Fi'
     'Drama,Family,Sport' 'Drama,Fantasy,Thriller' 'Action,Documentary'
     'Horror,Mystery,Sci-Fi' 'Action,History' 'Drama,Family,Romance'
     'Drama,Music' 'Documentary,Music' 'Comedy,Sci-Fi' 'Biography,Drama,War'
     'Adventure,Biography,Drama' 'Adventure,Drama,Western'
     'Adventure,Documentary,Drama' 'Crime,Drama,Horror' 'Sci-Fi,Thriller'
     'Drama,Fantasy' 'Animation,Comedy,Drama' 'Comedy,Romance,Sport'
     'Documentary,Drama,War' 'Adventure,Comedy,Romance' 'Drama,Music,Romance'
     'Comedy,Horror,Sci-Fi' 'Adventure,Documentary'
     'Biography,Documentary,Music' 'Biography,Comedy,Documentary'
     'Comedy,Horror,Romance' 'Comedy,Fantasy,Romance' 'Comedy,Crime,Thriller'
     'Comedy,Horror' 'Adventure,Animation,Documentary'
     'Adventure,Drama,Romance' 'Action,Sport' 'Action,Biography,Crime'
     'Drama,History,Sport' 'Animation,Sci-Fi' 'Adventure,Documentary,Music'
     'Comedy,Drama,Music' 'Action,Comedy,Mystery'
     'Action,Adventure,Documentary' 'Action,Drama,Sport'
     'Adventure,Fantasy,Mystery' 'Drama,Romance,War' 'Action,Horror,Mystery'
     'Fantasy,Horror,Thriller' 'Action,Romance,Sport' 'Drama,Mystery'
     'Adventure,Documentary,War' 'Drama,History,Romance'
     'Action,Drama,Western' 'Biography,Drama,Mystery' 'Drama,Mystery,Romance'
     'Adventure,Documentary,Sport' 'Drama,Music,Thriller'
     'Family,Horror,Romance' 'Action,Romance,Thriller' 'Drama,Fantasy,Mystery'
     'Comedy,Music,Romance' 'Drama,Fantasy,Sci-Fi' 'Action,Comedy,Documentary'
     'Crime,Horror,Mystery' 'Documentary,Western' 'Comedy,Mystery,Thriller'
     'Comedy,Drama,Fantasy' 'Sport' 'Crime,Fantasy,Thriller' 'Comedy,Sport'
     'Adventure,Documentary,History' 'Drama,Family,Music' 'Documentary,War'
     'Comedy,Mystery,Sci-Fi' 'Comedy,Documentary' 'Animation,Drama'
     'Adventure,Biography,Comedy' 'Horror,Music,Thriller' 'Comedy,Music,War'
     'Documentary,History,Western' 'Adventure,Animation' 'Action,Adventure'
     'Action,Documentary,Drama' 'Adventure,Family,Sci-Fi' 'Drama,Thriller,War'
     'Biography,Comedy,Crime' 'Western' 'Drama,Mystery,War'
     'Comedy,Drama,Mystery' 'Documentary,Drama,News' 'Adventure,Crime,Drama'
     'Family,Fantasy,Music' 'Drama,Fantasy,Music' 'Adventure,Horror,Sci-Fi'
     'Adventure,Comedy,Horror' 'Action,Horror,Thriller' 'Biography'
     'Drama,History,Mystery' 'Comedy,Horror,Mystery' 'Action,Horror'
     'Biography,Family,Sport' 'Comedy,Drama,Horror' 'Drama,Family,Thriller'
     'Comedy,Thriller' 'Adventure,Family' 'Crime,Mystery,Sci-Fi'
     'Documentary,Sport,Thriller' 'Drama,Family,History'
     'Fantasy,Horror,Sci-Fi' 'Adventure,Fantasy,Horror'
     'Adventure,Horror,Mystery' 'Animation,Documentary,Sci-Fi'
     'Animation,Horror' 'Crime,Drama,Romance' 'Drama,Musical'
     'Animation,Family' 'Drama,Family,Mystery' 'Action,Crime,Horror'
     'Adventure,Crime,Thriller' 'Horror,Romance,Thriller'
     'Biography,Drama,Fantasy' 'Comedy,Fantasy,Sci-Fi'
     'Crime,Documentary,History' 'Fantasy,Horror' 'Drama,Thriller,Western'
     'Crime,Documentary' 'Comedy,Fantasy,Musical' 'Documentary,Drama,History'
     'Horror,Sci-Fi' 'Documentary,History,War' 'Biography,Documentary,War'
     'Comedy,Romance,Thriller' 'Comedy,Crime,Horror' 'Adventure,Horror'
     'Music,War' 'Documentary,Music,War' 'Documentary,Drama,Reality-TV'
     'Comedy,Fantasy,Thriller' 'Documentary,Horror'
     'Documentary,Family,History' 'Adventure,Drama,Mystery'
     'Action,Biography,Documentary' 'Horror,Musical' 'Family,Sci-Fi']


# How many Genres are there?


```python
# number of genres

print(cleaned_df["genres"].nunique())
```

    335


# Creating a list for Generes

This seprates the total number of genres and creates a list. Some movies may have more than one type of genre. (Ex Jurassic World is Action, Adventure and Sci-Fi).


```python
#Reviewing genres and relation to gross
total_gross["genres"] = total_gross["genres"].apply(lambda x: x.split(",") if x else x)
total_gross.head()
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
      <th>movie</th>
      <th>production_budget</th>
      <th>worldwide_gross</th>
      <th>runtime_minutes</th>
      <th>genres</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Avatar</td>
      <td>425000000</td>
      <td>2776345279</td>
      <td>93.0</td>
      <td>[Horror]</td>
    </tr>
    <tr>
      <th>43</th>
      <td>Titanic</td>
      <td>200000000</td>
      <td>2208208395</td>
      <td>115.0</td>
      <td>[Family]</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Avengers: Infinity War</td>
      <td>300000000</td>
      <td>2048134200</td>
      <td>149.0</td>
      <td>[Action, Adventure, Sci-Fi]</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Jurassic World</td>
      <td>215000000</td>
      <td>1648854864</td>
      <td>124.0</td>
      <td>[Action, Adventure, Sci-Fi]</td>
    </tr>
    <tr>
      <th>67</th>
      <td>Furious 7</td>
      <td>190000000</td>
      <td>1518722794</td>
      <td>137.0</td>
      <td>[Action, Crime, Thriller]</td>
    </tr>
  </tbody>
</table>
</div>




```python
all_genres = set()

for genres in total_gross["genres"]:
    if genres:
        all_genres.update(genres)

all_genres
```




    {'Action',
     'Adventure',
     'Animation',
     'Biography',
     'Comedy',
     'Crime',
     'Documentary',
     'Drama',
     'Family',
     'Fantasy',
     'History',
     'Horror',
     'Music',
     'Musical',
     'Mystery',
     'News',
     'Reality-TV',
     'Romance',
     'Sci-Fi',
     'Sport',
     'Thriller',
     'War',
     'Western'}



# Tallying individual generes by movie

The next two steps were used to show all possible genres on the table for the movie. Followed by adding a 1.00 if the movie has that specific genre. This allows for futher analyzation of individual genres and their impact on worldwide_gross. 


```python
# Iterate through the set.
for genre in all_genres:
    # Make a new column in dataframe and fill the columns with zeros.
    total_gross[genre] = np.zeros(shape=total_gross.shape[0])
# Check that the changes took place.                                
total_gross.head()
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
      <th>movie</th>
      <th>production_budget</th>
      <th>worldwide_gross</th>
      <th>runtime_minutes</th>
      <th>genres</th>
      <th>Comedy</th>
      <th>Adventure</th>
      <th>Animation</th>
      <th>Mystery</th>
      <th>Music</th>
      <th>...</th>
      <th>Action</th>
      <th>Thriller</th>
      <th>News</th>
      <th>Romance</th>
      <th>Documentary</th>
      <th>Musical</th>
      <th>History</th>
      <th>Horror</th>
      <th>Reality-TV</th>
      <th>Crime</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Avatar</td>
      <td>425000000</td>
      <td>2776345279</td>
      <td>93.0</td>
      <td>[Horror]</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>43</th>
      <td>Titanic</td>
      <td>200000000</td>
      <td>2208208395</td>
      <td>115.0</td>
      <td>[Family]</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Avengers: Infinity War</td>
      <td>300000000</td>
      <td>2048134200</td>
      <td>149.0</td>
      <td>[Action, Adventure, Sci-Fi]</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Jurassic World</td>
      <td>215000000</td>
      <td>1648854864</td>
      <td>124.0</td>
      <td>[Action, Adventure, Sci-Fi]</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>67</th>
      <td>Furious 7</td>
      <td>190000000</td>
      <td>1518722794</td>
      <td>137.0</td>
      <td>[Action, Crime, Thriller]</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 28 columns</p>
</div>




```python
# Iterate through the genres column as an index. Movies can have more than one genre this should count all genres
# the genre rows. 
for index, row in total_gross.iterrows():
    # If the value in genres
    if row['genres']:
        # mathces a genre column
        for genre in row['genres']:
            # change that value to 1.
            total_gross.loc[index, genre] = 1
# Lets check our changes.
total_gross.head()
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
      <th>movie</th>
      <th>production_budget</th>
      <th>worldwide_gross</th>
      <th>runtime_minutes</th>
      <th>genres</th>
      <th>Comedy</th>
      <th>Adventure</th>
      <th>Animation</th>
      <th>Mystery</th>
      <th>Music</th>
      <th>...</th>
      <th>Action</th>
      <th>Thriller</th>
      <th>News</th>
      <th>Romance</th>
      <th>Documentary</th>
      <th>Musical</th>
      <th>History</th>
      <th>Horror</th>
      <th>Reality-TV</th>
      <th>Crime</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Avatar</td>
      <td>425000000</td>
      <td>2776345279</td>
      <td>93.0</td>
      <td>[Horror]</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>43</th>
      <td>Titanic</td>
      <td>200000000</td>
      <td>2208208395</td>
      <td>115.0</td>
      <td>[Family]</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Avengers: Infinity War</td>
      <td>300000000</td>
      <td>2048134200</td>
      <td>149.0</td>
      <td>[Action, Adventure, Sci-Fi]</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Jurassic World</td>
      <td>215000000</td>
      <td>1648854864</td>
      <td>124.0</td>
      <td>[Action, Adventure, Sci-Fi]</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>67</th>
      <td>Furious 7</td>
      <td>190000000</td>
      <td>1518722794</td>
      <td>137.0</td>
      <td>[Action, Crime, Thriller]</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 28 columns</p>
</div>




```python
c = list(total_gross.columns)
```


```python
genre_c = c[8:]

```


```python
genre_count = {}
for c in genre_c:
    count = np.sum(total_gross[c] == 1).sum()
    genre_count[c] = count
```

# How many movies are there in each genre?

This code shows the amount of movies in each genre. Note that a movie can have more than one genre.


```python
 
genre_count
```




    {'Mystery': 233,
     'Music': 84,
     'Sci-Fi': 212,
     'War': 48,
     'Western': 18,
     'Family': 189,
     'Sport': 70,
     'Drama': 1641,
     'Fantasy': 184,
     'Biography': 247,
     'Action': 647,
     'Thriller': 544,
     'News': 7,
     'Romance': 350,
     'Documentary': 452,
     'Musical': 28,
     'History': 90,
     'Horror': 381,
     'Reality-TV': 1,
     'Crime': 379}




```python
#cleaning total gross/genre data- has to be added so we can incorporate the new genre data
cleaned_df_explode = total_gross.explode("genres")
cleaned_df_explode
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
      <th>movie</th>
      <th>production_budget</th>
      <th>worldwide_gross</th>
      <th>runtime_minutes</th>
      <th>genres</th>
      <th>Comedy</th>
      <th>Adventure</th>
      <th>Animation</th>
      <th>Mystery</th>
      <th>Music</th>
      <th>...</th>
      <th>Action</th>
      <th>Thriller</th>
      <th>News</th>
      <th>Romance</th>
      <th>Documentary</th>
      <th>Musical</th>
      <th>History</th>
      <th>Horror</th>
      <th>Reality-TV</th>
      <th>Crime</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Avatar</td>
      <td>425000000</td>
      <td>2776345279</td>
      <td>93.0</td>
      <td>Horror</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>43</th>
      <td>Titanic</td>
      <td>200000000</td>
      <td>2208208395</td>
      <td>115.0</td>
      <td>Family</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Avengers: Infinity War</td>
      <td>300000000</td>
      <td>2048134200</td>
      <td>149.0</td>
      <td>Action</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Avengers: Infinity War</td>
      <td>300000000</td>
      <td>2048134200</td>
      <td>149.0</td>
      <td>Adventure</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Avengers: Infinity War</td>
      <td>300000000</td>
      <td>2048134200</td>
      <td>149.0</td>
      <td>Sci-Fi</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2953</th>
      <td>The Final Girls</td>
      <td>4500000</td>
      <td>0</td>
      <td>88.0</td>
      <td>Drama</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2953</th>
      <td>The Final Girls</td>
      <td>4500000</td>
      <td>0</td>
      <td>88.0</td>
      <td>Fantasy</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3814</th>
      <td>A Plague So Pleasant</td>
      <td>1400</td>
      <td>0</td>
      <td>76.0</td>
      <td>Drama</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3814</th>
      <td>A Plague So Pleasant</td>
      <td>1400</td>
      <td>0</td>
      <td>76.0</td>
      <td>Horror</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3814</th>
      <td>A Plague So Pleasant</td>
      <td>1400</td>
      <td>0</td>
      <td>76.0</td>
      <td>Thriller</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
<p>7204 rows × 28 columns</p>
</div>



# Statistical measures of worldwide gross for total dataset
Count-unique genre=305
Mean- World wide gross=94,3374,160
Std- 146,364,500.




```python
ww_gross_genre_df = cleaned_df_explode.groupby('genres')['worldwide_gross'].sum() / cleaned_df_explode['genres'].value_counts()
ww_gross_genre_df.describe()

```




    count    2.300000e+01
    mean     1.165129e+08
    std      8.766519e+07
    min      0.000000e+00
    25%      6.359949e+07
    50%      7.322647e+07
    75%      1.740119e+08
    max      3.095840e+08
    dtype: float64



# Ploting worldwide gross by genre

Movies that produce a high gross(Sci-Fi, Action, Animation, Adventure).

Movies that produce a low gross(News, documentary, Musical, Western, ect).


```python




#Genres
selected_genres = ['Drama', 'Musical', 'Sci-Fi', 'Western', 'Horror', 'Animation', 'War', 'Comedy',
                   'Fantasy', 'Crime', 'Music', 'Action', 'News', 'Thriller', 'Family', 'Romance',
                   'Biography', 'History', 'Adventure', 'Documentary', 'Mystery']

# Subsetting the data for selected genres
subset_df = cleaned_df_explode[cleaned_df_explode['genres'].isin(selected_genres)]

# Converting worldwide gross to billions
subset_df['worldwide_gross'] = subset_df['worldwide_gross'] / 1e9

# Plotting the box plot
plt.figure(figsize=(12, 8))
sns.boxplot(data=subset_df, x='genres', y='worldwide_gross')
plt.title('Worldwide Gross by Genre', fontsize=16)
plt.xlabel('Genre', fontsize=12)
plt.ylabel('Worldwide Gross (Billions)', fontsize=12)
plt.xticks(rotation=45)
plt.show()



```

    <ipython-input-43-475f9af719c1>:10: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      subset_df['worldwide_gross'] = subset_df['worldwide_gross'] / 1e9



    
![png](output_62_1.png)
    


# Top 10 Movie Genres for Worldwide Gross return


```python
# Group the data by genre and calculate the total worldwide gross
genre_gross = cleaned_df_explode.groupby('genres')['worldwide_gross'].sum()

#top 10 genres ww gross
top_genres = genre_gross.nlargest(10)

# Top 11+
other_genre_sum = genre_gross.drop(top_genres.index).sum()

# Top genres with others
combined_genres = top_genres.append(pd.Series(other_genre_sum, index=['Other']))

# Colors: Customize the colors of the pie slices
colors = ['#ff9999', '#66b3ff', '#99ff99', '#ffcc99', '#c2c2c2', '#b3b3cc', '#ffccff', '#ffb3e6',
          '#c2d6d6', '#d9d9d9', '#e6ffb3']

# Explode: Emphasize the top genres
explode = [0.1] * len(top_genres) + [0]

# Plotting the pie chart
plt.figure(figsize=(12, 8))
plt.pie(combined_genres, labels=combined_genres.index, explode=explode, colors=colors,
        autopct='%.2f%%', textprops={'fontsize': 12})

plt.title('Worldwide Gross by Genre', fontsize=16)

# Creating a legend
plt.legend(title='Genres', loc='center left', bbox_to_anchor=(1, 0.5))

plt.tight_layout()

# Saving the figure with expanded bounding box
plt.savefig('pie_chart.png', bbox_inches='tight')

plt.show()



```


    
![png](output_64_0.png)
    


# Summary statistics of Worldwide gross by Genre

Summary stats show that the mean gross return is highest for Sci-fi and Animation.

Summary stats show that Western and Biography return the less gross values.


```python
# Selected genres
selected_genres = ['News','Horror', 'Action', 'Adventure', 'Sci-Fi', 'Crime', 'Thriller', 'Drama', 'Fantasy', 'Romance', 'Animation', 'Comedy', 'Family', 'Musical', 'Biography', 'Music', 'Documentary', 'History', 'Mystery', 'Western', 'War', 'News']

# Subsetting the data for selected genres
subset_df = cleaned_df[cleaned_df['genres'].isin(selected_genres)]

# Grouping the data by genre and calculating summary statistics
genre_stats = subset_df.groupby('genres')['worldwide_gross'].describe()

# Plotting the mean worldwide gross for each genre
plt.figure(figsize=(12, 8))
genre_stats['mean'].plot(kind='bar', color='skyblue')
plt.title('Mean Worldwide Gross by Genre', fontsize=16)
plt.xlabel('Genre', fontsize=12)
plt.ylabel('Mean Worldwide Gross (Billions)', fontsize=12)
plt.xticks(rotation=45)
plt.show()

# Displaying the summary statistics
print(genre_stats)


```


    
![png](output_66_0.png)
    


                 count          mean           std         min          25%  \
    genres                                                                    
    Action        16.0  7.718570e+07  8.883734e+07         0.0   8631032.75   
    Adventure      7.0  9.387913e+07  1.854842e+08         0.0   2936282.50   
    Animation      8.0  1.839455e+08  3.363916e+08         0.0         0.00   
    Biography      5.0  1.906797e+06  3.212060e+06         0.0     32092.00   
    Comedy       112.0  6.051115e+07  7.661146e+07         0.0    954560.50   
    Crime          5.0  5.402849e+07  5.616716e+07     17639.0   4600000.00   
    Documentary  246.0  6.350865e+07  1.235838e+08         0.0   1114772.25   
    Drama        379.0  4.034965e+07  7.353359e+07         0.0    857725.00   
    Family        12.0  2.661076e+08  6.579303e+08         0.0     48111.00   
    Fantasy        4.0  2.065393e+08  2.485802e+08      1711.0  21010074.25   
    Horror        73.0  9.845474e+07  3.431472e+08         0.0    478595.00   
    Music          5.0  1.627513e+08  2.360596e+08   1381824.0   5149131.00   
    Musical        7.0  2.389734e+08  2.286122e+08    531806.0  37998221.00   
    Mystery        5.0  7.048935e+07  4.156773e+07  22673340.0  38253433.00   
    Romance       13.0  2.122573e+07  2.110834e+07         0.0   4109095.00   
    Sci-Fi        11.0  1.510624e+08  2.843647e+08         0.0     35867.50   
    Thriller      56.0  6.630358e+07  1.430822e+08         0.0   1581316.25   
    War            2.0  2.597455e+07  5.974419e+06  21750000.0  23862276.25   
    Western        2.0  3.276629e+07  4.633853e+07         0.0  16383144.00   
    
                         50%           75%           max  
    genres                                                
    Action        52872764.0  1.075103e+08  3.424165e+08  
    Adventure      5953886.0  7.344462e+07  4.984382e+08  
    Animation     37767690.5  1.468067e+08  9.628545e+08  
    Biography       719699.0  1.200000e+06  7.582196e+06  
    Comedy        28139824.0  9.576641e+07  3.360695e+08  
    Crime         41076865.0  9.962487e+07  1.248231e+08  
    Documentary   15416508.0  7.293425e+07  1.025491e+09  
    Drama         11295324.0  4.726783e+07  4.875198e+08  
    Family         5423953.5  3.928192e+07  2.208208e+09  
    Fantasy      145802138.5  3.313314e+08  5.345514e+08  
    Horror        16340767.0  6.524551e+07  2.776345e+09  
    Music          9082906.0  2.635914e+08  5.345514e+08  
    Musical      263591415.0  3.990714e+08  5.345514e+08  
    Mystery       81079566.0  8.218368e+07  1.282567e+08  
    Romance       19535005.0  2.670318e+07  5.897848e+07  
    Sci-Fi        28876702.0  7.559126e+07  8.211334e+08  
    Thriller      18805528.5  6.932313e+07  8.536286e+08  
    War           25974552.5  2.808683e+07  3.019910e+07  
    Western       32766288.0  4.914943e+07  6.553258e+07  


# Reviewing runtimes effect on Worldwide gross

Seperated the runtime data into 3 subcatagories short(<60 mins), med (60-120 mins) , long (120+ mins)

Long movies tend to return the highest worldwide gross. Meduium movies and short return lowest worldwide gross. 


```python
# Define the runtime categories and their corresponding labels
runtime_bins = [0, 60, 120, float('inf')]
runtime_labels = ['< 60 mins', '60-120 mins', '> 120 mins']

# Create the runtime_category column
cleaned_df['runtime_category'] = pd.cut(cleaned_df['runtime_minutes'], bins=runtime_bins, labels=runtime_labels)

# Plotting worldwide gross by runtime category
plt.figure(figsize=(12, 8))
sns.boxplot(x=cleaned_df['runtime_category'], y=cleaned_df['worldwide_gross'])
plt.title('Worldwide Gross by Runtime Category', fontsize=20)
plt.xlabel('Runtime Category', fontsize=15)
plt.ylabel('Worldwide Gross (Billions USD)', fontsize=15)

# Set y-axis limits
plt.ylim(0, 3e9)  # Set y-axis limit to 3 billion

# Set y-axis ticks at desired intervals
plt.yticks([0, 5e8, 1e9, 1.5e9, 2e9, 2.5e9, 3e9])

plt.tight_layout()  # Adjust spacing between subplots

plt.show()










```


    
![png](output_68_0.png)
    


# Summary statistics for runtimes impact on Worldwide gross
The chart shows that the summary stats are consistent with the box plot. Long movies return higher gross, while short and medium return less gross.


```python
# Calculate summary statistics for ww gross by runtime category
runtime_summary = cleaned_df.groupby('runtime_category')['worldwide_gross'].describe()

# Print the summary statistics
print(runtime_summary)

```

                       count          mean           std  min         25%  \
    runtime_category                                                        
    < 60 mins          282.0  8.620738e+07  1.622639e+08  0.0   2788004.5   
    60-120 mins       2549.0  7.961783e+07  1.636566e+08  0.0   1200000.0   
    > 120 mins         478.0  2.045094e+08  3.099314e+08  0.0  16446364.0   
    
                             50%           75%           max  
    runtime_category                                          
    < 60 mins         25653962.5  8.016036e+07  1.259200e+09  
    60-120 mins       22673340.0  8.332000e+07  2.776345e+09  
    > 120 mins        72912745.5  2.547346e+08  2.048134e+09  


# Summary stats for runtimes impact on Worldwide Gross


```python
# Group the data by runtime category and calculate the mean worldwide gross
mean_values = cleaned_df.groupby('runtime_category')['worldwide_gross'].mean()

# Define the categories
categories = ['Short', 'Medium', 'Long']

# Set the width of the bars
bar_width = 0.5

# Plotting the mean values for each category
plt.figure(figsize=(10, 6))
plt.bar(categories, mean_values, width=bar_width)

plt.title('Mean Worldwide Gross by Runtime Category')
plt.xlabel('Runtime Category')
plt.ylabel('Mean Worldwide Gross')
plt.show()


```


    
![png](output_72_0.png)
    



```python
print(cleaned_df.columns)

```

    Index(['movie', 'production_budget', 'worldwide_gross', 'runtime_minutes',
           'genres', 'runtime_category'],
          dtype='object')


# Production Budgets impact on Worldwide Gross

Broke up movie budgets into three catagories Small (< 50 Million), Med (50-100 Million), and Large (>100 Million)

Large budgets generated the most worldwide gross, including movies that generated over 2 billion USD.
Small budgets did not generate a single movie over 1 billion dollars.
Medium budgets did better than small budgets and generated a few movies over a billion dollars.


```python
# Y axis Billions
def billions_formatter(x, pos):
    return f'{x/1e9:.2f}B'

# Categorize the movie budgets into small, medium, and large
budget_labels = ['Small (<50M)', 'Medium (50M-100M)', 'Large (>100M)']
bins = [0, 50e6, 100e6, float('inf')]
cleaned_df['budget_category'] = pd.cut(cleaned_df['production_budget'], bins=bins, labels=budget_labels)

# Plotting worldwide gross by budget category
plt.figure(figsize=(12, 8))
sns.boxplot(x=cleaned_df['budget_category'], y=cleaned_df['worldwide_gross'])
plt.title('Worldwide Gross by Budget Category', fontsize=20)
plt.xlabel('Movie Budget (Millions USD)', fontsize=15)
plt.ylabel('Worldwide Gross (Billions USD)', fontsize=15)

# Set y-axis limits-#3b 
plt.ylim(0, 3e9)  

# Format y-axis labels as billions
formatter = FuncFormatter(billions_formatter)
ax = plt.gca()
ax.yaxis.set_major_formatter(formatter)

plt.ylim(0, 3e9)  # Set y-axis limit to 3 billion


plt.tight_layout()  # Adjust spacing between subplots

plt.show()







```


    
![png](output_75_0.png)
    


# Average worldwide gross by budget

Large budgets produced on average about 550,000,000
Med budgets produced on average about 180,000,000
Small budget worldwide gross. 40,000,000


```python
# Group the data by budget category and calculate the mean worldwide gross
mean_values = cleaned_df.groupby('budget_category')['worldwide_gross'].mean()

# Define the categories
categories = ['Small (<50M)', 'Medium (50M-100M)', 'Large (>100M)']

# Set the width of the bars
bar_width = 0.5

# Plotting the mean values for each category
plt.figure(figsize=(8, 6))
plt.bar(categories, mean_values, width=bar_width, color=['#1f77b4', '#ff7f0e', '#2ca02c'])

plt.title('Mean Worldwide Gross by Budget Category')
plt.xlabel('Budget Category')
plt.ylabel('Mean Worldwide Gross (Millions USD)')
plt.xticks(rotation=45)  # Rotate x-axis labels for better readability

plt.ylim(0, 600e6)  # Set y-axis limit to 800 million
plt.ylabel('Mean Worldwide Gross (Hundreds of Millions USD)')





plt.show()

```


    
![png](output_77_0.png)
    



```python
# Calculate summary statistics for ww gross by budget category
budget_summary = cleaned_df.groupby('budget_category')['worldwide_gross'].describe()

# Print the summary statistics
print(budget_summary)


```

                        count          mean           std        min          25%  \
    budget_category                                                                 
    Small (<50M)       2676.0  4.118832e+07  6.860501e+07        0.0     740932.0   
    Medium (50M-100M)   359.0  1.853351e+08  1.753544e+08        0.0   77592282.5   
    Large (>100M)       274.0  5.410824e+08  3.780591e+08  3100000.0  290048571.0   
    
                               50%           75%           max  
    budget_category                                             
    Small (<50M)        14868357.5  5.246054e+07  6.974580e+08  
    Medium (50M-100M)  137489730.0  2.303680e+08  1.160336e+09  
    Large (>100M)      409378800.5  7.134626e+08  2.776345e+09  


# Conclusion

## Genre's impact on Worldwide gross
Sci-Fi, Animation, Adventure, action (based on box-plot) produces the largest Worldwide Gross Genre such as western, bio, musical, romance, news are low Worldwide Gross earners.

## Runtime's impact on Worldwide Gross
Long runtimes are better for gross. Short and med do not produce high Worldwide gross.

## Budgets impact on Worldwide Gross
Higher budget increases likelihood of higher Worldwide Gross. Small budget does not produce billion dollar gross movies. A few medium budget movies have produced over a billion dollars in Worldwide Gross.

## Recommendations
The movies genre should be a mixture of Sci-Fi, Animation, Adventure and action. Microsoft Studios should not make Westerns, biographies, musicals or new films. The runtime of the movies should be over two hours long. A budget larger than 100 million dollars will produce the highest Worldwide Gross.


```python

```
