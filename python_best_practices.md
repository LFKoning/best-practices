# Best practices Python

## Style and formatting

### PEP8 compliant

PEP8 is the name of the document describing how you should format your Python code, it can be found here:
https://www.python.org/dev/peps/pep-0008/

Clear and consistent formatting is very important; it makes your code more readable, not only for yourself but also for others. Since most projects are collaborative in nature, this is extremely important!

Below the key points of PEP8 are outlined for your convenience.

Tip: Use the black package to automatically format your code to PEP8 standards.
Tip: Use pylint to check your code on formatting and errors.

### Indentation and white-space

- Use 4 spaces to indent lines.
- Top-level functions and classes are preceeded by 2 newlines.
- Class methods are preceeded by 1 newline.
- Use blank lines sparingly to indicate logical sections.
- Put a single newline at the end of your file.
- Use spaces around operators; ex. `x = 1` or `x = x + 1`.
- Don't use spaces around keyworded arguments; ex. `some_func(arg=1, other_arg=2)`.


### Use descriptive names

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
```
```

Tip: Learn to use tab completion when you are using longer variable names.

### Use docstrings

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


# Bit more elaborate description for a more complex function
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

```

Tip: Tools like Sphinx can automatically convert your docstrings into a documentation website.


### Use comments wisely

The goal of comments is to explain **why** you made certain choices in your code. The **how** should be apparent from the code itself and should not require additional explanation.

Here are some examples of superfluous comments:

```python
# Read in the data
df = pd.read_csv("data_scv", encoding="Windows-1252", decimal=",", sep=";")

# Fill missing values
df = df.fillna(0)
```

And here are some examples of more useful comments
```
# Caution: data are exported from Dutch Excel (cp1252, decimal comma, semicolon separator)!
df = pd.read_csv("data_scv", encoding="Windows-1252", decimal=",", sep=";")

# Data are centered, so 0 effectively mean fills
df = df.fillna(0)
```


## Principles

Tip: Type `import this` in Python for more guiding principles.

### Don't repeat yourself

Repetition is typically a sign that you can implement something more efficiently; so avoid repetition! Not only does it save you work (being lazy is a good thing), but it can also help others to understand and maintain your code.

Example:
```
# Repetition; inefficient and error prone
df["col_A"] = df["col_A"].fillna(df["col_A"].mean())
df["col_B"] = df["col_B"].fillna(df["col_B"].mean())
df["col_C"] = df["col_C"].fillna(df["col_A"].mean())

# A better approach
for col in "col_A", "col_B", "col_C":
	df[col] = df[col].fillna(df[col].mean()
```

The first approach is lengthy and does not make it explicit that `col_A`, `col_B` and `col_C` are all treated in a similar way. It is also more error prone; notice the copy-paste error on the last line. Finally, it is less flexible; adding another column is more work than in the second approach.

To conclude; every time you notice you are repeating certain lines of code, try coming up with a more re-usable solution!

### Separation of concerns

A typical data science project includes many different tasks; from reading data files, to preparing and combining data and ultimately to training or applying a statistical model. It is possible to perform all these steps in a single Python file that runs from top to bottom. Python does not really enforce or recommend a certain structure of your code. However, putting everything into a single script may make that script very hard to maintain.

It is a good idea to separate different concerns of your project into different functions, classes or scripts (modules). Some tips:

- Split different tasks (ex. read files, clean data, engineer features, build a model) into separate modules and / or classes.
- Split different steps (ex. fill missings, convert data types) into separate functions
- Add (at least) a single-line docstring to all your classes / methods / functions summarizing the concern the object is supposed to address.
- Is it hard to summarize the functionality in a single line? Reconsider whether your separation of concerns is fine grained enough. 


### Simple over complex

### Explicit over implicit

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


## Industrialisation

### Use version control (git)

Make sure that the essential parts of your code are always under version control. Git is the most common tool for implementing version control; it allows you to track all changes to your source code (or any text file). Note that changes to binary files are not tracked effectively, so try to avoid binary files whenever possible.



### Logging beats printing

Incorporating logging into your code really helps others understand what is going on when running your code. Logging has numerous advantages over simply printing output to the screen:

1. Logging allows the user to determine where the logs go to, printing only goes to the terminal.
2. Logging implements different levels (ex. `ERROR`, `WARNING`, `INFO`, `DEBUG`), printing does not allow such differentiation.
3. Logs are formatted in a nice and consistent manner.

It is easy to incorporate logging into your code:

```python
# Import the logging module into your codebase
import logging

# Create a logger instance
# Note: __name__ automatically refers to the namespace of your module
logger = logging.getLogger(__name__)

# Log an informative message
logger.info("This is an informative message.")

# Log a user-defined error
logger.error("This is an error without a Python exception.")
# Probably want to raise an exception here...

# Log a Python exception including a stack trace
x = "nonnumeric"
try:
	x = float(x)
except ValueError:
	logger.exception(f"Cannot convert x to float: value {x} is not numeric.")
	raise
```

### Packaging

### Testing, testing

The `pytest` package allows you to implement unit tests for your code. Unit tests are really important for the maintainability of your code; tests guard against unexpected or unwanted changes in functionality.

Some tips for creating unit tests:

- Test the main functionality of your code, including common exceptions.
- If your code has configuration options, create a test scenario for each option.
- Use only one assert statement per test.
- Give descriptive names to your test functions, ex: `test_value_error_on_non_numeric()`.
- Data sets can be supplied either as a fixture or as a file.
- Use parametrization if you need to run the same test on different inputs.

