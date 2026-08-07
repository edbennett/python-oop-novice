---
title: "Special methods"
teaching: 25
exercises: 15
questions:
- "How can classes allow their instances to work with standard Python
  operators?"
- "How can classes allow their instances to behave like iterables or collections?"
- "How can classes allow their instances to be called like functions?"
- "How can I avoid writing repetitive `__init__`, `__repr__`, and
  `__eq__` methods for simple data-holding classes?"
objectives:
- "Be able to implement methods like `__add__`, `__eq__`, and `__gt__`."
- "Be able to implement methods like `__len__`, `__iter__`, and
  `__reversed__`."
- "Be able to implement the `__call__` method."
- "Be able to use the `@dataclass` decorator to reduce boilerplate in
  data-holding classes."
keypoints:
- "Implement methods like `__eq__`, `__add__`, and `__gt__` to allow
  operations such as arithmetic and comparisons."
- "Implement `__repr__` to get more meaningful printouts when you
  output an object."
- "Implement methods like `__len__`, `__iter__`, and `__reversed__` to
  make instances of a class behave like a collection or iterable."
- "Implement the `__call__` method to make instances of a class
  callable like functions."
- "Use `@dataclass` (from the `dataclasses` module) to auto-generate
  `__init__`, `__repr__`, and `__eq__` for classes that mainly hold
  data; you can still override any of them yourself."
---

In the previous episodes, we built a `Triangle` class that could
represent a triangle by storing the lengths of its sides. Now,
mathematically speaking, two triangles with identical sides are the
same triangle. Let's see if Python agrees with this.

~~~
a_triangle = Triangle([3, 4, 5])
the_same_triangle = Triangle([3, 4, 5])
if a_triangle == the_same_triangle:
    print("Python thinks that these triangles are the same.")
else:
    print("Python thinks that these are different triangles.")
~~~
{: .language-python}

~~~
Python thinks that these are different triangles.
~~~
{: .output}

So, despite these triangles having been constructed with exactly the
same side lengths, Python distinguishes between them. By default,
Python will only consider two objects to be the same if they are
_identical_:

~~~
a_triangle = Triangle([3, 4, 5])
duplicate_triangle = a_triangle
if a_triangle == duplicate_triangle:
    print("Python thinks that these triangles are the same.")
else:
    print("Python thinks that these are different triangles.")
~~~
{: .language-python}

~~~
Python thinks that these triangles are the same.
~~~
{: .output}

This isn't great for our triangle example&mdash;we would much prefer
if we could compare equality of triangles without having to compare
the `side_lengths` property by hand. Fortunately, Python gives us a
way of doing this. If we implement the `__eq__` method of `Triangle`,
then Python learns how to compare triangles.

~~~
class Triangle(Polygon):
    def __init__(self, side_lengths):
        # Triangles have three sides
        super().__init__(side_lengths)
        assert len(self.side_lengths) == 3

    @classmethod
    def equilateral(cls, side_length):
        return cls([side_length] * 3)

    def area(self):
        """Returns the area of the triangle."""
        a, b, c = self.side_lengths
        p = (a + b + c) / 2
        return (p * (p - a) * (p - b) * (p - c)) ** 0.5

    def __eq__(self, other):
        """Returns True if the triangle self and the triangle other
        are the same triangle"""
        if not isinstance(other, Triangle):
            return False
        else:
            # Check all permutations
            if self.side_lengths == other.side_lengths:
                return True
            elif (
                self.side_lengths[1:] + [self.side_lengths[0]] ==
                other.side_lengths
            ):
                return True
            elif (
                [self.side_lengths[2]] + self.side_lengths[:2] ==
                other.side_lengths
            ):
                return True
            return False

a_triangle = Triangle([3, 4, 5])
the_same_triangle = Triangle([3, 4, 5])
if a_triangle == the_same_triangle:
    print("Python thinks that these triangles are the same.")
