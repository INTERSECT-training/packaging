---
title: "Metadata"
teaching: 10
exercises: 5
questions:
- "What metadata is important to add to your package?"
- "How to I add common functionality, like executable scripts?"
objectives:
- "Learn about the project table."
keypoints:
- "There are a variety of useful bits of metadata you should add."
---

In a previous lesson, we left the metadata in our `project.toml` quite minimal, just:
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

Note that TOML supports two ways two write tables and two ways to write arrays, so you might see this in a different form, but it should be recognizable.

### Keywords

A list of keywords for the project. This is mostly used to improve searchability.

```toml
keywords = ["example", "tutorial"]
```

### URLs

A set of links to help users find various things for your code; some common ones are `Homepage`, `Source Code`, `Documentation`, `Bug Tracker`, `Changelog`, `Discussions`, and `Chat`. It's a free-form name, though many common names get recognized and have nice icons on PyPI.

```toml
# Inline form
urls.Homepage = "https://pypi.org"
urls."Source Code" = "https://pypi.org"

# Sectional form
[project.urls]
Homepage = "https://pypi.org"
"Source Code" = "https://pypi.org"
```

### Classifiers

This is a collection of classifiers as listed at <https://pypi.org/classifiers/>. You select the classifiers that match
your projects from there. Usually, this includes a "Development Status" to tell users how stable you think your project is, and a few things like "Intended Audience" and "Topic" to help with search engines. There are some important ones though: the "License" (s) is used to indicate your license. You also can give an idea of supported Python versions, Python implementations, and "Operating System"s as well. If you have statically typed Python code, you can tell users about that, too.

```toml
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

> ## Prevent Inadvertent Publishing
> By adding the "Private :: Do Not Upload" trove classifier here, we ensure that
> the package will be
> [rejected when we try to upload it to PyPI](https://pypi.org/classifiers/#:~:text=To%20prevent%20a%20package%20from,beginning%20with%20%22Private%20%3A%3A%22.).
> If you want to upload to PyPI, you will need to remove that classifier.
{:.callout}

### License (special mention)

There also is a license field, but that was rather inadequate; it didn't support
multiple licenses, for example. Currently, it's best to indicate the license
with a Trove Classifier, and make sure your file is called `LICENSE*` so build
backends pick it up and include it in SDist and wheels. There's work on
standardizing an update to the format in the future. You can manually specify a
license file if you want:

```toml
license = {file = "LICENSE"}
```

However, some backends (like flit-core) ignore this field entirely. The
canonical location for a standard license since the early 2000's has been trove
classifiers.

> ## Verify file contents
> Always verify the contents of your SDist and Wheel(s) manually to make sure the license file is included.
>
> ```bash
> tar -tvf dist/package-0.0.1.tar.gz
> unzip -l dist/package-0.0.1-py3-none-any.whl
> ```
{:.callout}

## Functional metadata

The remaining fields actually change the usage of the package.

### Requires-Python

This is an important and sometimes misunderstood field. It looks like this:

```toml
requires-python = ">=3.8"
```

Pip will check if the Python version of the environment where the package being installed passes this expression. If it doesn't, pip will start checking older versions of the package until it finds one that passes. This is how `pip install numpy` still works on Python 3.7, even though NumPy has already dropped support for it.

You need to make sure you always have this line and that it stays accurate, since you can't edit metadata after releasing - you can only yank or delete release(s) and try again.

> ## Upper caps
>
> Upper caps are generally discouraged in the Python ecosystem, but they are (even more than usual) broken when used with `requires-python` field. This field was added to help users drop old Python versions, and the idea it would be used to restrict newer versions was not considered. The above field is not the right one to set an upper cap! Never upper cap this field and instead use Trove Classifiers to tell users what versions of Python your code was tested with.
{:.callout}

### Dependencies

Your package likely will need other packages from PyPI to run.

```toml
dependencies = [
  "numpy>=1.18",
]
```

You can list dependencies here without minimum versions, but if you have a lot of users, you might want minimum versions; pip will only upgrade an installed package if it's no longer viable via your requirements. You can also use a variety of markers to specify operating system specific packages.

> ## project.dependencies vs. build-system.requires
>
> What is the difference between `project.dependencies` vs. `build-system.requires`?
>
> > ## Answer
> >
> > `build-system.requires` describes what your project needs to "build", that is, produce an SDist or wheel. Installing a built wheel will _not_ install anything from `build-system.requires`, in fact, the `pyproject.toml` is not even present in the wheel! `project.dependencies`, on the other hand, is added to the wheel metadata, and pip will install anything in that field if not already present when installing your wheel.
{:.challenge}

### Optional Dependencies

Sometimes you have dependencies that are only needed some of the time. These can be specified as optional dependencies. Unlike normal dependencies, these are specified in a table, with the key being the option you pass to pip to install it. For example:

```toml
[project.optional-dependenices]
test = ["pytest>=6"]
check = ["flake8"]
plot = ["matplotlib"]
```

Now, you can run `pip install 'package[test,check]'`, and pip will install both the required and optional dependencies `pytest` and `flake8`, but not `matplotlib`.

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
name = "package"
dynamic = ["version"]

[tool.hatch]
version.path = "src/package/__init__.py"
```

## All together


Now let's take our previous example and expand it with more information. Here's an example:

```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "package"
version = "0.0.1"
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

[project.urls]
"Homepage" = "https://github.com/pypa/sampleproject"
"Bug Tracker" = "https://github.com/pypa/sampleproject/issues"
````

> ## Add metadata and check it.
> Take your existing package and add more metadata to it. Install it, then use
> `pip show -v <package>` to see the metadata. You can also look inside the wheel
> or SDist to see the metadata.
>
> > ## Solution
> > ```bash
> > pip install -e .
> > pip show -v <package-name>
> > ```
> {:.solution}
{:.challenge}


{% include links.md %}
