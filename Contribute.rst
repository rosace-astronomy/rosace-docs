.. _Contribute-label:

Adapt ROSACE to your observatory, and contribute
================================================

Welcome to the :ref:`ROSACE project <MainPage>`.

ROSACE is meant to be adapted: your own targets, your own scientific
programs, your own instrument. This page first explains how to adapt it to
your case, then how to help the project. The two often go together: when
your case needs something ROSACE cannot do yet, the change you make is
welcome in the project.

Contribute
----------

The project is developed by the community, and we need you! Here are the
ways to help:

* Install your own observatory and give us any user feedback.
* If you're a developer, contact us and tell us where you can help (there
  is so much to do!). The code is on Codeberg:
  https://codeberg.org/FrancoisCochard/Rosace-manager
* Write and/or translate the documentation (the plan is to get only an
  English version, but we never know).

Adapt ROSACE to your case
-------------------------

.. _CreateProgram-label:

Create a program and add stars
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

What a program is
~~~~~~~~~~~~~~~~~

An observing program (for example ``Be_stars`` or ``PN``) exists in two
places, which must carry exactly the same name:

.. list-table::
   :header-rows: 1
   :widths: 20 55 25

   * - Where
     - What it holds
     - How to change it
   * - The program file, ``programs/<name>.yaml``
     - **How** to observe: the list of acquisition steps (pointing, guiding,
       exposures, calibrations…), then of reduction steps, and how the
       result is shared.
     - A text editor
   * - The catalogue, database ``targets.db``
     - **What** to observe: the stars (name, coordinates, magnitude, spectral
       type), the programs, and which stars belong to which program — with,
       when needed, an exposure time of its own for a program/star pair.
     - The ROSACE API page (``/docs``)

A star is observed only if it is in the catalogue, linked to the program,
and asked for by the scheduler (see `Step 5 — Have the program observed`_
below). One star may belong to several programs.

The program name holds only unaccented letters, digits and ``_`` (no space,
no dash): ``My_Be``, ``WR_2026``. It must be the same, capitals included, in
the file name (``My_Be.yaml``) and in the catalogue (``My_Be``).

Where the catalogue is managed
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

There is no dedicated screen in the web interface yet. The catalogue is
managed through the ROSACE API page, a web page generated automatically:

1. Log in to the usual interface first, in the same browser
   (http://localhost:8001 for the Docker demo, http://localhost:8000 for a
   local installation).
2. Open the same address followed by ``/docs``:
   http://localhost:8001/docs.
3. Each line is an operation (for example ``POST /programs``). Click it, then
   **Try it out**, fill in the fields, and **Execute**. The answer shows below
   (*Response body*) with its code: 200 or 201 = done, 404 = not found,
   409 = already exists, 401 = you are not logged in (back to 1).

``GET`` operations only read: they can be tried without any risk.

Step 1 — Write the program file
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The simplest way is to copy an existing program that already observes the
way you want, and to change only its header. The files are in the
``programs/`` folder of the ROSACE code:

* local installation: ``rosace/rosace/programs/`` in the repository;
* Docker demo: ``/app/rosace/rosace/programs/`` in the container.

.. code-block:: yaml

   # ── Program: my Be stars ─────────────────────────────────
   program: My_Be
   description: "Monitoring of a few Be stars"
   sequencer:              # acquisition, in order
     - slew_72ed
     - slit_xy
     - pointing_72ed
     - focus
     - star_spectra_acquisition
     - stop_phd2_guiding
     - dark_acquisition
     - bias_acquisition
     - calib_acquisition
     - flat_acquisition
     - spectro_light_source_to_sky
   reduction:              # reduction, in order (may be empty: [])
     - calculate_flux
     - export_demetra
   sharing: blank_share    # a single value

Keep ``program:`` identical to the file name. ``rosace --list-subplugins``
lists the possible steps (in the Docker demo:
``docker exec rosace-demo python -m rosace.main --list-subplugins``). The
steps above are those of the instrument ROSACE was developed with (a 72 mm
refractor and an Alpy 600 spectrograph): for another instrument, start from
the provided program closest to it.

**In the Docker demo**, copy the file out of the container, edit it, and put
it back:

.. code-block:: bash

   docker cp rosace-demo:/app/rosace/rosace/programs/Be_stars.yaml ./My_Be.yaml
   # edit My_Be.yaml with a text editor
   docker cp ./My_Be.yaml rosace-demo:/app/rosace/rosace/programs/

The container's ``programs/`` folder is not in the data volume: a program
added this way disappears when the container is removed and created again
(for example to move to a newer version). Keep your copy.

Step 2 — Create the program in the catalogue
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

``POST /programs``, with this body:

.. code-block:: json

   {
     "name": "My_Be",
     "description": "Monitoring of a few Be stars"
   }

A name already taken, whatever its capitals, is refused (code 409).
``GET /programs`` lists every program.

Step 3 — Add the stars to the catalogue
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Stars are imported from Simbad (the CDS database, Strasbourg): give a name,
and ROSACE fetches the J2000 coordinates, the V magnitude and the spectral
type. An Internet connection is needed.