else:
    print("Python thinks that these are different triangles.")
~~~
{: .language-python}

~~~
Python thinks that these triangles are the same.
~~~
{: .output}

Great! We can compare equality. `__eq__` is the second example we've
seen of a so-called "special", "magic", or "dunder" (short for "double
underscore") method. These are methods that Python ascribes a special
meaning to; it guards the names of these with a double underscore `__`
on each side, so it is unlikely to collide with a name you might want
to use for a method of your own. These methods allow us to enable
instances of our classes to behave more like Python objects you're
used to dealing with, using the typical set of operators, rather than
needing to use method calls for everything.

Let's look at some more examples of these. Firstly, wouldn't it be
nice if we got something more descriptive when Python referred to our
`Triangle`s?

~~~
a_triangle
~~~
{: .language-python}

~~~
<__main__.Triangle at 0x1235e7b00>
~~~
{: .output}

We can do this by implementing the `__repr__` method (short for
"representation"). This is designed to be something that looks like
Python code&mdash;ideally, something that if you pasted it back in, you'd
get the same (or at least a similar) object. For the `Triangle`, this
could look like:

~~~
    def __repr__(self):
        return f"Triangle({self.side_lengths})"
~~~
{: .language-python}

Testing this now gives:

~~~
a_triangle = Triangle([3, 4, 5])
a_triangle
~~~
{: .language-python}

~~~
Triangle([3, 4, 5])
~~~
{: .output}


## Simplifying data-holding classes with `dataclasses`

Writing `__init__`, `__repr__`, and `__eq__` by hand every time we create a
class that mainly just holds a fixed set of values is repetitive. Python's
`dataclasses` module (part of the standard library since Python 3.7) can
generate all three of these for us, from a list of type-annotated fields.

Written by hand, a simple 2D point class looks like:

~~~
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __repr__(self):
        return f"Point(x={self.x!r}, y={self.y!r})"

    def __eq__(self, other):
        if not isinstance(other, Point):
            return NotImplemented
        return (self.x, self.y) == (other.x, other.y)
~~~
{: .language-python}

Using `@dataclass`, this becomes:

~~~
from dataclasses import dataclass

@dataclass
class Point:
    x: float
    y: float
~~~
{: .language-python}

~~~
p1 = Point(1, 2)
p2 = Point(1, 2)
print(p1)
print(p1 == p2)
~~~
{: .language-python}

~~~
Point(x=1, y=2)
True
~~~
{: .output}

The `x: float` and `y: float` lines are type-annotated field declarations,
not assignments&mdash;`@dataclass` reads them to work out what `__init__`
should accept and store, and what `__repr__` and `__eq__` should use.

> ## `@dataclass` doesn't replace all your methods
>
> `@dataclass` only ever generates `__init__`, `__repr__`, `__eq__`, and
> (with `order=True`) the ordering dunders `__lt__`, `__le__`, `__gt__`,
> and `__ge__`. Anything else&mdash;`area()`, `perimeter()`, arithmetic
> operators, custom validation&mdash;you still write yourself, exactly as
> before. It removes repetitive constructor/representation/equality
> boilerplate; it doesn't write your class's actual behaviour for you.
{: .callout}

> ## When the default `__eq__` isn't enough
>
> `@dataclass`'s generated `__eq__` compares fields in order, as if they
> were a tuple. That's not always what we want: our `Triangle` class
> needs to treat `[3, 4, 5]` and `[4, 5, 3]` as the same triangle, which a
> plain field-by-field comparison can't express. If you define `__eq__`
> yourself in the class body, it overrides the one `@dataclass` would
> otherwise have generated, so you can mix and match&mdash;let
> `@dataclass` write `__init__` and `__repr__`, and keep a hand-written
> `__eq__` where the default logic isn't right.
{: .callout}


## Other comparisons

What about if we want to know how two objects compare to each other?
We can do this by implementing `__lt__`, `__gt__`, `__le__`, `__ge__`,
and `__ne__`, representing `<`, `>`, `<=`, `>=`, and `!=`
respectively. For example, an implementation of `__lt__` might look
like:

~~~
    def sides_with_max_first(self):
        max_index = self.side_lengths.index(max(self.side_lengths))
        if max_index == 0:
            return self.side_lengths
        elif max_index == 1:
            return self.side_lengths[1:] + [self.side_lengths[0]]
        else:
            return [self.side_lengths[2]] + [self.side_lengths[:2]]

    def __lt__(self, other):
        if self.area() != other.area():
            return self.area() < other.area()
        elif self.perimeter() != other.perimeter():
            return self.perimeter() < other.perimeter()
        elif self == other:
            return False
        else:
            return self.sides_with_max_first() < other.sides_with_max_first()
~~~
{: .language-python}

Testing this:

~~~
a_triangle = Triangle([3, 4, 5])
b_triangle = Triangle([5, 12, 13])
a_triangle < b_triangle
~~~
{: .language-python}

~~~
True
~~~
{: .output}

Python then does two very nice things for us: firstly, since we have
defined `__lt__`, it can now sort lists of `Triangle`s for us. And
while we could leave only the `<` operator defined, this could be
confusing for those using the class; fortunately, given
implementations of `__eq__` and `__lt__`, Python can automatically
generate the other relational operators using the
`functools.total_ordering` decorator.

~~~
from functools import total_ordering

@total_ordering
class Triangle(Polygon):
   ...
~~~
{: .language-python}

> ## Ordering with `@dataclass`
>
> If a class uses `@dataclass`, you get the same result by passing
> `order=True` to the decorator (`@dataclass(order=True)`) instead of
> writing `__lt__` and applying `@total_ordering`&mdash;`dataclass` will
> generate all of `__lt__`, `__le__`, `__gt__`, and `__ge__` for you,
> again comparing fields in field-declaration order. This only works
> for the default, tuple-like comparison; `Triangle`'s "same triangle in
> any rotation" logic still needs to be written by hand, as above.
{: .callout}

> ## Sorting random triangles
>
> Add a class method that generates a triangle with three random edge
> lengths (for example, using `random.random()`). Use this to construct
> and sort a list of 10 random triangles.
>
>> ## Solution
>>
>> Add an import at the top of the file:
>>
>> ~~~
>> from random import random
>> ~~~
>> {: .language-python}
>>
>> Also add new class method:
>>
>> ~~~
>>     @classmethod
>>     def random(cls):
>>         """Returns a triangle with three random length sides in the
>>         range [0, 1).
>>         If the sum of the two short sides isn't longer than the
>>         long side (and so the triangle doesn't close), then try
>>         again. There is an infinitesimal probability that this
>>         method will never return, as randomness keeps delivering
>>         invalid triangles."""
>>
>>         random_triangle = cls([random(), random(), random()])
>>         while isinstance(random_triangle.area(), complex):
>>             random_triangle = cls([random(), random(), random()])
>>         return random_triangle
>> ~~~
>> {: .language-python}
>>
>> Testing this:
>>
>> ~~~
>> random_triangles = [Triangle.random() for _ in range(10)]
>> [triangle.area() for triangle in sorted(random_triangles)]
>> ~~~
>> {: .language-python}
> {: .solution}
{: .challenge}

> ## Arithmetic
>
> In the same way that `__lt__` and friends correspond to relational
> operators, arithmetic operations like `+`, `-`, `*`, etc. can be
> defined with methods like `__add__`, `__sub__`, and `__mul__`.
>
> Define a new class `ErrorBar` to represent a number with an
> associated error in Gaussian statistics, with fields `centre` and
> `error`. Use `@dataclass` so you don't have to write `__init__` by
> hand, and add `__repr__`, `__add__`, `__sub__`, `__mul__`, and
> `__truediv__` methods, making the (very unreasonable) assumption that
> all errors are uncorrelated.
>
>> ## Solution
>>
>> `@dataclass` generates `__init__` (and `__eq__`) for us from the
>> `centre` and `error` field declarations. We still write `__repr__`
>> ourselves, since we want the `"centre ± error"` format rather than
>> the default `ErrorBar(centre=..., error=...)` one that `@dataclass`
>> would otherwise generate&mdash;defining it in the class body
>> overrides the auto-generated version. The arithmetic methods aren't
>> something `@dataclass` can generate at all, so those are hand-written
>> as before.
>>
>> ~~~
>> from dataclasses import dataclass
>>
>> @dataclass
>> class ErrorBar:
>>     centre: float
>>     error: float
>>
>>     def __repr__(self):
>>         return f"{self.centre} ± {self.error}"
>>
>>     def __add__(self, other):
>>         centre = self.centre + other.centre
>>         error = (self.error ** 2 + other.error ** 2) ** 0.5
>>         return ErrorBar(centre, error)
>>
>>     def __sub__(self, other):
>>         centre = self.centre - other.centre
>>         error = (self.error ** 2 + other.error ** 2) ** 0.5
>>         return ErrorBar(centre, error)
>>
>>     def __mul__(self, other):
>>         centre = self.centre * other.centre
>>         error = centre * ((self.error / self.centre) ** 2 +
>>                           (other.error / other.centre) ** 2) ** 0.5
>>         return ErrorBar(centre, error)
>>
>>     def __truediv__(self, other):
>>         centre = self.centre / other.centre
>>         error = centre * ((self.error / self.centre) ** 2 +
>>                           (other.error / other.centre) ** 2) ** 0.5
>>         return ErrorBar(centre, error)
>> ~~~
>> {: .language-python}
>>
>> Testing this, note that we also get `__eq__` for free from
>> `@dataclass`:
>>
>> ~~~
>> a = ErrorBar(1.0, 0.1)
>> b = ErrorBar(1.0, 0.1)
>> print(a)
>> print(a == b)
>> print(a + ErrorBar(2.0, 0.2))
>> ~~~
>> {: .language-python}
>>
>> ~~~
>> 1.0 ± 0.1
>> True
>> 3.0 ± 0.223606797749979
>> ~~~
>> {: .output}
> {: .solution}
{: .challenge}


## Callable objects

By implementing the `__call__` method, we can allow instances of a
class to be called like functions. For example, returning to the
`FunctionPlotter` example:

~~~
from numpy import linspace, sin
from matplotlib.colors import is_color_like
from matplotlib.pyplot import show, subplots

class FunctionPlotter:
    def __init__(self, color="red", linewidth=1, x_min=-10, x_max=10):
        self.color = color
        self.linewidth = linewidth
        self.x_min = x_min
        self.x_max = x_max

    @property
    def color(self):
        return self._color

    @color.setter
    def color(self, color):
        assert is_color_like(color)
        self._color = color

    def plot(self, function):
        """Plot a function of a single argument.
        The line is plotted in the colour specified by color, and with width
        linewidth."""
        fig, ax = subplots()
        x = linspace(self.x_min, self.x_max, 1000)
        ax.plot(x, function(x), color=self._color, linewidth=self.linewidth)

    def __call__(self, *args, **kwargs):
        return self.plot(*args, **kwargs)

plotter = FunctionPlotter()
plotter(sin)

show()
~~~
{: .language-python}

> ## Subclassing with `__call__`
>
> Do we need to redefine `__call__` on each subclass of
> `FunctionPlotter` to get the correct version of the `plot()`
> function? Why/why not?
>
>> ## Solution
>>
>> No; `self` returns the current instance, so the call to
>> `self.plot()` will pick up the correct version of `plot()` for
>> whichever class the instance is.
> {: .solution}
{: .challenge}


## Collections and iterables

Python also gives us the power to make our objects behave like
iterable or collection types (for example tuples, lists, dicts, and
generators). For example, to let instances of the class behave with
the `len()` function, we implement `__len__()`. For example, adding
this to the `Polygon` class:

~~~
    def __len__(self):
        return len(self.side_lengths)
~~~
{: .language-python}

will define the length of the object as the number of edges that the
`Polygon` has. (Note that we shouldn't make this the
perimeter&mdash;Python expects `len()` to return a non-negative
integer.) Testing this,

~~~
a_polygon = Polygon([1, 2, 3, 4, 5])
print(len(a_polygon))
~~~
{: .language-python}

~~~
5
~~~
{: .output}

We can also let our code loop over elements of our objects by
implementing the `__iter__()` method, which should return an
_iterator_; this is a particular type of object in Python that makes
things like `for` loops work. We can get one of these from any
iterable via the `iter()` function.

~~~
     def __iter__(self):
         return iter(self.side_lengths)
~~~
{: .language-python}

We can now iterate through the sides of our `Polygon`s without having
to get the `side_lengths` property each time.

~~~
a_polygon = Polygon([1, 2, 3, 4, 5])
for side_length in a_polygon:
    print(side_length)
~~~
{: .language-python}

~~~
1
2
3
4
5
~~~
{: .output}

> ## In reverse
>
> The `reversed()` function returns an iterator over the elements of
> an iterable or collection going backwards. This is implemented for
> classes via the `__reversed__` method. Implement this for the
> `Polygon` class, and test your implementation.
>
>> ## Solution
>>
>> Method:
>>
>> ~~~
>>     def __reversed__(self):
>>         return reversed(self.side_lengths)
>> ~~~
>> {: .language-python}
>>
>> Test:
>>
>> ~~~
>> a_polygon = Polygon([1, 2, 3, 4, 5])
>> for side_length in reversed(a_polygon):
>>     print(side_length)
>> ~~~
>> {: .language-python}
>>
>> ~~~
>> 5
>> 4
>> 3
>> 2
>> 1
>> ~~~
>> {: .output}
> {: .solution}
{: .challenge}

> ## Getting specific elements
>
> You can also allow your code to access elements via square brackets,
> just like with lists. The `__getitem__()` method does this, taking
> the index (or key) being sought as its argument.
>
> For a non-dict-like collection, `__getitem__()` can work for both
> integer indices and for slices.
>
> Implement `__getitem__()` for the `Polygon` class. Since in our
> current implementation of `Polygon`, it doesn't make sense to take a
> subset of the sides, requesting a slice should raise `IndexError`;
> only requesting a single element with an integer index should work.
>
>> ## Solution
>>
>> Implementation:
>>
>> ~~~
>>     def __getitem__(self, key):
>>         if type(key) is int:
>>             return self.side_lengths[key]
>>         else:
>>             raise IndexError
>> ~~~
>> {: .language-python}
>>
>> Test:
>>
>> ~~~
>> a_polygon = Polygon([1, 2, 3, 4, 5])
>> print(a_polygon[2])
>> ~~~
>> {: .language-python}
>>
>> ~~~
>> 3
>> ~~~
>> {: .output}
> {: .solution}
{: .challenge}

> ## `for` loops with `__getitem__()`
>
> Once a class has `__getitem__()` defined, then Python will
> automatically work out how to loop over it, even in the absence of
> `__iter__()` (although adding this does make it more
> efficient). Even better, when `__len__()` is also implemented, then
> Python automatically knows how to `reversed()` the class as well.
>
> Test this by removing the implementations of `__iter__()` and
> `__reversed__()` from `Polygon` and testing the loops forwards and
> backwards again.
{: .challenge}

## More dunder methods

Python offers many more dunder methods than could possibly be covered
in this episode. A full listing, categorised by the functions that
they serve, can be found in [the Python
documentation][special-method-names]


[special-method-names]: https://docs.python.org/3/reference/datamodel.html#special-method-names
