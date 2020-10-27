# Pandas

This document gives some tips and tricks to effectively use pandas data frames. The examples in this document, use the following sample data sets:

```python
import numpy as np
import pandas as pd
import datetime as dt


df = pd.DataFrame({
    "x": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    "y": list("ABCDEFGHIJ"),
})

df_labels = df.set_index(df["y"].str.lower())
```

## DataFrame descriptives

To get more information about your DataFrame, use:

```python
df.info()
```

 This will print out useful information about `df`, including the column names, number of missing values, data types et cetera. It will also show memory usage excluding object columns; use `memory_usage="deep"` to get the exact memory usage.

To get more information about the values in each column, use:

```python
df.describe(include="all")
```

This will give you value frequencies, central tendencies, value range and dispersion for each column.

## Always avoid object columns

Always try to avoid columns of data type `object`; these take up a lot of memory and are slow to operate on.

If you have categorical columns (ie. string values that are repeated many times), cast them to the `Categorical` data type:

```python
# Series of 1000 categorical values
x = pd.Series(np.random.choice(["AAA", "BBB", "CCC"], 1000))
# Note: sys.getsizeof(x) returns 60152 bytes

# Convert to pandas Categorical data type
x = pd.Categorical(x)
# Note: Now sys.getsizeof(x) returns only 1284 bytes!
```

A typical source for `object` type columns comes from reading CSV data into pandas. However, `read_csv` allows you to specify data types on read like so:

```python
df = pd.read_csv("data.csv", dtype={"categorical_column": "category"})
```

This line ensures that the column `categorical_column` is converted to the `Categorical` data type when `data.csv` is loaded.

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

# Same, but using integer selection instead
# Note: Includes rows up to the 4th one.
df.iloc[0:4, 0]
```

Alternatively, you can also use the `query` method to perform more complex selections:

## Selecting rows with a logical expression

You can use logical expressions to select rows, like so:

```python
# Simplest form
df[df["x"] < 3]

# Using loc to only select column y
df.loc[df["x"] < 3, ["y"]]

# Using the index (RangeIndex)
df[df.index < 3]

# Using index labels
df_labels[df_labels.index.isin(["a",  "b", "e"])]

# Or using a True / False array
mask = [True, False] * 5
df[mask]
```

If you need to combine expressions, make sure to encapsulate them in parantheses `(...)`:

```python
# Select rows where x < 4 or x > 7
df[
    (df["x"] < 4) | (df["x"] > 7)
]
```

Note: you can also use the `query` method instead if you prefer:

<https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.query.html>

## In place changes

Take a look at this example:

```python
def prep_data(df):
    """Prepare the data."""

    df["timestamp"] = dt.datetime.now()
    df.fillna(0, inplace=True)

    return df
```

Suppose you would call the function like this: `df_clean = prep_data(df_raw)`. What would you expect `df_raw` to look like?

The answer might supprise you; it looks the same as `df_clean`! The reason is that the `prep_data` function makes changes directly to the provided DataFrame!

Making changes "in place" is generally a bad idea, especially in a function or method. Users typically do not expect that the DataFrame they provide gets changes along the way.

So how can we improve the function, look at this example:

```python
def prep_data(df):
    """Prepare the data."""

    df = df.assign(timestamp=dt.datetime.now())
    df = df.fillna(0)

    return df
```

Most DataFrame methods automatically create and return a copy of the DataFrame. So switching to the `assign` method made sure that the original DataFrame stays as-is. Same goes for the `fillna` method, from which we removed the `inplace=True` argument. Now the function works much more as you would expect!

## Method chaining

In the example above we see two lines starting with `df = df.<method>`; surely there has to be a more efficient way to write this down? There is and it is called "method chaining". Here is an updated example for the `prep_data` function:

```python
def prep_data(df):
    """Prepare the data."""

    return (
        df
        .assign(timestamp=dt.datetime.now())
        .fillna(0)
    )
```

Looks much cleaner! The DataFrame is passed along each step in the chain and methods are executed on it one after the other.

There are a few caveats though, take a look at this code:

```python
df.assign(
    a=np.random.normal(0, 1, df.shape[0]),
    b=df["a"] ** 2,
)
```

This will fail because the original DataFrame does not contain column `a`; the assignment has not yet taken place. Perhaps this helps:

```python
df = (
    df
    .assign(a=np.random.normal(0, 1, df.shape[0])
    .assign(b=df["a"] ** 2)
)
```

This will still fail: `df` in the second assign statement refers to the original DataFrame. This does not get updated until the entire chain of methods has finished. Only then the result is assigned to `df`...

This works though:

```python
df = (
    df
    .assign(a=np.random.normal(0, 1, df.shape[0])
    .assign(b=lambda df: df["a"] ** 2)
)
```

The `lambda` function assigns the DataFrame returned from the previous step (where the `a` columns was created) to the `df` variable.

## Avoid loops

Looping over the rows or columns of a DataFrame is typically slow; pandas is made to perform computations on entire columns or subsets. Whenever you are tempted to use a loop, rethink the problem to see if there is not a more efficient solution.

## See also

- [Modern Pandas blog post](https://tomaugspurger.github.io/modern-1-intro.html)
- [Making Pandas Fly](https://www.youtube.com/watch?v=N4pj3CS857c)
- [Efficient looping](https://medium.com/swlh/how-to-efficiently-loop-through-pandas-dataframe-660e4660125d)
