.. Copyright (C) 2020 GovReady PBC

.. _terminal: https://www.jetbrains.com/help/pycharm/settings-tools-terminal.html
.. _BaseCommand: https://docs.djangoproject.com/en/dev/howto/custom-management-commands/
.. _chromedriver.exe: https://chromedriver.chromium.org/downloads

.. _Developing for Govready-Q:

Developing for Govready-Q
=========================

.. meta::
  :description: These pages cover topics related to working with the source of GovReady-Q.

These pages cover topics related to working with the source of GovReady-Q.

You may also want to refer to the :ref:`Installation and Setup` section of the guide, and either :ref:`Desktop Installation` or :ref:`Server-based Installation`, depending on your development environment.

.. topic:: Contents

   .. toctree::
      :maxdepth: 1

      installing-govready-q-for-development
      data-model-design-guide/index
      code-style-guide/index


Examples for Setting Up Your Development Environment
####################################################

.bashrc
-------

The .bashrc file is THE file that will make your life a lot easier once you have a couple settings set.

To view your ``.bashrc`` file enter the following commands.

.. code:: bash

   cd ~
   cat .bashrc

We will be entering three lines at the end of this file.

1. Change directory directly to your govready-q root
____________________________________________________

.. code:: bash

    cd "/mnt/../path/to/govready-q"

2. The virtual environment activation command
_____________________________________________

.. code:: bash

    source <virtualenv>/bin/activate

Common values for ``<virtualenv>`` are ``venv`` or ``env``. GovReady-Q's ``.gitignore`` is set up for ``env``.

3. The alias to open any directory in the given filesystem with PyCharm ``charm . &``
_____________________________________________________________________________________

.. code:: bash

    alias charm="/mnt/path/to/JetBrains/'<version number>'/bin/pycharm64.exe"

For example, ``<version number>`` could be ``PyCharm 2020.1.1``.

Locate the absolute path to pycharm64.exe with ``which pycharm64.exe``. Single quotes must be placed around parts of the path that aren't contiguous. For example the version number should be entered as 'PyCharm 2020.1.1'.

PyCharm
########

PyCharm Professional is one of the IDEs we prefer, and it has many tools and features that you will find enhance development experience and we will explore a few here.

This first tip is primarily for Windows users using WSL to develop.

The terminal_ shell used can be configured
------------------------------------------

**File | Settings | Tools | Terminal for Windows and Linux**

**PyCharm | Preferences | Tools | Terminal for macOS** ``Ctrl+Alt+S``

.. figure:: /assets/settingstoolsterminal.png
   :alt: Tools > terminal location

   Tools > terminal location

.. figure:: /assets/toolsterminalpanel.png
   :alt: Tools > terminal location

   Tools > terminal location

.. figure:: /assets/cmdexepic.png
   :alt: Default command-line terminal

   Default command-line terminal

.. figure:: /assets/wslexepic.png
   :alt: Linux command-line terminal

   Linux command-line terminal

.. figure:: /assets/wslexeterminal.png
   :alt: WSL Terminal panel

   WSL Terminal panel

.. Copyright (C) 2020 GovReady PBC

.. _Supply chain and dependency management:

Supply chain and dependency management
#######################################

Our ``requirements.txt`` file is designed to work with ``pip install --require-hashes``, which ensures that every installed dependency matches a hash stored in this repository. The option requires that every dependency (including dependencies of dependencies) be listed, pinned to a version number, and paired with a hash. We therefore don't manually edit ``requirements.txt``. Instead, we place our immediate dependencies in ``requirements.in`` and run ``requirements_txt_updater.sh`` (which calls pip-tools's pip-compile command) to update the ``requirements.txt`` file for production.

Continuous integration is set up with CircleCI at https://circleci.com/gh/GovReady/govready-q and performs unit tests, integration tests, and security checks on our dependencies.

