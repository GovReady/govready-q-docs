.. Copyright (C) 2020 GovReady PBC

.. _Installing GovReady-Q for Development:

Installing GovReady-Q for Development
=====================================

This page provides additional tips for installing and running GovReady-Q in
a way that is suitable for making and testing changes to the software (i.e., in
a Dev environment).

.. _Quickstart:

Quickstart
----------

For local development, there is a quickstart script available to speed
up environment setup. After installing :ref:`system requirements<System Requirements>`
through your package manager, run the following four commands in order
to set up GovReady-Q in a new directory:

::

   git clone https://github.com/govready/govready-q
   cd govready-q
   virtualenv -p python3 env
   ./quickstart.sh

This will set up a ``local/environment.json`` file suitable for a dev
environment; set up local dependencies; and run the assorted initial
manage.py commands (``migrate``, ``load_modules``, ``first_run``, etc.).
Additionally, it can run common post-installation steps, based on user
input.

The ``quickstart.sh`` script is set up to take user input, and is
expected to be run interactively.

Creating local/environment.json file
------------------------------------

When you first run GovReady-Q with ``python manage.py runserver``,
you’ll be prompted to copy some JSON data into a file at
``local/environment.json`` like this:

::

   {
     "debug": true,
     "host": "localhost:8000",
     "https": false,
     "secret-key": "...something here..."
   }

This file is important for persisting login sessions, and you can
provide other Q settings in this file.

Invitations on local systems
----------------------------

You will probably want to try the invite feature at some point. The
debug server is configured to dump all outbound emails to the console.
So if you “invite” others to join you within the application, you’ll
need to go to the console to get the invitation acceptance link.

Updating the source code
------------------------

To update the source code from this repository you can ``git pull``. You
then may need to re-run some of the setup commands:

::

   # if you set up virtualenv
   git pull
   source env/bin/activate
   pip3 install -r requirements.txt
   ./fetch-vendor-resources.sh
   python3 manage.py migrate
   python3 manage.py load_modules

   # if you did NOT set up virtualenv
   git pull
   pip3 install --user -r requirements.txt
   ./fetch-vendor-resources.sh
   python3 manage.py migrate
   python3 manage.py load_modules

Certain files like the shell script file **./fetch-vendor-resources.sh** may display an error related to Windows' end of line characters used (``\r\n``) vs. Unix (``\n``).

Multiple methods are available to manage end of line characters between Unix and Windows. One method, easily set up when installing Git or Git-bash on Windows is to accept the default configuration setting "Checkout Windows-style, commit Unix-style."

Another method for handling different line endings between Windows and Unix is to use the ``git config core.autocrlf`` command. See `Github docs configuring-git-to-handle-line-endings <https://docs.github.com/en/free-pro-team@latest/github/using-git/configuring-git-to-handle-line-endings>`_.

Finally, you can use the ``dos2unix`` command to change files in a single file.

.. code-block:: bash

    sudo apt install dos2unix
    dos2unix /PATH/TO/YOUR/WINDOWS_FILE

.. note::

    If you need to go from unix to dos their is an equivalent command ``unix2dos /PATH/TO/YOUR/LINUX_FILE``
