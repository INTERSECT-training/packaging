---
title: "Publishing package and citation"
teaching: 20
exercises: 20
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

## Building SDists and wheels

The `build` package builds SDists (source distributions) and wheels (build
distributions). The SDist usually contains most of your repository, and requires
your build backend (hatchling, in this case) to build. The wheel is "built", the
contents are ready to unpack into standard locations (usually site-packages),
and does not contain configuration files like `pyproject.toml`. Usually you do
not include things like tests in the wheel. Wheels also can contain binaries for
packages with compiled portions.

You can build an SDist and a wheel (from that SDist) with `pipx` & `build`:

```bash
pipx run build
```

The module is named build, so `python -m build` is how you'd run it from nox.
The executable is actually named `pyproject-build`, since installing a `build`
executable would likely conflict with other things on your system.

This produces the wheel and sdist in `./dist`.

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
remove this if you are not in a tutorial. You'll also need to setup a token to
upload the package with. However, the best way to publish is from CI. This has
several benefits: you are always in a clean checkout, so you won't accidentally
include added or changed files, you have a simpler deployment procedure, and
you have more control over who can publish in GitHub.

> ## Create a noxfile to build
>
> Given what you've learned about `nox` and `build`, write a session that builds
> packages for you.
>
> > # Solution
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
# .github/workflows/cd.yml
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
    - uses: actions/checkout@v3
      with:
        fetch-depth: 0

    - name: Build SDist & wheel
      run: pipx run build

    - uses: actions/upload-artifact@v3
      with:
        path: dist/*
```

We've seen the setup before. We are calling the job `dist`, using an Ubuntu
runner, and checking out the code, including the git history so the version can
be computed with `fetch-depth: 0` (which can be removed if you are not using git
versioning).

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
  - uses: actions/download-artifact@v3
    with:
      name: artifact
      path: dist

  - uses: pypa/gh-action-pypi-publish@v1.5.1
    with:
      password: ${{ secrets.pypi_password }}
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

Finally, we use the PyPA's publish action, which uses twine internally, and we
set the password to a GitHub Secret, `PYPI_PASSWORD`, that we'll generate from
PyPI and add next. We will be using TestPyPI for this exercise.

## PyPI Tokens and GitHub Secrets

To make a release from GitHub, you'll need to setup authentication with PyPI by
generating a token.  Log into your test.pypi.org account and generate a token. If
it's the first time you've uploaded the project (it will be if you are following
along), then it will need to be account-scoped.  After you upload your first
release, you can delete the old token and upload a project-scoped one instead,
which is safer.

![API token button]({{ page.root }}/fig/apitoken.png)

Click your profile -> account settings, then click Add API token, under API
tokens.

![API token generation]({{ page.root }}/fig/maketoken.png)

Give it a name so you can identify it later, and select "Entire
account" (you should replace it after uploading a package with a scoped token
for that package).

![API token view]({{ page.root }}/fig/viewtoken.png)

Now copy it and go to GitHub, to your repository's setting. Select Secrets ->
Actions.  Click "New repository secret". Paste in your token here and give it
the name `PYPI_PASSWORD`.

![GitHub Actions Secrets page]({{ page.root }}/fig/ghasecret.png)

You can now deploy to PyPI! Remember to delete your token and repeat this
process with a scoped token one you've uploaded a package and can select it in
the scope drop down.


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

## Adding Zenodo

## The CITATION.cff file

{% include links.md %}
