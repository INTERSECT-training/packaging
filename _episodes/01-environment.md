---
title: "Environments and task runners"
teaching: 10
exercises: 10
questions:
- "How do you install and manage packages?"
- "How can you ensure others run the same code you do?"
objectives:
- "Learn about virtual environments"
- "Use a task runner to manage environments and run code"
keypoints:
- "Virtual environments isolate software"
- "Virtual environments solve the update problem"
- "A task runner makes it easier to contribute to software"
---


One of Python's biggest strengths is its ecosystem of packages which build upon and extend
the capabilities offered by the standard library.

We'll look at how:
- You should use virtual environments to manage the dependencies of different projects,
- You can install packages you want to build on using `pip`, and
- You can install Python applications you just want to use using `pipx`.

## Creating and Activating/Deactivating a Virtual Environment

Whenever we're developing or working in Python, the best practice is to use a
"virtual environment" which isolates the packages used for a particular project.

Python 3 comes with the `venv` module built-in, which supports making virtual environments.
To make one, you call the module with:

```bash
python3 -m venv .venv
```

This creates a new directory `.venv` containing:
- `.venv/bin`
  - A link to the Python version,
  - A link to the Python package installer `pip`, and
  - activation scripts.
- `.venv/lib/site-packages` where packages will be installed.

To activate the environment, source the activation script:

```bash
. .venv/bin/activate
```

Now `.venv/bin` has been added to your PATH, and usually your shell's prompt
will be modified to indicate you are "in" a virtual environment:
```
% . .venv/bin/activate
(.venv) %
```

> ## Upgrade `pip`
> Check the version of pip installed!
> If it's old, you might want to run `pip install --upgrade pip` or,
> for Python 3.9 or later, you can add `--upgrade-deps` to the venv creation line.
{:.challenge}

To "leave" the virtual environment, you
undo those changes by running the deactivate function the activation added to
your shell:

```bash
deactivate
```

The prompt will revert:

```
(.venv) % deactivate
%
```

