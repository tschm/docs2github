# Avoiding dependency hell

Dependency hell in its mildest form
can already be faced when compiling LaTeX documents
locally with pdflatex. A document may compile
on your personal machine, yet it may fail to do so on
the computer of your supervisor. The reason is that
you have a mismatch concerning the installed packages.

Such problems can be quite subtle and hard to catch
when developing software. Your supervisor may operate
using a different yet seemingly compatible version
of a package you rely on.

Any Python code you write assumes a certain environment
with some packages. The situation is similar when you code in R.
There are different strategies to address the issue.
We recommend to use **virtual environments** to manage your dependencies.

If you deploy entire applications you may want to use
containers, but we ask you to be patient. We will discuss
containers in a later chapter.

## Virtual environments

Virtual environments are a way to isolate the Python
packages used for a specific project. In particular,
virtual environments provide a simple path to ensure
you and your supervisor use the same environment.

An elegant way to deal with virtual environments is through
Poetry. Poetry is a tool for dependency management and packaging
in Python. It allows you to declare the libraries your project
depends on. It will manage (install/update) them for you.

## Poetry

We recommend to install poetry via

```bash
curl -sSL https://install.python-poetry.org | python3 -
```

You may want to confirm the installation by running

```bash
poetry --version
```

We recommend to start new projects on GitHub
using a template

scientific python

pyproject.toml
poetry.lock

## Poetry and pre-commit

## Pytest

## Further reading
