---
title: "Objects in Python"
teaching: 30
exercises: 20
questions:
- "What is object oriented programming?"
- "When should I use object oriented programming?"
- "What are objects in Python?"
- "What is a class or type?"
- "What is an instance of a class?"
- "What is a member?"
- "What is a method?"
- "Can objects belong to more than one class?"
- "How can objects be created from a class?"
objectives:
- "Be able to distinguish between class and object"
- "Be able to construct objects via a class's constructor"
keypoints:
- "`list` and `numpy.ndarray` are commonly-used classes; specific lists and arrays are corresponding objects"
- "Calling the class as a function constructs new objects of that class"
- "Objects have members and methods; members are attributes and methods dictate the behaviour of the object"
- "Classes can inherit from other classes; objects of the subclass are automatically also of the parent class"
---

## What is object oriented programming?

Since the 1990s, Python has grown in popularity. As a result, the complexity of some Python packages has reached the point where their 
application programming interface no longer reduces to simple function calls. Such packages might have an 
_object oriented_ design.

As a Python user, you will likely need to grasp the essence of object oriented programming in order to use these packages. Such packages are built around _objects_, i.e. data structures with _attributes_ and _methods_. Attributes are properties, we can think of them as properties (e.g. "red", "leather", etc.). Methods are functions that operate on the object or on other objects, we can think of them as verbs (e.g "walk", "eat", ...). The concepts of objects, attributes and methods will be further clarified in the course of this workshop.

Object oriented programming exists in other languages (C++, Java and Fortran). Many concepts presented here apply equally to these languages. However, Python is the ideal language to learn object oriented programming due to its interactivity.

## But...what is an object?

An object is a way of packaging data. Instead of holding a single number, an object may hold a collection of data, numbers, strings and perhaps other objects. The content of an object can be manipulated collectively as a single entity, for instance passed as one argument to a function.

> ## Provide an example of an object in Python
>
>> ## Solution
>> Everything that can be stored as a variable is an object in Python. This is true for a character string, a list, a dictionary, a function but also numbers. A number has real and imaginary parts.
> {: .solution}
{: .challenge}

## When should I use object oriented programming?

Object oriented programming allows you to perform complex tasks in a simpler way, sometimes in a more efficient way.

Consider for example a plotting package. Plotting data typically involves many fine grained operations (creating the plot, adding axis labels, title, legend etc.). One could write a function that performs all these operations with a single call. However, this function would take a lot of arguments to control all aspects of plotting. What if you wanted to change the title? You would have to call the function again with the same arguments except for one small change. This is error prone but also inefficient since most of the plot was fine and only minor tweaking was needed.

You may face the dilemma of using a function or a class when implementing code. Using a _procedural_ approach may be the right choice if the code

 * Performs a simple operation
 * The function takes only a few arguments
 * The function is pure (does not have a state) 
 * And you don't expect the need to add functionality with time

## An example showing the difference between procedural and object-oriented programming

In the procedural programming approach, the mean of a Numpy array can be computed by using
`numpy.mean`,

~~~
import numpy
numbers = numpy.arange(10)
print(numpy.mean(numbers))
~~~
{: .language-python}


However, we can also calculate the mean of a Numpy array as:

~~~
print(numbers.mean())
~~~
{: .language-python}


> ## The dot notation
>
> Note the `.` (dot) separating the object (`numbers`) from the `mean` method. The dot notation is a common feature of many object oriented programming languages. It means `mean` is a *function that belongs to*. In the first case, `mean` belongs to module `numpy` and in the second case `mean` belongs to object `numbers`. 
{: .callout}

Let's see if we can do this with a normal list:
~~~
more_numbers = [1, 2, 3, 4]
print(more_numbers.mean())
~~~
{: .language-python}
~~~
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
Cell In[3], line 2
      1 more_numbers = [1, 2, 3, 4]
----> 2 print(more_numbers.mean())

AttributeError: 'list' object has no attribute 'mean'
~~~
{: .output}


