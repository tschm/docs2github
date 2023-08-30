# Compiling LaTeX documents

We intend to compile a *.tex file after each commit and make
the resulting '*.pdf' document available on a draft branch.

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
tectonic hello.tex
```

should produce a file 'hello.pdf'

## Introduce a Makefile

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
  @./tectonic hello.tex --outdir publish

.PHONY: install
install: ## install tectonic
  @curl --proto '=https' --tlsv1.2 -fsSL https://drop-sh.fullyjustified.net | sh
```

For reasons not obvious yet we publish the compiled pdf file
in a subdirectory 'publish' we also create on the fly.

## Ignore the resulting pdf file

It is a bad idea to keep any binary
file on the main branch of a repository. We therefore
add

```gitignore
publish
tectonic
```

to the local '.gitignore' file of the repository.

## Introduce a workflow

The Makefile gave us a convenient tool to compile the latex file
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

This file describes a workflow that is performed after each push
into the repository. It consists of three steps.
First, we check out the repository. Second, we compile the latex file
using the Makefile we have created. In the final step we are using
a GitHub action to deploy the resulting 'publish' folder to the draft branch.
For this step we are using a 3rd party action that has been made
available by James Ives.

## Revisit 'README.md'

The 'README.md' file is the main entry point for any repository.

```markdown
# [Hello](https://github.com/USERNMAE/hello/blob/draft/Hello.pdf)

## Abstract

This paper is about ...
```

This will create a Headline with a link to your pdf file
on the draft branch.

## Summary

We are compiling LaTeX documents with tectonic using GitHub actions.
The resulting pdf file is published on a draft branch. You can now share
your repository with others. They will always find the latest version
readable version of your paper.
