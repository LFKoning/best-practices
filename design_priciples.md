# Design Principles

This section describes some guiding principles to structure and improve your code. These principles are not "always do this" or "never do that" kind of rules; they are
guidelines which you should keep in the back of your mind while writing code. This section describes the most important principles and illustrates their goal with one or two simple examples.

## The Zen of Python

Type `import this` in Python to read "The Zen of Python" by Tim Peters. It is a nice set of principles to go by! See if your code adheres to them or not:

```text
The Zen of Python, by Tim Peters

Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one-- and preferably only one --obvious way to do it.
Although that way may not be obvious at first unless you're Dutch.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea -- let's do more of those!
```

## Simple is better than complex

Also often referred to as KISS: Keep It Simple Stupid! This one sounds easy to do, but in practice there are many pitfalls that could make your code more complex than it needs to be.

For example, one could create a single function to handle many different cases. As a result, this function takes a lot of parameters and a involves many logical steps to handle all the different cases. An example:

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

- Having a hard time summarizing the functions behavior in a single line.
- Having functions with many arguments, some of which are only relevant to very specific cases.
- Having functions with many or lengthy `if ... elif ... else` statements.

Some tips to reduce complexity:

- Split complex functions up into smaller, more specialized functions.
- Focus on tackling a single problem in each function or class you write.
- Implement only functionality that is required right now / in the foreseeable future.
- Choose sensible defaults, hardcode things that are not expected to change frequently.

## Separation of concerns

Related to the topic above; complexity often arises from doing too many things in one go. A typical data science project includes many different tasks; reading data files, preparing and combining data, training machine learning models, et cetera.

It is possible to perform all these tasks in a single Python script that runs from top to bottom. However, doing so will make that script very hard to read and maintain.

It is therefore a good idea to separate different tasks or concerns of your project into different scripts, classes, and functions. Some tips for structuring your code:

- Split different tasks (ex. reading files, cleaning data, engineering features, training models) into separate modules and / or classes.
- Split different steps (ex. fill missings, convert data types) into separate functions
- Add (at least) a single-line docstring to all your classes, methods, and functions summarizing the task the object is supposed to do.
- If it is hard to come up with a single line summary; reconsider your separation of concerns and whether it is granular enough.

See also:

[Design Patterns](https://en.wikipedia.org/wiki/Software_design_pattern): Common design patterns for structuring your code.

## Don't repeat yourself

Repetition is typically a sign that you can implement something more efficiently; so avoid it. Not only does it save you work (being lazy is a good thing!), but it can also help others understand and maintain your code more easily.

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

To conclude; every time you find yourself repeating lines of code, try coming up with a more re-usable solution!

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

The loading now happens in the constructor. Therefore, if an error occurs the user will get an error straight away! This will make it easier for the user to debug their code.
