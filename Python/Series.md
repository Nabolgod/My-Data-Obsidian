Одномерный индексированный массив данных некоторого фиксированного типа.

```python
obj = pd.Series([1, 2, 3, 4])
print(obj) # 0 4; 1 7 ... dtype: int64 (тип numpy)

obj2 = pd.Series([4, 7, -5, 3], index=['d', 'b', 'a', 'c'])
print(obj2) # d 4; b 7 ... dtype: int64 (тип numpy)

fruits = np.array(['apple', 'orange', 'banan'])
series2 = pd.Series(fruits)
print(series2) # 0 "apple"; ... dtype: object

obj2[obj2 > 0] # d 4; b 7
obj2 * 2 # 8 ...
```
