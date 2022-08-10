---
title: "Jupyter to package"
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
- "First key point. Brief Answer to questions. (FIXME)"
---

Much research software is initially developed by hacking away in an interactive setting, such as in a [Jupyter Notebook](https://jupyter.org) or a Python shell. However, at some point when you have a more-complicated workflow that you want to repeat, and/or make available to others, it makes sense to package your functions into modules and ultimately a software package that can be installed. This lesson will walk you through that process.

Consider the `rescale()` function written as an exercise in the Software Carpentry [Programming with Python](https://swcarpentry.github.io/python-novice-inflammation/08-func/index.html) lesson. 

First, as needed, create your virtual environment and install NumPy with 
```bash
virtualenv .venv
source .venv/bin/activate
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
    L = np.min(input_array)
    H = np.max(input_array)
    output_array = (input_array - L) / (H - L)
    return output_array
```

and call the function with:
```python
>>> rescale(np.linspace(0, 100, 5))
```

which provides the output:

```
array([ 0.  ,  0.25,  0.5 ,  0.75,  1.  ])
```
{: .output}

## Creating our package

Let's create a Python package that contains this function.

First, create a new directory for your software package, called `package`, and move into that:

```bash
$ mkdir package
$ cd package
```

You should immediately initialize an empty Git repository in this directory; if you need a refresher on using Git for version control, check out the Software Carpentry [Version Control with Git](https://swcarpentry.github.io/git-novice/) lesson. 
(This lesson will not explicitly remind you to commit your work after this point.)

```bash
$ git init
```

Next, we want to create the necessary directory structure for your package. 
This includes:
- a `src` directory, which will contain another directory called `rescale` for the source files of your package itself; 
- a `tests` directory, which will hold tests for your package and its modules/functions (this can also go inside the `rescale` directory, but we recommend keeping it at the top level so that your test suite is not installed along with the package itself);
- a `docs` directory, which will hold the files necessary for documenting your software package.

```bash
$ mkdir -p src/rescale tests docs
```

(The `-p` flag tells `mkdir` to create the `src` parent directory for `rescale`.)

Putting the package directory and source code inside the `src` directory is not actually *required*;
instead, if you put the `<package_name>` directory at the same level as `tests` and `docs` then you could actually import or call the package directory from that location. 
However, this can cause several issues, such as running tests with the local version instead of the installed version.
In addition, this package structure matches that of compiled languages, and lets your package easily contain non-Python compiled code, if necessary.

Inside `src/rescale`, create the files `__init__.py` and `rescale.py`:

```bash
$ touch src/rescale/__init__.py src/rescale/rescale.py
```

`__init__.py` is required to import this directory as a package, and should remain empty (for now).
`rescale.py` is the module inside this package that will contain the `rescale()` function; 
copy the contents of that function into this file. (Don't forget the NumPy import!)

The last element your package needs is a `pyproject.toml` file. Create this with

```bash
$ touch pyproject.toml
```

and then provide the minimally required metadata, which include information about the build system (hatchling) and the package itself (`name` and `version`):

```toml
# contents of pyproject.toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "package"
version = "0.1.0"
```

The only elements of your package truly **required** to install and import it are the `pyproject.toml`, `__init__.py`, and `rescale.py` files.

## Installing and using your package

Now that your package has the necessary elements, you can install it into your virtual environment (which should already be active). From the top level of your project's directory, enter

```bash
$ pip install -e . 
```

The `-e` flag tells `pip` to install in editable mode, meaning that you can continue developing your package on your computer as you test it.

Then, in a Python shell or Jupyter Notebook, import your package and call the (single) function:

```python
>>> import numpy as np
>>> from package.rescale import rescale
>>> rescale(np.linspace(0, 100, 5))
```

```
array([0.  , 0.25, 0.5 , 0.75, 1.  ])
```
{: .output}

This matches the output we expected based on our interactive testing above! 😅

## Your first test

Now that we have installed our package and we have manually tested that it works, let's set up this situation as a test that can be automatically run using `nox` and `pytest`.

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
        np.array([0., 0.25, 0.5, 0.75, 1.0 ]),
        )
```

Next, update your `noxfile.py` to:
 - install `numpy`, necessary to run the package; 
 - install `pytest`, necessary to automatically find and run the test(s);
 - install the package itself; and
 - run the test(s)

with:

```python
# contents of noxfile.py
import nox

@nox.session
def tests(session):
    session.install('numpy', 'pytest')
    session.install('.')
    session.run('pytest')
```

and run using

```bash
$ nox
```

This should give you some information about what `nox` is doing, and show output along the lines of 
```
nox > Running session tests
nox > Creating virtual environment (virtualenv) using python in .nox/tests
nox > python -m pip install numpy pytest
nox > python -m pip install .
nox > pytest 
======================================================================= test session starts =================================================
platform darwin -- Python 3.9.13, pytest-7.1.2, pluggy-1.0.0
rootdir: /Users/niemeyek/Desktop/rescale
collected 1 item                                                                                                                                                  

tests/test_rescale.py .                                                                                                                [100%]

======================================================================== 1 passed in 0.07s ==================================================
nox > Session tests was successful.
```
{: .output}

This tells us that the output of the test function matches the expected result, and therefore the test passes! 🎉

We now have a package that is installed, can be interacted with properly, and has a passing test.
Next, we'll look at other files that should be included with your package.

{% include links.md %}

