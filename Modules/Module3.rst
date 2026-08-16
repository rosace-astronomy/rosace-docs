.. _Module3-label:

Step 3: Sequencer
=================

The module is part of the :ref:`ROSACE project <MainPage>`

This module is the core of the Robotic observation. It runs all the steps of an observation sequence.

Each observation runs a given observing sequence. You can write your own sequence, to match your scientific need. The sequence is made of plugins ; 
you can use existing plugins or write your owns (in Python). We've blank plugins to help you creating your own.

Here are the main (and required) principles for this module:

#. Each observation uses a specific sequence (mainly linked to the scientific goal of the observation).
#. A sequence is a single list of basic steps. No loops, no conditions, the system is very simple.
#. A single structure (Observation object) is used to give each step all the data it needs (target star, exposure duration, and so on). 
#. This structure can be append by each step.
#. This structure is also used at the end of the process to write the "Observing file", needed for the data reduction.
#. We use a common logging system ; each step can log any data you want.
#. If an error occurs during a step, an exception is raised, and the observation is stopped. Then a new observation is started, from the scheduler.