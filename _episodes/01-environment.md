---
title: "Environments"
teaching: 5
exercises: 5
questions:
- "Why do we use virtual environments?"
- "How do you install and manage packages?"
- "How do you install and manage python applications?"
objectives:
- "Learn about virtual environments"
- "Learn how to install Python packages and applications using `pip` and `pipx`"
keypoints:
- "Virtual environments isolate software dependencies"
- "Use a different virtual environment for each project"
- "Install packages using `pip` and applications using `pipx`"
- "Virtual environments solve the update problem"
---

A Python installation includes a standard library of packages, but the real power of Python
is available through additional packages which the user or developer can install and build on.

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


{% include links.md %}