**One star at a time:** ``POST /simbad/import``. Field ``name``: any name
Simbad knows — ``gam Cas``, ``HD 5394``, ``Navi``… ROSACE records the star
under its main Simbad name and keeps the name you typed as an alias when it
differs. Either can then be used everywhere (capitals do not matter). If the
star is already in the catalogue, even under another name, it is not created
twice: only the alias is added. To see what Simbad returns without recording
anything: ``GET /simbad/search``.

**A whole list:** ``POST /stars/import-list``. Prepare a text file, one star
name per line (empty lines are ignored):

.. code-block:: text

   gam Cas
   HD 5394
   zet Tau
   48 Lib

Choose it with **Choose file**, then **Execute**. The answer gives one line
per star: ``created`` (added), ``existing`` (already there) or ``error``
(Simbad does not know it or does not answer — see ``detail``). An error on
one line does not stop the next ones.

Step 4 — Link the stars to the program
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**One star:** ``POST /programs/{name}/stars/{star_name}``. ``name`` = the
program (``My_Be``), ``star_name`` = the star (main name or alias),
``exposure_time`` = optional, the duration of one exposure in seconds for
this star in this program. Running the operation again on the same pair
replaces the exposure time: this is also how to change it.

**Several stars:** ``POST /programs/import-list``. A YAML file, the program
then the list of its stars (already in the catalogue — no Simbad call here);
one file may fill several programs:

.. code-block:: yaml

   My_Be:
     - gam Cas
     - HD 5394
     - zet Tau

The answer, per line: ``linked``, ``program_not_found`` or
``star_not_found``. This operation sets no exposure time; to set one, use
the previous operation, star by star.

**The exposure time:**

* Without ``exposure_time``, ROSACE computes it from the V magnitude
  (configuration parameters ``exposure_time_mag0_seconds``,
  ``max_exposure_time_seconds`` and ``min_exposure_count``). This is the
  normal case.
* With it, the given value replaces this computation — keep it for the stars
  where the computation does not suit (a star that saturates, for example).
* An exposure longer than ``max_exposure_time_seconds`` is split into
  several exposures; there are always at least ``min_exposure_count``.
* A star with no V magnitude in Simbad and no ``exposure_time`` cannot be
  observed: the observation fails. Give it an exposure time.

**Check:** ``GET /programs/{name}/stars`` lists the program's stars with
their exposure time (``null`` = computed from the magnitude). To remove a
star from the program: ``DELETE /programs/{name}/stars/{star_name}`` (the
star stays in the catalogue).

Step 5 — Have the program observed
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The active scheduler (configuration key ``active_scheduler``) decides which
stars to observe, and in which order.

**Scheduler** ``scheduler_rosace_2026`` **(the demo's).** It follows the
observation list, a text file whose path is given by
``observation_list_path`` in the configuration
(``/root/.rosace-demo/targets/ObservationList.txt`` in the Docker demo). A
header line, then one line per requested observation:

.. code-block:: text

   Priority, Program, Star, comment
   1, My_Be, gam Cas, first of all
   2, My_Be, HD 5394,
   2, My_Be, zet Tau,

* ``Priority``: 1 goes first; at equal priority, the order of the file.
* Each line asks for one observation per night; to observe the same star
  twice, write two lines.
* A star too low (below ``min_target_altitude_deg``, or below the local
  horizon) is skipped for now, and offered again later.
* An invalid line (unknown program or star, star not linked to the program)
  is ignored, with a message in the Journal.
* The file is read again at each choice of target: it can be changed during
  a session.

**Scheduler** ``selector`` **(several programs).** Each program has its own
planner, declared by a ``scheduler:`` block in its file. The simplest one,
``random_pick``, draws at random a star of the program not yet observed
tonight:

.. code-block:: yaml

   scheduler:
     name: random_pick
     share_percent: 10          # share of telescope time aimed at (optional)
     params:
       min_interval_days: 5     # at most one successful observation every 5 days

The ``share_percent`` of all programs must not add up to more than 100:
otherwise the session stops at the first choice of target, with an error in
the Journal. Observations asked by hand go in the same observation list as
above (optional extra columns: ``Start_after``, ``End_before``, ``Series``).
See also :doc:`Modules/Module2`.

In the Docker demo
~~~~~~~~~~~~~~~~~~

Every step above works in the demo: you can create a program there, import
stars from Simbad and link them. But the demo only replays the frames
recorded during a real night, for its 4 stars: another star cannot be
observed there (there is no frame of it to replay, and the observation fails
at pointing). To observe your own stars, move on to the INDI simulators,
then to your instrument (see :ref:`GetStarted-label`).

Summary
~~~~~~~

.. list-table::
   :header-rows: 1
   :widths: 5 55 40

   * - #
     - Action
     - Where
   * - 1
     - Write ``programs/My_Be.yaml`` (copy of an existing program)
     - A text editor
   * - 2
     - Create the program ``My_Be``
     - ``POST /programs``
   * - 3
     - Import the stars from Simbad
     - ``POST /simbad/import`` or ``POST /stars/import-list``
   * - 4
     - Link the stars to the program (exposure time optional)
     - ``POST /programs/{name}/stars/{star_name}`` or
       ``POST /programs/import-list``
   * - 5
     - Check
     - ``GET /programs/{name}/stars``
   * - 6
     - Ask for the observations
     - Observation list, or ``scheduler:`` block

Your configuration, your instrument, your plugins
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To be documented: the configuration file, the hardware profile of your
instrument, and how to write a plugin of your own.

