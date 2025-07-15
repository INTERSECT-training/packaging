---
title: "Metadata"
teaching: 10
exercises: 5
---

:::::::::::::::::::::::::::::::::::::: questions

- What metadata is important to add to your package?
- How do I add common functionality, like executable scripts?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Learn about the project table.

::::::::::::::::::::::::::::::::::::::::::::::::

In a previous lesson, we left the metadata in our `pyproject.toml` quite minimal, just:

- a name and
- a version.

There are quite a few other fields that can really help your package on PyPI, however.
We'll look at them, split into categories:

- Informational: author, description, URL, etc.
- Functional: requirements, tool configurations etc.

There's also a special `dynamic` field that lets you list values
that are going to come from some other source.

## Informational metadata

### Name

Required. `.`, `-`, and `_` are all equivalent characters, and may be normalized to `_`. Case is unimportant. This is the only field that must exist statically in this table.

```toml
name = "some_project"
```

### Version

Required. Many backends provide ways to read this from a file or from a version control system, so in those cases you would add `"version"` to the `dynamic` field and leave it off here.

```toml
version = "1.2.3"
version = "0.2.1b1"
```

### Description

A string with a short description of your project.

```toml
description = "This is a very short summary of a very cool project."
```

### Readme

The name of the readme. Most of the time this is `README.md` or `README.rst`, though there is a more complex mechanism if a user really desires to embed the readme into your `pyproject.toml` file (you don't).

```toml
readme = "README.md"
readme = "README.rst"
```

### Authors and maintainers

This is a list of authors (or maintainers) as (usually inline) tables. A TOML table is very much like a Python dict.

```toml
authors = [
    {name="Me Myself", email="email@mail.com"},
    {name="You Yourself", email="email2@mail.com"},
]
maintainers = [
    {name="It Itself", email="email3@mail.com"},
]
```

Note that TOML supports two ways to write tables and two ways to write arrays, so you might see this in a different form, but it should be recognizable.

### Keywords

A list of keywords for the project. This is mostly used to improve searchability.

```toml
keywords = ["example", "tutorial"]
```

### URLs

A set of links to help users find various things for your code; some common ones are `Homepage`, `Source Code`, `Documentation`, `Bug Tracker`, `Changelog`, `Discussions`, and `Chat`. It's a free-form name, though many common names get recognized and have nice icons on PyPI.

```toml
# Inline form
urls."Source Code" = "https://github.com/<your github username>/example-package-YOUR-USERNAME-HERE"

# Sectional form
[project.urls]
"Source Code" = "https://github.com/<your github username>/example-package-YOUR-USERNAME-HERE"
```

### Classifiers

This is a collection of
["classifiers"](https://peps.python.org/pep-0301/#distutils-trove-classification).
You select the classifiers that match your projects from <https://pypi.org/classifiers/>.
Usually, this includes a "Development Status" to tell users how stable you think your project is,
and a few things like "Intended Audience" and "Topic" to help with search engines.
There are some important ones though: the "License" (s) is used to indicate your license.
You also can give an idea of supported Python versions, Python implementations, and "Operating System"s as well.
If you have statically typed Python code, you can tell users about that, too.

```toml
[project]
classifiers = [
  "Development Status :: 5 - Production/Stable",
  "Intended Audience :: Developers",
  "Intended Audience :: Science/Research",
  "License :: OSI Approved :: BSD License",
  "Operating System :: OS Independent",
  "Programming Language :: Python",
  "Programming Language :: Python :: 3",
  "Programming Language :: Python :: 3 :: Only",
  "Programming Language :: Python :: 3.8",
  "Programming Language :: Python :: 3.9",
  "Programming Language :: Python :: 3.10",
  "Programming Language :: Python :: 3.11",
  "Topic :: Scientific/Engineering",
  "Topic :: Scientific/Engineering :: Information Analysis",
  "Topic :: Scientific/Engineering :: Mathematics",
  "Topic :: Scientific/Engineering :: Physics",
  "Typing :: Typed",
  "Private :: Do Not Upload",
]
```

::::::::::::::::::::::::::::::::::::: callout

## Prevent Inadvertent Publishing

By adding the "Private :: Do Not Upload" classifier here, we ensure that
the package will be
[rejected when we try to upload it to PyPI](https://pypi.org/classifiers/#:~:text=To%20prevent%20a%20package%20from,beginning%20with%20%22Private%20%3A%3A%22.).
If you want to upload to PyPI, you will need to remove that classifier.

::::::::::::::::::::::::::::::::::::::::::::::::

### License

There are three ways to include your license:

1. The preferred way to include a standard license
   is to include a classifier starting with "License ::",
   ```toml
   [project]
   classifiers = [
     "License :: OSI Approved :: BSD License",
   ]
   ```
2. The other way to include a standard license is to
   put its name in the `license` field:
   ```toml
   [project]
   license = {text = "MIT License"}
   ```
3. You may also put the license in a file named `LICENSE` or `LICENSE.txt`
   and link it in the `license` field:

   ```toml
   [project]
   license = {file = "LICENSE"}
   ```

   If you do this, after the [`build` step](publishing.md),
   verify the contents of your SDist and Wheel(s) manually
   to make sure the license file is included,
   because some build backends may not support including the license
   using this field.

   ```bash
   tar -tvf dist/example_package_YOUR_USERNAME_HERE-0.1.1.tar.gz
   unzip -l dist/example_package_YOUR_USERNAME_HERE-0.1.1-py2.py3-none-any.whl
   ```

## Functional metadata

The remaining fields actually change the usage of the package.

### Requires-Python

This is an important and sometimes misunderstood field. It looks like this:

```toml
requires-python = ">=3.8"
```

Pip will check if the Python version of the environment where the package being installed passes this expression. If it doesn't, pip will start checking older versions of the package until it finds one that passes. This is how `pip install numpy` still works on Python 3.7, even though NumPy has already dropped support for it.

You need to make sure you always have this line and that it stays accurate, since you can't edit metadata after releasing - you can only yank or delete release(s) and try again.

::::::::::::::::::::::::::::::::::::: callout

## Upper caps

Upper caps (like `">=3.8,<4` or `"~=3.8"`) are generally discouraged in the Python ecosystem, but they are broken (even more than usual) when used with `requires-python` field. This field was added to help users drop old Python versions, and the idea it would be used to restrict newer versions was not considered. The above field is not the right one to set an upper cap! Never upper cap this field and instead use classifiers to tell users what versions of Python your code was tested with.

::::::::::::::::::::::::::::::::::::::::::::::::

### Dependencies

Your package likely will need other packages to run.
You can add dependencies on other packages like this:

```toml
[project]
...
dependencies = [
  "numpy",
]
```

Sometimes you have dependencies that are only needed some of the time.
These can be specified as optional dependencies.
Unlike normal dependencies, these are specified in a table,
with the key being the option you pass to pip to install it.
For example:

```toml
[project.optional-dependenices]
test = ["pytest>=6"]
check = ["flake8"]
plot = ["matplotlib"]
```

Now you can run:

```bash
pip install --editable '.[test,check]'
```

or – once it's published,

```bash
pip install 'example-package-YOUR-USERNAME-HERE[test,check]'
```

– and pip will install both the required and optional dependencies `pytest` and `flake8`,
but not `matplotlib`.

#### Setting minimum, maximum and specific versions of dependencies

_Whether_ you set versions on dependencies depends on what sort of package you are working on:

- **Library**: something that can be imported. Support the widest range you are able to test.
  If you have very few users, requiring recent versions of dependencies is probably fine.
  If you expect a large number of users, you should support (and test!) a few past releases of dependencies.
- **App (CLI/GUI/TUI)**: Something that is installable but not importable. Wide range preferred, but not as important (assuming users use pipx and don't add it to "dev" environments or things like that).
- **Application (deployment)**: Something like a website or an analysis. Not installable. Versions should be fully locked.

You can set ranges on your dependencies by specifying the ranges in the `pyproject.toml` file:

```toml
dependencies = [
  "tqdm",                # no specified range
  "numpy>=1.18",         # lower cap
  "matplotlib<4.0",      # upper cap
  "pandas>1.4.2,<=3.0",  # lower and upper caps
  "seaborn==0.13.2",     # specific version
]
```

If you have a range of versions supported, you should ideally
run your tests at least once with the minimum versions of your dependencies.
You can do this using:

- a `constraints.txt` file,
  which specifies pins on the minimum versions of all your dependencies, and
  then use `pip install --constraint constraints.txt --editable .` before running your tests .
- a tool like `uv` which supports
  installing with the lowest possible dependencies like this:
  `uv pip install --resolution=lowest .` before running your tests

Adding an "upper cap" like `"numpy>=1.18,<2.0"` is only recommended if you
are fairly sure the next version will break your usage of the library.
For more information, see this article on
[bound version constraints](https://iscinumpy.dev/post/bound-version-constraints/).

There are several ways to lock your dependencies completely:

- specify the versions in the `pyproject.toml` file, or
- allow ranges in the `pyproject.toml` file and
  - specify the versions for your development environment manually using `pip-tools`, or
  - using a locking package manager like PDM or Poetry.
    (Just add the lockfiles generated by the tools to your repository, and they should be
    used automatically. There is usually an update command that will update the local
    environment and the lock file.)

::::::::::::::::::::::::::::::::::::: callout

## Pinning Dependencies with `pip-tools`

Set up a `requirements.in` file with your unpinned dependencies. For example:

```txt
# requirements.in
packaging
```

Now, run pip-compile from pip-tools on your requirements.in to make a requirements.txt:

```bash
pipx run --spec pip-tools pip-compile requirements.in --generate-hashes
```

This will produce a `requirements.txt` with fully locked dependencies, including
hashes. You can always regenerate it when you want updates.

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: challenge

## project.dependencies vs. build-system.requires

What is the difference between `project.dependencies` vs. `build-system.requires`?

::::::::::::::::::::::::::::::::::::: solution

## Answer

`build-system.requires` describes what your project needs to "build", that is, produce an SDist or wheel. Installing a built wheel will _not_ install anything from `build-system.requires`, in fact, the `pyproject.toml` is not even present in the wheel! `project.dependencies`, on the other hand, is added to the wheel metadata, and pip will install anything in that field if not already present when installing your wheel.

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

### Entry Points

A Python package can have entry points. There are three kinds: command-line entry points (`scripts`), graphical entry points (`gui-scripts`), and general entry points (`entry-points`). As an example, let's say you have a `main()` function inside `__main__.py` that you want to run to create a command `project-cli`. You'd write:

```toml
[project.scripts]
project-cli = "project.__main__:main"
```

The command line name is the table key, and the form of the entry point is `package.module:function`. Now, when you install your package, you'll be able to type `project-cli` on the command line and it will run your Python function.

## Dynamic

Fields can be specified dynamically by your build backend. You specify fields to populate dynamically using the `dynamic` field.
For example, if you want `hatchling` to read `__version__.py` from `src/package/__init__.py`:

```
[project]
name = "example-package-YOUR-USERNAME-HERE"
dynamic = ["version"]

[tool.hatch]
version.path = "src/example_package_YOUR_USERNAME_HERE/__init__.py"
```

## All together

Now let's take our previous example and expand it with more information. Here's an example:

```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "example-package-YOUR-USERNAME-HERE"
version = "0.1.1"
dependencies = [
  "numpy"
]
authors = [
  { name="Example Author", email="author@example.com" },
]
description = "A small example package"
readme = "README.md"
requires-python = ">=3.8"
classifiers = [
    "Programming Language :: Python :: 3",
    "License :: OSI Approved :: MIT License",
    "Operating System :: OS Independent",
    "Private :: Do Not Upload",
]

[project.optional-dependencies]
test = ["pytest"]

[project.urls]
"Homepage" = "https://<your github username>.github.io/example-package-YOUR-USERNAME-HERE"
"Source Code" = "https://github.com/<your github username>/example-package-YOUR-USERNAME-HERE"
```

::::::::::::::::::::::::::::::::::::: challenge

## Add metadata and check it.

Take your existing package and add more metadata to it. Install it, then use
`pip show -v <package>` to see the metadata. You can also look inside the wheel
or SDist to see the metadata.

::::::::::::::::::::::::::::::::::::: solution

## Solution

```bash
pip install -e .
pip show -v example-package-YOUR-USERNAME-HERE
```

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::: keypoints

- Add informational metadata to tell people about your package.
- Add functional metadata to tell people how to install and use your package.

::::::::::::::::::::::::::::::::::::::::::::::::
