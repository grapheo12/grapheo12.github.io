+++
title = "Beginner's Guide to Python"
date = 2020-01-01

[taxonomies]
tags = ["python", "python3", "tutorial"]
+++

## What is Python?

> The *Pythonidae*, commonly known simply as pythons, are a family of
> nonvenomous snakes found in Africa, Asia, and Australia. Among its
> members are some of the largest snakes in the world.

\-- Wikipedia

Well, I didn't mean this Python!

<!-- more -->

Python is now among the top 3 programming languages in demand in
today's world. The reason for its popularity being: *Readability*,
*Robustness* and *a big collection of actively maintained libraries*.

Python was designed by the BDFL, *Guido van Rossum* in 1991. Since then
there has been different flavours of Python: *CPython* (the original
implementation), *Cython*, *IPython*, *PyPy* etc. They all serve
specific purposes. Here I'll be talking about CPython exclusively.

I am still learning Python. So this beginner's guide will be the path I
followed to reach to the stage that I'm currently in. Whether you want
Python for web development, for data scraping or for Data Analytics \--
you must go through these basics to be proficient in Python.

## First steps

I began my Python journey with *Zed A. Shaw*'s book **Learn Python
The Hard Way**. The book was a work in progress then and hence, quite
fortunately, it was free. This is not the case now. Besides teaching you
the syntax, this book teaches you the Pythonic Way of Programming. I
knew PHP before Python. But that I learnt solely from a web development
point of view. Hence Python was the first time I learnt to do general
purpose coding and trust me the learning curve is not that steep.

Next, I came across this excellent book called: **Think Python: How to
think like a computer scientist** by *Allen B. Downey*. At that time
Python 2.7 was in vogue and people were slowly adopting to Python 3.
This book, at that time, was written in Python 2. The 2nd edition has
now migrated to Python 3 though. I followed this book almost religiously
and read it twice. It covered all the basic concepts required to have a
successful core Python programmer. Apart from just basic syntax, the
book covers many useful topics like *Pickle*, *Database connection*,
*Image Processing with PIL*, *GUI Programming with Tkinter* etc. You
don't have to learn all this at the first go. But keep this book handy,
if you need some help later. Around this time I got to know the mantra
that every Python Programmer should follow:

```python
>>> import this

The Zen of Python, by Tim Peters

Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one-- and preferably only one --obvious way to do it.
Although that way may not be obvious at first unless you're Dutch.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea -- let's do more of those!
```

Things above may not make much sense if you're a complete beginner. But
over the time you'll get a hang of it.

After a long pause, when I took up Python again, the world had moved on
to Python 3. I then used the tutorial given in the **Official Python 3
docs** to regain my wits.

## What's next


After all these, one should really focus on being as Pythonic as (s)he
can. For this, I recommend learning *Generators*, *List Comprehension*
and *Decorators*. While doing OOP, learn to leverage the power of
*dunder methods* and *operator overloading*. Also try to follow the
**PEP8** guidelines in your code.

### Note

> It is good to know some core CS concepts involved behind the scenes,
> such as, **GIL**, **Method Resolution Order**, **Key-sharing Dict** etc.
> You can follow *Raymond Hettinger*'s lectures for the same.


One of the most important and often overlooked topic of Python is
**making packages** and publishing them to **PyPI** (**Py**thon
**P**ackage **I**ndex) and **dependency management**. The most useful
management tools are **virtualenv** and **pipenv**.

What you learn next totally depends on the domain of your usage. Here
are some domain-specific libraries that you should know:

1.  Web Development: Flask, Django
2.  Data Science/ML/DL: Numpy, Pandas, Matplotlib, Scikit-learn,
    Tensorflow, Pytorch
3.  Web Scraping: Selenium, Scrapy
4.  Generic good-to-know modules: argparse, re, multiprocessing,
    subprocess, sys, os, requests, logging, setuptools

### Warning
> Never just learn a concept or library. Use it in a learning project. It
> is impossible to learn every intricacy of a module in one tutorial. Keep
> the official docs and StackOverflow handy.

## TL;DR

**Here is a quick summary:**

-   Read **Learn Python The Hard Way** and **Think Python**.
-   Read the tutorial given in **Official Python 3 docs**.
-   Learn the Pythonic way of doing things (Idiomatic Python).
-   Learn domain specific libraries as discussed just above.
-   Code a lot in Python
