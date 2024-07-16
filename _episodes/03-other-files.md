---
title: "Other files that belong with your package"
teaching: 15
exercises: 0
questions:
- "What other files are important parts of your software package?"
objectives:
- "Create a README for a software package"
- "Add a software license to a software package"
- "Create a changelog for a package"
keypoints:
- "In addition to the source code and project specification, packages should include a README, LICENSE, and changelog."
- "Do not create a custom software license or modify an existing license; instead, choose from the list of available licenses."
- "You can also include `.gitignore` to avoid from committing non-source files."
---

We now have an installed, working Python package that provides some functionality.
Are we ready to push the code to GitHub (or your preferred code hosting service) for others to use and contribute to?
🛑 Not quite—we need to add a few more files at minimum to describe our package, and to actually make it open-source software.

Aside from the name of the package and docstring included with the (single) function, we haven't yet provided any description or other information about the package for anybody that comes across it.

We also haven't specified the terms and conditions under which the software may be downloaded, used, and/or modified.
This means that if we posted it online right now, due to copyright laws (in the United States, at least) nobody else would actually be able to use or modify the code, since we haven't given explicit permission to do so.

Lastly, as you continue working on your package, you will likely fix bugs and modify/add/remove functionality. Although these changes will *technically* be present in your Git logs—because you are committing regularly and writing descriptive commit messages, right? 😉—you should also maintain a file that describes these changes in a human-readable way.

## Creating a README

A README is a plaintext file that sits at the top level of your package (next to the `src`, `tests`, `docs` directories and `pyproject.toml` file) and provides general information about your software.
Modern READMEs are typically written in Markdown, or occasionally reStructuredText (ReST), due to the additional formatting options that services like GitHub nicely render.

A README is a form of software documentation, and should contain at minimum:
 - the name of your software package
 - a brief description of what your software does or provides
 - installation instructions
 - a brief usage example
 - the type of software license (with more information in a separate `LICENSE` file, described next)

