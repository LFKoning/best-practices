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

The best way to distribute a python project is to wrap it up as a package. A package includes your code, but also its dependencies on other python packages.

Tip: the `cookiecutter` package can help you setup your package very easily.

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

## Testing, testing

The `pytest` package allows you to implement unit tests for your code. Unit tests are really important for the maintainability of your code; tests guard against unexpected or unwanted changes in functionality.

Some tips for creating unit tests:

- Test the main functionality of your code, including common exceptions.
- If your code has configuration options, create a test scenario for each option.
- Use only one assert statement per test.
- Give descriptive names to your test functions, ex: `test_value_error_on_non_numeric()`.
- Data sets can be supplied either as a fixture or as a file.
- Use parametrization if you need to run the same test on different inputs.
