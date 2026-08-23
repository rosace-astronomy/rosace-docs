Introduction
============

Welcome to the :ref:`ROSACE project <MainPage>`.

Rosace proposes to create your own robotic observatory for Astronomical Spectroscopic observations.

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

We do think that the success of this project depends on few elements :

* The community (developers and users) must be large enough ; the system must not depend on one or few guys.
* A clear and up-to-date documentation is available.
* We propose a 'get started' path, to jump quiclky and easily into the project.
* The team is very open to newcomers - you're welcome!

In the next pages, you'll find more details:

* Why doing :ref:`Robotic Spectroscopy in Astronomy <WhyRoboticSpectro-label>`?
* What does (and does NOT) :ref:`ROSACE <WhatDoesROSACE-label>`?
* To get a technical overview: :ref:`ROSACE at a glance <AtAGlance-label>`.
* A list of :ref:`technical choices <TechnicalChoices-label>` that apply to ROSACE.
