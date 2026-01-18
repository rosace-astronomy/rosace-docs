Basic principles
================

Welcome to the :ref:`ROSACE project <MainPage>`.

The project is based on few principles:

* An observation is a consistent set of images and data, collected in an Observation File (yaml format).
* All files of one observing night are saved in a single folder
* All images and spectra are in FTIS format (which is THE format for astronomy files).
* We use only Open Source tools:

  * Linux [#f1]_.
  * Python (3.10 or above) as much as possible.
  * Git for revision control.
  * Github repo to share the code.
  * Rest API interfaces (to split front-end and back-end).
  * User interfaces in (JavaScript / TypeScript). Works with any web browser.
  * INDI server and devices (INDI is for Linux what ASCOM is for Windows).
  * Sphinx for the documentation.
  * Few standard Python libraries :

    * Astropy, for all Astronomy related stuff.
    * FastAPI for the Rest API interfaces.
    * MQTT and TIG (Telegraf / InfluxDB / Grafana) to reacord real time data.
    * Logging for the loggers (all actions are recorded).
    * PyTransitions (for Finite States Machine).
    * Cmd.cmd (for getting a command line interface (CLI), useful for debugging).

* The dome or shelter can open and close whatever is the telescope position [#f2]_.
* Each module can be installed locally or remotely (client-server architecture).
* We can use Kstars for monitoring the actions (very useful for debugging).
* We love the concept of continuous improvement ; a small step every day.

* There is a single configuration file (in yaml format) for each module.

.. rubric:: Footnotes

.. [#f1] We know that most of people are using Windows. The door is not closed. Since we use Python 
  (which is not platform dependant) and ASCOM (under Windows) platform offers the same kind of feature than INDI (under Linux),
  this can probably be adapted in close future - despite Windows is not Open Source.

.. [#f2] This is a simple and efficient way to split functions and protect the instrument. But we keep in mind that this condition is not made by lots of observatories
  (most of the rolling shelter ones), and there is no strong stopper to this.