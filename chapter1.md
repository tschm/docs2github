# Compiling LaTeX documents 

Assume we are working on a '*.tex' in a small group.

We intend to compile the *.tex file after each commit and make
the resulting '*.pdf' document available on a draft branch.

In general it's a bad idea to keep large frequently changing
binary files on the main branch of any repository.

## Install tectonic

We recommend the use of tectonic

```bash
curl --proto '=https' --tlsv1.2 -fsSL https://drop-sh.fullyjustified.net | sh
````

tectonic is

## Compile the latex file

```bash
tectonic f.tex
```

Put the resulting f.pdf file into .gitignore

## Introduce a Makfile

The command to install tectonic was somewhat clumsy and for that reason we introduce

```make
.DEFAULT_GOAL := help

.PHONY: help
help:  ## Display this help screen
	@echo -e "\033[1mAvailable commands:\033[0m"
	@grep -E '^[a-z.A-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | awk 'BEGIN {FS = ":.*?## "}; {printf "  \033[36m%-18s\033[0m %s\n", $$1, $$2}' | sort

.PHONY: compile
compile: install ## Compile document
	@tectonic book.tex

.PHONY: clean
clean: ## clean the folder
	@git clean -d -X -f

.PHONY: install
install: ## install tectonic
	@curl --proto '=https' --tlsv1.2 -fsSL https://drop-sh.fullyjustified.net | sh
```

we can now perform

make compile

## Introduce a workflow

We want to automate the compilation such that the document is (re)compiled after each commit.
We create the file .github/workflows/latex.yml


