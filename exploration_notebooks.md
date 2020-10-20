# Exploration Notebooks


## Single-purpose notebooks

Try to use separate notebooks for every task in your project. For example, use a separate notebook for each dataset you are going to explore. Perhaps also use seperate notebooks for every model type you are trying. Make another notebook for comparing the final models to each other et cetera.

Using separate notebooks for different tasks ensures that notebooks do not become too lengthy and complex.

Example:
```
+-- notebooks/
    |
    +-- exploration/
    |    +-- transaction_data.ipynb
    |    +-- interest_rates.ipynb
    |
    +-- models/
        +-- linear_model.ipynb
        +-- arima_model.ipynb
```

## Descriptive names

Use descriptive names for your notebooks; do not just pile up `Untitled.ipynb` files. With separate notebooks for different tasks in your project, coming up with a good name should not be too hard.

## Description on top

Put a short descriptive (Markdown) cell on top of your notebook, briefly describing the goal and overall structure of the notebook.

Example:
```
# Linear Model

This notebook fits a simple linear model to predict a time-series of transaction data.
```

## Use sections

Use section headers to split up different subtasks in your notebook, for example loading the data, cleansing it, generating descriptive statistics and plots, et cetera.

## Finishing up

