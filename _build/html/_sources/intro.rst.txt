Introduction
============

Rosace proposes to create your own robotic observatory for Astronomical Spectro observations.

The name of the project is ROSACE, for **Robotic Observations and Spectrocopic Astronomy, a Collaborative Experience**

Our intention is that many observers, with different instruments and for various scientific programs can use this system. The final goal is that we can produce
all together valuable data for the science, for a better understanding of the Universe we're living in.

We do think that Spectroscopic observations **cannot be fully standard**. The observation sequence will depend on your own setup, and the data reduction will
vary from one scientific goal to the other. You will probably have to 'put your hands on', and write some code to adapt the system to your own need. We want
to make these adaptations as simple as possible.

We also consider that the system must be able to manage several science programs ; none can use a telescope at 100% of the time.
A science program can have its own observing sequence and own data reduction process. in ROSACE, each observation must be part of 
a given scientific program.

One key of a robotic observatory is that it really runs fully automatically. If each observation requires 2 minutes of human brain, 
it will quickly be overloaded - because every night can produce dozens of observations. Then a full automatic process is required.
The only human part can be the validation, to make sure that all went well - the system must give all the tools to do it very fast.

To make this system accessible for everyone, we've organized the project with independant plugins, with few general rules, and the code is intended to be easy
to read and maintain. Managing several small problems is better than a big one. 

To help you starting with ROSACE, we propose this documentation, and a :ref:`Get Started page <GetStarted-label>`. You can start with simulators, 
no need for a real instrument in a first step.

We work over the long term. People will change, instruments will change, new science opportunities will arise. The platform must be able to adapt
to all these changes. To make this, we do our best to be clear on what is stable over the time (for instance the Observing File format), and what can 
evolve (the instruments, the observing sequences, and so on).

We think that continous improvement is the best way to move forward - one small step everyday. 

The core of the system is an Orchestrator. It manages the observations from initialization of the observatory to its closure, and runs the observing and data reduction sequences for each target stars.
The observing sequence is made of plugins, that you can adapt to your own needs. And the same for the data reduction sequence. 

Before starting the observing process, you must make sure that the observing conditions (weather) are good enough - this is out of the scope of ROSACE.

The Orchestrator runs different steps, one after the other. This is described in this figure:

.. image:: Images/Schema_ROSACE.png
  :width: 600px
  :height: 400px
  :scale: 100%
  :align: center

It has a simple behavior: it starts the session by an INIT step (for instance to connect the hardware and cooling down the cameras).
Then, it loops for each target the scheduler (to define what is the next target), the sequencer (for data acquisition), the data reduction
and the sharing (to send the data to the right place, like a database). At the end of the session, it closes it (disconnect the hardware, and so on).

Each step for the sequencer and data reduction is made of plugins that you can adapt to your own needs.

The list of plugins that must be used for a given observation are descrived in a Program file (.yaml format). This means the each observation 
can have it own sequence (for data acquisiton, for data reduction, and for sharing).The plugins are dynamically loaded. 

The Orchestrator creates an Observation file at the beginning of the observation. This file will go through all the steps (plugins), 
and each plugin will enrich it. At the end of the observation, this Observation file is the memory of the observation. 
All necessary data are recorded there: date, target, scientific program, raw images, data processing steps, and at the end the processed spectrum.

This Observation file is the only information that is known by each plugin. This makes all plugins independant from each other - and this is why it is easy to make or adapt your own plugins.

You'll find more details of each step in the following pages:

* Step 1: :ref:`initialization (once at the beginning of the session) <Module1-label>`
* Step 2: :ref:`Shelduler (what is the next target to observe) <Module2-label>`
* Step 3: :ref:`Sequencer (run the actual observation) <Module3-label>`
* Step 4: :ref:`Data reduction (to get a scientific result) <Module4-label>`
* Step 5: :ref:`Display, validate and share the results <Module5-label>`
* Step 6: :ref:`Archiving the results <Module6-label>`
* Step 7: :ref:`Closing the session (once at the end of the session) <Module7-label>`

We do think that the success of this project depends on few elements :

* The community (developers and users) must be large enough ; the system must not depend on one or few guys.
* A clear and up-to-date documentation is available.
* We propose a 'get started' path, to jump quiclky and easily into the project.
* The team is very open to newcomers - you're welcome!