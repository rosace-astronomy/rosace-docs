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

Steps 3 to 4 will be documented here soon.

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

You can still create your own program and add stars to the catalogue in the
demo: see :ref:`CreateProgram-label`.

Step 2: Install ROSACE on your computer and run the same demo
-------------------------------------------------------------

What it is
^^^^^^^^^^

The same demo as step 1, but ROSACE now runs directly on your computer,
from its source code: no Docker any more. This is the installation you will
keep for the next steps (simulators, then your own instrument), and the one
to use if you want to read or change the code.

The demo data stay apart from any real installation: everything the demo
writes goes to ``~/.rosace-demo``.

What you need
^^^^^^^^^^^^^

* **Linux** (ROSACE is developed on Ubuntu; any recent distribution should
  do).
* **git**, and **Python 3.11 or newer** with its ``venv`` module (Ubuntu
  24.04 and Debian 13 have recent enough versions; Ubuntu 22.04's 3.10 is
  too old).
* **astrometry.net** (the ``solve-field`` program): the demo plate-solves
  the recorded guide images for real.
* About **125 MB** to download for the demo data, and 1 GB of disk space.

On Debian or Ubuntu:

.. code-block:: bash

   sudo apt install git python3-venv astrometry.net

Get the code
^^^^^^^^^^^^

Anywhere you like, for example in your home directory:

.. code-block:: bash

   git clone https://codeberg.org/FrancoisCochard/Rosace-manager.git
   cd Rosace-manager

The web interface comes already built: nothing else to compile.

Create a Python environment
^^^^^^^^^^^^^^^^^^^^^^^^^^^

ROSACE is installed in its own Python *virtual environment*, so that its
libraries never interfere with the rest of your system. The environment can
be anywhere: inside the repository, or with your other environments (for
example ``~/.venvs/rosace``). From the repository's root:

.. code-block:: bash

   python3 -m venv ~/.venvs/rosace
   source ~/.venvs/rosace/bin/activate
   pip install -e rosace/

``source .../activate`` *activates* the environment: in this terminal,
``python`` and ``rosace`` are now those of the environment. In a new
terminal, activate it again (or see `Use rosace from any terminal`_ below).
Check: ``rosace --version`` prints the version.

Get the demo data
^^^^^^^^^^^^^^^^^

The demo replays the frames of a real night: download them (about 120 MB),
check the download, and put them in ``~/.rosace-demo/replay``:

.. code-block:: bash

   curl -LO https://codeberg.org/FrancoisCochard/Rosace-manager/releases/download/demo-data-2026-10-14/rosace-demo-data-2026-10-14.tar
   sha256sum rosace-demo-data-2026-10-14.tar
   mkdir -p ~/.rosace-demo
   tar -xf rosace-demo-data-2026-10-14.tar -C ~/.rosace-demo
   mv ~/.rosace-demo/replay_data ~/.rosace-demo/replay

The checksum printed by ``sha256sum`` must read
``623405982b7c9653676ee0f9c2e296ec472cc10f9dc654b1e63f4ba344801501``. The
``.tar`` file can be deleted afterwards.

Tell astrometry.net where the index files are
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To plate-solve an image, ``solve-field`` needs *index files* covering that
part of the sky. The demo data hold the few the demo needs, in
``~/.rosace-demo/replay/astrometry``. Add this folder to astrometry.net's
configuration (once):

.. code-block:: bash

   echo "add_path $HOME/.rosace-demo/replay/astrometry" | sudo tee -a /etc/astrometry.cfg

(``$HOME`` is replaced by your home directory: the configuration needs the
full path.)

Start the demo
^^^^^^^^^^^^^^

With the environment active:

.. code-block:: bash

   rosace --demo-mode

The server runs in this terminal and shows its log; **Ctrl+C** stops it.
Then open http://localhost:8000 and use the demo exactly as in step 1
(`Use the demo`_): log in with ``rosace`` / ``rosace`` (the demo account is
created automatically), click **START**. If port 8000 is already used, add
``--port 8001`` and open http://localhost:8001.

Everything the demo produces is in ``~/.rosace-demo``: ``observations/``,
``log/rosace.log``, ``databases/``.

Run the demo again
^^^^^^^^^^^^^^^^^^

As in step 1, each star is observed once per astronomical day. To start a
fresh demo, stop the server (Ctrl+C), erase the demo's data (the demo data
downloaded above are kept), and start it again:

.. code-block:: bash

   rosace --purge-demo --apply
   rosace --demo-mode

Without ``--apply``, ``--purge-demo`` only lists what it would erase.

Use rosace from any terminal
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Optional, done once, **with the environment active**:

.. code-block:: bash

   ln -s "$(command -v rosace)" ~/.local/bin/rosace
   echo "eval \"\$($VIRTUAL_ENV/bin/register-python-argcomplete rosace)\"" >> ~/.bashrc

The first line puts a link to the command in ``~/.local/bin``: ``rosace``
then works in any terminal, without activating the environment (it knows
which Python to use). The second line turns on **Tab completion** of its
options in every new terminal (``$VIRTUAL_ENV``, set by the activation, is
replaced by your environment's full path).

Check, in a new terminal: ``rosace --version`` prints the version, and
``rosace --pu`` followed by Tab offers ``--purge-simulated``,
``--purge-failed`` and ``--purge-demo``. Activating the environment is then
only needed to work on the code.

Useful commands
^^^^^^^^^^^^^^^

.. list-table::
   :header-rows: 1

   * - Action
     - Command
   * - Start the demo
     - ``rosace --demo-mode`` (``--port 8001`` to change the port)
   * - Stop it
     - Ctrl+C in its terminal
   * - List every option
     - ``rosace --help``
   * - Fresh demo (server stopped)
     - ``rosace --purge-demo --apply``
   * - Get a newer version
     - ``git pull`` in the repository, then ``pip install -e rosace/`` with
       the environment active, and reload the page in your browser
       (Ctrl+Shift+R)

If something goes wrong
^^^^^^^^^^^^^^^^^^^^^^^

* **rosace: command not found**: the environment is not active in this
  terminal (``source ~/.venvs/rosace/bin/activate``), or, for the link of
  `Use rosace from any terminal`_, ``~/.local/bin`` is not yet in your
  ``PATH``: log out and in again.
* **"Replay dataset not found" in the log, nothing observed**: the demo data
  are not in ``~/.rosace-demo/replay`` — see `Get the demo data`_.
* **Every observation fails at pointing ("plate solve" in the log)**:
  ``solve-field`` is not installed, or does not find the index files — see
  `Tell astrometry.net where the index files are`_.
* **The page looks old or broken after an update**: reload it bypassing the
  cache (Ctrl+Shift+R).
* **--purge-demo refuses to run**: the demo server is still running; stop it
  first.

Step 3: Run ROSACE with the INDI simulators
-------------------------------------------
(to be done)

Step 4: Connect your own instrument
-----------------------------------
(to be done)
