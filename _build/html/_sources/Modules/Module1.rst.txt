.. _Module1-label:

Module 1: Dome / Shelter manager
================================

The module is part of the :ref:`ROSACE project <MainPage>`

This module is made to manage the Observatory infrastructure: Dome or Shelter, and weather station.

It is able to decide wether we can start the observation, based on the weather station. When all is OK, it opens the dome / shelter and 
gives the order to the Module 3 (sequencer) for starting the observation. 

It also decide to stop observations if weather conditions are not met anymore (or simply if the night is over). 

In few words, mission of the Module 1 is to Protect the Instrument permanently. 