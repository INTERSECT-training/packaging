---
title: "Checks and tests"
teaching: 10
exercises: 10
questions:
- "How do you ensure your code will work well?"
objectives:
- "Learn about the basics of setting up tests."
- "Learn about the basics of setting up static checks"
keypoints:
- "Run tests and static checks on your codebase."
---

In this episode we'll give an introduction to setting up your project for
- tests with `pytest`, and
- static checks (also known as linters, formatters, and type checkers).

## Testing

The most popular testing framework is pytest, so we will focus on that. Python
has a built-in framework too, but it's really intended for Python's own tests,
and adding a dependency for testing only is fine even for the most strict
no-dependencies allowed packages, since users don't need tests. Tests should be as easy
to write as possible, and pytest handles that beautifully.

### Test directory

There are several options for test directory. The recommendation is `/tests`
(with an `s`), at the root of your repository. Combined with `/src/<package>`
layout, you will have the best experience avoiding weird edge cases with package
importing.

> ## User runnable tests
>
> Tests should distributed with your SDist, but not your wheel. Sometimes, you
> might want some simple Tests a user can run in order to verify that their
> system works. Adding a `/src/<package>/tests` module using Python's `unittest`
> that does some very quick checks to validate the package works is fine (though
> it should not be your entire test suite!).
{:.discussion}

### Pytest configuration

A recommended pytest configuration in your `pyproject.toml` is:

```toml
[tool.pytest.ini_options]
minversion = "7.0"
addopts = ["-ra", "--showlocals", "--strict-markers", "--strict-config"]
xfail_strict = true
filterwarnings = ["error"]
log_cli_level = "info"
testpaths = [
  "tests",
]
```

- The `minversion` will print a nicer error if your `pytest` is too old.
- The `addopts` setting will add whatever you put there to the command line when you run;
  - `-ra` will print a summary "r"eport of "a"ll results,
    which gives you a quick way to review what tests failed and were skipped, and why.
  - `--showlocals` will print locals in tracebacks.
  - `--strict-markers` will make sure you don't try to use an unspecified fixture.
  - And `--strict-config` will error if you make a mistake in your config.
- `xfail_strict` will change the default for `xfail` to fail the
tests if it doesn't fail - you can still override locally in a specific xfail
for a flaky failure.
- `filter_warnings` will cause all warnings to be errors
(you can add allowed warnings here too, see below).
- `log_cli_level` will report `INFO` and above log messages on a failure.
- Finally, `testpaths` will limit `pytest` to just looking in the folders given - useful if it tries to pick up
things that are not tests from other directories.

[See the docs](https://docs.pytest.org/en/stable/customize.html) for more options.

pytest also checks the current and parent directories for a `conftest.py` file.
If it finds them, they will get run outer-most to inner-most. These files let
you add fixtures and other pytest configurations (like hooks for test discovery,
etc) for each directory. For example, you could have a "mock" folder, and in
that folder, you could have a `conftest.py` that has a mock fixture with
`autouse=True`, then every test in that folder will get this mock applied.

In general, do not place a `__init__.py` file in your tests; there's not often a
reason to make the test directory importable, and it can confuse package
discovery algorithms. You can use `pythonpath=["tests/utils"]` to allow you to
import things inside a `tests/utils` folder - though many things can be added to
`conftest.py` as fixtures.

Python hides important warnings by default, mostly because it's trying to be
nice to users. If you are a developer, you don't want it to be "nice". You want
to find and fix warnings before they cause user errors! Locally, you should run
with `-Wd`, or set `export PYTHONWARNINGS=d` in your environment. The `pytest`
warning filter "error" will ensure that `pytest` will fail if it finds any
warnings. You can list warnings that should be hidden or just shown without
becoming errors using the syntax
`"<action>:Regex for warning message:Warning:package"`, where `<action>` can
tends to be `default` (show the first time) or `ignore` (never show). The regex
matches at the beginning of the error unless you prefix it with `.*`.


## Static checks

In addition to tests, which run your code, there are also static checkers that
look for problems or format your code without running it. While tests only check
the parts of the code you write tests for, and only the things you specifically
think to check, static checkers can verify your entire codebase is free of
certain classes of bugs. Unlike a compiled language, like C, C++, or Rust, there
is no required "compile" step, so think of this like that - an optional step you
can add that can find things that don't make sense, invalid syntax, etc.

### The pre-commit framework

There's a tool called pre-commit that is used to run static checks. (Technically
it can run just about anything, but it's designed around speed and works best
with checks that take under a couple of seconds - perfect for static checks.)

You can install pre-commit with `pipx`, `pip`, your favorite package manager, or
even run it inside `nox`.

You run pre-commit like this:

```bash
pre-commit run --all-files
```

This runs pre-commit on all files; the default is to just check staged changes
for speed. As you might have guessed from the name, you can also make pre-commit
run as a git pre-commit hook, with `pre-commit install`. You can also keep your
pre-commit config up to date with `pre-commit autoupdate`.

You can add pre-commit checks inside a `.pre-commit-config.yaml` file. There are some
"standard" checks most projects include:

