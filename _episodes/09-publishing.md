---
title: "Publishing package and citation"
teaching: 10
exercises: 5
questions:
- "How do I publish a package?"
- "How do I make my work citable?"
objectives:
- "Learn about publishing a package on PyPI"
- "Learn about making work citable"
keypoints:
- "CI can publish Python packages"
- "Tagging and GitHub Releases are used to publish versions"
- "Zenodo and CITATION.cff are useful for citations"
---

There are two major formats used to publish python packages:
- **source distribution** (`sdist` for short):
  - Contains most of your repository, including tests, and
  - Requires your build backend (hatchling, in this case) to build.
- **wheel**:
  - The wheel is "built", the
    contents are ready to unpack into standard locations (usually site-packages),
    and does not contain configuration files like `pyproject.toml`.
  - Usually you do not include things like tests in the wheel.
  - Wheels also can contain binaries for packages with compiled portions.


## Building SDists and wheels

You can build an SDist and a wheel (from that SDist) with `pipx` and the `build` package:

```bash
pipx run build
```

The module is named build, so `python -m build` is how you'd run it from nox.
The executable is actually named `pyproject-build`, since installing a `build`
executable would likely conflict with other things on your system.

This produces the wheel and sdist in `./dist`.

You can validate the files generated using
```bash
pipx run twine check dist/*
```