> ## What about conda?
>
> The same concerns apply to Conda. You should always make separate environments and
> use those. Quick tips:
>
> ```bash
> conda config --set auto_activate_base false  # turn off the default environment
> conda env create -n some_name  # or use paths with `-p`
> conda activate some_name
> conda deactivate
> ```
>
> Conda has inspired replacements like
> [Mamba](https://github.com/mamba-org/mamba),
> and more recently [Pixi](https://github.com/prefix-dev/pixi).
> These typically aim to speed up creation of environments and
> resolution of dependencies,
> by using faster languages – where Conda is written in Python,
> Mamba uses C++ and Pixi uses Rust.
>
> If you are using Conda, but the environment creation process takes too long,
> consider trying Mamba or Pixi.
{:.callout}

## Installing Packages

To install a package,
first make sure the environment is activated using `. .venv/bin/activate` first,
then call:

```bash
pip install <package>
```

> ## Install `numpy`
>
> - Install the numpy package into your virtual environment.
> - Test it by opening a Python session and running `import numpy as np` then `np.arange(15).reshape(3, 5)`.
>
> > ## Solution
> > ```
> > % . .venv/bin/activate
> > (.venv) % pip install numpy
> > (.venv) % python
> > Python 3.12.3 (main, Apr  9 2024, 08:09:14) [Clang 15.0.0 (clang-1500.3.9.4)] on darwin
> > Type "help", "copyright", "credits" or "license" for more information.
> > >>> import numpy as np
> > >>> np.arange(15).reshape(3, 5)
> > array([[ 0,  1,  2,  3,  4],
> >        [ 5,  6,  7,  8,  9],
> >        [10, 11, 12, 13, 14]])
> > >>> exit()
> > (.venv) %
> > ```
> {:.solution}
{:.challenge}

> ## Installing Packages in the "base" and "user" environment
>
> You will see two _very_ common recommendations when installing a package, neither of
> which you should use unless you know what you're doing:
>
> ```bash
> pip install <package>         # Use only in virtual environment!
> ```
> This will try to install globally, and if you don't have permission, will install to your
> user site packages. In global site packages, you can get conflicting versions
> of libraries, you can't tell what you've installed for what, packages can
> update and break your system; it's a mess. This is the *"update problem"*.
>
> ```bash
> pip install --user <package>  # Almost never use
> ```
> This will install in your user directory. This is even worse worse,
> because all installs of Python on your computer share it, so you might override
> and break things you didn't intend to. And with pip's new smart solver,
> updating packages inside a global environment can take many minutes and produce
> unexpectedly solves that are technically "correct" but don't work because it
> backsolved conflicts to before issues were discovered.
>
> There is a solution: virtual environments (libraries) or pipx (applications).
>
> There are likely a _few_ libraries (ideally just `pipx`) that you just have to
> install globally. Go ahead, but be careful, and always use your system package
> manager instead if you can, like
> - [`brew` on macOS](https://brew.sh),
> - [`winget`](https://learn.microsoft.com/en-us/windows/package-manager/winget/) or [`scoop`](https://scoop.sh/) on Windows,
> - `apt-get` on Ubuntu.
>
{:.caution}

## Installing Applications

There are many Python packages that provide a command line interface and are
not really intended to be imported (`pip`, for example, should not be
imported). It is really inconvenient to have to set up venvs for every command
line tool you want to install, however. [`pipx`](https://pipx.pypa.io/),
from the makers of `pip`, solves this problem for you.

If you `pipx install` a package, it will be
created inside a new virtual environment, and
just the executable scripts will be exposed in your regular shell.

Pipx also has a `pipx run <package>` command, which will download a package and
run a script of the same name, and will cache the temporary environment for a
week. This means you have all of PyPI at your fingertips in one line on any
computer that has pipx installed!

> ## Install `pipx` and `cowsay`
>
> - Install `pipx` by following the [installation instructions](https://pipx.pypa.io/).
> - Test it by running `pipx run cowsay -t "venvs are the foo\!"`
>
> > ## Solution
> >
> > Ubuntu Linux:
> > ```
> > sudo apt update
> > sudo apt install pipx
> > pipx ensurepath
> > ```
> >
> > macOS:
> > ```
> > brew install pipx
> > pipx ensurepath
> > ```
> >
> > Then:
> > ```
> > % pipx run cowsay -t "venvs are the foo\!"
> >   __________________
> > | venvs are the foo! |
> >   ==================
> >                   \
> >                    \
> >                      ^__^
> >                      (oo)\_______
> >                      (__)\       )\/\
> >                          ||----w |
> >                          ||     ||
> > ```
> {:.solution}
{:.challenge}


## Task runner

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

Other examples are:
- [make][] (fully general),
- [rake][] (Ruby general),
- [invoke][] (Python general), and
- [tox][] (Python packages).

> ## Task Runner as Crutch
> Task runners can be a crutch, allowing poor packaging practices to be
> employed behind a custom script, and they can hide what is actually happening.
{:.caution}

[nox]: https://nox.thea.codes
[tox]: https://tox.readthedocs.io
[invoke]: https://www.pyinvoke.org
[rake]: https://ruby.github.io/rake/
[make]: https://www.gnu.org/software/make/
[hatch]: https://hatch.pypa.io/

### Nox

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

#### Running nox

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

#### Writing a Noxfile

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


> ## Passing arguments through
> Try passing `--verbose` to pytest.
>
> > ## Solution
> > ```bash
> > nox -s tests -- --verbose
> > ```
> {:.solution}
{:.challenge}


> ## Virtual environments
>
> Nox is really just doing the same thing we would do manually (and printing all the steps except the
> exact details of creating the virtual environment. You can see the virtual environment in `.nox/tests`!
> How would you activate this environment?
>
> > ## Solution
> > ```bash
> > . .nox/tests/bin/activate
> > ```
> {:.solution}
{:.challenge}


#### Backends

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

> #### Alternative backends
>
> Try running your tests with the default `virtualenv` and the `uv|virtualenv` backend.
>
> How does the execution time change?
{:.challenge}

### Hatch

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
This will create a python environment in which your tests will run
and execute the tests.

{% include links.md %}
