---
layout: post-toc
title: Data Science Cheatsheet
tags: tutorial coding
permalink: ds
---

The goal of this Python Data Science Cheatsheet is to list the most commonly needed concepts and tools for beginners, which is created based on many [references](#references).

## Python Collection Data Types

There are four collection data types in the Python: List/Tuple/Set/Dictionary

The difference lies in:

- Ordered (indexed) vs. Unordered (un-indexed)
- Mutable (the value of an item can be changed) vs. Immutable
- Duplicate vs. Unique (is duplicate items allowed or not)

The definitions are as follows. You can check usage examples in [Python Cheatsheet](/python)

- List `[...]` is a collection that is **ordered**, **mutable** and allows **duplicate items**.
- Tuple `(...)` is special type of list that is **ordered**, **immutable**, and allows **duplicate items**.
- Set `{...}` is a collection that is **unordered**, **immutable**, with **unique items**.
- Dictionary `{key1:value1, key2:value2,...}` is a collection that is **unordered**, **mutable**, with **unique keys**.

NOTE: Python 3.7+, dictionaries are ordered.

## Numpy

Numpy is the core library for scientific computing in Python, which provides a high-performance multidimensional array objects and related tools to work with the array.

Import convention:
```
import numpy as np
```
### Creating Numpy Arrays from Python Lists

Let's try to create the following arrays from lists. Axes are the dimensions of the array. The default axis is 0, which refers to row and axis 1 refers to column, etc. 

![axis](https://user-images.githubusercontent.com/595772/109432830-17430900-79db-11eb-831b-bcd43ca95f9a.png)

```
# 1D array
a = np.array([2, 5, 6, 9])  

# 2D array
b = np.array([
    [3.5, 4.0, 6.5], 
    [0.4, 0.9, 4.7],
])

# 3D array
c = np.array([
    [
        [7, 1],
        [9, 4],
        [2, 3],
    ],
    [
        [4, 5],
        [0, 6],
        [8, 0],
    ],
    [
        [1, 3],
        [3, 9],
        [6, 9],
    ],
    [
        [5, 8],
        [8, 8],
        [4, 7],
    ],
])
```

- `.ndim`: number of axes (dimensions) of the array
- `.size`: total number of elements of the array
- `.shape`: a tuple of integers that indicate the number of elements in each axis
- `.dtype`: data type of the array

```
print(a.ndim, a.size, a.shape, a.dtype)
print(b.ndim, b.size, b.shape, b.dtype)
print(c.ndim, c.size, c.shape, c.dtype)

1 4 (4,) int64
2 6 (2, 3) float64
3 24 (4, 3, 2) int64
```

### Numpy Array vs. Python List

- A list can have elements of different types but Numpy array can only have elements of the same type.
- Numpy array enables element-wise computing, such as adding 1 to all elements.

```python
# element-wise computing
a = np.array([1, 2, 3, 4])
b = np.array([5, 6, 7, 8])
print(a + 2)
print(a + b)

[3 4 5 6]
[ 6  8 10 12]
```
- Numpy array has various functions for scientific computing.

```python
a = np.array([1, 3, 5, 9])
b = np.array([
    [3.5, 4.0, 6.5], 
    [0.4, 0.9, 4.7],
])
print(a.sum(), a.min(), a.max(), a.mean(), np.median(a), np.std(a))
print(b.sum())
 # adding rows (axis=0) and adding columns (axis=1)
print(b.sum(axis=0), b.sum(axis=1))

18 1 9 4.5 4.0 2.958039891549808
20.0
[ 3.9  4.9 11.2] [14.  6.]
```
- A slice of a Python list is a copy while a slice of a Numpy array is a view (same way of slicing using `[start:stop:step]`)

For list:

```python
# slices of a Python list are copies of the list
# changing slices DOES NOT change the original list
a = [1, 2, 3, 4, 5]
b = a[2:4]
print(a, b)

b[1] = 9  # change b
print(b)
print(a)  # a does not change

[1, 2, 3, 4, 5] [3, 4]
[3, 9]
[1, 2, 3, 4, 5]

```
For Numpy array:

```python
# slices of a Numpy array are views of the array
# changing the slices will change the original array!!!
c = np.array([1, 2, 3, 4, 5])
d = c[2:4]  # d is a view of c
print(d)

d[1] = 9  # change d
print(d)
print(c)  # c changed!

[3 4]
[3 9]
[1 2 3 9 5]
```
use `np.copy()` to make a copy of a numpy array:

```python
c = np.array([1, 2, 3, 4, 5])
f = np.copy(c)  # make a copy of c
f = f[2:4]
print(f)
f[1] = 20  # change f
print(f)
print(c)  # c is not changed

[3 4]
[ 3 20]
[1 2 3 4 5]
```
- Numpy array is way more faster than Python list for computation

### Initial Placeholder Arrays

Create evenly spaced values by step value: (start, stop, step):

```
>>>> np.arange(1, 10, 2) 
array([1, 3, 5, 7, 9])
```

Create evenly spaced values by number of samples: (start, stop, num-of-samples):

```python
>>>> np.linspace(1, 10, 5)
array([ 1.  ,  3.25,  5.5 ,  7.75, 10.  ])
```

Create arrays with random numbers

```python
>>>> np.random.random((2, 3))
array([[0.65107703, 0.91495968, 0.85003858],
       [0.44945067, 0.09541012, 0.37081825]])

```

Create arrays with zeros and ones:

```python
np.zeros ((2, 3))  # 2 rows 3 columns
np.ones ((2, 3, 4))  # 3D array with all ones
```

See more: [DataCamp Numpy Cheatsheet]({{site.baseurl}}/assets/img/posts/cheatsheet/numpy.png)

[Back to Top](#title)

## Pandas

[Pandas](https://pandas.pydata.org/) is a fast, powerful, and flexible data analysis and manipulation tool.

Use the following import convention:

```python
import pandas as pd
```

### Pandas Series

A pandas series is a one dimensional **labeled** array (a Python array is only indexed but not labeled):

```python
# a series is an array with labeled index, default to 0, 1, 2...
s = pd.Series([7, 9, 8])

0    7
1    9
2    8
dtype: int64
```

You can specify index labels:

```python
# series is array with labeled index
s = pd.Series([7, 9, 8], index=['a', 'b', 'c'])

a    7
b    9
c    8
dtype: int64
```

You can use the label to access the element, e.g. `s['c']`

[Back to Top](#title)

### Pandas DataFrame

A pandas DataFrame is a two dimensional **labeled** data structure with columns of potentially different types:

<img class="mx-auto" src="{{site.baseurl}}/assets/img/posts/cheatsheet/dataframe.png">

Create a pandas dataframe from a Python dictionary:

```python
# column names by default are keys in the dict
countries = {
    'country': ['USA', 'China', 'Japan'],
    'gdp': [20.49, 13.40, 4.97],
    'population':[331, 1439, 126],
}
df = pd.DataFrame(countries)

  country    gdp  population
0     USA  20.49         331
1   China  13.40        1439
2   Japan   4.97         126
```

Choose certain columns from keys:

```python
df = pd.DataFrame(countries, columns=['country', 'gdp'])

  country    gdp
0     USA  20.49
1   China  13.40
2   Japan   4.97
```

Read data from CSV (Comma-separated values) files: ([countries-2021.csv.zip](https://github.com/harrywang/harrywang.github.io/files/8162154/countries-2021.csv.zip)
):

The data file is top 10 countries by GDP (in trillions) in 2021 with information on population (in millions), area (in millions square kilometer $km^2$), capital, continent.

```python
df = pd.read_csv('countries-2021.csv')
df

          country    gdp  population  area          capital      continent
0   United States  20.49      331.00  9.53  WASHINGTON D.C.  North America
1           China  13.40     1439.32  9.60          Beijing           Asia
2           Japan   4.97      126.48  0.38            Tokyo           Asia
3         Germany   4.00       83.78  0.36           Berlin         Europe
4  United Kingdom   2.83       67.89  0.24           London         Europe
5          France   2.78       65.27  0.64            Paris         Europe
6           India   2.72     1380.00  3.29        New Delhi           Asia
7           Italy   2.07       60.46  0.30             Rome         Europe
8          Brazil   1.87      212.56  8.52         Brasilia  South America
9          Canada   1.71       37.74  9.98           Ottawa  North America
```
- `.index`:  returns the index labels
- `.columns`: returns the column names
- `.info()`: prints information about a DataFrame including the index dtype and columns, non-null values and memory usage.

```python
df.info()

<class 'pandas.core.frame.DataFrame'>
RangeIndex: 10 entries, 0 to 9
Data columns (total 6 columns):
 #   Column      Non-Null Count  Dtype  
---  ------      --------------  -----  
 0   country     10 non-null     object 
 1   gdp         10 non-null     float64
 2   population  10 non-null     float64
 3   area        10 non-null     float64
 4   capital     10 non-null     object 
 5   continent   10 non-null     object 
dtypes: float64(3), object(3)
memory usage: 608.0+ bytes
```

- `.describe()`: prints descriptive statistics for numerical columns excluding NaN values

```python
df.describe()

             gdp   population      area
count  10.000000    10.000000  10.00000
mean    5.684000   380.450000   4.28400
std     6.243788   549.780564   4.51295
min     1.710000    37.740000   0.24000
25%     2.232500    65.925000   0.36500
50%     2.805000   105.130000   1.96500
75%     4.727500   301.390000   9.27750
max    20.490000  1439.320000   9.98000
```

- `.head(n)`/`.tail(n)`: returns the first/last n rows (default n=5)

```python
df.head()

           country    gdp  population  area          capital      continent
0   United States  20.49      331.00  9.53  WASHINGTON D.C.  North America
1           China  13.40     1439.32  9.60          Beijing           Asia
2           Japan   4.97      126.48  0.38            Tokyo           Asia
3         Germany   4.00       83.78  0.36           Berlin         Europe
4  United Kingdom   2.83       67.89  0.24           London         Europe
```

[Back to Top](#title)

### Selecting data in DataFrames using `[]`

- Slicing a DataFrame by **rows** (you have to specify a range): `df[start:stop]`
- Slicing a DataFrame by **columns**:
    - one column: `df['column name']` or if `df.column_name` if there are no space in the column name
    - multiple columns: `df[['column name1', 'column name2',...]]`

```python
# slice by rows
df[1:3]

  country    gdp  population  area  capital continent
1   China  13.40     1439.32  9.60  Beijing      Asia
2   Japan   4.97      126.48  0.38    Tokyo      Asia
```
```python
# select one column
df['country']  # same as df.country

0     United States
1             China
2             Japan
3           Germany
4    United Kingdom
5            France
6             India
7             Italy
8            Brazil
9            Canada
Name: country, dtype: object
```
```python
# select multiple columns
df[['country','gdp']]

          country    gdp
0   United States  20.49
1           China  13.40
2           Japan   4.97
3         Germany   4.00
4  United Kingdom   2.83
5          France   2.78
6           India   2.72
7           Italy   2.07
8          Brazil   1.87
9          Canada   1.71
```

### Selecting data in DataFrames using `.loc` and `.iloc`

`[]` can only do simple data selections by rows and columns.  More complicated data selection can be done by using the following methods:
- [`.iloc`](https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#selection-by-position) is primarily integer position based (from 0 to length-1 of the axis)

```python
# select rows with index 1, 4, and 6
df.iloc[[1, 4, 6]]

# here 1 and 4 are treated as indexes
# row 4 is NOT included, i.e. UK
df.iloc[1:4, :2]

   country    gdp
1    China  13.40
2    Japan   4.97
3  Germany   4.00
```

- [`.loc`](https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#selection-by-label) is primarily label based - **NOTE**: even numbers are treated as labels!

```python
# here 1 adn 4 are treated as labels
# row 4 is included, i.e. UK
df.loc[1:4, ['country', 'gdp']]  

          country    gdp
1           China  13.40
2           Japan   4.97
3         Germany   4.00
4  United Kingdom   2.83
```

### Filtering Rows using Masking Expressions

- Filtering using **one** expression: `df[expression 1]`: one expression

```python
# returns countries with GDP greater than 3 trillions
df[df['gdp'] > 3]

         country    gdp  population  area          capital      continent
0  United States  20.49      331.00  9.53  WASHINGTON D.C.  North America
1          China  13.40     1439.32  9.60          Beijing           Asia
2          Japan   4.97      126.48  0.38            Tokyo           Asia
3        Germany   4.00       83.78  0.36           Berlin         Europe

```
- Filtering using **multiple** expressions:
    - `df[(expression 1) & (expression 2) & ...]`: multiple masking conditions with AND relationship
    - `df[(expression 1) | (expression 2) |...]`: multiple masking conditions with OR relationship

```python
# returns countries with GDP greater than 2 trillions and population less than 100 millions
df[(df['gdp'] > 2) & (df['population'] < 100)]

          country   gdp  population  area capital continent
3         Germany  4.00       83.78  0.36  Berlin    Europe
4  United Kingdom  2.83       67.89  0.24  London    Europe
5          France  2.78       65.27  0.64   Paris    Europe
7           Italy  2.07       60.46  0.30    Rome    Europe
```

### Derived Columns

You can derive new columns from existing ones:

```python
# calculate GDP per capita (GDP divided by population)
# a trillion has 12 zeros, a million has 6 zeros
df['gdp per capita'] = df.gdp * 1000000 / df.population
df

          country    gdp  population  area          capital  gdp per capita
0   United States  20.49      331.00  9.53  WASHINGTON D.C.    61903.323263
1           China  13.40     1439.32  9.60          Beijing     9309.951922
2           Japan   4.97      126.48  0.38            Tokyo    39294.750158
3         Germany   4.00       83.78  0.36           Berlin    47744.091669
4  United Kingdom   2.83       67.89  0.24           London    41685.078804
5          France   2.78       65.27  0.64            Paris    42592.308871
6           India   2.72     1380.00  3.29        New Delhi     1971.014493
7           Italy   2.07       60.46  0.30             Rome    34237.512405
8          Brazil   1.87      212.56  8.52         Brasilia     8797.515995
9          Canada   1.71       37.74  9.98           Ottawa    45310.015898
```

[Back to Top](#title)


## References

- [https://jakevdp.github.io/PythonDataScienceHandbook/](https://jakevdp.github.io/PythonDataScienceHandbook/)
- [https://www.datacamp.com/blog/numpy-cheat-sheet-data-analysis-in-python](https://www.datacamp.com/blog/numpy-cheat-sheet-data-analysis-in-python)
- [https://www.datacamp.com/blog/pandas-cheat-sheet-for-data-science-in-python](https://www.datacamp.com/blog/pandas-cheat-sheet-for-data-science-in-python)
-[https://www.datacamp.com/blog/matplotlib-cheat-sheet-plotting-in-python](https://www.datacamp.com/blog/matplotlib-cheat-sheet-plotting-in-python)

[Back to Top](#title)