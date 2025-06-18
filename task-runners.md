---
title: "Task runners"
teaching: 5
exercises: 5
---

:::::::::::::::::::::::::::::::::::::: questions 

- How can you ensure others run the same code you do?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Use a task runner to manage environments and run code

::::::::::::::::::::::::::::::::::::::::::::::::


A task runner is a tool that lets you specify a set of tasks via a common
interface.


Use of a task runner is optional, but can be helpful to:
- make it easy and simple for new contributors to run things,
- make specialized developer tasks easy,
- avoid making single-use virtual environments for docs and other rarely run tasks.

Task runner preferences are subjective and diverse.
Different people prefer different task runners because they are
more flexible, simpler to understand, specialized to one language,
general to many languages, and so on.

Examples we'll cover include:
- [nox][] (Python semi-general), and
- [hatch][] (specialized for Python package management).

There are many other task runners for different languages, including:
- [make][] (fully general),
- [invoke][] (Python general), and
- [tox][] (Python packages).

::::::::::::::::::::::::::::::::::::: caution

## Task Runner as Crutch
Task runners can be a crutch, allowing poor packaging practices to be
employed behind a custom script, and they can hide what is actually happening.

::::::::::::::::::::::::::::::::::::::::::::::::


[nox]: https://nox.thea.codes
[tox]: https://tox.readthedocs.io
[invoke]: https://www.pyinvoke.org
[rake]: https://ruby.github.io/rake/
[make]: https://www.gnu.org/software/make/
[hatch]: https://hatch.pypa.io/

::::::::::::::::::::::::::::::::::::: callout


## Further reading
See the [Scientific Python Development Guide page on task runners][scientific-python-task-runners]
for more information.

::::::::::::::::::::::::::::::::::::::::::::::::

[scientific-python-task-runners]: https://learn.scientific-python.org/development/guides/tasks/

## Nox

Nox has two strong points that help with this concern. First, it is very
explicit, and even prints what it is doing as it operates. Unlike the older
tox, it does not have any implicit assumptions built-in. Second, it has very
elegant built-in support for both virtual and Conda environments. This can
greatly reduce new contributor friction with your codebase.

A daily developer is not expected to use nox for simple tasks, like running
tests or linting. You should not rely on nox to make a task that should be made
simple and standard (like building a package) complicated. You are not expected
to use nox for linting on CI, or sometimes even for testing on CI, even if
those tasks are provided for users. Nox is a few seconds slower than running
directly in a custom environment - but for new users and rarely run tasks, it
is _much_ faster than explaining how to get setup or manually messing with
virtual environments. It is also highly reproducible, creating and destroying
the temporary environment each time by default.

Since nox is an application, you should install it with `pipx`. If you use
Homebrew, you can install `nox` with that (Homebrew isolates Python apps it
distributes too, just like pipx).

### Running nox

If you see a `noxfile.py` in a repository, that means nox is supported. You can start
by checking to see what the different tasks (called `sessions` in nox) are provided
by the noxfile author. For example, if we do this on `packaging.python.org`'s repository:

```bash
nox -l  # or --list-sessions
```

```
Sessions defined in /github/pypa/packaging.python.org/noxfile.py:

- translation -> Build the gettext .pot files.
- build -> Make the website.
- preview -> Make and preview the website.
- linkcheck -> Check for broken links.

sessions marked with * are selected, sessions marked with - are skipped.
```
{:.output}

You can see that there are several different sessions. You can run them with `-s`:

```bash
nox -s preview
```

Will build and start up a preview of the site.

If you need to pass options to the session, you can separate nox options with
and the session options with `--`,
e.g. `nox -s preview -- --quiet` to pass the `--quiet` flag to the session named `preview`.

### Writing a Noxfile

For this example, we'll need a minimal test file for pytest to run. Let's make
this file in a local directory:

```python
# test_nox.py


def test_runs():
    assert True
```

Let's write our own noxfile. If you are familiar with pytest, this should look
familiar as well; it's intentionally rather close to pytest. We'll make a
minimal session that runs pytest:

```python
# noxfile.py
import nox


@nox.session()
def tests(session):
    session.install("pytest")
    session.run("pytest")
```

A noxfile is valid Python, so we import nox. The session decorator tells nox
that this function is going to be a session. By default, the name will be the
function name, the description will be the function docstring, it will run on
the current version of Python (the one nox is using), and it will make a
virtual environment each time the session runs, though all of this is
changeable via keyword arguments to session.

The session function will be given a `nox.Session` object that has various
useful methods. `.install` will install things with pip, and `.run` will run a
command in a session. The `.run` command will print a warning if you use an
executable outside the virtual environment unless `external=True` is passed.
Errors will exit the session.

Let's expand this a little:


```python
# noxfile.py
import nox


@nox.session()
def tests(session: nox.Session) -> None:
    """
    Run our tests.
    """
    session.install("pytest")
    session.run("pytest", *session.posargs)
```

This adds a type annotation to the session object, so that IDE's and type
checkers can help you write the code in the function. There's a docstring,
which will print out nice help text when a user lists the sessions. And we pass
through to pytest anything the user passes in via `session.posargs`.


Let's try running it:

