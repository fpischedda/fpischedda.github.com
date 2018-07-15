.. link: 
.. description: 
.. tags: pyqt python datepicker
.. date: 2013/12/10 21:52:42
.. title: Our QT date picker
.. slug: our-qt-date-picker

==================
Our QT date picker
==================

One of the first works carried out just entering at SysGrove (http://sysgrove.com ) 
consisted in rewriting the date picker component in our application and 
requests regarding this component was that it was similar, at least as a 
feature, to the date picker available in windows 7 ™. The most interesting 
aspect of this component is the ability to be able to do some sort of zoom 
dates, I try to explain with an example:
 
- It would be a classic date picker where are the days of the current month and
  the ability to change the month with the classic arrows 
- Clicking on the button in the middle of the arrows the days are replaced by
  months and the arrows will change the current year
- Again by clicking on the button in the middle of the arrows months will be
  replaced by the years and the arrows will change the decade
- Selecting a year will return a month view and by selecting a month will
  return a day view

Some images could help to understand the behaviour:

.. figure:: /images/qt-calendar/date-picker-day.png
   :alt: day selector

   The day selector, with month changer arrows; if you click the middle button
   you'll see the month selector.


.. figure:: /images/qt-calendar/date-picker-month.png

   The month selector, if you click on the middle button you'll see the year
   selector.


.. figure:: /images/qt-calendar/date-picker-year.png

   The selector selector.

Fortunately SysGrove is a company that believes strongly in open source and
sharing so I got permission to release this as open source project component,
nice! :)
 
At present the appearance of the component is not that great and you have to
put hand to customize the code of the component , in the future I hope to be
able to allow the customization of the graphical component directly in the
process of creating an instance rather than in its base code.
 
The source of this component for the moment can be found in the main repository
of our application ( https://bitbucket.org/sysgrove/sysgrove ) path sysgrove /
ui / classes / widgets / calendar.py
