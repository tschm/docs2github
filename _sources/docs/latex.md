# Compiling LaTeX documents

We intend to compile a '.tex' file after each commit and make
the resulting '.pdf' document available on a draft branch.
Linking from your README file to this branch will
simplify sharing your work with others. Your '.pdf' file
is not some historic snapshot of your work but a living document
updated with each commit. Interested readers do not have
to clone your repository and compile the document locally.
At the same time you are warned if your changes break
the compilation.

## Install [tectonic](https://tectonic-typesetting.github.io/en-US/)

We recommend the use of tectonic as an alternative to pdflatex.
It is a modern TeX engine that is easy to install and most suitable
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

```make
.DEFAULT_GOAL := compile

.PHONY: compile
compile: ## Compile the '*.tex' file
  @mkdir -p publish
  @./tectonic hello.tex --outdir publish

.PHONY: install
install: ## install tectonic
  @curl --proto '=https' --tlsv1.2 -fsSL https://drop-sh.fullyjustified.net | sh
```

For reasons not obvious yet, we publish the compiled 'hello.pdf' file
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
just a storage place for files. It is a platform that allows us to
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
into the repository. It consists of three steps:

* Check out the repository.
* Compile the latex file via the 'make' route.
* Deploy the 'publish' folder to the draft branch.

For the final step we are using a 3rd party action that has been made
available by James Ives.

## Revisit README

The README file is the main entry point for any repository.

```markdown
# [Hello](https://github.com/USERNMAE/hello/blob/draft/Hello.pdf)

## Abstract

This paper is about ...
```

This will create a Headline with a link to your pdf file
on the draft branch.

## Use a centralized workflow

We have created a workflow that simplifies the workflow above
even further. It is deployed on in the [central repository]
(<https://github.com/cvxgrp/.github>) by Stanford's cvx group.

```yaml
name: Build LaTeX document

on:
  push

jobs:
  build_paper:
    runs-on: ubuntu-latest

    steps:
      - uses: cvxgrp/.github/actions/latex@main
        with:
          paper: 'hello.tex'
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

This workflow performs all the steps introduced above.

## Summary

We are compiling LaTeX documents with tectonic using GitHub actions.
The resulting pdf file is published on a draft branch.

We also use this task as a warm-up exercise for the more refined
workflows that will be introduced in the next sections. However,
the fundamental idea remains the same. Local changes are pushed
into a repository. GitHub actions respond to these events and
perform tasks that are described in workflow files.

Those tasks can build software, run tests, deploy applications,
construct docker images, and much more.
We will see examples for all of these in the following sections.

## Further reading

We have touched [tectonic](https://tectonic-typesetting.github.io/en-US/)
and have successfully applied it for various repositories at Stanford
University. In particular, we point the reader to the lecture notes by
[Michael Howes](https://github.com/Michael-Howes/first-year-stats-notes).
