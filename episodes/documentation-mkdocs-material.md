---
title: "Documentation with MkDocs"
teaching: 10
exercises: 10
---

:::::::::::::::::::::::::::::::::::::: questions

- How do I document my project?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Learn how to set up documentation using Material for MkDocs

::::::::::::::::::::::::::::::::::::::::::::::::

In this lesson, we'll outline creating a documentation webpage
using the [MkDocs](https://www.mkdocs.org/) framework
with the [Material](https://squidfunk.github.io/mkdocs-material/) theme.

You will:

- add the necessary dependencies to your `pyproject.toml` file,
- set up a basic documentation directory,
- create a configuration which comes with a table of contents by default,
- start a preview server,
- add a page which includes a code listing for your `rescale` function.

## Dependencies

Start by installing the `material-mkdocs` package into you virtual environment.

Add this to `pyproject.toml`:

```toml
[project.optional-dependencies]
...
docs = [
  "mkdocs-material"
]
```

... then reinstall using `pip install --editable ".[docs]"`.

::::::::::::::::::::::::::::::::::::: callout

## `doc` or `docs`

The [python packaging standard](https://packaging.python.org/en/latest/specifications/core-metadata/#provides-extra-multiple-use)
for the name of this extra is `doc`, whereas `docs` is ~3x more popular.

::::::::::::::::::::::::::::::::::::::::::::::::

## Template

Create an empty site using:

```bash
mkdocs new .
```

This will create files and directories as follows:

```
.
├─ docs/
│  └─ index.md
└─ mkdocs.yml
```

- The `index.md` file will contain the content on the front page of your documentation website.
- The `mkdocs.yml` file is the configuration file for the documentation.

## Configuration

In the `mkdocs.yml` file, set the site name and add some additional lines to enable the theme:

```yaml
site_name: Example Package YOUR USERNAME HERE
site_url: https://<your github username>.github.io/example-package-YOUR-USERNAME-HERE
theme:
  name: material
```

::::::::::::::::::::::::::::::::::::: callout

## `site_url` is important

It is important to set the `site_url` because it's assumed to be set by a number of plugins.

It's set here to a [GitHub Pages](https://pages.github.com/) address –
you can set it to `https://<your github username>.github.io/example-package-YOUR-USERNAME-HERE`
or any other domain where you want to publish.

::::::::::::::::::::::::::::::::::::::::::::::::

## Preview

You can preview the site as you change it by running the "preview server":

```bash
mkdocs serve
```

## Update `index.md`

And add (a correct) `docs/index.md` yourself:

````md
<!--- docs/index.md -->

# Example Package YOUR USERNAME HERE

`example-package-YOUR-USERNAME-HERE` is a simple Python library that contains a single function for rescaling arrays.

## Installation

You can install the package by calling:

```bash
pip install git+https://github.com/<your github username>/example-package-YOUR-USERNAME-HERE
```

## Usage

```python
import numpy as np
from example_package_YOUR_USERNAME_HERE.rescale import rescale

# rescales over 0 to 1
rescale(np.linspace(0, 100, 5))
```
````

::::::::::::::::::::::::::::::::::::: callout

## `README.md` vs `index.md`

Often, similar information will be contained in the repository README
and the index page of the documentation – installation instructions,
basic usage, licensing etc., and so it's common to want to include
(parts of) the README in the index page.

Sphinx has built-in tools to allow you to include parts of another markdown file
directly, but MkDocs doesn't.

We'd recommend writing the `index.md` and `README.md` files separately,
so that you can vary the information and instructions you present
for the particular audience.

For instance, someone viewing the repository can be expected to know where to download
the source code from, whereas someone viewing the documentation website might not.

::::::::::::::::::::::::::::::::::::::::::::::::

## Add Code Reference

We'll add a new page to the documentation with the docstrings from the package.

- Add the `mkdocstrings` plugin to `mkdocs.yml`:

  ```yaml
  # mkdocs.yml
  plugins:
    - mkdocstrings
  ```

- Include the `mkdocstrings[python]` package in the `docs` dependencies of the pyprojects.toml:

  ```toml
  # pyproject.toml
  [project.optional-dependencies]
  docs = [
    "mkdocs-material",
    "mkdocstrings[python]"
  ]
  ```

- Add a new page `docs/ref.md` with the content:

  ```md
  # Code Reference

  ::: example_package_YOUR_USERNAME_HERE.rescale
  ```

- Stop the preview server using <kbd>Ctrl</kbd>-<kbd>C</kbd>
- Reinstall using `pip install --editable ".[docs]"` since we added a new dependency
- Reload the documentation preview using `mkdocs serve`

MkDocs automatically adds the additional page to your documentation.

## Publish to GitHub Pages

To publish the documentation to GitHub pages, run:

```bash
mkdocs gh-deploy
```

The documentation will be made available at the URL
`https://<your github username>.github.io/example-package-YOUR-USERNAME-HERE`

Once this is deployed, you can add an additional URL to the `pyproject.toml` file,
which will be included in the package metadata and linked to on PyPI.

```toml
# pyproject.toml

[project.urls]
Homepage = "https://<your github username>.github.io/example-package-YOUR-USERNAME-HERE"
```

## Read The Docs

If you want to use https://readthedocs.org to build your docs, you'll need to add a `.readthedocs.yml` file. Find details at [https://docs.readthedocs.io/en/stable/config-file](https://docs.readthedocs.io/en/stable/config-file).

## Challenges

::::::::::::::::::::::::::::::::::::: challenge

## Adding a page

Try adding another page.

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: callout

## Further reading

- [https://diataxis.fr/](https://diataxis.fr/) outlines an excellent framework for planning documentation.
- [https://squidfunk.github.io/mkdocs-material/](https://squidfunk.github.io/mkdocs-material/) outlines all the options and many of the plugins available with Material for MkDocs, including syntax highlighting, and making comprehensive code listings for projects with many files.

::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::: keypoints

- MkDocs is great for documentation

::::::::::::::::::::::::::::::::::::::::::::::::
