.. title: cx_Freeze, again
.. slug: cx_freeze_again
.. date: 2016-11-13 21:45:17 UTC+01:00
.. tags: cx_Freeze Python PyQT Windows
.. category: cx_Freeze Python PyQT5
.. link: 
.. description: 
.. type: text


We meet again, xz_Freeze
------------------------

It's been a long time since the last post and for sure I was not thiking about
another post about cx_Freeze.
I have spent this week end trying (and at the end  succeding) to build an
update to an old software. For future memory I'll write briefly the steps needed
to get a functional build without losing my mind.

Brief description of the problem
________________________________

The software is a desktop application used to upload email attachments to a
web based data store; the application talk to the web app using a "RESTy" api,
and fetches the file from a email address; the GUI is written with the beatiful
PyQT5 (someone may prefere PySide) and usually this dependency is a pain to
work with but I have to admit that the newer version installs gracefuly.

OS version and things to download
_________________________________

The person using this software needs to run it from a Windows OS and at this time
Windows 7 is the oldes worsion of the OS I want to support (no Vista, please)
since addressing Windows 10 will produce an executable with no retro compatibily
and Windows 8 have less installs than 7.

The first software needed to be dowloaded is obviusly the Python 3.5 interpreter
and after its installation came the first surprise, it doesn't run because of
some missing dlls; after some research I have downloaded the redistribuitable
runtime and it started to work as expected.

After Python was ready I've had to install the pip utility which was not
automatically installed, maybe I have forgotten to chek the corresponding
option in the installer; anyway everything installed correctly using it, this is
a brief list of dependencies:

- requests: OK
- paramiko: OK
- imapclient: OK
- PyQT: (surprisingly!) OK
- cx_Freeze: Not ok at all..

cx_Freeze requires Widonws build tools that can freely (as in beer) downloaded
and installed but even after that I was not able to install it from pip;
fortunately some kind people provided a procompiled wheel for it but be sure
to download the 5.0 version because the 4.x was not able to produce a proper
executable.

Additional dependencies and some setup.py notes
_______________________________________________

Having not touched I tought that the setup script was soemwhat correct since
the last build was succesful, nothing more far from the truth; the first thing
has been a nice surprise, cx_Freeze recognized correctly all the dlls to use in
the final package and referencing additional dlls is not needed anymore, good
work cx_Freeze guys!

After the first build ihave started a cycle of try, fix retry until the
application could as expected, here is a list of additional dependencis that I
had to install and reference in the setup.py file:

- appdir
- packaging
- atexit

appdir and atexit only need to be referenced as packages instead packaging
requires some more fine tuining so I had to add this additional sub_packages
to the includes settings of the build_exe_options dictionary:

- packaging.version
- packaging.specifiers
- packaging.requirements

Final words
___________

It took me a couple of hours of trial/error to be able to ship the build and I
hope to not have to repeat this madness again soon; if I'll need to create a new
build in the future I hope that this little post will halp me not to waste my
time again.
