---
layout: post
title: Python Decorators
description: This article depicts what python decorators are and how they can be used in the context of Django
categories: Technical
---
## What are Decorators and Python Decorators?

If you look for Decorator pattern on [wikipedia](https://en.wikipedia.org/wiki/Decorator_pattern), you will find:

>In object-oriented programming, the decorator pattern (also known as Wrapper, an alternative naming shared with the Adapter pattern) is a design pattern that allows behavior to be added to an individual object, either statically or dynamically, without affecting the behavior of other objects from the same class

In python, decorators are used to alter the funtionality of the methods without changing the code of the function. It is not exactly same as Decorator pattern but some analogy can be made between them as they are quite similar. Simply put, decorators decorate the function by adding some extra features to the function. The major advantage of using decorators is that it provides [separation of concern](https://en.wikipedia.org/wiki/Separation_of_concerns). Things like validation, authentication, authorization can go to one place and can be used at multiple locations without worrying about its implementation everytime. Also, one knows where should one look if one needs to change the strategy used in any of the above function and it will be reflected everywhere. 


## Where are they used?

In web development specifically, decorators are generally used for validations, memoization, adding filters, etc. All the authorization and authentication libraries provide decorators that do the job of validating the requests. For example you can add `@login_required` above every view method that requires authentication. You can write `@authenticate_api` above every API view that reqiures headers authentication. And as the decorator code resides in a separate function, it can be used on any no. of methods for validation. 
It can also be used for caching the responses of a function i.e memoization. It can take in the request parameters that come from the decorated function, construct a key of it, try to fetch its value from the cache, return the value if found, else perform the computation that resides in the decorated function. It allows you to write the caching logic only once in the decorator. You can then use it anywhere in your project without going through caching woes everytime you add something


## How to use them? (Some examples) 

There are two ways to construct decorators:

###Using Functions:

Python has a useful feature of passing functions as variables. This feature is the basis of how decorators work. The decorator will take the function that it has to modify, add some additional code and return the modified function. Now, whenever the decorated function is called, the function returned by the decorator is called instead. See the below example for its implementation 

```python
"""
This adds the welcome message to
the function
"""
def welcome(func):
    def _inner(*args, **kargs):
        return "hello " + func(*args, **kargs)
    return _inner

@welcome
def name():
    return "Aash"

print name() ## returns hello aash
print name.__name__ ## returns welcome
```
#### Easy-to-debug decorator:

`name.__name__` returns the name of the decorator (welcome) instead of the function name. It is a pain to debug the functions that are being decorated as we won't be able to know the actual name of the function being used. There is a way provided by python to handle this:

```python
from functools import wraps

def sophisticated_welcome(func):
    @wraps(func)
    def _inner(*args, **kargs):
        return "Hello " + func(*args, **kargs) + ". Please make yourself comfortable."
    return _inner

@sophisticated_welcome
def name():
    return "Aash"

print name() ## returns Hello Aash. Please make yourself comfortable
print name..__name__ ## returns name
```

#### Parameterizing Decorators:

Apart from that it is also possible to parameterize the decorator function:

```python
def super_welcome(extra):
    def greet(func):
        def _inner(*args, **kargs):
            return "Hello " + func(*args, **kargs) + ". I have some " + extra + " for you!"
        return _inner
    return greet

@super_welcome("chocolates")
def name():
    return "Aash"

print name() ## returns Hello aash. I have some chocolates for you
```

#### Using Multiple Decorators/Decorator Chaining:

You can also use multiple decorators on a single function. In such cases, it is important to understand how decorator chaining works.

```python
def eat(func):
    def _inner(*args, **kargs):
        return func(*args, **kargs) + " Would you like to order something to eat?"
    return _inner

def welcome(func):
    def _inner(*args, **kargs):
        return "Welcome " + func(*args, **kargs) + "!"
    return _inner
 
@eat
@welcome
def name():
    return "Aash"

print name() ## returns Welcome Aash! Would you like something to eat?

```

As we can see, welcome is called first, followed by eat. Thus, decorators are called from **bottom up**. This kind of method is particularly used in cases where you want to both bypass CSRF authentication and add token-based authencations to the third-party API request view.

### Using Classes:

You can also use a class instead of function as a decorator.

```python
class java_kind_of_welcome:
    def __init__(self, func):
        self.func = func

    def __call__(self, *args, **kargs):
        return "Hello " + self.func(*args, **kargs) + ". I am a class. What can I do for you?"

@java_kind_of_welcome
def name():
    return "Aash" 

print name() # returns Hello Aash. I am a class. What can I do for you?

```

The important thing to realize is that the decorator should be callable. It doesn't matter if it is a class or a function. 


## References:

http://stackoverflow.com/questions/4650333/difference-between-decorator-classes-and-decorator-functions

https://wiki.python.org/moin/PythonDecorators

https://artemrudenko.wordpress.com/2013/04/15/python-why-you-need-to-use-wraps-with-decorators/

http://python-3-patterns-idioms-test.readthedocs.org/en/latest/PythonDecorators.html



