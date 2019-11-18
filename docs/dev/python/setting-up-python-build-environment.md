# Setup the Python Build Environment

This document describes how to set up an environment for the development of the Glean Python bindings.

Instructions for installing a copy of the Glean Python bindings into your own environment for use in your project are TBD.

## Prerequisites

Glean requires Python 3.7 or later.

Make sure it is installed on your system and accesible on the `PATH` as `python3`.

### Setting up Rust

If you've already set up Rust for building Glean for Android or iOS, you already have everything you need and can skip this section.

Rust can be installed using `rustup`, with the following commands:

- `curl https://sh.rustup.rs -sSf | sh`
- `rustup update`

## Create a virtual environment

It is recommended to do all Python development inside a virtual environment to make sure there are no interactions with other Python libraries installed on your system.

You may want to manage your own virtual environment if, for example, you are developing a library that is using Glean and you want to install Glean into it.  If you are just developing Glean itself, however, Glean's `Makefile` will handle the creation and use of a virtual environment automatically (though the `Makefile` unfortunately does not work on Microsoft Windows).

The instructions below all have both the manual instructions and the `Makefile` shortcuts.

### Manual method

The following instructions use the basic [virtual environment functionality that comes in the Python standard library](https://docs.python.org/3/library/venv.html).
Other higher level tools exist to manage virtual environments, such as [pipenv](https://pipenv.kennethreitz.org/en/latest/) and [conda](https://docs.conda.io/en/latest/).
These will work just as well, but are not documented here.
Using an alternative tool would replace the instructions in this section only, but all other instructions below would remain the same.

To create a virtual environment, enter the following command, replacing `<venv>` with the path to the virtual environment you want to create.  You will need to do this only once.

```bash
  $ python3 -m venv <venv>
```

Then activate the environment. You will need to do this for each new shell session when you want to develop Glean.
The command to use depends on your shell environment.

| Platform | Shell           | Command to activate virtual environment |
|----------|-----------------|-----------------------------------------|
| POSIX    | bash/zsh        | `source <venv>/bin/activate`            |
|          | fish            | `. <venv>/bin/activate.fish`            |
|          | csh/tcsh        | `source <venv>bin/activate.csh`         |
|          | PowerShell Core | `<venv>/bin/Activate.ps1`               |
| Windows  | cmd.exe         | `<venv>\Scripts\activate.bat`            |
|          | PowerShell      | `<venv>\Scripts\Activate.ps1`            |

Lastly, install Glean's Python development dependencies into the virtual environment.

```bash
cd glean-core/python
pip install -r requirements_dev.txt
```

### Makefile method

```bash
  $ make python-setup
```

> *Note:* If you wish to change the location of the virtual environment that the `Makefile` uses, set the `GLEAN_PYENV` environment variable.

## Build the Python bindings

### Manual method

First, rebuild the Rust core if any Rust changes were made:

```bash
  $ cargo build  # If there were Rust changes
  $ cd glean-core/python
  $ python setup.py install
```

### Makefile method

This will implicitly setup the Python environment, rebuild the Rust core and then build the Python bindings.

```bash
  $ make build-python
```

## Running the Python unit tests

### Manual method

Make sure the Python bindings are built, then:

```bash
  $ cd glean-core/python
  $ py.test
```

### Makefile method

The following will run the Python unit tests using `py.test`:

```bash
  $ make test-python
```

You can send extra parameters to the `py.test` command by setting the `PYTEST_ARGS` variable:

```bash
  $ make test-python PYTEST_ARGS="-s --pdb"
```

## Linting, formatting and type checking

The Glean Python bindings use the following tools:

- Linting: [flake8](http://flake8.pycqa.org/en/latest/)
- Formatting: [black](https://black.readthedocs.io/en/stable/)
- Type-checking: [mypy](http://mypy-lang.org/)

### Manual method

```bash
  $ cd glean-core/python
  $ flake8 glean tests
  $ black glean tests
  $ mypy glean
```

### Makefile method

To just check the lints:

```bash
  $ make pythonlint
```

To reformat the Python files in-place:

```bash
  $ make pythonfmt
```

## Building the Python API docs

The Python API docs are built using [pdoc3](https://pdoc3.github.io/pdoc/).

### Manual method

```bash
  $ python -m pdoc --html glean --force -o build/docs/python
```

### Makefile method

```bash
  $ make python-docs
```