```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: "v4.6.0"
    hooks:
      - id: check-added-large-files
      - id: check-case-conflict
      - id: check-merge-conflict
      - id: check-symlinks
      - id: check-yaml
      - id: debug-statements
      - id: end-of-file-fixer
      - id: mixed-line-ending
      - id: requirements-txt-fixer
      - id: trailing-whitespace
```

There are a few things to dissect here. First, we have a `repos` table. This
holds a list of git repositories pre-commit will use. They each have `repo`
(pointing at the repo URL), a `rev`, which holds a _non-moving_ tag (pre-commit
caches environments based on this tag), and a `hooks` table that holds the hooks
you want to use from that repo.

You can look at the docs (or the `pre-commit-hooks.yaml` file in the repo you
are using!) to see what `id`'s you can select. There are more options as well -
in fact, every pre-defined field can be overridden by providing the field when
you use the hook.

The checks above, from the first-part `pre-commit/pre-commit-hooks` repo, are
especially useful in the "installed" mode (where only staged changes are
checked).

### Black

The defacto standard today for code formatting is Black. Unlike other
auto-formatters, Black has very, very few configuration points - it's generally
easy to recognized blacked code.  And once you are use to it, it is _faster_ to
read blacked code, as you can start relying on indentation patterns around
brackets and such. And while no standard will please everyone, Black's style was
designed around minimizing conflicts when merging (which is already a benefit
with any formatter), which was a great practical choice.

To use Black in pre-commit:

```yaml
  - repo: https://github.com/psf/black
    rev: "24.4.2"
    hooks:
      - id: black
```

While you can turn formatting on and off inline, generally try to write your
code so that it formats well.  If something looks bad, maybe break it into two
operations, using a variable. You'll likely find yourself writing better code
with Black.

Also, an auto-formatter helps highlight errors in your code. If you forget a
comma in a list, causing strings to auto-concatenate, the blacked form often
makes it much easier to spot the error; this is just one example.

For some examples of black, see the [Black Playground](https://black.vercel.app/)

### Ruff

Ruff has recently exploded as the most popular linting tool for Python, and it's
easy to see why. It's tens to hundreds of times faster than similar tools like
flake8, and has dozens of popular flake8 plugins and other tools (like isort and
pyupgrade) all well maintained and shipped in a single Rust binary. It is highly
configurable in a modern configuration format (in `pyproject.toml`!). And it
supports auto-fixes, something common outside of Python, but rare in the Python
space before now.

To use Ruff from pre-commit:

```yaml
- repo: https://github.com/charliermarsh/ruff-pre-commit
  rev: "v0.5.2"
  hooks:
    - id: ruff
      args: ["--fix", "--show-fixes"]
```

And you'll want a bit of configuration in your `pyproject.toml`:

```toml
[tool.ruff]
src = ["src"]
lint.extend-select = [
  "B",           # flake8-bugbear
  "I",           # isort
  "PGH",         # pygrep-hooks
  "RUF",         # Ruff-specific
  "UP",          # pyupgrade
]
```


> ## Replacement for Black
> You can add an additional hook, `- id: ruff-format` which is meant to be a drop-in replacement for `black`.
> Remove `black`'s pre-commit configuration if you do.
{:.callout}

You can a more complete suggested config at the
[Scientific-Python Development Guide](https://learn.scientific-python.org/development/guides/style/#ruff).


### MyPy

The biggest advancement since the development of Python 3 has been the addition of _optional_ static typing.
Static checks in Python have a _huge_ disadvantage vs. a more "production" focused language like C++: you can't
tell what types things are most of the time! For example, is this function well defined?

```python
def bit_count(x):
    return x.bit_count()
```

A static checker _can't_ tell you, since it depends on how it is called. `bit_count("hello")` is an error, but you
won't know that until it runs, hopefully in a test somewhere. However, now contrast that with this version:

```python
def bit_count(x: int) -> int:
    return x.bit_count()
```

Now this is well defined; a type checker will tell you that this function is
valid (and it will even be able to tell you it is invalid if you target any
Python before 3.10, regardless of the version you are using to run the check!),
and it will tell you if you try to call it with anything that's not an `int`,
anywhere - regardless if the function is part of a test or not!

You do have to add static types to function signatures and a _few_ variable
definitions (usually variables can be inferred automatically), but the payoff is
well worth it - a static type checker can catch many things, and doesn't require
writing tests!

Here's how you run mypy, a first party and popular type checker, from pre-commit:

```yaml
- repo: https://github.com/pre-commit/mirrors-mypy
  rev: "v1.10.0"
  hooks:
    - id: mypy
      files: src
      args: []
```

You will need to add `additional_dependencies: [numpy]` as the pre-commit `mypy`
runs in a separate virtual environment which doesn't have `numpy` installed.

```
  hooks:
    - id: mypy
      files: src
      args: []
      additional_dependencies: [numpy]
```

You can learn about configuring mypy in the
[Scientific-Python Development Guide](https://learn.scientific-python.org/development/guides/mypy/).  You also
need to add any packages that have static types to `additional_dependencies: [...]`.

### Going further

See the Style guide at
[Scientific-Python Development Guide](https://learn.scientific-python.org/development/guides/style)
for a lot more suggestions on static checking.


{% include links.md %}
