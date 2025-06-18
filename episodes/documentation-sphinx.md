---
title: "Documentation with Sphinx"
teaching: 10
exercises: 10
---

:::::::::::::::::::::::::::::::::::::: questions 

- How do I document my project?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Learn how to set up documentation

::::::::::::::::::::::::::::::::::::::::::::::::



In this lesson, we'll outline creating a documentation webpage using Sphinx.

You will:
- set up a basic documentation directory,
- create a configuration including the modern MyST plugin to get markdown support,
- start a preview server,
- create a table of contents,
- add a page,
- include part of your README.md,
- choose a theme.

## Configuration

We'll start with the built-in template. Start by creating a `docs/` directory
within your project (i.e. next to `src/`).

::::::::::::::::::::::::::::::::::::: spoiler 

## Why not Sphinx-Quickstart?

You _could_ use sphinx-quickstart to set up a basic template if you'd like.

```bash
pipx run --spec sphinx sphinx-quickstart --no-makefile --no-batchfile --ext-autodoc --ext-intersphinx --extensions myst_parser --suffix .md docs
```

But this will put Restructured Text into the `index.md` file, and doesn't really generate that much for you. You can instead add the `docs/conf.py` file yourself, which is what we'll do here.

::::::::::::::::::::::::::::::::::::::::::::::::


You first file is a configuration file, `docs/conf.py`:

```python
# docs/conf.py

project = "example"
extensions = ["myst_parser"]
source_suffix = [".rst", ".md"]
```

## Index

And add (a correct) `docs/index.md` yourself:

````md
# package

```{toctree}
:maxdepth: 2
:hidden:

```

## Indices and tables

* {ref}`genindex`
* {ref}`modindex`
* {ref}`search`
````

As you add new pages, you will list them in the toctree above.

## Dependencies

Add the docs dependencies to `pyproject.toml`:

```toml
[project.optional-dependencies]
docs = [
  "myst_parser >=0.13",
  "sphinx >=4.0",
]
```

## Preview Server

You can install these dependencies using `pip install --editable ".[docs]".

To run the Sphinx preview server, you can install `sphinx-autobuild`, then run:

```bash
sphinx-autobuild --open-browser -b html "./docs" "_build/html"
```

This will rebuild if you change files, as well.

## Nox session

You can set up a task runner like nox to run this for you:

```python
@nox.session(reuse_venv=True)
def docs(session: nox.Session) -> None:
    """
    Build the docs. Use "--non-interactive" to avoid serving.
    """

    serve = session.interactive
    extra_installs = ["sphinx-autobuild"] if serve else []
    session.install("-e.[docs]", *extra_installs)

    session.chdir("docs")

    shared_args = (
        "-n",  # nitpicky mode
        "-T",  # full tracebacks
        "-b=html",
        ".",
        f"_build/html",
        *session.posargs,
    )

    if serve:
        session.run("sphinx-autobuild", "--open-browser", *shared_args)
    else:
        session.run("sphinx-build", "--keep-going", *shared_args)
```

And you now have working docs that you can generate and view cross platform with `nox -s docs`!

## Read the Docs

If you want to use https://readthedocs.org to build your docs, you'll also want the following `.readthedocs.yml`:

```yaml
version: 2
build:
  os: "ubuntu-22.04"
  tools:
    python: "3.11"
sphinx:
  configuration: docs/conf.py
python:
  install:
    - method: pip
      path: .
      extra_requirements:
        - docs
```

::::::::::::::::::::::::::::::::::::: challenge

## Adding a page

Try adding a page. Remember to update your `index.md` table of contents.

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: challenge

## Readme in docs

If you want to include your readme in your docs, you can add something like this:

````md
```{include} ../README.md
:start-after: <!-- SPHINX-START -->
```
````

And you use `<!-- SPHINX-START -->` to mark where you want your docs part of
your `README.md` to start (generally after the title and badges).
```markdown
# Example Package YOUR USERNAME HERE

<!-- SPHINX-START -->

`example-package-YOUR-USERNAME-HERE` is a simple Python library that contains a single function for rescaling arrays.
```

::::::::::::::::::::::::::::::::::::::::::::::::


::::::::::::::::::::::::::::::::::::: checklist

## Selecting a nicer theme

A really nice theme, used by PyPA projects like pip and pipx, is `furo`. To use it, add this line to your `conf.py`:

```python
html_theme = "furo"
```

And add `"furo"` to your `docs` extra in your `pyproject.toml`.

::::::::::::::::::::::::::::::::::::::::::::::::


::::::::::::::::::::::::::::::::::::: callout

## Further reading

To see a more complete example, read [Scientific-Python's docs guide](https://learn.scientific-python.org/development/guides/docs/).

::::::::::::::::::::::::::::::::::::::::::::::::





:::::::::::::::::::::::::::::::::::::: keypoints 

- Sphinx is great for documentation

::::::::::::::::::::::::::::::::::::::::::::::::
