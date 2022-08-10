---
title: "Other files that belong with your package"
teaching: 20
exercises: 0
questions:
- "What other files are important parts of your software package?"
- "What software license should you use for your project?"
objectives:
- "Create a README for a software package"
- "Choose a software license for a software package"
- "Create a CHANGELOG for a package"
keypoints:
- "In addition to the source code and project specification, packages should include a README, LICENSE, CHANGELOG, and `.gitignore`."
- "Do not create a custom software license or modify an existing license; instead, choose from the list of available licenses."
- "You can also include `.pre-commit-config.yaml` to automatically check simple issues with your code before committing it."
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

~~~markdown
# Package

`Package` is a simple Python library that contains a single function for rescaling arrays.

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
~~~

You can see more guidance on creating READMEs at <https://www.makeareadme.com>.

> ## Keep your READMEs relatively brief
>
> You should try to keep your README files *relatively* brief, rather than including
> very detailed documentation to this file. This should only be a high-level introduction,
> with detailed theory, examples, and other information reserved for a true documentation
> website.
{: .callout}

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

Most research software uses permissive licenses like the MIT or BSD 3-clause license.
For an easy choice, we recommend using the **BSD 3-Clause License**, which includes a specific clause preventing the names of creators/contributors from being used to endorse or promote derivatives, without permission.
However, you should choose the specific license that best fits your needs.

Create a `LICENSE.txt` file using

```bash
$ touch LICENSE.txt
```

and copy the **exact** text of the [BSD 3-Clause License](https://choosealicense.com/licenses/bsd-3-clause/), modifying only the year and names:

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

> ## Do not write your own license!
>
> You should never try to write your own software license, or modify the text of an existing
> license.
>
> Although *we* are not lawyers, the licenses approved and maintained by the Open
> Source Initiative have gone through a rigorous review process, including legal review,
> to ensure that they are both consistent with the Open Source Definition and also are
> legally valid.
{: .callout}

## Keeping a CHANGELOG

TODO

## Additional files for Git

TODO

{% include links.md %}
