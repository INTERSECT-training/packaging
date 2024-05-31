---
title: "Versioning"
teaching: 10
exercises: 5
questions:
- "How do you choose your versions?"
- "How do you set version limits on dependencies?"
- "How do you set a version?"
objectives:
- "Know the different versioning schemes and drawbacks"
- "Know how to set a version in a package"
keypoints:
- "SemVer is an abbreviated changelog"
- "SemVer is not the solution to all problems"
- "Packages should have a version attribute"
- "You can single source your version"
---

Versioning is a surprisingly deep topic with some strong opinions floating
around. We will look at a couple of popular versioning schemes. We will also
discuss how you should use the versions your dependencies provide.

Then we will cover some best practices in setting your own version, along with a
few tips for a good changelog. You'll learn the simplest way, along with some
advanced technique to single-source your version, including using your VCS's
tags as versions. This makes mistakes during releases harder and to allow every
single commit to come with a unique version.

## Versioning schemes

There are three commonly used versioning schemes that you will usually select
from. Most packages follow one of these with some variations. A _few_ packages
have custom schemes (LaTeX's pi based scheme comes to mind!), but this covers
almost all software.

### SemVer: Semantic Versioning

SemVer lays out a set of rules, summarized here, based on a version of the form
`<major>.<minor>.<patch>`. These are:

* If your API changes in a breaking way, the `major` number must be incremented.
* If you add API but don't break existing API, you can increment the `minor` version.
* If you only fix bugs, you can increment the `patch` version.

And obviously set the smaller version values to zero when you increment a larger one.

This seems simple, but what is a breaking change? The unintuitive answer is that
it depends on how many users you have.  Every change could be a breaking change
to someone if you have enough users (at least in a language like Python where
everything can be accessed if you try hard enough). The closer you try to follow "true"
SemVer, the more major releases you will have, until you've lost the usefulness
of SemVer and every release is basically a major release. An example of a
package that has a lot of users and tries to follow SemVer pretty closely is
Setuptools, which is on version 68 as of the time of writing. And minor/patch
release still break some users.

> #### Avoiding breakage
>
> You can't be sure that a minor a patch release will not break you. No library
> can follow SemVer close enough to be "true" SemVer unless they only release
> major versions. "They didn't follow SemVer closely enough" is not a good argument
> for any package causing breakage.
{:.callout}

A more realistic form of SemVer, and a better way to think about it, is as an abbreviated changelog and author intent.
In this form:

* Bumping a `patch` version means there's nothing to see, just fixing things, you are probably fine.
* Bumping a `minor` version means that there might be interesting things to see, but nothing you have to see.
* Bumping a `major` version means you really should look, changes might even be needed for users.

If a release breaks your code, you are more likely to be able to get a followup
patch release fixing your use case if it's a patch or minor version.  If you
view SemVer this way, a lot less grief will occur from broken expectations.
Also, changelogs are really important, so having a way to notify people when
there's things to look at is really useful.

A final variation that is very common in the Python community is a deprecation
cycle addition.  This says that a feature that is deprecated in a minor version
can be removed in a minor version after a certain time or number of minor
versions. Python itself uses this (due partially to an extreme aversion to ever
releasing another major version), along with NumPy and others. Python's
deprecation cycle is two releases (3.12 deprecations can be removed in 3.14),
and NumPy's is three.

> #### Getting deprecation warnings
>
> In Python, the deprecation warnings are `PendingDeprecationWarning`,
> `DeprecationWarning`, and `FutureWarning`, in that order. The first two
> are often not shown by default, so always run your test suite with full
> warnings as errors enabled, so that you are able to catch them. And if
> it's your library, doing at least one final release with `FutureWarning` is
> a good idea as it's not hidden by default.

Some suggestions for how you depend on packages will be given later, but let's
look at the other two common mechanisms first.

> #### Python only
>
> Discussions of SemVer need to be centered on Python and the Python community.
> Other Languages have different communities and different tools. For example,
> JavaScript's NodeJS supports each dependency getting their own copy of their
> dependency. This makes the ecosystem's expectations of pinning totally different.

### ZeroVer

Sometimes packages start with a `0`; if they do that, they are using a modified
version of SemVer, sometimes called ZeroVer. In this, the first non-zero digit
is a major version, and the second non-zero value is somewhere between a minor
and patch. There are several reasons for this version scheme; sometimes it is to
indicate the package is still under heavy development. Though, in practice,
selecting a point to release "1.0" can be quite difficult; a user's expectation
(this is stable now) and the developers expectation (some huge new feature added
/ rewrite, etc) are completely at odds. This has led some very popular projects to
still be using ZeroVer.

### CalVer: Calendar based versioning

Another scheme growing in popularity in the Python community is the CalVer
scheme. This sets the version number based on release date. There are several
variations; some projects literally place the date (two or four digit year
followed by month then day), and some blend a little bit of SemVer in by making
the second or third digit SemVer-like.  An example of a popular (non-Python)
project using CalVer is Ubuntu - the version (like 22.04) is the release year
and month. Pip uses a blended CalVer; Pip releases three major versions per year
like `23.1.0`; the final release of the year is `23.3.patch`. Those these are
technically major versions, they tend to try to do larger changes between years.

CalVer does two things well; it communicates how old a release is without having
to check release notes, and it allows deprecation cycles (remember we said those
were important in Python!) to be expressed in terms of time - if you want a one
year deprecation cycle, you know exactly what release the item will be
removed/changed in. It also helps clearly communicate the problem with SemVer -
you don't know when something will make a breaking release for _you_, so don't
predict / depend on something breaking. You might be tempted to set `package<2`,
but you should never be tempted to set `package<24` - it's not SemVer! (See the
section on locking!)

