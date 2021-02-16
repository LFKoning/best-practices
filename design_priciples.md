# Design Principles

This section describes some guiding principles to structure and improve your code. These principles are not "always do this" or "never do that" kind of rules; they are more general ideas which you should try to follow when writing code. This section describes the most important principles and illustrates their goal with one or two simple examples.

Tip: Type `import this` in Python for more guiding principles.

## Don't repeat yourself

Repetition is typically a sign that you can implement something more efficiently; so avoid repetition. Not only does it save you work (being lazy is a good thing!), but it can also help others understand and maintain your code more easily.

Example:

```python
# Repetition; inefficient and error prone
df["col_A"] = df["col_A"].fillna(df["col_A"].mean())
df["col_B"] = df["col_B"].fillna(df["col_B"].mean())
df["col_C"] = df["col_C"].fillna(df["col_A"].mean())

# A better approach
for col in "col_A", "col_B", "col_C":
    df[col] = df[col].fillna(df[col].mean()
```

The first approach is lengthy and does not make it explicit that `col_A`, `col_B` and `col_C` are all treated in the same way. It is also more error prone; did you notice the copy-paste error on the last line? Finally, it is less flexible; adding another column is more work in the first appoach compared to the second approach.

To conclude; every time you notice you are repeating certain lines of code, try coming up with a more re-usable solution!

## Separation of concerns

A typical data science project includes many different tasks; reading data files, preparing and combining, training or applying a statistical model, et cetera. It is possible to perform all these tasks in a single Python file that runs from top to bottom; Python does not really enforce or recommend a certain structure of your code. However, putting everything into a single script will make that script very hard to maintain.

Therefore it is a good idea to separate different concerns of your project into different functions, classes, and scripts (modules). Some tips:

- Split different tasks (ex. read files, clean data, engineer features, build a model) into separate modules and / or classes.
- Split different steps (ex. fill missings, convert data types) into separate functions
- Add (at least) a single-line docstring to all your classes / methods / functions summarizing the concern the object is supposed to address.
- Is it hard to summarize the functionality in a single line? Reconsider your separation of concerns and whether it is fine grained enough.

## Simple is better than complex

This one sounds easy to do, but in practice there are many pitfalls that could make your code more complex than it needs to be. For example, in an effort to reduce repetition one could create a single function to handle many different cases. The function than takes a lot of configuration parameters and a involves lots of logical steps to handle all the different cases. An example:

```python
def data_prep(df, scale=True, dummies=True, fill_num="mean", fill_cat="missing", method=None):
    """Prepare the data by filling missing values, scaling numerical values and
    creating dummies for categorical variables."""

    ...
```

In this case it might be better to create separate functions for each column type rather than a single one to deal with all of them.

```python
def prep_numeric(df, scale=True, fill="mean"):
    """Fills missing numerical values, then scales them to [0, 1]."""

    ...

def prep_categorical(df, dummies=True, fill="missing"):
    """Fills missing categorical values, then creates dummy variables."""

    ...
```

Here are some pointers for identifying when your code may be overly complex:

- Functions with many arguments, some of which are only relevant to very specific cases.
- Functions for which it is hard to summarize its behavior in a single line.
- Functions with many or lengthy `if ... elif ... else` statements.

Some tips to reduce complexity:

- Split complex functions up into smaller, more specialized functions.
- Focus on tackling a single problem in each function or class you write.
- Do not implement functionality you do not need right now / in the foreseeable future!
- Choose sensible defaults, hardcode whatever does not change frequently.

## Explicit is better than implicit

Your code should always make as explicit as possible what is going on and where variables or values come from. For example, the code below works but is very implicit where `x` comes from:

```python
def print_func():
    print(x)

x = 3
print_func()
```

An improvement here would be to pass the variable that is printed as an argument to the function. This example is quite obvious, but there are more subtle, implicit ways in which unexpected behavior can pop up. Take a look at this example:

```python
def mean_fill(df, columns):
    for column in columns:
        df[column] = df[column].fillna(df[column].mean())
    return df

raw_df = pd.read_csv("data.csv")
processed_df = mean_fill(raw_df)
```

What do you think `raw_df` will look like at the end of this script? The answer might suprise you; it looks the same as `processed_df`! What happens is that `raw_df` gets changed "in place" in the `mean_fill()` function and will therefore also have its missing values imputed. A user will probably not expect this implicit behavior, so it is best avoided.

## Fail Fast

Direct feedback trumps delayed feedback, so try to report on errors as fast as possible. Take look at these two implementations of a `DataReader` class:

```python
class DataReader:
    """
    Reads and processes data files.

    Parameters
    ----------
    path : str
        Path the data file.
    """

    def __init__(self, path):
        self._path = path

    def read(self):
        """Reads the data and returns it as a pandes DataFrame."""

        return pd.read_csv(self._path)
```

If the file provided by `path` does not exist, the user of your class will not find out until the `read()` method is called. The user then needs to go back to the construction of the object to fix the problem...

A better approach might be:

```python
class DataReader:
    """
    Reads and processes data files.

    Parameters
    ----------
    path : str
        Path the data file.
    """

    def __init__(self, path):
        self._data = pd.read_csv(self._path)

    def read(self):
        """Returns the data as a pandes DataFrame."""

        return self._data
```

The loading now happens in the constructor. Therefore, if an error occurs the user will get an error straight away!
