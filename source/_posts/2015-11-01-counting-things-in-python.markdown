---
layout: post
title: "Counting things in Python: a history"
date: 2015-11-01 01:56:53 -0700
comments: true
categories: python
---

Let's take a look at different ways to count the number of times things appear in a list.  The "Pythonic" way to do this has changed as Python has changed so we also discuss a bit of Python history to put these methods in context.

## If Statement

It's January 1, 1997 and we're using Python 1.4.  We have a list of colors and we'd love to know how many times each color occurs in this list.  Let's use [a dictionary][1.4]!

```python
colors = ["Brown", "Red", "Green", "White", "Yellow", "Yellow", "Brown", "Brown", "Black"]
color_counts = {}
for c in colors:
    if color_counts.has_key(c):
        color_counts[c] += 1
    else:
        color_counts[c] = 1
```

**Note:** we're not using the `c in color_counts` idiom because that won't be invented until Python 2.2!

After running this we'll see that our `color_counts` dictionary now contains the counts of each color in our list:

```pycon
>>> color_counts
{'Brown': 3, 'Yellow': 2, 'Green': 1, 'Black': 1, 'White': 1, 'Red': 1}
```

That was pretty simple.  We just looped through each color, checked if it was in the dictionary, added the color if it wasn't, and incremented the count if it was.

We could also write this as:

```python
colors = ["Brown", "Red", "Green", "White", "Yellow", "Yellow", "Brown", "Brown", "Black"]
color_counts = {}
for c in colors:
    if not color_counts.has_key(c):
        color_counts[c] = 1
    color_counts[c] += 1
```

This might be a little slower on sparse lists (lists with lots of non-repeating colors) because it does two statements instead of one, but we're not worried about performance, we're worried about what is Pythonic.  We think this looks more Pythonic so we stick with it.

## Try Block

It's January 2, 1997 and we're still using Python 1.4.  We woke up this morning with a sudden realization: our code is practicing "Look Before You Leap" (LBYL) when we should be practicing "Easier to Ask Forgiveness, Than Permission" ([EAFP][]) because EAFP is more Pythonic.  Let's refactor our code to use a try-except block:

```python
colors = ["Brown", "Red", "Green", "White", "Yellow", "Yellow", "Brown", "Brown", "Black"]
color_counts = {}
for c in colors:
    try:
        color_counts[c] += 1
    except KeyError:
        color_counts[c] = 1
```

Now our code attempts to increment the count for each color and if the color isn't in the dictionary, a `KeyError` will be raised and we will instead set the color count to 1 for the color.

## get Method

It's January 1, 1998 and we've upgraded to Python 1.5.  We've decided to refactor our code to use the [new `get` method on dictionaries][1.5]:

```python
colors = ["Brown", "Red", "Green", "White", "Yellow", "Yellow", "Brown", "Brown", "Black"]
color_counts = {}
for c in colors:
    color_counts[c] = color_counts.get(c, 0) + 1
```

It's cool that this is all one line of code, but we're not entirely sure if this is more Pythonic.  Is this more readable?  Is this more Pythonic?

We decide this might be too clever and we change our code back to use a `try` block.

## setdefault

It's January 1, 2001 and we're now using Python 2.0!  We've heard that [dictionaries have a `setdefault` method now][2.0] and we decide to refactor our code to use this new method:

```python
colors = ["Brown", "Red", "Green", "White", "Yellow", "Yellow", "Brown", "Brown", "Black"]
color_counts = {}
for c in colors:
    color_counts.setdefault(c, 0)
    color_counts[c] += 1
```

The `setdefault` method is being called on every loop, regardless of whether it's needed, but this does seem a little more readable.  We decide that this is more Pythonic than our previous solutions and commit our change.

## comprehension and set

It's January 1, 2005 and we're using Python 2.4.  We realize that we could solve our counting problem using sets ([released in Python 2.3][2.3] and made into [a built-in in 2.4][2.4]) and list comprehensions ([released in Python 2.0][pep 202]).  After further thought, we remember that [generator expressions][pep 289] were also just released in Python 2.4 and we decide to use one of those instead of a list comprehension:

```python
colors = ["Brown", "Red", "Green", "White", "Yellow", "Yellow", "Brown", "Brown", "Black"]
color_counts = dict((c, colors.count(c)) for c in set(colors))
```

**Note**: we're didn't use a dictionary comprehension because those weren't invented until [Python 2.7][pep 274].

This works.  It's one line of code.  But is it Pythonic?

