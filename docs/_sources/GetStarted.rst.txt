.. _GetStarted-label:

Get started
===========

We propose a step-by-step path to start with ROSACE. Each step asks a little
more of your computer, and brings you closer to a real observatory:

1. **Try the Docker demo** — nothing to install but Docker. Described in full
   below.
2. **Install ROSACE on your computer and run the same demo** — the real
   application, still without any hardware (``rosace --demo-mode``).
3. **Run ROSACE with the INDI simulators** — a simulated camera and mount
   (driven by KStars/Ekos) and PHD2 guiding: ROSACE in close to real
   conditions.
4. **Connect your own instrument** — adapt the hardware profile, the
   configuration and, if needed, a few plugins, and start real observations.

Steps 2 to 4 will be documented here soon.

The source code of ROSACE, and the Docker image, are on Codeberg:
https://codeberg.org/FrancoisCochard/Rosace-manager


Step 1: Try the Docker demo
---------------------------

What it is
^^^^^^^^^^

The demo runs the **real** ROSACE process — target selection, pointing and
plate solving, PHD2 guiding, science and calibration exposures, then the whole
reduction down to the final spectrum and its summary PDF — on **replayed
hardware**: instead of driving a telescope, the devices hand back the frames
recorded during a real night (4 observations of 2026-10-14: psi Per, eps Cas,
M 57 and eta Ori), and a fake PHD2 replays that night's guiding. Each exposure
only lasts a few seconds on screen.

The demo data are anonymized: the observer is ``rosace``, the site
``Rosace-Demo``, its position rounded to the degree.

What you need
^^^^^^^^^^^^^

* **Docker**: on Linux, your distribution's ``docker`` package (add your user
  to the ``docker`` group, or prefix the commands with ``sudo``); on Windows
  or macOS, `Docker Desktop <https://www.docker.com/products/docker-desktop/>`_,
  started before you type the commands below.
* About **350 MB** to download and **1.1 GB** of disk space.
* An internet connection (download, then for the demo itself: the validation
  page looks up earlier spectra in the BeSS database).
* The image is built for **amd64** (Intel/AMD) processors. An Apple Silicon
  Mac (M1, M2...) runs it through emulation, more slowly.

Start the demo
^^^^^^^^^^^^^^

Type, in a terminal (Linux, macOS) or PowerShell (Windows), on one line:

.. code-block:: bash

   docker run -d -p 8001:8000 -v rosace-demo-data:/root/.rosace-demo --name rosace-demo codeberg.org/francoiscochard/rosace-demo

Docker downloads the image the first time (a few minutes), then starts it in
the background. What the options mean:

* ``-p 8001:8000``: ROSACE answers on port 8000 inside the container, reached
  at port **8001** of your computer. If 8001 is already used, choose another
  number on the left (e.g. ``-p 8080:8000``) and use it in the address below.
* ``-v rosace-demo-data:/root/.rosace-demo``: a Docker *volume* that keeps
  the demo's databases, observations and log when the container is removed.
* ``--name rosace-demo``: the name used by every command below.

Without a version number, Docker takes the latest published version
(``latest``). A given version is asked for by its number, e.g.
``codeberg.org/francoiscochard/rosace-demo:0.1.0.0``.

Use the demo
^^^^^^^^^^^^

1. Open http://localhost:8001 in your browser. The page shows an orange
   **DEMO MODE** badge.
2. Log in with user ``rosace``, password ``rosace``. These are fixed demo
   credentials: **never make this port reachable from the Internet**.
3. Click **START**. The session starts (connection to the replayed devices,
   camera cooldown), then the scheduler offers the recorded stars one after
   the other, about a minute and a half each.
4. Watch the session:

   * the **observations list** shows each observation's stages, and the steps
     of its acquisition and reduction while they run;
   * the **Journal** shows the log as it is written;
   * **Guidage** shows the guide images (pointing, slit measurement), with
     what was measured drawn over them;
   * **Spectre 2D** shows each frame taken by the science camera (target,
     darks, bias, flats, calibration lamp);
   * **Spectre 1D** shows the final spectrum, once its reduction is over.

   The reduction of an observation runs while the next one is being acquired:
   the four reductions are over about 6 minutes after the start.
5. Open http://localhost:8001/be/ : the **Be stars validation page**, where
   the observer checks each Be spectrum before sending it to the BeSS
   database (spectrum, guiding, sky images, earlier spectra of the same star).
6. Click **STOP** whenever you want: the current observation finishes, then
   the session closes.

The image viewers share the same mouse gestures: left drag selects a zone,
right drag moves the image, wheel zooms, right click opens a menu, the
**Aide** button lists them all.

Look at the files
^^^^^^^^^^^^^^^^^

Everything the demo produces is in the volume, under ``/root/.rosace-demo``
inside the container: ``observations/`` (one directory per night, holding the
observation files, the raw frames, the final spectra and their summary PDFs),
``log/rosace.log``, ``databases/``. To copy the observations to your current
directory:

.. code-block:: bash

   docker cp rosace-demo:/root/.rosace-demo/observations ./rosace-demo-observations

Run the demo again
^^^^^^^^^^^^^^^^^^

The scheduler observes each star once per astronomical day (local noon to
noon): a second START the same day finds nothing to observe and waits. To
start a fresh demo, erase its data (the replay dataset is kept), then restart
the container:

.. code-block:: bash

   docker exec rosace-demo python -m rosace.main --purge-demo --apply
   docker restart rosace-demo

Useful commands
^^^^^^^^^^^^^^^

.. list-table::
   :header-rows: 1

   * - Action
     - Command
   * - Follow the log
     - ``docker logs -f rosace-demo``
   * - Stop / start again
     - ``docker stop rosace-demo`` / ``docker start rosace-demo``
   * - Get a newer version
     - ``docker pull codeberg.org/francoiscochard/rosace-demo``, then
       ``docker rm -f rosace-demo`` and the ``docker run`` command above
       (the volume keeps the demo's data)
   * - Remove the container (keeps the volume)
     - ``docker rm -f rosace-demo``
   * - Remove everything
     - ``docker rm -f rosace-demo``, ``docker volume rm rosace-demo-data``,
       ``docker rmi codeberg.org/francoiscochard/rosace-demo``

If something goes wrong
^^^^^^^^^^^^^^^^^^^^^^^

* **The page does not open**: check the container runs (``docker ps``), and
  read its log (``docker logs rosace-demo``). The server needs a few seconds
  to start.
* **"port is already allocated"**: another program uses port 8001. Remove the
  container (``docker rm -f rosace-demo``) and run it again with another port.
* **"Conflict. The container name "/rosace-demo" is already in use"**: the
  container already exists. Start it with ``docker start rosace-demo``, or
  remove it first (``docker rm -f rosace-demo``).
* **Nothing is observed after START**: the stars were already observed today
  — see "Run the demo again".

What the demo does not show
^^^^^^^^^^^^^^^^^^^^^^^^^^^

The demo replays a fixed night: you cannot add your own targets with real
observations, and the sky, the Sun and the hour do not matter. Configuration
changes, your own instrument and real observations come with steps 2 to 4.

Step 2: Install ROSACE on your computer and run the same demo
-------------------------------------------------------------
(to be done)

Step 3: Run ROSACE with the INDI simulators
-------------------------------------------
(to be done)

Step 4: Connect your own instrument
-----------------------------------
(to be done)
