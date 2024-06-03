---
title: "Environments"
teaching: 5
exercises: 5
questions:
- "How do you install and manage packages?"
objectives:
- "Learn about virtual environments"
keypoints:
- "Virtual environments isolate software"
- "Virtual environments solve the update problem"
---

You will see two _very_ common recommendations when installing a package:

```bash
pip install <package>         # Use only in virtual environment!
pip install --user <package>  # Almost never use
```

Don't use them unless you know exactly what you are doing! The first one will
try to install globally, and if you don't have permission, will install to your
user site packages. In global site packages, you can get conflicting versions
of libraries, you can't tell what you've installed for what, packages can
update and break your system; it's a mess. And user site packages are worse,
because all installs of Python on your computer share it, so you might override
and break things you didn't intend to. And with pip's new smart solver,
updating packages inside a global environment can take many minutes and produce
unexpectedly solves that are technically "correct" but don't work because it
backsolved conflicts to before issues were discovered.

There is a solution: virtual environments (libraries) or pipx (applications).

There are likely a _few_ libraries (ideally just `pipx`) that you just have to
install globally. Go ahead, but be careful (and always use your system package
manager instead if you can, like [`brew` on macOS](https://brew.sh) or the
Windows ones -- Linux package managers tend to be too old to use for Python libraries).


## Virtual Environments

> The following uses the standard library `venv` module. The `virtualenv`
> module can be installed from PyPI, and works identically, though is a bit
> faster and provides newer pip by default.
{:.callout}

Python 3 comes with the `venv` module built-in, which supports making virtual environments.
To make one, you call the module with


```bash
python3 -m venv .venv
```

This creates links to Python and pip in `.venv/bin`, and creates a
site-packages directory at `.venv/lib`. You can just use `.venv/bin/python` if
you want, but many users prefer to source the activation script:

```bash
. .venv/bin/activate
```

(Shell specific, but there are activation scripts for all common shells here).
Now `.venv/bin` has been added to your PATH, and usually your shell's prompt
will be modified to indicate you are "in" a virtual environment. You can now
use `python`, `pip`, and anything you install into the virtualenv without
having to prefix it with `.venv/bin/`.

> Check the version of pip installed! If it's old, you might want to run `pip
> install -U pip` or, for modern versions of Python, you can add
> `--upgrade-deps` to the venv creation line.
{:.challenge}

To "leave" the virtual environment, you
undo those changes by running the deactivate function the activation added to
your shell:

```bash
deactivate
```

> ## What about conda?
>
> The same concerns apply to Conda. You should avoid installing things to the
> `base` environment, and instead make environments and use those above. Quick tips:
>
> ```bash
> conda config --set auto_activate_base false  # turn off the default environment
> conda env create -n some_name  # or use paths with `-p`
> conda activate some_name
> conda deactivate
> ```
{:.callout}


## Pipx

There are many Python packages that provide a command line interface and are
not really intended to be imported (`pip`, for example, should not be
imported). It is really inconvenient to have to set up venvs for every command
line tool you want to install, however. `pipx`, from the makers of `pip`,
solves this problem for you.  If you `pipx install` a package, it will be
created inside a new virtual environment, and just the executable scripts will
be exposed in your regular shell.

Pipx also has a `pipx run <package>` command, which will download a package and
run a script of the same name, and will cache the temporary environment for a
week. This means you have all of PyPI at your fingertips in one line on any
computer that has pipx installed!

{% include links.md %}
