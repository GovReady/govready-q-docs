.. Copyright (C) 2020 GovReady PBC

.. _Windows:
.. _wsl: https://docs.microsoft.com/en-us/windows/wsl/install-win10
.. _virtualenv: https://pypi.org/project/virtualenv/
.. _pip-tools: https://pypi.org/project/pip-tools/
.. _wkhtmltopdf: https://wkhtmltopdf.org/downloads.html
.. _Ubuntu: https://askubuntu.com/questions/1034313/ubuntu-18-4-libqt5core-so-5-cannot-open-shared-object-file-no-such-file-or-dir
.. _strip: https://sourceware.org/binutils/docs/binutils/strip.html


Windows
=======

GovReady-Q is primarily designed to be installed on Unix-like operating systems, but we include these instructions for installing GovReady-Q on Windows computers.

There are three ways to run GovReady-Q on Windows.

To try GovReady-Q, or to run it without modifications, we recommend using a container solution.  See :ref:`Container-based Installation` and :ref:`Docker Local`.

.. note::
   For some libraries like `magic` there is no equivalent in Windows so to work around this we can use Windows' Subsystem for Linux(WSL_). WSL allows you to install your Linux distribution of choice and is only in 64-bit versions of Windows 10 from version 1607. This guide will be using the Ubuntu(20.04) distribution. For a detailed list of instructions on how to enable and install it visit the official documentation at WSL_. To check if your efforts were successful, run in any windows terminal the following command:

.. code:: bash

    wsl -l -v

This should display a table in standard output close to:

+------------+------------+-----------+
|  NAME      |STATE       | VERSION   |
+============+============+===========+
| Ubuntu     | Running    |  1        |
+------------+------------+-----------+

Integrated Development Environment
______________________________________

Govready is IDE-agnostic but for the purposes of this tutorial we will assume you are using Pycharm(2020.xx). One exceedingly excellent feature of PyCharm is the ease at configuring and managing virtual environments and terminals used in each project. In order to seamlessly use WSL in PyCharm you can configure your terminal to run WSL by going to settings by settings an absolute or relative path to the WSL executable file C:\Windows\System32\wsl.exe or `wsl.exe`

In this Unix-like environment on your Windows computer we can essentially follow the :ref:`Server-based Installation`. However, to maintain a clean interpreter make sure to create and activate a virtual environment with virtualenv(as WSL **cannot** create virtual environments):

.. code:: bash

   virtualenv venv
   source venv/bin/activate

The :ref:`Server-based Installation` already includes a section on installing required libraries. However, you need to install these dependencies as well as is done in the CircleCI configuration file ``config.yml``

Additional dependencies:
------------------------------

.. code:: bash

   pip3 install -r requirements.txt
   sudo apt update && sudo apt install -y git curl unzip locales libmagic1 graphviz pandoc xvfb wkhtmltopdf #! xvfb and wkthmltopdf are used in conjunction to headless convert html to pdf.
   sudo sed -i "s/^[# ]*en_US.UTF-8/en_US.UTF-8/" /etc/locale.gen && sudo /usr/sbin/locale-gen #! Installs the U.S. locale (see `apt install locales` above), which we reference explicitly in Q for formatting and parsing numbers. Usually only needed on slim builds of Debian images.


Finally, to ensure pdf generation with ``wkhtmltopdf`` can occur:
_____________________________________________________________________________

.. code:: bash

    sudo strip --remove-section=.note.ABI-tag /usr/lib/x86_64-linux-gnu/libQt5Core.so.5

.. note::
    Here is a discuss for why this is necessary in Ubuntu_. Additionally, if you are intersted in understanding what strip_ is doing with this argument combination ``--remove-section=<sectionname> <objfile>``. Here ``sectionname`` in ``--remove-section=sectionname`` is ``.note.ABI-tag`` and ``objfile`` is ``/usr/lib/x86_64-linux-gnu/libQt5Core.so.5`` the part of libQt5Core that needs symbols discarded from it. Essentially, removing information from this object file that is not essential allowing for proper usage by ``wkhtmltopdf``.

Run server
----------

   Run the test server with ``python manage.py runserver`` or ``./ manage.py runserver`` and visit your GovReady-Q site in your web browser at
   http://localhost:8000/ or your set govready-url in ``environment.json``.