We remember the [Zen of Python][], which [started in a python-list email thread][zen email] and was [snuck into Python 2.2.1][import this].  We type ``import this`` at our REPL:

```pycon
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

Our code is more complex (**O(n<sup>2</sup>)** instead of **O(n)**), less beautiful, and less readable.  It was a fun experiment, but this one-line solution is less Pythonic than what we already had.  We decide to revert this change.

## defaultdict

It's January 1, 2007 and we're using Python 2.5.  We've just found out that [`defaultdict` is in the standard library][2.5] now.  This should allow us to set `0` as the default value in our dictionary.  Let's refactor our code to count using a `defaultdict` instead:

```python
from collections import defaultdict
colors = ["Brown", "Red", "Green", "White", "Yellow", "Yellow", "Brown", "Brown", "Black"]
color_counts = defaultdict(int)
for c in colors:
    color_counts[c] += 1
```

That `for` loop is so simple now!  This is almost certainly more Pythonic.

We realize that our `color_counts` variable does act slightly differently, but it inherits from `dict` and has all the same behaviors.  Instead of converting this to a `dict`, we'll assume the rest of our code practices proper duck typing and just leave this dict-like object as-is.

```pycon
>>> color_counts
defaultdict(<type 'int'>, {'Brown': 3, 'Yellow': 2, 'Green': 1, 'Black': 1, 'White': 1, 'Red': 1})
```

## Counter

It's January 1, 2011 and we're using Python 2.7.  We've been told that our `defaultdict` code is no longer the most Pythonic way to count colors.  [A `Counter` class was included in the standard library][2.7] in Python 2.7 and it does all of the work for us!

```python
from collections import Counter
colors = ["Brown", "Red", "Green", "White", "Yellow", "Yellow", "Brown", "Brown", "Black"]
color_counts = Counter(colors)
```

Could this get any simpler?  This must be the most Pythonic way.

Like `defaultdict`, this returns a dict-like object (a `dict` subclass actually), which should be good enough for our purposes, so we'll stick with it.

```pycon
>>> color_counts
Counter({'Brown': 3, 'Yellow': 2, 'White': 1, 'Green': 1, 'Black': 1, 'Red': 1})
```

## Pythonic is a relative term

Per the [Zen of Python][], "there should be one-- and preferably only one-- obvious way to do it".  This is an aspirational message.  There isn't always one obvious way to do it.  That obvious way can vary by time, need, and level of expertise.

Notice that we didn't focus on runtime performance for these solutions.  The time complexity for most of these solutions remained the same `O(n)` so run-time could vary based on the Python implementation.

While performance isn't our main concern, [I did measure the run-times on CPython 3.5.0][performance].  It's interesting to see how each implementation changes in relative efficiency based on the density of color names in the list.

### Related Resources

- [import this and the Zen of Python](http://www.wefearchange.org/2010/06/import-this-and-zen-of-python.html): Zen of Python trivia borrowed from this post
- [Permission or Forgiveness](https://www.youtube.com/watch?v=AZDWveIdqjY): Alex Martelli discusses Grace Hopper's EAFP
- [Python How To: Group and Count with Dictionaries](https://codefisher.org/catch/blog/2015/04/22/python-how-group-and-count-dictionaries/): while writing this post, I discovered this related article

[1.4]: https://docs.python.org/release/1.4/lib/node13.html
[1.5]: https://docs.python.org/release/1.5/lib/node13.html
[2.0]: https://docs.python.org/release/2.0/lib/typesmapping.html
[2.3]: https://docs.python.org/release/2.3/lib/module-sets.html
[2.4]: https://docs.python.org/release/2.4/lib/types-set.html
[2.5]: https://docs.python.org/release/2.5/lib/defaultdict-objects.html
[2.7]: https://docs.python.org/2.7/library/collections.html#collections.Counter
[eafp]: https://docs.python.org/2/glossary.html#term-eafp
[pep 202]: https://www.python.org/dev/peps/pep-0202/
[pep 274]: https://www.python.org/dev/peps/pep-0274/
[pep 289]: https://www.python.org/dev/peps/pep-0289/
[zen email]: https://mail.python.org/pipermail/python-list/1999-June/001951.html
[import this]: http://svn.python.org/view/python/tags/r221/Lib/this.py?revision=25249&view=markup
[performance]: https://gist.github.com/treyhunner/0987601f960a5617a1be
[zen of python]: https://www.python.org/dev/peps/pep-0020/
