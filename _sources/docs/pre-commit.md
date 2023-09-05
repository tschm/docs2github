# Acting before committing

pre-commit hooks are a state of mind.
They act on your code before you push it to the repository.
We will meet them over and over again in this book.
The first toy examples illustrate how they work.

## Install pre-commit

pre-commit is one of those rare tools we recommend to make
available in your central Python environment.

Hence

```bash
pip install pre-commit
````

is an acceptable thing to do. We will soon discuss the
setup of Python environments in much greater detail.

## Create a pre-commit configuration file

pre-commit is configured via a file called `.pre-commit-config.yaml`.
We create this file in the root directory of our repository.

```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.4.0
    hooks:
      - id: end-of-file-fixer
      - id: trailing-whitespace

  - repo: https://github.com/igorshubovych/markdownlint-cli
    rev: v0.35.0
    hooks:
      - id: markdownlint-fix
```

In this file we have defined three hooks. The first two are
provided by the pre-commit project itself. The third one is
provided by Igor Shubovych and covers the linting of
markdown files, e.g. the 'README' file.

That's a typical setup. You will find a lot of useful hooks
from various sources. You could write your own hooks but
that's a topic for another day.

You could take inspiration from the
[pre-commit marketplace](https://pre-commit.com/hooks.html).
It helps to study large projects like the pandas
repository. They tend to have a very elaborate setup.

## Run pre-commit hooks locally

Of course, you should run the hooks locally before you push
your work. You can either install them via

```bash
pre-commit install
```

and then run repeated attempts to commit your code or you
could include a dedicated job in your Makefile for this, e.g.

```makefile
.DEFAULT_GOAL := compile

.PHONY: compile
compile: ## Compile the '*.tex' file
  @mkdir -p publish
  @./tectonic hello.tex --outdir publish

.PHONY: install
install: ## install tectonic
  @curl --proto '=https' --tlsv1.2 -fsSL https://drop-sh.fullyjustified.net | sh

.PHONY: pre-commit
pre-commit: ## run pre-commit hooks
  @pre-commit run --all-files
```

On the command line you can now do

```bash
make pre-commit
```

and it will run through all the hooks defined in the configuration file
if pre-commit is installed.

## Check your hooks

When working in teams you can be assured that not all members
will (manage to) run or install the hooks.
However, you can check on them within a workflow.

```yaml
name: pre-commit

on:
  pull_request:
  push:

jobs:
  pre-commit:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - uses: pre-commit/action@v3.0.0
```

This will run the pre-commit hook in post-commit mode.
In the best case, it will find nothing to do and confirms that
pre-commit has done its job.

## A public demo repository

We have created a public demo repository for you to play with.
See [Hydra](https://github.com/tschm/hydra) on GitHub.

Note the workflows, the makefile and the configuration file for
the pre-commit hooks.

## Further reading