In this case Python will complain with an error. How does Python know it can do this for `numbers` but not `more_numbers`? This will be explained later.

## An example of object oriented design from the scikit-learn package

The example below shows how object oriented programming lies at the heart of many Python packages. Here, a linear regression model `mymodel` is created, the model is fitted with data and predictive values `ypred` are inferred from the model after it is fitted, 
~~~
from sklearn.linear_model import LinearRegression
# create a model
mymodel = LinearRegression(fit_intercept=False)
# fit/train the model
mymodel.fit(X=[[1,],[2,]], y=[1, 2])
# predict/infer the output given some data
ypred = mymodel.predict(X=[[1.2,],[1.8,], [2.2,]])
~~~
{: .language-python}
While it would have been possible to write a function that performs the `fit` and `predict` operations together, equivalent to
~~~
def fit_and_predict(Xtrain, ytrain, Xpred):
    return LinearRegression(fit_intercept=False).fit(X=Xtrain, y=ytrain).predict(X=Xpred)
~~~
{: .language-python}
the object oriented design provides additional advantages. Specifically, the `predict` operation can be called as many times as desired once the model is fitted. In many machine learning algorithms, predicting values is cheap compared to fitting. Therefore, by separating the `fit` and the `predict` calls we can be more efficient.

Note that the reason we can chain LinearRegression, fit and predict together is that the `fit` call returns the object. 

> ## Discuss the pros/cons of object oriented programming over procedural programming
>
>> ## Solution
>> This is not an exhaustive list but here are some differences:
>>  * The procedural approach is _generic_, it works both on lists and numpy arrays
>>  * The object oriented approach requires a specific type, which makes it safer
>>  * The object oriented way may be more concise
>>  * In the object oriented way we're telling people what they can do with an object
> {: .solution}
{: .challenge}


## What are classes, instances, members and methods?

>## Class
> A _class_ is a type of object. It is the answer to the question "what is ...?". To use a real-world example, we could have a class `Handbag` that describes all types of handbags. Question: "what is that object?". Answer: "It's a `HandBag`".
{: .callout}

>## Instance
> We say that an object of a particular class is an _instance_ of that class. The handbag you're holding right now is an _instance_ of the `Handbag` class.
{: .callout}

> ## Member
> A handbag is made of lots of stuff (fabric, strap, handle, zipper, etc.). Each of these items can be a _member_ of the 
> `Handbag` class.
{: .callout}

> ## Method
> You can do many things with a handbag. You can carry it, you can lend it to a friend, offer it as a birthday present or you 
> can swing it into the face of your worst enemy. These actions are called _methods_.
{: .callout}

{% include image.html url="../assets/img/handbag-anatomy.svg"
alt="Cartoon illustration of a handbag with arrows pointing to its
labelled parts: the handle, the zipper, and the strap"
caption="Some of the members of the Handbag class: its handle, zipper, and strap."%}

## Finding out to which class an object belongs to

In literature, you'll find a subtle
distinction between _class_ and _type_. However, since in Python 3
we can't have one without the other, we will use both terms interchangeably.

We can obtain the type of an object with the `type` function.

~~~
type(numbers)
~~~
{: .language-python}

~~~
numpy.ndarray
~~~
{: .output}

We can check if an object is an _instance of_ a particular class with the `isinstance` function.

~~~
isinstance(numbers, numpy.ndarray)
~~~
{: .language-python}

~~~
True
~~~
{: .output}

or 

~~~
isinstance(numbers, list)
~~~
{: .language-python}

~~~
False
~~~
{: .output}

Every object is created with a single class, which can't be changed. The class of an object can also provide behaviour that the object might have, by providing functions to objects in its class. As seen before, these functions can be called by using a dot after the variable name, for example:
~~~
numbers.mean()
~~~
{: .language-python}

