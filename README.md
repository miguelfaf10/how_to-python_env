# Setting Up a Python Project Environment

This guide is based on insights from:
1. [How to Setup an Awesome Python Environment for Data Science](https://towardsdatascience.com/how-to-setup-an-awesome-python-environment-for-data-science-or-anything-else-35d358cc95d5)
2. [Dev Setup for Python DS Project](https://blog.peiyingchi.com/2020/02/27/dev-setup-for-python-ds-project/)

## Overview
This document outlines the steps to set up a robust Python environment, suitable for data science or other Python-based projects. It includes instructions for managing Python versions, creating virtual environments, managing dependencies, and setting up Jupyter notebooks.

### Tools
- **Python Version Management**: Use [pyenv](https://github.com/pyenv/pyenv/) for managing different Python versions.
- **Virtual Environments and Dependencies**: Utilize [Poetry](https://python-poetry.org/) for handling virtual environments and dependencies.
- **Interactive Development**: Employ Jupyter notebooks for an interactive development experience.
- **Package Management**: Packages are typically installed using `pip`. Alternatives or best practices, if any, will be discussed later.

## Initial Setup

### Installing pyenv and pyenv-virtualenv
1. Install `pyenv`:
    ```sh
    curl https://pyenv.run | bash
    ```
2. Install `pyenv-virtualenv` plugin:
    ```sh
    git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv
    ```

### Creating and Managing Python Environments
1. Install a specific Python version:
    ```sh
    pyenv install -l
    pyenv install 3.x.x
    pyenv global 3.x.x
    pyenv versions
    ```
2. Create a project-specific virtual environment:
    ```sh
    pyenv virtualenv 3.x.x <my-env>
    pyenv local <my-env>
    ```

## Jupyter Notebook Integration
1. Within your virtual environment, install Jupyter and other required packages:
    ```sh
    pip install jupyter --debug
    pip install ipykernel
    ipython kernel install --user --name=<my_env>
    ```
2. Launch Jupyter Notebook:
    ```sh
    jupyter notebook
    ```

## Setting Up Global Python Tools Environment
This section covers the installation of global tools like Poetry and pre-commit in a separate `tools` virtual environment.

### Installing Poetry
1. Create and activate a `tools` virtual environment:
    ```sh
    pyenv virtualenv 3.x.x tools
    pyenv activate tools
    pip install poetry
    pyenv deactivate
    ```
2. Make the `tools` environment globally available:
    ```sh
    pyenv global 3.x.x tools
    poetry --version
    ```

### Installing Pre-commit
Install pre-commit in the `tools` environment. It is used locally and not required in CI/CD pipelines.
```sh
pyenv activate tools
pip install pre-commit
pyenv deactivate
pre-commit --version
```

## Creating a Jupyter-Focused Environment
Set up a dedicated `jupyter` virtual environment for Jupyter notebooks and extensions.
```sh
pyenv virtualenv 3.x.x jupyter
pip install jupyter jupyter_nbextensions_configurator jupyter_contrib_nbextensions
jupyter contrib nbextension install --user
jupyter nbextensions_configurator enable --user
```

## Starting a New Project with Poetry
1. Create a project directory, initialize Git, and set the Python version:
    ```sh
    mkdir myproject
    cd myproject
    git init
    pyenv local 3.8.6
    ```
2. Initialize the project with Poetry:
    ```sh
    poetry init
    ```
3. Add dependencies using Poetry, e.g., `poetry add pandas`.

## Development Environment Enhancements

### Integrating Jupyter
Add Jupyter support in the project's virtual environment:
```sh
poetry add --dev ipykernel
poetry run ipython kernel install --user --name=<myproject>
```

### Code Formatting with Black
For consistent code formatting, add Black as a development dependency:
```sh
poetry add --dev black=19.3b0
poetry run black .
```

### Type Checking with MyPy
Implement type checking with MyPy:
```sh
poetry add --dev mypy
poetry run mypy .
```
Customize MyPy behavior with a `mypy.ini` file, referring to the [official documentation](https://mypy.readthedocs.io/en/stable/config_file.html) for more details.

### Automating with Pre-commit
1. Create a `.pre-commit-config.yaml` file at the project root and configure hooks for MyPy and Black:
    ```yaml


    repos:
    -   repo: https://github.com/ambv/black
        rev: 19.3b0
        hooks:
        - id: black
          language_version: python3.7
    -   repo: https://github.com/pre-commit/mirrors-mypy
        rev: v0.740
        hooks:
        - id: mypy
    ```
2. Install and run pre-commit hooks:
    ```sh
    pre-commit install
    pre-commit run --all-files
    ```

By following these instructions, you will have set up a comprehensive and robust Python environment for your project. This setup ensures version consistency, enhances code quality, and automates various checks to streamline your development workflow.
