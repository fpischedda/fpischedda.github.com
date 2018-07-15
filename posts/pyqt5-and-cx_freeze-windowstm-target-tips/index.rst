.. title: PyQT5 and cx_Freeze Windows™ target tips
.. slug: pyqt5-and-cx_freeze-windowstm-target-tips
.. date: 2014/05/02 13:04:54
.. tags: python pyqt5 cx_freeze packaging windows
.. link: 
.. description: 
.. type: text

One of my preferred benefits of Python_ is its portability and if coupled
with good libraries such as PyQT5_ the possibilities are endless.

Now imagine you created a new shiny desktop application and you want to
distribute it, how can you package the app for easy installation?

Say hello to cx_Freeze_
=======================

cx_Freeze_ is a tool that can create a standalone version of your application
so you can easily distribute it and the end user don't have to:

- download your source code
- a python interpreter
- setup pip or easy_install, libraries and so on

Another nice feature of cx_Freeze_ is that it can create an installer for many
different operating systems giving your application a more "professional" look.

I strongly suggest giving it a try; using one of the sample setup scripts
should be enough to get started.

Some minor issues
=================

A simple application which only depends on pure python packages usually will
not give you any headaches but if you have dependencies like PyQT5 a bit of
attention is required.
In my case not all the required DLL were included in the installer package and
that generated a strange error message that was very hard to debug but thanks
to sites like StackOverflow_ I've found a nice fix_ for it.
It is worth noting that linked solution is not (always?) enough but there is a
quick solution (at least in my case): add the file libegl.dll to the
"include_files" cx_Freeze_ building options.

How to test your shiny new installer
====================================

In order to test your installer and be sure that all the DLLs are incuded and
your application is not "cheating" on you using system DLLs I suggest to create
a clean windows installation inside a virtual machine; this way you can test
your installer in a real case scenario and fix your build scripts accordingly.

.. _Python: http://www.python.org
.. _PyQT5: http://pyqt.sourceforge.net/Docs/PyQt5/index.html
.. _cx_Freeze: http://cx-freeze.sourceforge.net
.. _StackOverflow: http://www.stackoverflow.com
.. _fix: http://bit.ly/1kvWVAa
