.. link: 
.. description: 
.. tags: python sqlalchemy
.. date: 2013/12/11 15:33:03
.. title: Logging model changes with SQLAlchemy listeners
.. slug: logging-model-changes-with-sqlalchemy-listeners

================================================
Logging modela changes with SQLAlchemy listeners
================================================

Scenario
--------

Imagine you have a slew of models in your application, at some point you feel 
the need to log somewhere creation, modification or deletion of data belonging 
to these models.
How to proceed without having to modify the classes one by one?

What's on sqlalchemy
--------------------

SQLAlchemy (http://sqlalchemy.org) offers a couple of interesting mechanisms: 
the first concerns the possibility to hook to some event listeners such as 
before_insert, before_update, before_delete and the corresponding after_*.
Additional help is provided by sqlalchemy the opportunity to work on a model 
after its definition by overriding the method __declare_last__.
Using these facts, and assuming that you have defined a model named MyModel,
if we wanted to intercept the event "after_insert" we could write the following 
code:

.. code:: python

 class MyModel(object):
 #lets pretend to have defined our model

   def after_insert(mapper, connection, target):
     #do some stuff
     pass

   @classmethod
   def __declare_last__(cls):
     event.listen(cos, "after_insert", cls.after_insert)

Whenever an object of class MyModel will be entered into the database
after_insert method will be called , passing as parameters the mapping of the
model, the connection and the target is none other than the object that has
just been entered into the database.

In the event that you are intercepting the creation or deletion of an object 
is sufficient to access its primary key to identify it in your log, but if we 
wanted to know which fields have been modified, with new and old values, as a 
result of an update it gets a little more complicated, but not too much.
In fact sqlalchemy allows us, quite easily , to check the status of the fields
of an object using the function sqlalchemy.orm.attributes.get_history (http://docs.sqlalchemy.org/en/latest/orm/session.html#sqlalchemy.orm.attributes.get_history).
This function is called for each field, it returns an object of type History (http://docs.sqlalchemy.org/en/latest/orm/session.html#sqlalchemy.orm.attributes.History)
which we will use the method has_changes() to check for changes, and if there were,
getting the new and old values of the field that we are analyzing, for example:

.. code:: python

 h = get_history(target, "a_field")
 if h.has_changes():
   #do something using h.deleted list to get the old values
   #do something using h.added list to get the new values

LoggableMixin
-------------

Clearly to do this for all models of an application may be costly in terms of 
time and code maintenance (and extremely annoying) so you might think about
creating a generic Mixin with which to extend the models of our application.
Below is the skeleton for the implementation of the above mixin, omitting the 
details of where and how the logs are stored:

.. code:: python

 class LoggableMixin(object):

   def after_insert(mapper, connection, target):
     #do some stuff for the insert
     pass

   def after_update(mapper, connection, target):
     #do some stuff for the update, maybe saving the changed fields values using get_history
     pass

   def after_delete(mapper, connection, target):
     #do some stuff
     pass

   @classmethod
   def __declare_last__(cls):
     event.listen(cos, "after_insert", cls.after_insert)
     event.listen(cos, "after_update", cls.after_update)
     event.listen(cos, "after_delete", cls.after_delete)

so, for each model we want to log changes it will be sufficient to inherit from LoggableMixin:

.. code:: python

 class MyModel(SomeSuperClass, LoggableMixin):
   pass


Improvements
------------

One of the first improvements you can make to the class LoggableMixin could be
the separation of the class in three different classes eg . LogInsertMixin, LogUpdateMixin LogDeleteMixin,
in my case I preferred to have it all together given the small size of the class.
A second improvement would be the generalization of mixin allowing you to 
specify which functions (or methods) to be assigned to different listeners,
and once more the specific needs of the application I'm working on does not
require this level of abstraction and can live well with this approach.

Conclusions
-----------

SQLAlchemy provides a number of services to work with the model, the system
just described would not have been so easy to implement if it were not for the
quality of the API of sqlalchemy.
I invite anyone to go deeper in the documentation for sqlalchemy (http://docs.sqlalchemy.org)
because within it are preserved gems of great value.
For those wishing to see a concrete implementation of the topics discussed in
this post they can take a look at the file sysgrove/models.py in the repository
at https://bitbucket.org/sysgrove/sysgrove
