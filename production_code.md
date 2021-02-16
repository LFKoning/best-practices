# Production Code

## Use version control (git)

Make sure that the essential parts of your code are always under version control. Git is the most common tool for implementing version control; it allows you to track all changes to your source code (or any text file). Note that changes to binary files are not tracked effectively, so try to *avoid binary files* whenever possible.

For a quick intro on how to use git see:

- <https://www.freecodecamp.org/news/what-is-git-and-how-to-use-it-c341b049ae61/>
- <https://www.codecademy.com/learn/learn-git>

Even if you are the only person working on a project, git helps you track changes and allows you to go back to a previous version of your work easily. In this scenario, it is OK to just work on a single branch (usually `master`) to keep track of your changes.

If you are collaborating with others on a project, it is good practice to create your own `feature branch` to keep track of your changes. This prevents your changes from interfering with those made by other data scientist. Once you `merge` your branch into the `master` branch you need to resolve any conflicts.

A `pull request` is a nice way to perform a merge; it allows you to add descriptive information on the functionality you have added. It also allows you to add reviewers; a reviewer can check the quality and functionality of your code before merging it.

To sum up:

- Always use version control when writing code.
- Use branches when collaborating with others on the same project.
- Use pull requests when merging your branch into the master branch.
- Use descriptive names for your commits and branches.

## Packaging

The best way to distribute a python project is to wrap it up as a package. Notable advantages of creating a package are:

1. A package provides a neat way to structure your project.
2. A package is installed and does not need to reside inside your working directory.
3. A package can include dependencies; other packages it relies on to function properly.
4. Different dependencies can be specified for development and production environments.

Now that the advantages of packages are clear, let's take a look at their most basic structure:

```text
src/
tests/
setup.py
```

The `cookiecutter` package can help you setup your package very easily, to install it type:

```bash
pip install cookiecutter
```

Once installed, you can quickly create the structure for your project using a template like so:

```bash
cookiecutter <template>
```

## Documentation

Provide proper documentation for your project; at least include a `README.md` in the main folder of your project describing:

- The goal of the project
- The most important design choices (ex. model type)
- The project's high level structure (ex. folder / module structure)
- How to install and use the code
- Links to relevant additional resources or references

The documentation should be written in Markdown, which is an easy to learn markup language. There are a few different dialects, but the basics are all the same. For a cheat sheet see:

<https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet>

## Logging beats printing

Incorporating logging into your code really helps others understand what is going on when running your code. Logging has numerous advantages over simply printing output to the terminal:

1. Logging allows the user to determine where the logs go to, printing only goes to the terminal.
2. Logging allows you to use different levels (ex. `ERROR`, `WARNING`, `INFO`, `DEBUG`), printing does not allow such differentiation.
3. Logs are formatted in a nice and consistent manner.

It is very easy to incorporate logging into your code, here are some examples:

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

On a more technical level, the logging process roughly works like this:

1. A logging function is called on a `Logger` (ex. `info`)
2. The `Logger` checks whether it is enabled for that level (ex. level is `DEBUG`).
3. If so, the `Logger` emits a `LogRecord` for the log message.
4. `Handler`s attached to the `Logger` pick up the `LogRecord`.
5. Each `Handler` checks whether it is enabled for the level of the `LogRecord`.
6. If so, the `Handler` formats the `LogRecord` using a `Formatter`.
7. Finally, the `Handler` prints the message or stores it to a file.

For a more detailed, graphical schema of this flow click here: [python logging flow](https://docs.python.org/3/_images/logging_flow.png)

A more elaborate example, that logs to a file in a custom format looks like this:

```python
# Create and configure the logger
logger = logging.getLogger(__name__)
logger.setLevel(logging.INFO)

# Create file handler and custom formatter
file_handler = logging.FileHandler("log_messages.csv")
file_formatter = logging.Formatter(
    fmt="%(asctime)s,%(levelname)s,%(pathname)s,%(lineno)d,%(name)s,%(message)s"
)

# Attach formatter to handler and handler to logger
file_handler.setFormatter(file_formatter)
logger.addHandler(file_handler)

# Log a test message
logger.info("Test logging message")
```

## Testing, testing

The `pytest` package allows you to implement unit tests for your code. Unit tests are really important for the maintainability of your code; tests guard against unexpected or unwanted changes in functionality.

Some tips for creating unit tests:

- Test the main functionality of your code, including common exceptions.
- If your code has configuration options, create a test scenario for each option.
- Use only one assert statement per test.
- Give descriptive names to your test functions, ex: `test_value_error_on_non_numeric()`.
- Data sets can be supplied either as a fixture or as a file.
- Use parametrization if you need to run the same test on different inputs.

For example, let's take this function:

```python
def diff_series(ts, lag=1):
    """Computes lagged difference of a time-series."""

    if not isinstance(ts, pd.Series):
        raise ValueError("Time series should be a pandas Series object.")

    if not ts.dtype in ["int64", "float64"]:
        raise ValueError(f"Time series must be numeric, got {ts.dtype} instead.")

    try:
        lag = int(lag)
    except ValueError:
        raise ValueError("Lag should be provided as an integer.")

    return ts.diff(lag)
```

And write some tests for it:

```python
import pytest


class TestDiffSeries:
    """Test class to group up all our diff_series tests."""

    sample_ts = pd.Series([1, 2, 4, 2, 3])

    def test_error_no_series(self):
        """Tests ValueError is raised when time-series is not numeric."""

        with pytest.raises(ValueError):
            diff_series(pd.Series(["1", "2", "3", "4"]))

    def test_error_no_int_lag(self):
        """Tests ValueError is raised when lag is not provided as int."""

        with pytest.raises(ValueError):
            diff_series(self.sample_ts, "A")

    @pytest.mark.parametrize(
        "lag, expected",
        [
            (1, pd.Series([np.nan, 1, 2, -2, 1])),
            (2, pd.Series([np.nan, np.nan, 3, 0, -1])),
            (-1, pd.Series([-1, -2, 2, -1, np.nan]))
        ],
        ids=["lag 1", "lag 2", "lead 1"]
    )
    def test_lags(self, lag, expected):
        """Tests different lags on self.sample_ts."""

        result = diff_series(self.sample_ts, lag)
        pd.testing.assert_series_equal(result, expected)
```

Maybe you can come up with a few more edge cases for properly testing this function? For example, what should happen when the time series is empty? Should the function return an empty series or should you throw an error or warning? What if the lag is larger than the length of the series?

When you are designing tests, you might also discover edge cases that you did not think of when designing your function. That is why writing tests is so useful!
