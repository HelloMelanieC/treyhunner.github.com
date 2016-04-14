---
layout: post
title: "How to loop with indexes in Python"
date: 2016-04-07 09:59:03 -0700
comments: true
categories: python
---

If you're moving to Python from C or Java, you might be confused by Python's `for` loops.  **Python doesn't actually have for loops**... at least not the same kind of `for` loop that C-based languages have.  Python's `for` loops are actually [foreach loops][foreach].

In this article I'll compare Python's `for` loops to those of other languages and discuss the usual ways we solve common problems with `for` loops in Python.

## For loops in other languages

Before we look at Python's loops, let's take a look at a for loop in JavaScript:

```javascript
var colors = ["red", "green", "blue", "purple"];
for (var i = 0; i < colors.length; i++) {
    console.log(colors[i]);
}
```

This JavaScript loop looks nearly identical in C/C++ and Java.

In this loop we:

1. Set a counter variable `i` to 0
2. Check if the counter is less than the array length
3. Execute the code in the loop *or* exit the loop if the counter is too high
4. Increment the counter variable by 1


## Looping in Python

Now let's talk about loops in Python.  First we'll look at two slightly more familiar looping methods and then we'll look at the idiomatic way to loop in Python.

### while

If we wanted to mimic the behavior of our traditional C-style `for` loop in Python, we could use a `while` loop:

```python
colors = ["red", "green", "blue", "purple"]
i = 0
while i < len(colors):
    print(colors[i])
    i += 1
```

This involves the same 4 steps as the `for` loops in other languages (note that we're setting, checking, and incrementing `i`) but it's not quite as compact.

This method of looping in Python is very uncommon.

### range of length

I often see new Python programmers attempt to recreate traditional `for` loops in a slightly more creative fashion in Python:

```python
colors = ["red", "green", "blue", "purple"]
for i in range(len(colors)):
    print(colors[i])
```

This first creates a range corresponding to the indexes in our list (`0` to `len(colors) - 1`).  We can loop over this range using Python's for-in loop (really a [foreach][]).

This provides us with the index of each item in our `colors` list, which is the same way that C-style `for` loops work.  To get the actual color, we use `colors[i]`.

### for-in: the usual way

Both the while loop and range-of-len methods rely on looping over indexes.  Note that we don't actually care about these indexes: we're only using the indexes for the purpose of retrieving elements from our list.

Because we don't actually care about the indexes in our loop, there is **a much simpler method of looping** we can use:

```python
colors = ["red", "green", "blue", "purple"]
for color in colors:
    print(color)
```

So instead of retrieving the item indexes and looking up each element, we can just loop over our list using a plain for-in loop.

The other two methods we discussed are sometimes referred to as [anti-patterns][] because they are programming patterns which are widely considered unidiomatic.

## We need indexes

What if we actually need the indexes?  For example, let's say we're printing out president names along with their numbers (based on list indexes).

### range of length

We could use `range(len(colors))` and then lookup `colors[i]` like before:

```python
presidents = ["Washington", "Adams", "Jefferson", "Madison", "Monroe", "Adams", "Jackson"]
for i in range(len(presidents)):
    print("President {}: {}".format(i + 1, presidents[i]))
```

But there's a more idiomatic way to accomplish this task: use the `enumerate` function.

### enumerate

Python's built-in `enumerate` function allows us to loop over a list and retrieve both the index and the value of each item in the list:

```python
presidents = ["Washington", "Adams", "Jefferson", "Madison", "Monroe", "Adams", "Jackson"]
for num, name in enumerate(presidents, start=1):
    print("President {}: {}".format(num, name))
```

Python's `enumerate` function gives us an iterable where each element is a tuple that contains the index of the item and the original item value.

The `enumerate` function is meant for solving the task of:

1. Accessing each item in a list (or another iterable)
2. Also getting the index of each item accessed

So whenever we need item indexes while looping, we should think of `enumerate`.

## We need to loop over multiple things

Often when we use list indexes, it's to look something up in another list.

### enumerate

For example, here we're looping over two lists at the same time using indexes to look up corresponding elements:

```python
colors = ["red", "green", "blue", "purple"]
ratios = [0.2, 0.3, 0.1, 0.4]
for i, color in enumerate(colors):
    ratio = ratios[i]
    print("{}% {}".format(ratio * 100, color))
```

We only need the index in this scenario because we're using it to lookup a corresponding element from our second list.

### zip

We don't actually care about the index when looping here.  Our goal is actually to loop over two lists at once.

This use case of looping over multiple lists at once is common enough that there's a special function for this too.

The `zip` function allows us to **loop over multiple lists at the same time**:

```python
colors = ["red", "green", "blue", "purple"]
ratios = [0.2, 0.3, 0.1, 0.4]
for color, ratio in zip(colors, ratios):
    print("{}% {}".format(ratio * 100, color))
```

The `zip` function takes multiple lists and returns an iterable that provides the corresponding elements of each list as we loop.

## Looping cheat sheet

Loop over a single list with a regular for-in:

```python
for n in numbers:
    print(n)
```

Loop over multiple lists at the same time with `zip`:

```python
for header, rows in zip(headers, columns):
    print("{}: {}".fromat(header, ", ".join(rows)))
```

Loop over a list while keeping track of indexes with `enumerate`:

```python
for num, line in enumerate(lines):
    print("{0:03d}: {}".fromat(num, line))
```

## In Summary

If you find yourself tempted to use `range(len(my_list))` or a loop counter, think about whether you can restructure your problem to use `zip`, `enumerate`, or a combination of the two.

1. If you need to loop over multiple lists at the same time, use `zip`
2. If you only need to loop over a single list just use a for-in loop
3. If you need to loop over a list and you need item indexes, use `enumerate`

Try using the cheat sheet above.

Happy looping!

[anti-patterns]: https://en.wikipedia.org/wiki/Anti-pattern
[foreach]: https://en.wikipedia.org/wiki/Foreach_loop
