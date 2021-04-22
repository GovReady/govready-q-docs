.. Copyright (C) 2020 GovReady PBC

.. _Automated testing:
.. _chromedriver: https://chromedriver.chromium.org/
.. _VcXsrv : https://sourceforge.net/projects/vcxsrv/

Automated testing
=================

GovReady-Q's unit tests and integration tests are currently combined. Our integration tests uses Selenium to simulate user interactions with the interface.

.. code-block:: bash

    python manage.py test -v 3


**IDE Test Configuration - Settings File Not Found**

When executing tests through an IDE, the test configuration may error due to a lack of specifying a settings file.
In the default test interpreter, set the settings file to siteapp/settings.py.
This file contains the Django settings for the GovReady-Q project.
