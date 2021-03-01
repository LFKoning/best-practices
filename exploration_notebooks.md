# Exploration Notebooks

## Single-purpose notebooks

Try to use separate notebooks for each task in your project. For example, use a separate notebook for each dataset you are going to explore. Perhaps also use seperate notebooks for every model type you are trying. Make another notebook for comparing the final models to each other et cetera.

Using separate notebooks for different tasks ensures that notebooks do not become too lengthy and complex.

Example:

```text
+-- notebooks/
    |
    +-- exploration/
    |   +-- transaction_data.ipynb
    |   +-- interest_rates.ipynb
    |
    +-- models/
        +-- linear_model.ipynb
        +-- arima_model.ipynb
```

## Descriptive names

Use descriptive names for your notebooks; do not just pile up `Untitled.ipynb` files. With separate notebooks for different tasks in your project, coming up with a good name should not be too hard.

## Summary on top

Use a descriptive Markdown cell on top of your notebook to briefly summarize the purpose and structure of the notebook.

Example:

```markdown
# Linear Model

This notebook uses a simple linear model to predict a time-series of transaction data based on the following features:

- Interest rates: benchmark interest rates
- National holidays: obtained from the `workalendar` package.
- Fourier series: Seasonality is modelled through fourier series.

Feature selection is done using lasso regularization.

Uses `TimeSeriesSplit` from `scikit-learn` to perform cross validation.
```

## Use sections

Use section headers to split up different subtasks in your notebook, for example loading the data, cleansing it, generating descriptive statistics and plots, et cetera.

## Clean up

Try to keep your notebooks as clean as possible; remove cells with code you no longer need or just used for testing. Look over your notebook code every now and then to check whether all the code is still usefull or not. If needed, make a separate section where you store code that is only used for testing or that is not / less relevant to the goal of the notebook.

## Import utility code (if available)

Moving (utility) code to an external file can really help keep your notebooks clean and readable. Methods for loading data or generating certain plots are good candidates to move to an external file or even better: an installable package.

For example, take a look at the following file structure:

```text
+-- notebooks/
    |
    +-- exploration/
        |
        +-- readers
        |    +-- __init__.py        # turns folder into importable module
        |    +-- transactions.py    # contains read_transactions function
        |
        +-- transaction_exploration.ipynb
        +-- plotting.py             # contains plotting functions
```

In the `transaction_exploration.ipynb` notebook you can easily import plotting functions from `plotting.py` like so:

```python
# Import the entire plotting module
import plotting

# Import a single plotting function
from plotting import correlation_plot
```

In similar vein you can import from modules. For example, to import the `read_transactions` function from `readers/transactions.py` just use:

```python
from readers.transactions import read_transactions
```

Note that the `__init__.py` in the `readers/` folder is required to make the folder an importable module!

Finally, you should be aware that code is imported only once by Python. So when you make changes to the `read_transacactions` function **after** importing it, those changes will **not** be directly available in your notebook!

One solution is to simply restart the kernel and re-import your module or function, but this is quite inefficient. Another solution is to use the `autoreload` magic like so:

```python
%load_ext autoreload
%autoreload 2
```

With `autoreload` your module is re-imported whenever changes to its code are detected. This ensures that you have the latest version available without having to restart te kernel!
