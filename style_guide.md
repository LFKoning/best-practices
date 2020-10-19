# Style and formatting

## PEP8 compliant

PEP8 is the name of the document describing how you should format your Python code, it can be found here:

<https://www.python.org/dev/peps/pep-0008/>

Clear and consistent formatting is very important; it makes your code more readable, not only for yourself but also for others. Since most projects are collaborative in nature, this is extremely important!

Below the key points of PEP8 are outlined for your convenience.

Tip: Use the `black` package to automatically format your code to PEP8 standards.

Tip: Use the `pylint` package to check your code on formatting and errors.

## Indentation and white-space

- Use 4 spaces to indent lines.
- Top-level functions and classes are preceeded by 2 newlines.
- Class methods are preceeded by 1 newline.
- Use blank lines sparingly to indicate logical sections.
- Put a single newline at the end of your file.
- Use spaces around operators; ex. `x = 1` or `x = x + 1`.
- Don't use spaces around keyworded arguments; ex. `some_func(arg=1, other_arg=2)`.

## Line length and wrapping long lines

PEP suggests the following maximum line lengths:

- 79 characters for code
- 72 characters for comments and docstrings

However, the auto-formatting tool `black` uses a line length of 88 characters so it is easier to stick with that setting.

Wrapping long lines can be done with:

- parentheses `(...)`
- brackets `[..]`
- braces `{...}`

Examples:

```python
# Wrapping a long list of function arguments
some_function(
    arg1=...,
    arg2=...,
    arg3=...,
    ...
)

# Wrapping a long string
raise ValueError(
    "Cannot convert series to float; not all value are numeric. "
    "Please check your input data for any non-numeric values."
)

# Wrapping a long list
some_list = [
    "long string value 1", "long string value 2", "long string value 3",
    "long string value 4", "long string value 5", "long string value 6",
    ...
]
```

Note that `black` sometimes has a different opinion on how long lines should be wrapped; in exceptional cases you can use `#fmt-on` / `#fmt-off` comments to turn automatic formatting on or off respectively.

## Use descriptive names

- Give your variables, functions and classes descriptive names.
- A name should describe the value or functionality as brief and accurate as possible.
- Avoid single-letter names, unless it's extremely obvious what they refer to (ex. math / statistical symbol).

- Use these styles for names:
  - variables: use `snake_case`
  - functions/methods: use `snake_case`
  - classes: use `CamelCase`
  - constants: use `ALL_CAPS_WITH_UNDERSCORES`

- Use underscores to:
  - Avoid name clashes with reserved words, ex.: `class` => `class_`.
  - Indicate a private class property or method, ex: `_private_property`.
  - Use double underscores to prevent access to a property, ex: `__cant_access_me`.
  - Double-underscore (dunder) methods implement special functionality, ex: `__add__()`

Examples:

```python
# Hmmm, the logging interface breaks the snake_case rules!
logger = logging.getLogger(__name__)

# The name tmp is not very descriptive; what kind of data is involved here?
tmp = pd.read_csv("data.csv")

# Same, might want to add some description: survey_df, transaction_df, etc.
df = pd.read_csv("data.csv")

# If the function handles all kinds of data, maybe df is descriptive enough...
def fill_missings(df):
    ...

# Use an underscore to distinguish from the built-in min() function.
# Note: Not using an underscore will mask the built-in function in your namespace.
min_ = df["date"].min()

# Protected / private attributes in a class
class Dog:

    # Special function: Object constructor
    def __init__(self, name):
        self._name = name
        self.__secret = name[::-1]

    # Special function: String representation of the object
    def __repr__(self):
        return f"Dog(\"{self._name}\")"

pluto = Dog("Pluto")

# Works, but acesses a private property.
# Note: Authors may change these properties without notice.
pluto._name

# Throws an attribute error
pluto.__secret
```

Tip: Learn to use tab completion when you are using longer variable names.

## Use docstrings

Use docstrings for every class or function you implement; at the very least provide a single line summarizing the intended functionality of the class or function.
If it turns out to be hard to summarize the functionality into a single line, consider splitting the function up into multiple functions.

For complex functions, you could provide a more elaborate docstring describing the process and design choices for the function. Note that the "why" is more important than the "how"; how the function works should also be apparent from reading its code.

Finally, you can describe function arguments, return values and exceptions to further aid the user in understanding your function.

Examples:

```python
# Single line docstring for a simple function
def mean_fill_numeric(df):
    """Mean fill missing values in numeric columns."""

    ...


# More elaborate description for a more complex function
def fill_missing(df):
    """Fill missing values in a DataFrame.

    Uses the mean for numeric columns and the mode for categorical values.
    Leaves other columns as is.
    """

    ...


# Describing arguments, return values and exceptions.
# Note: This style is called numpydoc style.
def mean_fill_numeric(df):
    """Mean fill missing values in numeric columns.

    Parameters
    ----------
    df : pandas.DataFrame
        DataFrame with numeric data type columns.

    Returns
    -------
    pandas.DataFrame
        Copy of the DataFrame with missing values mean filled.

    Raises
    ------
    TypeError
        Raises type error if non-numeric columns were present.
    """

    ...


# Exmaple of a class docstring
class Dog:
    """Dog class for demonstration purposes.

    Class docstrings should list the constructor arguments under
    the Parameters section.

    In addition, they can also have an
    Attributes and Methods section. These sections should only list
    *public* attributes and methods.

    Parameters
    ----------
    name : str
        Name for the dog.

    Methods
    -------
    bark()
        Prints a message to the terminal.
    """

    def __init__(self, name):
        self._name = name
        self.__secret = name[::-1]

    def bark(self):
        """Prints a message to the terminal."""
        print(f"{self.name} says WOOF!")
```

See also:

- <https://numpydoc.readthedocs.io/en/latest/format.html>
- <https://realpython.com/documenting-python-code/>

Tip: Tools like Sphinx can automatically convert your docstrings into a documentation website.

## Use comments effectively

The goal of comments is to explain **why** you made certain choices in your code. The **how** should be apparent from the code itself and should not require additional explanation.

Here are some examples of superfluous comments:

```python
# Read in the data
df = pd.read_csv("data_scv", encoding="Windows-1252", decimal=",", sep=";")

# Fill missing values
df = df.fillna(0)
```

And here are some examples of more useful comments:

```python
# Caution: data are exported from Dutch Excel (cp1252, decimal comma, semicolon separator)!
df = pd.read_csv("data_scv", encoding="Windows-1252", decimal=",", sep=";")

# Data are centered, so 0 effectively mean fills
df = df.fillna(0)
```

Comments can also be handy while designing and structuring your code; you can start by creating functions (and/or classes) and writing down their intended functionality in comments. When you are happy with the structure, you can start with replacing the comments by actual code.

See also: <https://realpython.com/python-comments-guide/>