1. CI runs ``requirements_txt_checker.sh`` which ensures ``requirements.txt`` is in sync with ``requirements.in``. This script is set up to run against any similar files as well, such as MySQL-specific ``requirements_mysql.*`` files.
2. CI checks that there are no known vulnerabilities in the dependencies using [pyup.io](https://pyup.io/).
3. CI checks that all packages are up to date with upstream sources (unless the package and its latest upstream version are listed in ``requirements_txt_checker_ignoreupdates.txt``).

If you wish to update a package make sure to change its reference in ``requirements.txt`` and ``requirements.in``. For example in upgrading gevent I would change gevent==1.4.0 to gevent==20.9.0 in both files. From there I would run ``requirements_txt_updater.sh`` which updates the hashes for gevent.

.. note::
   This logic applies to any requirements_* files (e.g. requirements_util.txt/requirements_util.in)

Management Commands
###################

GovReadyQ's ``manage.py`` uses ``django.core.management`` to orchestrate management commands.


Implementation
---------------

.. code:: bash

   from django.core.management import execute_from_command_line

   execute_from_command_line(sys.argv)

I will summarize the fantastic documentation the Django development team has for BaseCommand_ in order for you to create your own commands. Bottom line, we create a new class that inherits from the Django object ``BaseCommand``.

For this walk-through I will outline how the management command ``./manage.py compliance_app`` allows us to list all available app sources.

First and foremost we need to ensure we have a **management/commands** directory under the Django app of choice, in this example **guidedmodules** (which registers the command when guidedmodules is included in **INSTALLED_APPS**). Then a new file **compliance_app.py** for our ``compliance_app`` command argument. In this file we import and inherit ``BaseCommand`` into our new class. Adding some help text to remind us of this commands purpose.

.. note::
   We are now in **~/govready-q/guidedmodules/management/commands/compliance_app.py**

.. code-block:: bash

   from django.core.management.base import BaseCommand

   class Command(BaseCommand):
       help = 'Creates a new compliance app. Lists available appsource names if no command-line arguments are given.'

We can now add override the ``handle`` method which is the only required implementation. An overview provided here.

.. code-block:: bash

   class Command(BaseCommand):

   ...

   def handle(self, *args, **options):
        # if no appsource specified
            # Show valid appsources by iterating through all AppSource objects and prints the slug name and path

        # other argument logic that includes appsource specified


For additional arguments **compliance_app** also overrides ``add_arguments``. Here we specify the argument to enter (``'appsource'``), the number of arguments (``"?"``), and its type (``str``). These arguments are then implemented in the handle override.

.. code-block:: bash

   class Command(BaseCommand):

   ...

   def add_arguments(self, parser):
        parser.add_argument('appsource', nargs="?", type=str)
        parser.add_argument('--path', nargs="?", type=str)
        parser.add_argument('appname', nargs="?", type=str)

   ...

Currently Implemented
---------------------

**guidedmodules**

    +----------------------------------------------------------+-----------------------------------------------------------------------------------------------------+
    | Command                                                  |    Usage Notes                                                                                      |
    +==========================================================+=====================================================================================================+
    | ``./manage.py apps_catalog``                             |  Lists all apps in the configured app stores                                                        |
    +----------------------------------------------------------+-----------------------------------------------------------------------------------------------------+
    | ``./manage.py assemble --init path/to/app assemble.yaml``|   creating an 'empty' YAML file for a given app                                                     |
    +----------------------------------------------------------+-----------------------------------------------------------------------------------------------------+
    | ``./manage.py assemble assemble.yaml outdir``            |  Make a directory ``mkdir outdir``. Then assemble the app's output documents to an output           |
    +----------------------------------------------------------+-----------------------------------------------------------------------------------------------------+
    | ``./manage.py assemble --startapps assemble.yaml``       |  Start component apps automatically                                                                 |
    +----------------------------------------------------------+-----------------------------------------------------------------------------------------------------+
    | ``./manage.py compliance_app``                           |  lists available app sources                                                                        |
    +----------------------------------------------------------+-----------------------------------------------------------------------------------------------------+
    | ``./manage.py compliance_app mysource``                  |  creates a new local AppSource named mysource                                                       |
    +----------------------------------------------------------+-----------------------------------------------------------------------------------------------------+
    | ``./manage.py compliance_app mysource myapp``            |  creates a new app at path/to/apps/myapp                                                            |
    +----------------------------------------------------------+-----------------------------------------------------------------------------------------------------+
    | ``./manage.py load_modules``                             |  Updates the system modules from the YAML specifications in AppSources                              |
    +----------------------------------------------------------+-----------------------------------------------------------------------------------------------------+
    | ``./manage.py upgrade_project``                          |  Upgrades a project to a new version of an app or associates it with a different compliance app     |
    +----------------------------------------------------------+-----------------------------------------------------------------------------------------------------+

**siteapp**

    +----------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+
    | Command                                                  |    Usage Notes                                                                                                                            |
    +==========================================================+===========================================================================================================================================+
    | ``./manage.py db_before_090``                            |  Check if version 0.9.0 migration has been run. Return "False" if database not initialized or 0.9.0 migrations has been run               |
    +----------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+
    | ``./manage.py first_run``                                |  Interactively set up an initial user and organization                                                                                    |
    +----------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+
    | ``./manage.py first_run --non-interactive``              |  Non-interactively set up an initial user and organization                                                                                |
    +----------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+
    | ``./manage.py send_notification_emails``                 |  Sends emails for notifications                                                                                                           |
    +----------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+
    | ``./manage.py send_notification_emails forever``         |  Sends emails for notifications forever                                                                                                   |
    +----------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+
    | ``./manage.py test_screenshots``                         |  Generate screenshots of the application using Selenium for creating test artifacts and documentation. Run on non-production servers only.|
    +----------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+

* **siteap.test_screenshots arguments**

    .. note::
       Need chromedriver.exe_ available in PATH for Selenium

    A throwaway test database is used so that this command cannot see any existing
    user data, and database changes are not persistent. However, it would not be
    advisable to run this command on a production system.

    Examples:

    Create screenshots for the FISMA Level app:

    .. code-block:: bash

       ./manage.py test_screenshots --app-source '{ "slug": "govready-apps-dev", "type": "git", "url": "https://github.com/GovReady/govready-apps-dev", "path": "apps" }' \
       --app govready-apps-dev/fisma_level \
       --path screenshots.pdf

    The ``--app-source`` argument can be repeated multiple times if more than one AppSource
    is needed to run the script.

    Create screenshots for authoring a new app and set
    (approximate) output image size:

    .. code-block:: bash

       ./manage.py test_screenshots --author-new-app \
                                --path screenshots.pdf \
                                --size 1024x768

    +---------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | Argument                                                            |    Usage Notes                                                                                                                                              |
    +=====================================================================+=============================================================================================================================================================+
    | ``./manage.py test_screenshots --org-name``                         |  The name of the temporary Organization that will be created.                                                                                               |
    +---------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | ``./manage.py test_screenshots --app-source {source JSON}``         |  An AppSource definition in JSON. This argument can be repeated.                                                                                            |
    +---------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | ``./manage.py test_screenshots  --app [source/app]``                |  The AppSource slug plus app name of a compliance app to fill out.                                                                                          |
    +---------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | ``./manage.py test_screenshots --test [testid]``                    |  The ID of the test to run defined in the app's app.yaml 'tests' key, or @filename to load a test from a YAML file.                                         |
    +---------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | ``./manage.py test_screenshots --author-new-app``                   |  Take screenshots for Q documentation showing how to author a new compliance app.                                                                           |
    +---------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | ``./manage.py test_screenshots  --path [dir_or_pdf]``               |  The path to write screenshots into, either a directory or a filename ending with .pdf.                                                                     |
    +---------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | ``./manage.py test_screenshots --size [widthXheight]``              |  The width and height, in pixels, of the headless web browser window, or 'maximized'.                                                                       |
    +---------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | ``./manage.py test_screenshots --mouse-speed [seconds]``            |  Each mouse move will have this duration.                                                                                                                   |
    +---------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | ``./manage.py test_screenshots --version``                          |  Show program's version number and exit                                                                                                                     |
    +---------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | ``./manage.py test_screenshots -v {0,1,2,3}, --verbosity {0,1,2,3}``|  Verbosity level; 0=minimal output, 1=normal output, 2=verbose output, 3=very verbose output                                                                |
    +---------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | ``./manage.py test_screenshots --settings SETTINGS``                |  The Python path to a settings module, e.g. "myproject.settings.main". If this isn't provided, the DJANGO_SETTINGS_MODULE environment variable will be used.|
    +---------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | ``./manage.py test_screenshots --pythonpath PYTHONPATH``            |  A directory to add to the Python path, e.g. "/home/djangoprojects/myproject".                                                                              |
    +---------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | ``./manage.py test_screenshots --traceback``                        |  Raise on CommandError exceptions                                                                                                                           |
    +---------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | ``./manage.py test_screenshots --no-color``                         |  Don't colorize the command output.                                                                                                                         |
    +---------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | ``./manage.py test_screenshots --force-color``                      |  Force colorization of the command output.                                                                                                                  |
    +---------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | ``./manage.py test_screenshots --skip-checks``                      |   Skip system checks.                                                                                                                                       |
    +---------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------+
