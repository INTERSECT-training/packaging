---
title: "Documentation"
teaching: 0
exercises: 0
questions:
- "How do I document my project?"
objectives:
- "Learn how to set up documentation"
keypoints:
- "Sphinx is used for documentation"
---

Documentation used to require learning RestructureText (sometimes referred to as
ReST / RST), but today we have great choices for documentation in markdown, the
same format used by GitHub, Wikipedia, and others.  You should select one of the
two major documentation toolchains, sphinx or mkdocs. We will select the older
Sphinx for this tutorial, and use the modern MyST plugin to get markdown
support.

## Basic template

We'll start with the built-in template. Start by creating a `docs/` directory
within your project (i.e. next to `src/`).

You _could_ use sphinx-quickstart to set up a basic template if you'd like.

```bash
pipx run --spec sphinx sphinx-quickstart --no-makefile --no-batchfile --ext-autodoc --ext-intersphinx --extensions myst_parser --suffix .md docs
```

But this will put Restructured Text into the `index.md` file, and doesn't really generate that much for you. You can instead add the `docs/conf.py` file yourself:

```python
import importlib.metadata

project = "<package>"
copyright = "2023, Your Name"
author = "Your Name"
version = release = importlib.metadata.version("project-name")

extensions = [
    "myst_parser",
    "sphinx.ext.autodoc",
    "sphinx.ext.intersphinx",
    "sphinx_copybutton",
]

exclude_patterns = [
    "_build",
    "Thumbs.db",
    ".DS_Store",
    "**.ipynb_checkpoints",
    ".env",
    ".venv",
]

source_suffix = [".md", ".rst"]

html_theme = "furo"

intersphinx_mapping = {
    "python": ("https://docs.python.org/3", None),
}
```

Compared to what would have been generated, we are using `furo` for the theme
(used by the PyPA and related to the theme being worked on for CPython 3.13's
docs). We also are including the version number, both possible file extensions,
some options for myst, and better exclude patterns. And less extra comments and
unnecessary stuff.

And add (a correct) `docs/index.md` yourself:

````md
# repo-review

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

Even if you used it, the generation won't add a `docs` extra (or
`docs/requirements.txt`, if you prefer not making a public extra) for you, so
you'll need to add this yourself to `pyproject.toml`:

```toml
[project.optional-dependencies]
docs = [
  "furo",
  "myst_parser >=0.13",
  "sphinx >=4.0",
  "sphinx-copybutton",
]

```

And add a session to your `noxfile.py` to generate docs:

```python
@nox.session(reuse_venv=True)
def docs(session: nox.Session) -> None:
    """
    Build the docs. Pass "--serve" to serve.
    """

    parser = argparse.ArgumentParser()
    parser.add_argument("--serve", action="store_true", help="Serve after building")
    args, posargs = parser.parse_known_args(session.posargs)

    session.install(".[docs]")
    session.chdir("docs")

    session.run(
        "sphinx-build",
        "-b",
        "html",
        ".",
        f"_build/html",
        *posargs,
    )

    if args.serve:
        session.log("Launching docs at http://localhost:8000/ - use Ctrl-C to quit")
        session.run("python", "-m", "http.server", "8000", "-d", "_build/html")
```

And you now have working docs that you can generate and view cross platform with `nox -s docs -- --serve`!

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

## API docs

To build API docs, you need to add the following nox job:

```python
@nox.session
def build_api_docs(session: nox.Session) -> None:
    """
    Build (regenerate) API docs.
    """
    session.install("sphinx")
    session.chdir("docs")
    session.run(
        "sphinx-apidoc",
        "-o",
        "api/",
        "--module-first",
        "--no-toc",
        "--force",
        "../src/<package>",
    )
```

And you'll need this added to your `docs/index.md`:

````md
```{toctree}
:maxdepth: 2
:hidden:
:caption: API

api/repo_review
```
````

Note that your docstrings are still parsed as Restructured Text.

## Readme in docs

If you want to include your readme in your docs, you can add something like this:

````md
```{include} ../README.md
:start-after: <!-- SPHINX-START -->
```
````

And you use `<!-- SPHINX-START -->` to mark where you want your docs part of
your readme to start (generally after the title and badges).

{% include links.md %}
