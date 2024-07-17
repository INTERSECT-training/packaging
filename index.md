---
layout: lesson
root: .  # Is the only page that doesn't follow the pattern /:path/index.html
permalink: index.html  # Is the only page that doesn't follow the pattern /:path/index.html
---

When you are programming, usually you are solving a problem - the programming
is simply a means to an end. If that's the case, then packaging is a means to a
means to an end. This often makes packaging the last thing you think about, but
it's actually one of the most important parts of any work, as it's what makes
code obtainable and reusable!

Packaging is absolutely critical as soon as you:

* Work on more than one thing
* Share your work with anyone (even if not as a package)
* Work in more than one place
* Upgrade or change anything on your computer

Unfortunately, packing has a _lot_ of historical cruft, bad practices that have
easy solutions today but are still propagated. This material tries to correct
that by showing you a clean, modern way to write and work with Python packages.

## Lesson Plan

We're going to create a Python package from scratch, then publish it.
We'll look at the following important groups of files:

```files
.   
├── src/                                     } Minimum to make the code 
│   └── example_package_YOUR_USERNAME_HERE/  } work and be installable
│   │   ├── __init__.py                      }
│   │   └── rescale.py                       } 
├── tests/                                   }
│   └── test_rescale.py                      }
├── pyproject.toml                           } < Deep dive into metadata 
│                                            }   and versioning
│
├── .git/       } Store the history of the code
├── .gitignore  }
│
├── .venv/  } Environment to run the code
│
├── README        } Tell people browsing the code what it's for,
├── CHANGELOG     } how it's changed,
├── LICENSE       } under what conditions they can use it,
├── CITATION.cff  } how they can cite it.
│
├── docs/         } Document the code in more detail
│   ├── index.md  }
│   └── conf.py   }
│
├── .pre-commit-config.yaml  } Automate tasks which run _on_ the code
├── noxfile.py               } like style-checks, tests, and publishing
└── .github/                 } 
    └── workflows/           }
        ├── test.yaml        }
        └── publish.yaml     }
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
