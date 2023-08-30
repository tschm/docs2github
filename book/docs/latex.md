# Compiling LaTeX documents

We intend to compile a *.tex file after each commit and make
the resulting '*.pdf' document available on a draft branch.

It would be a bad idea to keep a large frequently changing
binary file on the main branch of any repository.

## Install [tectonic](https://tectonic-typesetting.github.io/en-US/)

We recommend the use of tectonic as an alternative to pdflatex.
It is a modern TeX engine that is easy to install and use and most suitable
for our purposes.

```bash
curl --proto '=https' --tlsv1.2 -fsSL https://drop-sh.fullyjustified.net | sh
````

This installation will later be performed on the fly in an automated process.

## Compile the latex file

The file hello.tex contains the following content

```latex
\documentclass{article}
\begin{document}
Hello World!
\end{document}
```

Running now on the command line

```bash
tectonic f.tex
```

should produce a file 'f.pdf'

## Introducing a Makefile

Makefiles are a convenient way to automate tasks. We will use them
for the compilation of our latex files and in particular
to perform the clumsy installation of tectonic on the fly.

Our goal is to create two commands

```bash
make install
```

and

```bash
make compile
```

We achieve this with the following Makefile

```makefile
.DEFAULT_GOAL := compile

.PHONY: compile
compile: ## Compile the '*.tex' file
  @mkdir -p publish
 @./tectonic f.tex --outdir publish

.PHONY: install
install: ## install tectonic
 @curl --proto '=https' --tlsv1.2 -fsSL https://drop-sh.fullyjustified.net | sh
```

## Ignore the resulting pdf file

As mentioned above it is a bad idea to keep any binary
file on the main branch of a repository. We therefore
add

```gitignore
publish
tectonic
```

to the local .gitignore file of the repository.

## Introduce a workflow

The Makefile gave us a convenient way to compile the latex file
even on a machine without any LaTeX installation. Our next goal
is to perform this compilation automatically after each commit.

There's a fundamental lesson to learn here. GitHub is no longer
a mere repository of files. It is a platform that allows us to
respond to events, to perform tasks, and to automate workflows.

We create the file '.github/workflows/latex.yml'

```yaml
name: Build LaTeX document with make

on:
  push

jobs:
  build_paper:
    runs-on: ubuntu-latest

    steps:
      - name: Set up Git repository
        uses: actions/checkout@v3

      - name: Compile LaTeX document
        shell: bash
        run: |
          make install
          make compile

      - name: GitHub Pages action
        uses: JamesIves/github-pages-deploy-action@v4.4.3
        with:
          # The branch the action should deploy to.
          branch: draft
          # The folder the action should deploy.
          folder: publish
```
