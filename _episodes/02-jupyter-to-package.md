---
title: "Idea to Package"
teaching: 20
exercises: 5
questions:
- "How do we take code in a Jupyter Notebook or Python script and turn that into a package?"
- "What are the minimum elements required for a Python package?"
- "How do you set up tests?"
objectives:
- "Create and install a Python package"
- "Create and run a test"
keypoints:
- "Use a `pyproject.toml` file to describe a Python package"
---

Much research software is initially developed by hacking away in an interactive setting,
such as in a [Jupyter Notebook](https://jupyter.org) or a Python shell.
However, at some point when you have a more-complicated workflow that you want to repeat,
and/or make available to others,
it makes sense to package your functions into modules and ultimately a software package that can be installed.
This lesson will walk you through that process.

> ## Check Setup
> - Ensure you're in an empty git repository (see [Setup](../setup) for details).
> - Ensure you've created and activated your virtual environment (see [Environment](../01-environment) for details):
> ```bash
> . .venv/bin/activate
> ```
{:.callout}

Consider the `rescale()` function written as an exercise in the Software Carpentry
[Programming with Python](https://swcarpentry.github.io/python-novice-inflammation/08-func/index.html) lesson.


Install NumPy:
```bash
pip install numpy
```

Then, in a Python shell or Jupyter Notebook, declare the function:

```python
import numpy as np


def rescale(input_array):
    """Rescales an array from 0 to 1.

    Takes an array as input, and returns a corresponding array scaled so that 0
    corresponds to the minimum and 1 to the maximum value of the input array.
    """
    low = np.min(input_array)
    high = np.max(input_array)
    output_array = (input_array - low) / (high - low)
    return output_array
```

and call the function with:

```python
rescale(np.linspace(0, 100, 5))
```

which provides the output:

```
array([ 0.  ,  0.25,  0.5 ,  0.75,  1.  ])
```
{: .output}

## Create a minimal package

Let's create a Python package that contains this function.

Create the necessary directory structure for your package.
This includes:
- a `src` ("source") directory, which will contain another directory called `package` for the source files of your package itself,
- a `tests` directory, which will hold tests for your package and its modules/functions,
- a `docs` directory, which will hold the files necessary for documenting your software package.

```bash
$ mkdir -p src/package tests docs
```

(The `-p` flag tells `mkdir` to create the `src` parent directory for `package`.)

> ## Directory Structure
> Putting the package directory and source code inside the `src` directory is not actually *required*.
> - If you put the `<package_name>` directory at the same level as `tests` and `docs` then you could actually import or call the package directory from that location.
> - However, this can cause several issues, such as running tests with the local version instead of the installed version.
> - In addition, the `src/` package structure matches that of compiled languages, and lets your package easily contain non-Python compiled code, if necessary.
{:.callout}

Inside `src/package`, create the files `__init__.py` and `rescale.py`:

- `__init__.py` is required to import this directory as a package, and should remain empty (for now).
  ```bash
  $ touch src/package/__init__.py
  ```
- `rescale.py` is the module inside this package that will contain the `rescale()` function;
  ```bash
  $ touch src/package/rescale.py
  ```

Copy the `rescale()` function into `rescale.py` file. (Don't forget the NumPy import!)

The last element your package needs is a `pyproject.toml` file. Create this with

```bash
$ touch pyproject.toml
```

and then provide the minimally required metadata, which include
- information about the package itself (`name`, `version` and `dependencies`) and
- build system (hatchling):

```toml
# contents of pyproject.toml

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "package"
version = "0.1.0"
dependencies = [
  "numpy"
]
```

The package name given here, "package," matches the directory `src/package` that contains the code.
We've chosen 0.1.0 as the starting version for this package; you'll see more in a later episode about versioning,
and how to specify this without manually writing it here.

> ## Build Backend
> The build backend is a program which will convert the source files into a package
> ready for distribution. It determines how your project will specify
> its configuration, metadata and files.
> You should choose one that suits your preferences.
>
> We selected a specific backend - hatching.
> It had a good default configuration, is fast, has some powerful features,
> and supports a growing ecosystem of plugins.
> There are other backends too, including ones for
> [compiled projects](https://learn.scientific-python.org/development/guides/packaging-compiled).
{:.callout}

> ## Minimal Working Package
> The only elements of your package strictly _required_ to install and import it are
> the `pyproject.toml`, `__init__.py`, and `rescale.py` files.
{:.callout}

At this point, your package's file structure should look like this:

```bash
.
├── docs/
├── pyproject.toml
├── src/
│   └── package/
│   │   ├── __init__.py
│   │   └── rescale.py
├── tests/
└── .venv/
```

## Installing and using your package

Now that your package has the necessary elements, you can install it into your virtual environment (which should already be active).
From the top level of your project's directory, enter:

```bash
$ pip install --editable .
```

The `--editable` flag tells `pip` to install in editable mode, meaning that you can continue developing your package on your computer as you test it. Note, you will often see the short option `-e`.

Then, in a Python shell or Jupyter Notebook, import your package and call the (single) function:

```python
import numpy as np
from package.rescale import rescale

rescale(np.linspace(0, 100, 5))
```

```
array([0.  , 0.25, 0.5 , 0.75, 1.  ])
```
{: .output}

This matches the output we expected based on our interactive testing above! 😅

## Your first test

Now that we have installed our package and we have manually tested that it works,
let's set up this situation as a test that can be automatically run using `pytest`.

In the `tests` directory, create the `test_rescale.py` file:

```bash
touch tests/test_rescale.py
```

In this file, we need to import the package, and check that a call to the `rescale` function with our known input returns the expected output:
```python
# contents of tests/test_rescale.py
import numpy as np
from package.rescale import rescale


def test_rescale():
    np.testing.assert_allclose(
        rescale(np.linspace(0, 100, 5)),
        np.array([0.0, 0.25, 0.5, 0.75, 1.0]),
    )
```

To use pytest to run this (and other tests), we need to install `pytest`.

Here we add it to the `pyproject.toml` file,
by adding a block below the dependencies with an "extra" called `test`:

```toml
...
dependencies = [
  "numpy"
]

[project.optional-dependencies]
test = ["pytest"]

[build-system]
...

```

You install the project with the "extra" using:
```bash
pip install --editable ".[test]"
```

You can run the tests using `pytest`:
```
(.venv) % pytest
======================== test session starts ========================
platform darwin -- Python 3.12.3, pytest-8.2.1, pluggy-1.5.0
rootdir: /Users/john/Developer/packaging-example
configfile: pyproject.toml
collected 1 item

tests/test_rescale.py .                                       [100%]

========================= 1 passed in 0.14s =========================
```

This tells us that the output of the test function matches the expected result, and therefore the test passes! 🎉

> ## Update `noxfile.py` with Tests
>
>
> Take the `noxfile.py` you created in an earlier episode, and modify it to
>  - install `numpy`, necessary to run the package;
>  - install `pytest`, necessary to automatically find and run the test(s);
>  - install the package itself; and
>  - run the test(s)
>
> > ## Solution
> >
> > ```python
> > # contents of noxfile.py
> > import nox
> >
> >
> > @nox.session
> > def tests(session):
> >     session.install(".[test]")  # this installs all of the dependencies
> >     session.run("pytest")
> > ```
> >
> > Have `nox` run your tests with the command
> >
> > ```bash
> > $ nox
> > ```
> {:.solution}
{:.challenge}

## Commit and Push

Don't forget to commit all your work using `git`.
(In the rest of the lesson, we won't remind you to do this,
but you should still make atomic commits regularly.)


Commit the relevant files, first the code:

```bash
git add src/package/{__init__,rescale}.py
git add tests/test_rescale.py
git commit -m "feat: add basic rescaling function
```

... then the metadata:
```bash
git add pyproject.toml
git commit -m "build: add minimal pyproject.toml"
```

... then push those to the `origin` remote repository.
```bash
git push origin main
```

> ## Always `git add` individual files until you've set up your `.gitignore`
>
> When working with git, it's best to
> - stage **individual files** using `git add FILENAME ...`,
> - check what you're about to commit using `git status`, before you
> - commit with `git commit -m "COMMIT MESSAGE"`.
>
> You can also use a graphical tool which makes it easy to see at a glance
> what is is you're committing.
>
> Adding a `.gitignore` file (which we'll cover later), will help avoid
> inadvertently committing files like the virtual environment directory,
> and is a prerequisite for using `git commit -a` which commits everything.
{:.callout}

> ## Conventional commits
> In this example, we use
> [conventional commit messages](https://www.conventionalcommits.org/)
> which look like `<type>: <description>`.
>
> Each commit should do one and only one "thing" to the code, for instance:
> - add a new feature (type: `feat`), *or*
> - fix a bug (type: `fix`), *or*
> - rename a function (type: `refactor`), *or*
> - add documentation (type: `docs`), *or*
> - change something affecting the build system (type: `build`).
>
> By doing only one thing per commit, it's easier to:
> - write the commit message,
> - understand the history by looking at the `git log`, and
> - revert individual changes you later decide could be done in a better way.
{:.callout}

> ## Check your package
> Check that you can install your package and that it works as expected.
>
> If everything works, you should be able to install your package (in a new virtual environment):
> ```
> python3 -m venv .venv2
> . .venv2/bin/activate
> python3 -m pip install git+https://github.com/<your github username>/packaging-example
> ```
> Open a python console and call the rescale function with some data.
>
> Switch back to the original virtual environment before going onto the next lesson:
> ```
> . .venv/bin/activate
> ```
{:.challenge}

You now have a package that is:
- installed in editable mode in an isolated environment,
- can be interacted with in tests and in an interactive console, and
- has a passing test.

Next, we'll look at other files that should be included with your package.

{% include links.md %}
