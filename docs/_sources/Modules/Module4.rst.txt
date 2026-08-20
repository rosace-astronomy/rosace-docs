.. _Module4-label:

Step 4: Data Reduction
======================

The step is part of the :ref:`ROSACE project <MainPage>`

Once the observing data is recorded, and the observation is completed, we must process it, to get the scientific result,
corrected from any instrumental effect.

This is the job of this Step 4. 

Of course, the data reduction process depends on each scientific program. 

In a first time, we do this reduction step thanks to SpecINTI software. Then, we'll develop our own process - fully Open Source, in Python.

The data reduction process is a list of reduction operations (like master dark, spectrum extraction or wavelength calibration). ROSACE will 
propose a list of such operations, but you can write your owns, to adapt to your case.

Like for the Sequencer, this is the Observation file that moves the whole data of the observation through all the data reduction steps.