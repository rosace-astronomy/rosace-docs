.. _Module1-label:

Step 1: session initialization
==============================

The step is part of the :ref:`ROSACE project <MainPage>`

This step runs only once at the beginning of the session. It connects all the servers and devices, and starts few
operations, like cooling down the cameras (if any).

A session_ID est created for each session, and stored in the sessions database.

All images and data of a given session are stored in a single folder on the disk. The folder name is based on the date and time.
The folder is defined for a time range going from noon to noon the following day (to make sure that all data of a night
are in a single folder).

This means that if several sessions are started during a same night (for instance if the weather is temporarly bad), they
will all use the same folder.