```bash
nox -s tests
```

```
nox > Running session tests
nox > Creating virtual environment (virtualenv) using python3.10 in .nox/tests
nox > python -m pip install pytest
nox > pytest
==================================== test session starts ====================================
platform darwin -- Python 3.10.5, pytest-7.1.2, pluggy-1.0.0
rootdir: /Users/henryschreiner/git/teaching/packaging
collected 1 item

test_nox.py .                                                                          [100%]

===================================== 1 passed in 0.05s =====================================
nox > Session tests was successful.
```
{:.output}

You can pass arguments through to the `session.run` command
by prefixing them with `--` on the command line.
For instance, to pass `--verbose` to pytest:

```bash
nox -s tests -- --verbose
```

If you have specified your test dependencies using an the `test` extra,
you can install all those dependencies more simply:

```python
@nox.session
def tests(session):
    session.install(".[test]")  # this installs all of the dependencies
    session.run("pytest")
```


::::::::::::::::::::::::::::::::::::: challenge

## Virtual environments

Nox is really just doing the same thing we would do manually (and printing all the steps except the
exact details of creating the virtual environment. You can see the virtual environment in `.nox/tests`!
How would you activate this environment?

::::::::::::::::::::::::::::::::::::: solution

## Solution
```bash
. .nox/tests/bin/activate
```

::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::



::::::::::::::::::::::::::::::::::::: challenge

## Add documentation generation to your task runner (MkDocs)

Add the commands do preview and build your MkDocs documentation using `nox`.

::::::::::::::::::::::::::::::::::::: solution

## Solution
Add a session to your `noxfile.py` to generate docs:

```python
# noxfile.py

import nox

@nox.session()
def preview_docs(session: nox.Session):
    """Show the documentation preview."""
    session.install(".[docs]")
    session.run("mkdocs", "serve")

@nox.session()
def build_docs(session: nox.Session):
    """Build the documentation."""
    session.install(".[docs]")
    session.run("mkdocs", "build")
```
You now have working docs that you can generate and view cross platform with `nox -s preview_docs`!

::::::::::::::::::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::





### Backends

It's possible to use different backends than `venv` and `pip` when running `nox`.
`uv` is a fast package installer and resolver, written in Rust and designed to be a
replacement for `pip`. Using it can lead to enormous performance gains,
which can be useful when you create and destroy virtual environments with `nox`
many times per day.

- Update the top of your `noxfile.py`:
  ```python
  # noxfile.py

  import nox

  nox.needs_version = ">=2024.3.2"
  nox.options.default_venv_backend = "uv"
  ```
- Install `uv` using your package manager ([installation instructions](https://github.com/astral-sh/uv)).

You can also specify `nox.options.default_venv_backend = "uv|virtualenv"`
which will fallback to `virtualenv` if `uv` is not installed


::::::::::::::::::::::::::::::::::::: callout

## Alternative backends

Try running your tests with the default `virtualenv` and the `uv|virtualenv` backend.

How does the execution time change?

::::::::::::::::::::::::::::::::::::::::::::::::


## Hatch

Hatch is a Python "Project Manager" which can:
- build packages,
- manage virtual environments,
- manage multiple python versions for a project,
- run tests using `pytest`,
- run static analysis on code using `ruff`,
- **execute scripts with specific dependencies and python versions** (this is the "task runner" part)
- publish packages to PyPI,
- help bump version numbers of packages,
- use templates to create new python projects

To initialize an existing project for `hatch`,
enter the directory containing the project and run the following:
```bash
hatch new --init
```
This will interactively guide you through the setup process.

To run tests using `hatch`, run the following:
```bash
hatch test
```
This will:
- create a python environment in which your tests will run, then
- run the tests.

Alongside built-in commands like `test`, `hatch` allows adding custom scripts.

For instance, to add an environment and scripts
for viewing and publishing the Material for MkDocs documentation,
you can add the following lines to the `pyproject.toml` file:

```toml
[tool.hatch.envs.doc]
dependencies = [
  "mkdocs-material",
  "mkdocstrings[python]"
]

[tool.hatch.envs.doc.scripts]
serve = "mkdocs serve --dev-addr localhost:8000"
build = "mkdocs build --clean --strict --verbose"
deploy = "mkdocs gh-deploy"
```

This specifies a new environment `doc`
with the `mkdocs-material` and `mkdocstrings` dependencies needed, and
scripts `serve`, `build` and `deploy` defined within that environment.

Then to view the documentation locally, run `hatch run <ENV>:<SCRIPT>`, e.g.:
```bash
hatch run doc:serve
```
to run the preview server, or
```bash
hatch run doc:build
```
to build the documentation, ready for deployment.

The key benefits here are that:
- these scripts run within an isolated environment,
- the simple commands like `hatch run doc:serve` allow the developer to
  use arguments like `--dev-addr localhost:8000` without needing to remember or think about them.

The developer must decide whether these benefits outweigh
the added complexity of an additional layer of abstraction,
which will hinder debugging if something goes wrong.





:::::::::::::::::::::::::::::::::::::: keypoints 

- A task runner makes it easier to contribute to software

::::::::::::::::::::::::::::::::::::::::::::::::
