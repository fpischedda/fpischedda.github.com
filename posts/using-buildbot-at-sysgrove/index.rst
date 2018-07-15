.. link: 
.. description: 
.. tags: buildbot CI python
.. date: 2013/12/10 21:25:11
.. title: Using buildbot at SysGrove
.. slug: using-buildbot-at-sysgrove

====================
Buildbot at SysGrove
====================

What is buildbot
----------------

BuildBot (http://buildbot.net/) is a framework created to simplify the
development of automation solutions in the field of software development.
Unlike other systems such as Jenkins (http://jenkins-ci.org/) or TravisCI
(https://travis-ci.org/) buildbot only provides a framework on which to build
the infrastructure that you need .
The project has client-server architecture where the server sends to the client
the work to be performed, the server is called master and the clients are
called slaves; the framework supports multiple masters and multiple slaves, in
this way the system architecture can easily grow with the needs of your
project.
Buildbot is available for virtually any platform, even if the current version
0.8.x platform support win32-64 is not exactly the best though still usable
with some changes (see http://trac.buildbot.net/wiki/RunningBuildbotOnWindows )

Buildbot at sysgrove
====================

For our development needs in SysGrove( http://sysgrove.com) buildbot is used to
generate the release of our application for the win32 platform. Every 12 hours
we verify that there are some changes in our repository
(https://bitbucket.org/sysgrove/sysgrove) and in this case, the following
operations are performed:
- Download the project from the repository (we use git, but the system supports hg, svn, bazaar etc ...)
- Launch the test suite (in this case we use the testing tool discovery nosetests)
- Generate the executable using py2exe
- Build the initial database (sqlite and postgres)
- It creates the archive with the application executable and test database(s)
- Load the new release on "Confluence"

Clearly doing all these steps manually is tedious and can beprone to errors 
or oversights that would decrease the quality of our product.

Our setup
=========

As mentioned at the beginning we (http://sysgrove.com/) need to create an 
executable for the platform win32 (and possibly in the future for linux and 
osx) for which we need to run our script inside a win32 OS; given the relative 
difficulty of configuring a master on windows it was decided to have the master 
in a linux host (debian jessie) and a slave in a virtual machine 
with windows 7 ™ since that the slaves give far fewer problems .

Configuration
=============

The configuration file, as with cfg extension, is a simple python script, you
can use this file to configure these main areas:
- Slaves: you can define how many slaves you want each with its own identity
- The scheduler: the scheduler indicate how and when the activities will be planned by the master
- The builder: indicate the steps to take to create a build
- Notifiers: the master can communicate in many ways the status of the build such as irc, email or web
- Various configurations such as the database to use to store the state of the system, the identity of the project (name, url) etc.

Pitfalls
========

As I said earlier I had some problems with windows, probably due to my 
inexperience with the version of python for this operating system.
One of the first difficulty concerns sqlalchemy, an ORM for python; buildbot 
requires in fact a version of sqlalchemy == 0.7.10 as our application requires 
a newer version.
Typically, such a problem is solved easily using the handy tool 
virtualenv (https://pypi.python.org/pypi/virtualenv) creating an environment 
for the master (the part that needs access to a db, sqlite by default) and one 
environment for the slave, but given my experience with windows, I could not 
separate the environments (which is why the master is on a debian host and 
the slave is a on windows VM).

Conclusions
===========

Once you understand the general concepts of buildbot, extend and customize the 
functionality becomes simply a work of configuring and creating scripts that do
the real work, besides the web interface by default enables us to keep track of 
our builds and forcing new if it were needed.

Clearly this is not a "point and click" tool to everyone and this is a good 
thing, in my humble opinion and responsibility for the maintenance of such 
a system requires knowledge of a typical IT department and not a manager who,
if desired, can always send us an email to request a build rather than messing 
around with his hands no expert ;)