We say that the `numpy.ndarray` class provides the `mean` _method_. Since `numbers` belongs to the class `numpy.ndarray`, we can use the `mean` method on the object referred to by `numbers`, by calling `numbers.mean()`. This allows objects of the `numpy.ndarray` class to provide functionality specific to objects of class `numpy.ndarray`.

Every instance of `numpy.ndarray` has a member `shape`, which holds the dimensions of the underlying data.

~~~
numbers.shape
~~~
{: .language-python}

~~~
(10,)
~~~
{: .output}

> ## Mutable vs immutable objects
>
> It's worth noting that both mutable and immutable objects can have methods. Methods of immutable objects, however, can't change
> the underlying object. If needed, they will return a brand new object, and set the expected value in the new object. 
> Methods of mutable objects can, and often do, change the object.
{: .callout}

> ## Other common classes
>
> 1. What other classes have you encountered previously when using Python?
> 2. What members do these classes contain?
> 3. What methods do they provide?
{: .challenge}

## Making an object

A class can be called as a function, in which case it constructs new instances
of itself. While this is not the only way to make objects, it is one that all
classes offer. For example, a new list can be created as:

~~~
students = list()
print(students)
print(type(students))
~~~
{: .language-python}

~~~
[]
<class 'list'>
~~~
{: .output}

> ## Making a Numpy array
>
> While all classes can be constructed by calling their name, some classes
> don't recommend this route. For example, `numpy.ndarray` is used internally
> by Numpy to initialise its arrays, but Numpy recommends using one of the
> higher-level functions like `numpy.zeros`, `numpy.ones`, `numpy.empty`, or
> `numpy.asarray` to construct an array (of zeroes, of ones, without
> initialising the data, and initialising from an existing data structure like
> a list, respectively).
{: .callout}

## Finding out what's in an object

Now that we know how to create objects (i.e. instances of a class), we may want to know:
 1. What's in the object
 2. And what can we do with an object.

Python is unique in that it lets you peek inside the object. This is known as introspection. The content of an object can be gleaned with the `dir` function. For instance,

~~~
dir(students)
~~~
{: .language-python}
which lists members and methods. Somewhere at the bottom of the list you'll see `sort`, a method that sorts the list of students,
~~~
type(students.sort)
~~~
{: .language-python}

~~~
builtin_function_or_method
~~~
{: .output}

## Inheritance

Object-oriented programming allows relationships to be defined between classes/types.
One class may be considered to be a specialisation or _subclass_ of another.
For a real-world example, a car could be considered a specialisation or subclass of the class of all vehicles.

This is very frequently seen in the way Python handles exceptions. For example,
if we check what type a `ValueError` is, we see that it is of
`class 'ValueError'`:

~~~
an_error = ValueError("A value must be provided")
print(type(an_error))
if isinstance(an_error, ValueError):
    print("an_error is a ValueError")
~~~
{: .language-python}

~~~
<class 'ValueError'>
an_error is a ValueError
~~~
{: .output}

However, we can also check if it is an `Exception`:

~~~
if isinstance(an_error, Exception):
    print("an_error is an Exception")
~~~
{: .language-python}

~~~
an_error is an Exception
~~~
{: .output}

This is because `ValueError` is a subclass of `Exception`: value errors are a
specific type of exception that can occur, and so should have all the same
logic that is common to all exceptions.

![Tree of Python exception classes](../fig/exception-hierarchy.svg)

One place this can be used is to structure exception handling; for example:

~~~
numerator = 5
denominator = 0

try:
    print(numerator, "divided by", denominator, "is", numerator / denominator)
except ZeroDivisionError:
    print("You can't divide by zero!")
except Exception:
    print("Something else went wrong.")
~~~
{: .language-python}

`ZeroDivisionError` is another subclass of `Exception`. On encountering an
exception, Python checks each `except` in turn to see whether the exception
matches the class being tested for. The more specific `ZeroDivisionError`
catches the specific case of dividing by zero, but the block is skipped for
all other issues, which are then handled by the more general `Exception`.

{% include links.md %}

