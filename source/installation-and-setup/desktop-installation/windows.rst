.. Copyright (C) 2020 GovReady PBC

.. _Windows:
.. _wsl: https://docs.microsoft.com/en-us/windows/wsl/install-win10
.. _virtualenv: https://pypi.org/project/virtualenv/
.. _pip-tools: https://pypi.org/project/pip-tools/
.. _wkhtmltopdf: https://wkhtmltopdf.org/downloads.html
.. _Ubuntu 18.4 libQt5Core.so.5: https://askubuntu.com/questions/1034313/ubuntu-18-4-libqt5core-so-5-cannot-open-shared-object-file-no-such-file-or-dir
.. _strip: https://sourceware.org/binutils/docs/binutils/strip.html
.. _Pycharm: https://www.jetbrains.com/help/pycharm/configuring-line-endings-and-line-separators.html
.. _Notepad++: https://support.nesi.org.nz/hc/en-gb/articles/218032857-Converting-from-Windows-style-to-UNIX-style-line-endings

Windows
=======

GovReady-Q is primarily designed to be installed on Unix-like operating systems, but we include these instructions for installing GovReady-Q on Windows computers.

There are three ways to run GovReady-Q on Windows.

To try GovReady-Q, or to run it without modifications, we recommend using a container solution.  See :ref:`Container-based Installation` and :ref:`Docker Local`.

.. note::
   For some libraries like `magic` there is no equivalent in Windows so to work around this we can use the Windows Subsystem for Linux (WSL_). WSL allows you to install your Linux distribution of choice and is only in 64-bit versions of Windows 10 from version 1607. This guide will be using the Ubuntu (20.04) distribution. For a detailed list of instructions on how to enable and install it visit the official documentation at WSL_. To check if your efforts were successful, run in any windows terminal the following command:

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

Govready is IDE-agnostic but for the purposes of this tutorial we will assume you are using Pycharm (2020.xx). One exceedingly excellent feature of PyCharm is the ease of configuring and managing virtual environments and terminals used in each project. In order to seamlessly use WSL in PyCharm you can configure your terminal to run WSL by setting an absolute or relative path to the WSL executable file ``C:\Windows\System32\wsl.exe`` or ``wsl.exe``

In this Unix-like environment on your Windows computer we can essentially follow the :ref:`Server-based Installation`. However, to maintain a clean interpreter make sure to create and activate a virtual environment with virtualenv(as WSL **cannot** create virtual environments):

.. code:: bash

   virtualenv venv
   source venv/bin/activate

.. note::
    To make sure PyCharm opens your terminals with the virtual environment activated, you can run these commands in your ``.bashrc``. This includes a change of directory to your project directory and then the activation command described in :ref:`Developing for Govready-Q`. To view your ``.bashrc`` file enter the following commands.

.. code:: bash

   cd ~
   cat .bashrc

The :ref:`Server-based Installation` already includes a section on installing required libraries. However, you need to install these dependencies as well, as is done in the CircleCI configuration file ``config.yml``

Run the install script to install required Python libraries, initialize GovReady-Q's database and create a superuser. This is the same command for all database backends.

.. code:: bash

   # Run the install script to install Python libraries,
   # initialize database, and create Superuser
   ./install-govready-q

.. note::
   When using any shell scripts on Windows make sure the file's format is in Unix Format to avoid a read error. You can change with an EOL conversion (CRLF to LF) with PyCharm_ or Notepad++_ using install-govready-q.sh. If you forget to change the EOL for the files you will have to manually add in an organization and related data (Help squad).

.. note::
   The command ``install-govready-q.sh`` creates the Superuser interactively allowing you to specify username and password.

   The command ``install-govready-q.sh --non-interactive`` creates the Superuser automatically for installs where you do
   not have access to interactive access to the command line. The auto-generated username and password will be output (only once) to the stdout log.


Additional dependencies:
------------------------

.. code:: bash

   pip3 install -r requirements.txt
   sudo apt update && sudo apt install -y git curl unzip locales libmagic1 graphviz pandoc xvfb wkhtmltopdf #! xvfb and wkthmltopdf are used in conjunction to convert html to pdf headlessly.
   sudo sed -i "s/^[# ]*en_US.UTF-8/en_US.UTF-8/" /etc/locale.gen && sudo /usr/sbin/locale-gen #! Installs the U.S. locale (see `apt install locales` above), which we reference explicitly in Q for formatting and parsing numbers. Usually only needed on slim builds of Debian images.


Finally, to ensure pdf generation with ``wkhtmltopdf`` can occur:

.. code:: bash

    sudo strip --remove-section=.note.ABI-tag /usr/lib/x86_64-linux-gnu/libQt5Core.so.5

The rationale for this fix in Ubuntu is discussed in this post about `Ubuntu 18.4 libQt5Core.so.5`_.

strip_ removes information from the object file that is not essential, allowing ``wkhtmltopdf`` to run properly.

In the ``strip`` command shown, ``.note.ABI-tag`` is the section to operate on, and ``/usr/lib/x86_64-linux-gnu/libQt5Core.so.5`` is the object file to operate on.

Run server
----------

   Run the test server with ``python manage.py runserver`` or ``./ manage.py runserver`` and visit your GovReady-Q site in your web browser at
   http://localhost:8000/ or as specified by ``govready-url`` in ``environment.json``.
