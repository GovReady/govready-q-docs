.. Copyright (C) 2020 GovReady PBC

.. _Developing for Govready-Q:
.. _terminal: https://www.jetbrains.com/help/pycharm/settings-tools-terminal.html

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