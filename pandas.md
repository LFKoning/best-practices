# Pandas

This document gives some tips and tricks to effectively use pandas data frames. The examples in this document, use the following sample data sets:

```python
import pandas as pd

df = pd.DataFrame({
    "x": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    "y": list("ABCDEFGHIJ"),
})

df_labels = df.set_index(df["y"].str.lower())
```

## Simple select statements

Columns are easily selected using their name, for example:

```python
# Select both x and y columns.
df[["x", "y"]]

# Select only the x column as DataFrame.
# Note: using a single-item list to select x.
df[["x"]]

# Select only the x column as Series.
# Note: using a str to select x.
df["x"]
```

Sets of rows can be selected using slices, for example:

```python
# Select first five rows.
# Note: Exclusive slicing, the 5th row is excluded.
df[0:5]

# Same using row labels.
# Note: Inclusive slicing, the "e" row is included.
df_labels["a":"e"]

# Select even rows.
df[::2]
```

Pandas also offers a these convenience functions to quickly peek at your data:

```python
# Get first 5 rows
df.head()

# Get last 10 rows
df.tail(10)

# Sample 10 random rows
df.sample(10)
```

## Selecting rows and columns

For more complex select statements, use the the `loc` and `iloc` methods, for example:

```python
# Select all rows and x and y columms
df.loc[:, ["x", "y"]]

# Select first 3 rows
df.loc[0:3, ["x", "y"]]

# Select 3 rows of column x as series
df.loc[0:3, "x"]

# Same, but using indexed selection instead
df.iloc[0:4, 0]
```

Alternatively, you can also use the `query` method to perform more complex selections:

```python

```

## In place or not

## Method chaining

## Avoid loops

See also: [Modern Pandas blog post](https://tomaugspurger.github.io/modern-1-intro.html)
