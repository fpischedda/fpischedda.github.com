.. link: 
.. description: 
.. tags: rabbitmq scaling
.. date: 2013/02/28 21:13:45
.. title: Having fun with RabbitMQ
.. slug: having-fun-with-rabbitmq

===============================================
Exploring Distributed Calculation With RabbitMQ
===============================================

Preface
-------

Lately I’ve had to work on a complex web application which have started to have
an increased (and increasing) number of users.
Unfortunately this web application was not built to scale and so problems 
started to get to the surface.
When the user base was small to medium the load on the application server was 
fairly low and we could focus on adding new features and growing our user base
until we landed on the Facebook platform.
At some point users started to came and the scheduled task took 10-20-40-70-90
minutues to complete leaving the users to stare at the “calculating tasks” page.

The first approved (the correct word should be "imposed") solution was to
migrate our servers to AWS so we could increase host perfomance using bigger 
and bigger servers until we reached the limits of the single machine again.

After a lot of battling with the management we had the opportunity to detach
the application core from the web site and started to build the new architecture
with these loosely defined requirements:

- better use of resources
- better code organization and quality
- horizontal scaling
- manageabilty
- ready for future development

I admit that those where my requirements, but the management undestood that they 
have lost the ability to understand the architecture beyhond a simple web site 
with a bounch of scheduled routines that, honestly, worked with the help of an
infinite dose of luck…and also my prototype run in a fraction of time.

Building blocks
---------------

Our old backend has been broken in to those pieces:

- libcore : the base code extracted from the old and stinky web site
- RabbitMQ message broker
- job scheduler : send messages to appropriate exchanges at appropriate time
- workers : consume messages sent by the scheduler

Leaving libcore apart doing its stuff lets focus on the scheduler and the 
workers; for details about RabbitMQ please refer to its web site
(http://www.rabbitmq.com/).

As a scheduling library I have choosen the open source Quartz.NET
(http://quartznet.sourceforge.net/) which provided a simple but powerful
interface to schedule jobs, I especially liked the ability to specify job
triggers with the well known and compact cron syntax.
Long story short the scheduled jobs harvests the tasks and send messages to
exchanges accordingly.
I think that the whole core plus the jobs are not bigger than some hundreds of
lines of code. Wonderful!

In order to create a worker one should simply have to inherit from a 
BaseConsumer class and override the "consume" method to do its tasks when a
message is received. Done. Cool :)

Conclusion
----------

Given this simple architecture what are the benefits? Lets see:

- scaling: I simply have to add more workers in the same or different machines and the calculating power will increase accordingly
- flexibility: if a new task is required I have to create a new scheduled job and its consumer (even in a different language or platform) and maybe a new message type (messages are simple JSON described objects); after all is tested I have to update the scheduler and add a new worker either by updating a running worker or adding a new one without affecting the whole system; yes it can be done while the system is crunching its task and yes, obviously without affecting the web site(s)
- cost effective: if we have a lot of task to complete at a certain hour in the day I could simply turn on a couple of more machines when I need it and turn those off when the whole work is completed; if you have ever used the Amazon AWS infrastructure you understand of much one can save using this approach
- testing: having everything well separated I can test each block individually instead of having a code blob that is barely manageable

With this short post I hope to increase interest in this simple way of doing
distribuited computing using a message broker and a simple but well architected
system.