Due to this, several core Python packaging projects (like pip, packaging, and
virtualenv) use CalVer. CalVer is especially useful for something that talks to
external services (like pip). Another popular library using it is `attrs`.

## Versions on your dependencies

### Specifying a range

How you set versions on dependencies depends on what sort of package you are working on:

* **Library**: something that can be imported. Support the widest range possible.
* **App (CLI/GUI/TUI)**: Something that is installable but not importable. Wide range preferred, but not as important (assuming users use pipx and don't add it to "dev" environments or things like that).
* **Application (deployment)**: Something like a website or analysis. Not installable. Versions should be fully locked.

#### Minimums:

For libraries and apps, you should try to support a wide range of dependencies
if possible, depending on how many users you expect. For a small number of
users, simply requiring recent versions of everything is probably fine. If you
expect a large number of users (enough that your package will be picked up by
Linux distros, for example), then you should try to support a few past releases
of dependencies, and ideally run your tests at least once with the minimum
versions of your dependencies. You can do this with a `constraints.txt` file,
which specifies pins on the minimum versions of all your dependencies, and then
add `-c constraints.txt` when pip installing.

#### Maximums:

You can read an article on maximums
[here](https://iscinumpy.dev/post/bound-version-constraints/). TL;DR: don't set
maximums on your dependencies unless you are fairly sure the next version will
break your usage of the library.  There are some special cases, mostly around
how important a dependency is for you; if you are writing a pytest plugin, you
are probably monitoring pytest pretty closely and can react to a breaking
release. (But this is also true, maybe more true, if you don't cap!)


### Locking

The solution to breaking updates from dependencies is specific for applications
(like websites an analyses) - you should lock your environment! There are two
ways to do this: manually with piptools, or using a locking package manager
(like PDM or Poetry).

#### Manual: piptools

Set up a `requirements.in` file with your unpinned dependencies. For example:

```txt
# requirements.in
packaging
```

Now, run pip-compile from pip-tools on your requirements.in to make a requirements.txt:

```bash
pipx run --spec pip-tools pip-compile requirements.in --generate-hashes
```

This will produce a `requirements.txt` with fully locked dependencies, including
hashes.  You can always regenerate it when you want updates.


#### Automatic

Tools like PDM generate a lock file (in some custom format) when they set up
environments. Just add these files to your repository, and they should be used
automatically. You usually have an update command that will update the local
environment and the lock file. (Other languages have similar tooling as well,
like Rust, Ruby, NodeJS, etc.)

## Setting a version on your package

When you start a project, select one of the major versioning schemes.

### Two locations

The "standard" way to set a version is by setting it manually in your `pyproject.toml`:

```toml
[project]
version = "1.2.3"
```

And placing it in your source code accessible as `__version__` on your package.
You don't have to do that, since a user can use
`importlib.metadata.version("<package_name>")` to get the version, but this is a
very common practice and is useful if there is an issue and you need a hard copy
of the version in the source for an improperly packaged file.

If you use this method, you should probably set up some automatic way to bump
the version in both places, or some sort of check/test that verifies they are in
sync.

### Single location (hatchling)

Most build backends provide methods to get the version from your source code. In hatchling,
it looks like this:

```toml
[project]
dynamic = ["version"]

[tool.hatch]
version.path = "src/<package>/__init__.py"
```

### VCS versioning (hatchling)

Technically, there's another source for the version: git tags. Some backends
provide ways to use these as the single version source.  This also means every
commit gets a unique version, since "commits past tag" is usually added to the
version number. In hatchling, it looks like this:


```toml
[build-system]
requires = ["hatchling", "hatch-vcs"]

[project]
dynamic = ["version"]

[tool.hatch]
version.source = "vcs"
build.hooks.vcs.version-file = "src/<package>/version.py"
```

This will get the version from VCS, and will also write out a version file that
you can then import `version` (or `__version__` in recent versions) from and
provide as `__version__` for your package.

If you also want git tarballs to contain the version, add the following to your `.gitattributes` file:

```txt
.git_archival.txt  export-subst
```

And the following `.git_archival` file:

```txt
node: $Format:%H$
node-date: $Format:%cI$
describe-name: $Format:%(describe:tags=true,match=*[0-9]*)$
ref-names: $Format:%D$
```

Now `git archive`'s output (like the tarballs GitHub provides) will also include
the version information (for recent versions of git)!

> ## Add a versioning system
> Add one of the two single-version systems listed above to your package.
>
{:.challenge}

{% include links.md %}
