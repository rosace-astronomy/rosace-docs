.. _WhatDoesROSACE-label:

What does (and what does not) ROSACE software?
==============================================

Welcome to the :ref:`ROSACE project <MainPage>`.

At the time of its creation (summer 2026), ROSACE was essentially an open-source software platform designed to
automate observations. This software acts as an orchestrator, linking together various tasks: initializing the
observation session, defining the next target to be observed, executing the observation sequence, processing the
data, validating and sharing the results, and closing the session. These tasks are common to all instruments and
all scientific programs; they form the core of spectroscopic observations.

Where each observatory’s specific characteristics come into play is in the concrete actions of the observation
sequence and data reduction. These actions are carried out by “plugins” (software modules), and this is where
each observatory can adapt to its hardware and scientific program(s).

Of course, not everything needs to be rewritten from scratch each time, since even though the instruments differ,
a large portion of the specific tasks remains very similar. The approach is in the spirit of the open-source
community: we build upon what our predecessors have already done as much as possible (thanks to them!), so we
can focus solely on what is specific to our application.

ROSACE therefore provides a working platform based on several key principles:

- An orchestrator capable of sequencing observations, with an initialization phase and a session closure phase. Data reduction and result dissemination tasks are performed in the background (to avoid wasting valuable observation time).
- An observation is a coherent set of information (date, target, images, data reduction steps, results). All this information is compiled into an observation file (in .yaml format) that is created at the start of the observation and is updated step by step as the process progresses. At the end of the observation, it serves as a comprehensive record of the observation.
- All information related to an observation session (images, observation file, results, etc.) is gathered in a single directory: the session directory.
- A local database containing all observable objects; there’s no need to be constantly connected to observe. And, of course, objects can easily be added to this database.
- A centralized configuration file (there is also a separate hardware configuration file, which allows for the management of multiple different instruments).
- A centralized logging system is also included to keep track of all events that occur during the observation session. Another local database records all sessions and all observations.
- A mechanism for dynamically loading plugins. The list of plugins for a given observation is specified in an observation program file (also in .yaml format). This is what makes it possible to perform different observations for different scientific programs within a single session.
- The application has a user interface (web browser), but can also operate without one (robotic observations assume that there is no observer in front of a screen!).

What ROSACE DOES NOT DO:

- An external device must decide when to start and stop observations based on observation conditions (weather, day/night, etc.). This device must ensure the instrument’s safety. ROSACE can be interrupted, but will not shut down on its own.
- ROSACE is not a scientific program in and of itself. It allows you to connect to various programs; it is likely that the number of such programs will increase in the coming years.
- ROSACE does not guarantee that it will work with your instrument as-is. There will most likely be an adaptation phase, which will require you to “tinker (a bit) with the code.”