> ## Conda
>
> Building for conda is quite different. If you just have a pure Python package, you
> should just use pip to install in conda environments until you have a conda package
> that depends on your package and wants to add it into it's requirements.
>
> If you do need to build a conda package, you'll need to either
> [propose a new recipe](https://github.com/conda-forge/staged-recipes) to
> conda-forge, or set up the build infrastructure yourself and publish to an
> anaconda.org channel.
{:.callout}


## Manually publishing

> ## Do you need to publish to PyPI?
>
> Not every package needs to go on PyPI. You can pip install directly from
> git, or from a URL to a package hosted somewhere else, or you can set up
> your own wheelhouse and point pip at that. Also an "application" like a
> website or other code you deploy probably does not need to be on PyPI.
{:.discussion}

You can publish files manually with `twine`:

```bash
pipx run twine upload -r testpypi dist/*
```

The `-r testpypi` tells twine to upload to TestPyPI instead of the real PyPI -
remove this if you are not in a tutorial.

> To run this locally, you'll also need to setup an API token to upload the package with.
> Create a token at [https://test.pypi.org/manage/account/](https://test.pypi.org/manage/account/).
{:.callout}

However, the best way to publish is from CI. This has
several benefits: you are always in a clean checkout, so you won't accidentally
include added or changed files, you have a simpler deployment procedure, and
you have more control over who can publish in GitHub.

> ## Create a noxfile to build
>
> Given what you've learned about `nox` and `build`, write a session that builds
> packages for you.
>
> > ## Solution
> > ```python
> > import nox
> >
> > @nox.session()
> > def build(session):
> >     session.install("build")
> >     session.run("python", "-m", "build")  # can use pyproject-build instead
> > ```
> {:.solution}
{:.challenge}

## Building in GitHub Actions

GitHub Actions can be used for any sort of automation task, not just building
tests. You can use it to make your releases too! Combined with the version
control feature from the previous lesson, making a new release can be a simple
procedure.

Let's first set up a job that builds the file in a new workflow:

```yaml
# .github/workflows/publish.yml
on:
  workflow_dispatch:
  release:
    types:
    - published
```

This has two triggers. The first, `workflow_dispatch`, allows you to manually trigger the
workflow from the GitHub web UI for testing. The second will trigger whenever you make a
GitHub Release, which will be covered below. You might want to add builds for your main branch,
as well. We will make sure uploads to PyPI only happen on releases later.

Now, we need to set up the builder job:

```yaml
jobs:
  dist:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
      with:
        fetch-depth: 0

    - name: Build SDist & wheel
      run: pipx run build

    - uses: actions/upload-artifact@v4
      with:
        path: dist/*
```

We've seen the setup before. We are calling the job `dist`, using an Ubuntu
runner, and checking out the code, including the git history so the version can
be computed with `fetch-depth: 0` (which can be removed if you are not using git
versioning).

> ## Test and upload action
>
> There's a great action for building and inspecting a pure Python package:
> ```yaml
> - uses: hynek/build-and-inspect-python-package@v2
> ```
> This action builds, runs various checkers, then uploads the package to `Packages`.
> If you use this, you'll need to download the artifact from `name: Packages`.
{:.callout}

The next step builds the wheel and SDist. Pipx is a supported package manager on
all GitHub Actions runners.

The final step uploads an Actions "artifact". This allows you to download the
produced files from the GitHub Actions UI, and these files are also available to
other jobs. The default name is `artifact`, which is as good as any other name
for the moment.

We could have combined the build and publish jobs if we really wanted to, but
they are cleaner when separate, so we have a publish job as well.

{% raw %}
```yaml
publish:
  needs: [dist]
  runs-on: ubuntu-latest
  if: github.event_name == 'release' && github.event.action == 'published'

  steps:
  - uses: actions/download-artifact@v4
    with:
      name: artifact
      path: dist

  - uses: pypa/gh-action-pypi-publish@release/v1
    with:
      repository-url: https://test.pypi.org/legacy/
```

This job requires that the previous job completes successfully with `needs:`. It
has an `if:` block as well that ensures that it only runs when you publish. Note
that Actions usually requires `${{ ... }}` to evaluate code, like
`github.event_name`, but blocks that always are evaluated, like `if:`, don't
require manually wrapping in this syntax.
{% endraw %}

Then we download the artifact. You need to tell it the `name:` to download
(otherwise it will download all artifacts into named folders). We used the
default `artifact` so that's needed here. We want to unpack it into `./dist`, so
we set the `path:` to that.

Finally, we use the PyPA's publish action. You will need to go to PyPI and tell
it where you are publishing from so that the publish can happen via PyPI's
trusted publishers. We are using Test PyPI for this exercise - remove the
`with:` block to publish to real PyPI.

## Making a release

A release on GitHub corresponds to two things: a git tag, and a GitHub Release.
If you create the release first, a lightweight tag will be generated for you.
If you tag manually, remember to create the GitHub release too, so users can see
the most recent release in the UI and will be notified if they are watching your
releases.

Click Releases -> Draft a new release. Type in or select a tag; the recommended
format is `v1.2.3`; that is, a "v" followed by a version number. Give it a
title, like "Version 1.2.3"; keep this short so that it will be readable on the
web UI.  Finally, fill in the description (there's an autogenerate button that
might be helpful).

When you release, this will trigger the GitHub Action workflow we developed and
upload your package to TestPyPI!

## Digital Object Identifier (DOI)

You can add a repository to <https://zenodo.org> to get a DOI once you publish. Follow the instructions in the
[GitHub Documentation](https://docs.github.com/en/repositories/archiving-a-github-repository/referencing-and-citing-content).

To test the functionality, you can use the [Zenodo Sandbox](https://sandbox.zenodo.org/).

## The CITATION.cff file

From [https://citation-file-format.github.io/](https://citation-file-format.github.io/):
> `CITATION.cff` files are plain text files with human- and machine-readable citation information for software (and datasets).
> Code developers can include them in their repositories to let others know how to correctly cite their software.

This file format is becoming a de-facto standard, and is supported by GitHub, Zenodo and Zotero.

The CITATION.cff file looks like this:

```yaml
cff-version: 1.2.0
message: "If you use this software, please cite it as below."
authors:
  - family-names: Druskat
    given-names: Stephan
    orcid: https://orcid.org/1234-5678-9101-1121
title: "My Research Software"
version: 2.0.4
identifiers:
  - type: doi
    value: 10.5281/zenodo.1234
date-released: 2021-08-11
```

You can validate your file by running:

```bash
pipx run cffconvert --validate
```

{% include links.md %}
