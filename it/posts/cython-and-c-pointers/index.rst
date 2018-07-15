.. title: Cython and C pointers
.. slug: cython-and-c-pointers
.. date: 2017-01-06 16:43:40 UTC+01:00
.. tags: python cython c
.. category: 
.. link: 
.. description: 
.. type: text

Cython_ is a powerful tool that can speed up your Python_ code or can help you
to quickly create an extension module for a library that has c bindings.

This post is not a cython tutorial but its about a problem I have encountered
during the development of a wrapper to Wakaama LWM2M library; if you
want to learn more about Cython_ please refer to its documentation_.

During the development of said wrapper I have had a nasty strange bug affecting
my code, at some point some object referenced by a pointer kept at the C library
side seemed to change its value! For example think about this situation:

- you pass an object to a certain c function
- the object's pointer is stored internally by the c library
- you retrieve the object from the c library but its content its not the same...it's not even an instance of the same class!

To reproduce this behaviour please have a look at overwrite.py in my test project_.

At first I've been thinking about memory corruption caused by the C library code;
this wrong assumption costed me at least two days of messing with gdb (that
saved my life in another memory related bug) that have not given any useful
insight.

I don't remember how I've spotted this problem but probably I should have tryed
everything until something pointed me to the right direction.

What seems to happen here is something like this:

1. create an object and assign it to a variable A
2. store its pointer in the C library
#. get back the pointer of the object; it will be the same object as referenced by A
#. assign a new object to A
#. create an object assign it to a variable B
#. get back the pointer of the object; it will NOT be the same object as
   referenced by A but it will be the one referenced by B 

When the variable A is referencing the new object the old one is not referenced
by anyone in the python world and its memory will became available to store the
new object assigned to B and the pointer in the C world is now pointing to what
is referenced by B.

It may seems obvious but when it happened to me it was inside a callback
originating in the C code and being handled by a function defined in the
cython context.

Lesson learned
______________

When working with two different languages together
nothing is naive especially when you are wrapping some code that you don't
really know and that can bite your ass as soon as you think "what can go wrong?"


.. _Python: https://www.python.org/
.. _Cython: http://www.cython.org/
.. _documentation: https://cython.readthedocs.io/en/latest/
.. _project: https://github.com/fpischedda/cython-post
