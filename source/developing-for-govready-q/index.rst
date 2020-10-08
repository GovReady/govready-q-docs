.. Copyright (C) 2020 GovReady PBC

.. _Developing for Govready-Q:
.. _terminal: https://www.jetbrains.com/help/pycharm/settings-tools-terminal.html
.. _BaseCommand: https://docs.djangoproject.com/en/dev/howto/custom-management-commands/

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


General Development Tips:
###########################

.bashrc
---------------

The .bashrc file is THE file that will make your life a lot easier going once you have a couple settings set.

To view your ``.bashrc`` file enter the following commands.

.. code:: bash

   cd ~
   cat .bashrc

We will be entering three lines at the end of this file.

1. A Change of directory directly to your govready-q root:
______________________________________________________________________________

.. code:: bash

    cd "/mnt/../path/to/govready-q"

2. The virtual environment activation command:
______________________________________________________________________________

virtualenv example: venv

.. code:: bash

    source <virtualenv>/bin/activate

3. The alias to open any directory in the given filesystem with PyCharm ``charm . &``:
__________________________________________________________________________________________________

version number example : PyCharm 2020.1.1

.. code:: bash

    alias charm="/mnt/path/to/JetBrains/'<version number>'/bin/pycharm64.exe"

.. note::

    Locate the absolute path to pycharm64.exe with ``which pycharm64.exe``. Single quotes must be placed around parts of the path that aren't contiguous. For example the version number should be entered as 'PyCharm 2020.1.1'.

PyCharm
########

PyCharm Professional has a lot of tools and features that you will find enhance development experience and we will explore a few here.

This first tip is primarily for Windows users using WSL to develop.

1. The terminal_ shell used can be configured
_______________________________________________

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


Management Commands:
################################################

GovReadyQ's ``manage.py`` uses ``django.core.management`` to orchestrate management commands.


Implementation
---------------

.. code:: bash

   from django.core.management import execute_from_command_line

   execute_from_command_line(sys.argv)

I will summarize the fantastic documentation the Django development team has for BaseCommand_ in order for you to create your own commands. Bottom-line we create a new class that inherits from the Django object ``BaseCommand``.

For this walk-through I will outline how the management command ``./manage.py compliance_app`` allows us to list all available app sources.

First and foremost we need to ensure we have a **management/commands** directory under the Django app of choice, in this example **guidedmodules**(which registers the command when guidedmodules is included in **INSTALLED_APPS**). Then a new file **compliance_app.py** for our ``compliance_app`` command argument. In this file we import and inherit ``BaseCommand`` into our new class. Adding some help text to remind us of this commands purpose.

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


For additional arguments **compliance_app** also overrides ``add_arguments``. Here we specify the argument to enter(``'appsource'``), the number of arguments(``"?"``), and its type(``str``). These arguments are then implemented in the handle override.

.. code-block:: bash

   class Command(BaseCommand):

   ...

   def add_arguments(self, parser):
        parser.add_argument('appsource', nargs="?", type=str)
        parser.add_argument('--path', nargs="?", type=str)
        parser.add_argument('appname', nargs="?", type=str)

   ...

Currently Implemented
----------------------------

**guidedmodules**:

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

