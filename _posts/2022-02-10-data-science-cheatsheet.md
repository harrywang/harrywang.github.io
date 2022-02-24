---
layout: post-toc
title: Python Data Science Cheatsheet
tags: tutorial coding
permalink: ds
---

This Python Data Science Cheatsheet is created based on many [references](#references).

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

NOTE: after python 3.7, dictionaries are ordered.

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

```
>>>> np.linspace(1, 10, 5)
array([ 1.  ,  3.25,  5.5 ,  7.75, 10.  ])
```

Create arrays with random numbers

```
>>>> np.random.random((2, 3))
array([[0.65107703, 0.91495968, 0.85003858],
       [0.44945067, 0.09541012, 0.37081825]])

```

Create arrays with zeros and ones:

```
np.zeros ((2, 3))  # 2 rows 3 columns
np.ones ((2, 3, 4))  # 3D array with all ones
```

See more: [DataCamp Numpy Cheatsheet]({{site.baseurl}}/assets/img/posts/cheatsheet/numpy.png)

[Back to Top](#title)


## References

- [https://jakevdp.github.io/PythonDataScienceHandbook/](https://jakevdp.github.io/PythonDataScienceHandbook/)
- [https://www.datacamp.com/blog/numpy-cheat-sheet-data-analysis-in-python](https://www.datacamp.com/blog/numpy-cheat-sheet-data-analysis-in-python)
- [https://www.datacamp.com/blog/pandas-cheat-sheet-for-data-science-in-python](https://www.datacamp.com/blog/pandas-cheat-sheet-for-data-science-in-python)
-[https://www.datacamp.com/blog/matplotlib-cheat-sheet-plotting-in-python](https://www.datacamp.com/blog/matplotlib-cheat-sheet-plotting-in-python)

[Back to Top](#title)