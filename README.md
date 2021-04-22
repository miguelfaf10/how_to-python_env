# how_to-python_env

Based on : 
1- https://towardsdatascience.com/how-to-setup-an-awesome-python-environment-for-data-science-or-anything-else-35d358cc95d5
2- https://blog.peiyingchi.com/2020/02/27/dev-setup-for-python-ds-project/


## Python version/virtualenvs/dependencies tools

 - Use pyenv [pyenv](https://github.com/pyenv/pyenv/) to manage Python versions and Poetry 
 - Use [poetry](https://python-poetry.org/) for virtualenvs/dependencies.
 - Use Jupyter notebooks

All packages installations done with `pip` but not sure if it would be better to use something else...


## Create a python installation
```sh
# Install Python version 3.x.x
pyenv install 3.x.x
```


## Global python tools virtual environment

Tools here include: poetry, pre-commit...

Install these in a python `tools` virtualenv and make it available by defining it as global.

### Instalation of Poetry

```sh
# Create a virtual environment called tools that is based on Python 3.x.x
pyenv virtualenv 3.x.x tools 
# Install poetry into the tools virtual env
pyenv activate tools
pip install poetry 
# Check installed poetry version
poetry --version
# Leave the virtual env 
pyenv deactivate 
# This does not work yet 
poetry --version
# Add your tools virtual env to the globally available ones
pyenv global 3.x.x tools
# Now this works and you can start using poetry
poetry --version
```

### Installation of Pre-commit
Install it locally in tools and not inside Poetry project, as pre-commit is only used locally and not on a CI/CD server. 

```sh
# Install pre-commit into the tools virtual env
pyenv activate tools
pip install pre-commit 
# Leave the virtual env 
pyenv deactivate
# As we have already added the tool venv, it will work directly
pre-commit --version
```

## Global python jupyter virtual environment

Install Jupyter notebooks in a python `jupyter`virtualenv and also make it globally available.

Also install and configure `nb-extensions`

```sh
# Create a virtual environment called tools that is based on Python 3.x.x
pyenv virtualenv 3.x.x jupyter
pip install 

pip install jupyter jupyter_nbextensions_configurator jupyter_contrib_nbextensions  # jupyter related
jupyter contrib nbextension install --user
jupyter nbextensions_configurator enable --user
```

## Creating a new project with poetry

1. Create a project directory (e.g. 'myproject') and `cd` into it. 
2. `git init` 
3. Fixate the Python version for that project with the `pyenv local` command (e.g. `pyenv local 3.8.6`). This creates a `.python-version` file that you can put under source control. Within the `myproject` directory tree, `python` will now be automatically resolved to the specified version. Your system Python (in fact, any other Python versions you might have installed) remain untouched. 
4. Create a new poetry project (`poetry init`). This creates a `pyproject.toml` which contains project metadata + dependencies and can also be checked into git. 
5. Add dependencies with `poetry add`. Here, you could for instance add pandas (`poetry add pandas`).


## Adding development packages

### Jupyter support

Add local ipykernel package to poetry `.venv` and add it to the kernels available in jupyter

```sh
poetry add --dev ipykernel
poetry run ipython kernel install --user --name=<myproject>
```

### Consistent Formatting and Readability with: Black

Use Black for formating codebase

```sh
## We add black as a development dependency with --dev as we don't
# need it when it comes to production
poetry add --dev black=19.3b0
# Assume we are inside the current toplevel dsexample folder
poetry run black .
```

### Type-Correctness with: MyPy

Add mypy and type-checking to your project using poetry is as easy as adding black

```sh
# We add mypy as a development dependency with --dev as we don't
# need it when it comes to production
poetry add --dev mypy
# Assume we are inside the current toplevel dsexample folder
poetry run mypy .
```

Running mypy might create a lot of errors. For sure you can configure it to only warn you about the things you are interested in. You do that by adding a mypy.ini file to your project and refer you and future me to the documentation for more details.
Automate the Automation

### Automatize commit checks with: Pre-commit

To use it, you first need to add a config file called .pre-commit-config.yaml to the top-level folder of your project. In that file, you configure all the hooks that should run. With mypy and black, this file looks like

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

Last, you must tell pre-commit to set up the hooks by executing

```sh
# I assume your are in the top level folder
pre-commit install
```

Now, the hooks will run on every commit. The black hook will not only check for formatting issues but also format the files accordingly. Whenever you add a new hook, so also at the very beginning, it is recommended to manually run pre-commit on all files as is it only touches the files that have been changed since the last commit

```sh
pre-commit run --all-files
```

And that’s it, we have automated the automation!
