---
title: "Jupyter to package"
teaching: 20
exercises: 5
questions:
- "How do we take code in a Jupyter Notebook or Python script and turn that into a package?"
- "What are the minimum elements required for a Python package?"
- "How do you set up tests?"
objectives:
- "Create and install a Python package"
- "Create and run a test"
keypoints:
- "First key point. Brief Answer to questions. (FIXME)"
---

Much research software is initially developed by hacking away in an interactive setting, such as in a [Jupyter Notebook](https://jupyter.org) or a Python shell. However, at some point when you have a more-complicated workflow that you want to repeat, and/or make available to others, it makes sense to package your functions into modules and ultimately a software package that can be installed. This lesson will walk you through that process.

Consider the `rescale()` function written as an exercise in the Software Carpentry [Programming with Python](https://swcarpentry.github.io/python-novice-inflammation/08-func/index.html) lesson. In a Python shell or Jupyter Notebook, you can declare the function and call it:

```python
def rescale(input_array):
"""Takes an array as input, and returns a corresponding array scaled so
that 0 corresponds to the minimum and 1 to the maximum value of the input array.
"""
    L = numpy.min(input_array)
    H = numpy.max(input_array)
    output_array = (input_array - L) / (H - L)
    return output_array

rescale(numpy.linspace(0, 100, 5))
```

which provides this output:

```
array([ 0.  ,  0.25,  0.5 ,  0.75,  1.  ])
```
{: .output}

Let's create a Python package that calls this function using the arguments shown above.

First, create a new directory for your software package, called `rescale`, and move into that:

```bash
$ mkdir rescale
$ cd rescale
```

You should immediately initialize an empty Git repository in this directory; if you need a refresher on using Git for version control, check out the Software Carpentry [Version Control with Git](https://swcarpentry.github.io/git-novice/) lesson.

```bash
$ git init
```

Next, 

> ## Note
>
> In practice, you will likely not hardcode arguments inside a package, but instead 
> take in user-provided inputs via a file or command-line interface, but that is beyond
> the scope of this lesson.
{: .callout}

{% include links.md %}