In addition, a README may also contain:
 - badges near the top that quickly show key information, such as the latest version, whether the tests are currently passing
 - information about how people can contribute to your package
 - a [code of conduct](https://www.contributor-covenant.org) for people interacting around your project (in GitHub Issues or Pull Requests, for example)
 - contact information for authors and/or maintainers

Create a README using

```bash
$ touch README.md
```

and then add these elements:

````markdown
# Package

`package` is a simple Python library that contains a single function for rescaling arrays.

## Installation

Download the source code and use the package manager [pip](https://pip.pypa.io/en/stable/) to install `package`:

```bash
pip install .
```

## Usage

```python
import numpy as np
from package.rescale import rescale

# rescales over 0 to 1
rescale(np.linspace(0, 100, 5))
```

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.

## License
TBD
````

You can see more guidance on creating READMEs at <https://www.makeareadme.com>.

> ## Keep your READMEs relatively brief
>
> You should try to keep your README files *relatively* brief, rather than including
> very detailed documentation to this file. This should only be a high-level introduction,
> with detailed theory, examples, and other information reserved for a true documentation
> website.
{:.callout}

## Choosing a software license

Now, your package includes a README file, which tells someone who finds the source code a bit about how to use your project and also contribute to it.
However, you still need one more element before uploading it to GitHub or another code-hosting service: a **software license** that explicitly gives specific permissions to users and contributors.
Simply making your project available publicly is not the same as making it an open-source software project.

By default, when you make a creative work such as software (but also including writing and images), your work is under exclusive copyright.
Others cannot use, copy, share/distribute, or modify your work without your permission.
This is often a good thing, because it means you can put your work out into the world, and copyright protects you as the creator and owner of the work.
Open Source Guides has more about the [legal side of open source software](https://opensource.guide/legal/).

However, if you have created research software and plan to share it openly, you *want* others to use your software, and possibly contribute to it. (Who doesn't love having other people fix the bugs in their code?)

A software license provides the explicit permissions for others to use, modify, or share your code, and lays out the specific rules for any restrictions about how they can do those things.
To pick a license, use resources like [Choose a License](https://choosealicense.com) or [Civic Commons "Choosing a License"](http://wiki.civiccommons.org/Choosing_a_License/) based on how you want others to interact with your software.
You can also see the [full list of open-source licenses](https://opensource.org/licenses/category) approved by the Open Source Initiative, which maintains the Open Source Definition.

For a new project, you essentially have one major choice to make:
 1. Do you want to allow others to use your software in almost any way they want, or
 2. Do you want to require others to share any uses of your project in an open way?

These two categories are "permissive" and "copyleft" licenses.
Common permissive licenses include the [MIT License](https://choosealicense.com/licenses/mit/) and [BSD 3-Clause License](https://choosealicense.com/licenses/bsd-3-clause/).
The [GNU General Public License v3.0 (or GNU GPLv3) License](https://choosealicense.com/licenses/gpl-3.0/) is a common copyleft license.

Most research software uses permissive licenses like the:
- [BSD 3-Clause License](https://choosealicense.com/licenses/bsd-3-clause/)
  which includes a specific clause preventing the names of creators/contributors
  from being used to endorse or promote derivatives, without permission,
- [MIT License](https://choosealicense.com/licenses/mit/), or the
- [Apache License 2.0](https://choosealicense.com/licenses/apache-2.0/).

In addition, when working on a project with others or as part of a larger effort, you should check if your collaborators have already determined an appropriate license; for example, on work funded by a grant, a particular license may be mandated by the proposal/agreement.

Create a `LICENSE` file using

```bash
$ touch LICENSE
```

and copy the **exact** text of the license you chose, modifying only the year and names.

For instance, for the [BSD 3-Clause License](https://choosealicense.com/licenses/bsd-3-clause/):

```
BSD 3-Clause License

Copyright (c) [year], [fullname]

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

1. Redistributions of source code must retain the above copyright notice, this
   list of conditions and the following disclaimer.

2. Redistributions in binary form must reproduce the above copyright notice,
   this list of conditions and the following disclaimer in the documentation
   and/or other materials provided with the distribution.

3. Neither the name of the copyright holder nor the names of its
   contributors may be used to endorse or promote products derived from
   this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
```

That's it!

> ## Do not write your own license!
>
> You should never try to write your own software license, or modify the text of an existing
> license.
>
> Although *we* are not lawyers, the licenses approved and maintained by the Open
> Source Initiative have gone through a rigorous review process, including legal review,
> to ensure that they are both consistent with the Open Source Definition and also are
> legally valid.
{:.callout}

> ## Adding a license badge to README
>
> Badges are a fun and informative way to quickly show information about your software package
> in the README. [Shields.io](https://shields.io) is a resource for generating badge images
> in SVG format, which can easily be added to the top of your README as links pointing to more
> information.
>
> Using [Shields.io](https://shields.io) (or an example found elsewhere), generate the Markdown
> syntax for adding a badge describing the BSD 3-Clause License we chose for this example package.
>
> > ## Solution
> >
> > The Markdown syntax for adding a badge describing the BSD 3-Clause License is:
> >
> > ```markdown
> > [![License](https://img.shields.io/badge/license-BSD-green.svg)](https://opensource.org/licenses/BSD-3-Clause)
> > ```
> > and will render as [![License](https://img.shields.io/badge/license-BSD-green.svg)](https://opensource.org/licenses/BSD-3-Clause)
> {: .solution}
{: .challenge}

## Keeping a CHANGELOG

Over time, our package will likely evolve, whether through bug fixes, improvements, or
feature changes. For example, the `rescale` function in our package does not have a way
of properly treating cases where the max and min of the array are the same (i.e., when
the array holds the same number repeated). For example:

```python
import numpy as np
from package.rescale import rescale

a = 2 * np.ones(5)
rescale(a)
```

gives

```
rescale.py:11: RuntimeWarning: invalid value encountered in divide
  output_array = (input_array - low) / (high - low)
array([nan, nan, nan, nan, nan])
```
{: .output}

This is probably not the desired output; instead, let's say we want to rescale all the
values in this array to 1. We can modify the function to properly handle this situation:

```python
def rescale(input_array):
    """Rescales an array from 0 to 1.

    Takes an array as input, and returns a corresponding array scaled so that 0
    corresponds to the minimum and 1 to the maximum value of the input array.
    """
    low = np.min(input_array)
    high = np.max(input_array)
    if np.allclose(low, high):
        output_array = input_array / low
    else:
        output_array = (input_array - low) / (high - low)
    return output_array
```

Now, when we call rescale (no need to reinstall or upgrade the package, since we previously installed using editable mode):

```python
import numpy as np
from package.rescale import rescale

a = 2 * np.ones(5)
rescale(a)
```

we get the desired behavior:

```
array([1., 1., 1., 1., 1.])
```
{: .output}

Great! Let's commit that change using Git, with a message and perhaps update the version to 0.1.1 to
indicate the package has changed (more to come on that in a later episode on versioning).

That may be enough for us to record the change, but how will a user of your package know that the
functionality has changed? It's not exactly easy to hunt through Git logs and try to find which
commit message(s) align with the changes since the last version.

Instead, we can [keep a changelog](https://keepachangelog.com) in a `CHANGELOG.md` file,
also at the top level of your package's directory. In this Markdown-formatted file, you should
record major changes to the package made since the last released version. Then, when you decide
to release a new version, you add a new section to the file above this list of changes.

Changes should be grouped together based on the type; suggestions for these come from the
[Keep a Changelog project](https://keepachangelog.com/en/1.0.0/) by
[Olivier Lacan](http://olivierlacan.com/):
 - `Added` for new features,
 - `Changed` for changes in existing functionality,
 - `Deprecated` for soon-to-be removed features,
 - `Removed` for now-removed features,
 - `Fixed` for any bug fixes, and
 - `Security` in case of vulnerabilities.

For example, our initial release was version 0.1.0, and we have now changed the functionality.
Our CHANGELOG should look something like:

```markdown
# Changelog
All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## Unreleased
### Changed
- rescale function now scales constant arrays to 1

## [0.1.0] - 2022-08-09
### Added
- Created rescale() function and released package
```

If at this point you want to increment the version to 0.1.1 to indicate this small fix
to the behavior, you would add a new section for this version:

```markdown
## Unreleased

## [0.1.1] - 2022-08-10
### Changed
- rescale function now scales constant arrays to 1

## [0.1.0] - 2022-08-09
### Added
- Created rescale() function and released package
```

Note that the version numbers are shown as links in these examples, although the links
are not included in the file snippets. You should add definitions of these links
at the bottom of the file, using (for example) GitHub's ability to compare between
tagged versions:

```markdown
[0.1.1]: https://github.com/<username>/package/compare/v0.1.1...v0.1.0
```

> ## Other locations
>
> Once we have things inside the `docs/` folder, `docs/changelog.md` is also a
> good place, and keeps your outer directory a bit cleaner. But we'll put it
> here for now so that we don't interfere with that lesson.
{:.callout}

> ## Automating Changelog Management
> There are several tools which are intended to help manage a changelog.
> Broadly speaking, they use specifically formatted commit messages
> to generate the changelog.
> They usually impose a specific way of working,
> require consistency and discipline when writing the messages,
> often need manual tweaking of the changelog after generation.
>
> ### `commitizen`, "conventional commits" and SemVer
> [`commitizen`][commitizen] is a release management tool which
> helps developers to write [conventional commits][]
> and can generate a grouped and sorted changelog (`commitizen changelog`).
> Generating a new release, tagging it with an updated version number,
> and generating a changelog
> can be automated using [GitHub Actions][commitizen auto release].
>
> This requires:
> - squashing each change into a single commit,
> - consistency and discipline when writing commit messages.
>
> ### GitHub
> GitHub can "generate release notes" with each release of code,
> which is a list of the titles of the **pull requests** included in the release.
> People can view the release notes on GitHub.
>
> These release notes:
> - only appear on GitHub,
> - aren't automatically grouped and sorted,
> - only include changes which were part of a pull request title.
>
{:.callout}

[conventional commits]: https://www.conventionalcommits.org/
[commitizen]: https://commitizen-tools.github.io/commitizen/
[commitizen auto release]: https://commitizen-tools.github.io/commitizen/tutorials/github_actions/

## Additional files for Git

At this point, your package has most of the supplemental files that it needs to be
shared with the world. However, there are some additional files you can add to help
with your Git workflow.

### .gitignore

After adding and committing the files above, you might have noticed that `git status`
points out a few files/directories that you do not want it to track:

```
On branch main
Untracked files:
  (use "git add <file>..." to include in what will be committed)
	__pycache__/
	src/package/__pycache__/
	tests/__pycache__/

nothing added to commit but untracked files present (use "git add" to track)
```
{: .output}

Fortunately, you can instruct Git to ignore these files, and others that you will never
want to track, using a `.gitignore` file, which goes at the main directory level of your package.

This file tells Git to ignore either specific files or directories, or those that match a certain
pattern via the wildcard character (e.g., `*.so`). The [Git reference manual](https://git-scm.com)
has very [detailed documentation](https://git-scm.com/docs/gitignore) of possible `.gitignore`
file syntax, but for convenience GitHub maintains a
[collection of `.gitignore` files](https://github.com/github/gitignore) for various languages
and tools.

For your project, you should copy or download the [Python-specific `.gitignore` file](https://github.com/github/gitignore/blob/main/Python.gitignore) file into a local `.gitignore`:

```
# Byte-compiled / optimized / DLL files
__pycache__/
*.py[cod]
*$py.class

# C extensions
*.so

# Distribution / packaging
.Python
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
share/python-wheels/
*.egg-info/
.installed.cfg
*.egg
MANIFEST

# PyInstaller
#  Usually these files are written by a python script from a template
#  before PyInstaller builds the exe, so as to inject date/other infos into it.
*.manifest
*.spec

# Installer logs
pip-log.txt
pip-delete-this-directory.txt

# Unit test / coverage reports
htmlcov/
.tox/
.nox/
.coverage
.coverage.*
.cache
nosetests.xml
coverage.xml
*.cover
*.py,cover
.hypothesis/
.pytest_cache/
cover/

# Translations
*.mo
*.pot

# Django stuff:
*.log
local_settings.py
db.sqlite3
db.sqlite3-journal

# Flask stuff:
instance/
.webassets-cache

# Scrapy stuff:
.scrapy

# Sphinx documentation
docs/_build/

# PyBuilder
.pybuilder/
target/

# Jupyter Notebook
.ipynb_checkpoints

# IPython
profile_default/
ipython_config.py

# pyenv
#   For a library or package, you might want to ignore these files since the code is
#   intended to run in multiple environments; otherwise, check them in:
# .python-version

# pipenv
#   According to pypa/pipenv#598, it is recommended to include Pipfile.lock in version control.
#   However, in case of collaboration, if having platform-specific dependencies or dependencies
#   having no cross-platform support, pipenv may install dependencies that don't work, or not
#   install all needed dependencies.
#Pipfile.lock

# poetry
#   Similar to Pipfile.lock, it is generally recommended to include poetry.lock in version control.
#   This is especially recommended for binary packages to ensure reproducibility, and is more
#   commonly ignored for libraries.
#   https://python-poetry.org/docs/basic-usage/#commit-your-poetrylock-file-to-version-control
#poetry.lock

# pdm
#   Similar to Pipfile.lock, it is generally recommended to include pdm.lock in version control.
#pdm.lock
#   pdm stores project-wide configurations in .pdm.toml, but it is recommended to not include it
#   in version control.
#   https://pdm.fming.dev/#use-with-ide
.pdm.toml

# PEP 582; used by e.g. github.com/David-OConnor/pyflow and github.com/pdm-project/pdm
__pypackages__/

# Celery stuff
celerybeat-schedule
celerybeat.pid

# SageMath parsed files
*.sage.py

# Environments
.env
.venv
env/
venv/
ENV/
env.bak/
venv.bak/

# Spyder project settings
.spyderproject
.spyproject

# Rope project settings
.ropeproject

# mkdocs documentation
/site

# mypy
.mypy_cache/
.dmypy.json
dmypy.json

# Pyre type checker
.pyre/

# pytype static type analyzer
.pytype/

# Cython debug symbols
cython_debug/

# PyCharm
#  JetBrains specific template is maintained in a separate JetBrains.gitignore that can
#  be found at https://github.com/github/gitignore/blob/main/Global/JetBrains.gitignore
#  and can be added to the global gitignore or merged into this file.  For a more nuclear
#  option (not recommended) you can uncomment the following to ignore the entire idea folder.
#.idea/
```

You can see that a few patterns are commented out, and can be uncommented if they apply to
your project and/or workflow. You can also clean up sections of the file that do not apply
to your situation, but there's no real need to do so since you likely won't look at this
file again.

Once you have added this to the top level of your project (alongside the `.git` directory),
and told Git to track it (`git add .gitignore`, `git commit -m 'adds gitignore file'`),
Git will automatically begin following your rules:

```bash
$ git status
```

```
On branch main
nothing to commit, working tree clean
```
{: .gitignore}

## Summary

At this point, if you have added all of these files, your package's file structure
should look something like this:

```bash
.
├── .git/
├── .venv/
├── .gitignore
├── docs/
├── pyproject.toml
├── src/
│   └── package/
│   │   ├── __init__.py
│   │   └── rescale.py
└── tests/
    └── test_rescale.py
```

The `.git` and `.venv` directories would have been automatically generated
by Git and Virtualenv respectively. You may also see additional directories
like `__pycache__` and `.pytest_cache`.

{% include links.md %}
