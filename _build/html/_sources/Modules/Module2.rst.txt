.. _Module2-label:

Step 2: Scheduler
=================

The step is part of the :ref:`ROSACE project <MainPage>`

Getting an observatory that is able to work automatically is good. But then, as soon as it is operational, the key question is:
'what is my next target'. 

This is the mission of the Step 2 to reply this question, and define at any time what is the next target to observe.

The strategy can be very simple - for example, you can set a list of targets in a text file.

But is can be also very rich if you have many observatories working together, managing multiple scientific programs. In such cases, 
the question of the next target can be tricky. This module will adapt to any cases - starting by the simple one, of course.

As of Oct. 2026, the Scheduler can manage multiple scientific programs (such as Be_stars, NP, Cataclysmic...). In fact, Rosace requests to all the programs for their next targets, 
and selects the one that weill be really observed based on a simple question : what we'll miss if we don't observe it now.

It considers the position in the sky (with a local horizon), prevent any mount pier change, and so on.

Each program can be given a percentage of observing time. And of course there is always a manual list with high priority, if you want to observe a special target tonight.
