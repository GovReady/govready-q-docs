.. Copyright (C) 2020 GovReady PBC

.. _Windows:
.. _gitbash: https://gitforwindows.org/
.. _virtualenv: https://pypi.org/project/virtualenv/
.. _pip-tools: https://pypi.org/project/pip-tools/
.. _wkhtmltopdf: https://wkhtmltopdf.org/downloads.html


Windows
=======

GovReady-Q is designed to be installed on Unix-like operating systems.

There are three ways to run GovReady-Q on Windows.

To try GovReady-Q, or to run it without modifications, we recommend using a container solution.  See :ref:`Container-based Installation` and :ref:`Docker Local`.


.. note::
   For Windows, the Docker local installation guide is the same process except for the creation of your GovReady-Q Django
   Superuser account and organization. For this section you need to pre-append **winpty** when using Git Bash since the
   input device is not a TTY. **winpty docker container exec -it govready-q first_run**

For a deeper trial of GovReady-Q, or to install it for development (making changes to the source code), you should set up
a Unix-like environment on your Windows computer, and then refer to :ref:`Server-based Installation`.

Finally a third way is to use virtualenv_ and gitbash_ (included in git for windows)

Requirements
----------------

- virtualenv_
- gitbash_
- pip-tools_
- wkhtmltopdf_


Navigate Git Bash to the root directory of govread-q-docs
-----------------------------------------------------------

- cd /path/to/govread-q-docs

Create and activate a virtual environment with virtualenv
------------------------------------------------------------

- virtualenv venv
- source venv/Scripts/activate

Install all required libraries
-----------------------------------

- pip install -r requirements.txt

.. note::
   Sample of errors when missing required libraries. `the magic library is not contained in windows`: In windows there is
no magic library so make sure to pip install python-magic-bin

Running server
-----------------------------------
 Finally run the test server with *python manage.py runserver* and visit your GovReady-Q site in your web browser at:
    http://localhost:8000/
