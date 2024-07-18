---
layout: lesson
root: .  # Is the only page that doesn't follow the pattern /:path/index.html
permalink: index.html  # Is the only page that doesn't follow the pattern /:path/index.html
---

What is a package?
* An organized collection of modules containing functions and classes with a common purpose.
* Packages allow developers to organize code in manageable and reuseable structured units.

Why is packaging important in scientific research?

* **Reusability:** Common code can be used across many projects
* **Collaboration:** Distribute your work with collaborators
* **Reproducibility:** Allows anyone to run code with the same dependencies to reproduce your results
* **Portabilit:y** Work in more than one place (e.g. distributed computing)

If you have used Python for scientific computation, you've likely encountered widely distributed open-source packages like [NumPy](https://numpy.org/), [SciPy](https://scipy.org/), or [Pandas](https://pandas.pydata.org/). However, it's important to note that packages can exist at many scales and contexts, ranging from personal and organizational use to wide public distributions.

In this tutorial we'll walk through methods and tools used to create and distribute your own Python packages.

## Lesson Plan

We're going to create a Python package from scratch, then publish it. Packages often include additional files for configuration, documentation, and automations. We'll look at the following important groups of files:

```files
.
├── src/                    ─┐
│   └── example_package_YOUR_USERNAME_HERE/
│   │   ├── __init__.py      │
│   │   └── rescale.py       ├─ Minimum to make the code
├── tests/                   │  work and be installable
│   └── test_rescale.py      │
├── pyproject.toml          ─┘ ← Metadata and versioning
│
├── .git/                   ─┬─ Store the history of the code
├── .gitignore              ─┘
│
├── .venv/                  ─── Environment to run the code
│
├── README                  ─┐
├── CHANGELOG                ├─ Tell people browsing the code what it's for,
├── LICENSE                  │  how it's changed, under what conditions they
├── CITATION.cff            ─┘  can use it, and how they can cite it
│
├── docs/                   ─┐
│   ├── index.md             ├─ Document the code in more detail
│   └── conf.py             ─┘
│
├── .pre-commit-config.yaml ─┐
├── noxfile.py               │
└── .github/                 ├─ Automate tasks which run _on_ the code
    └── workflows/           │  like style-checks, tests, and publishing
        ├── test.yaml        │
        └── publish.yaml    ─┘
```

{: .checklist }

> ## See Also
>
> This is a tutorial. For reference material, you should bookmark the following guides:
>
> - [learn.scientific-python.org/development](https://learn.scientific-python.org/development)
> - [packaging.python.org](https://packaging.python.org)


> ## Prerequisites
>
> * Basic Python
{: .prereq}

{% include links.md %}
