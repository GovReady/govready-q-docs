.. Copyright (C) 2020 GovReady PBC

.. _Automated testing:
.. _chromedriver: https://chromedriver.chromium.org/
.. _VcXsrv : https://sourceforge.net/projects/vcxsrv/

Automated testing
=================

GovReady-Q's unit tests and integration tests are currently combined. Our integration tests uses Selenium to simulate user interactions with the interface.

To run the integration tests, you'll also need to install chromedriver:

.. code-block:: bash

   sudo apt-get install chromium-chromedriver   (on Ubuntu)
   brew cask install chromedriver               (on Mac)

Navigate within your terminal to GovReady-Q top level directory.

Then run the test suite with:

.. code-block:: bash

    python manage.py test

.. note::
   Depending on your Python3 configuration, you may need to run

   .. code:: bash

      python3 manage.py test

To selectively run tests from individual modules:

.. code-block:: bash

    # test rendering of guided modules
    python manage.py test guidedmodules
    
    # test general siteapp logic
    python manage.py test siteapp
    
    # test discussion functionality
    python manage.py test discussion

Or to selectively run tests from individual classes or methods:

.. code-block:: bash

    # run tests from individual test class
    python manage.py test siteapp.tests.GeneralTests
    
    # run tests from individual test method
    python manage.py test siteapp.tests.GeneralTests.test_login

    # run tests from different apps in sequence
    python manage.py test siteapp.tests.GeneralTests.test_create_portfolios discussion.tests.DiscussionTests

To produce a code coverage report, run the tests with `coverage`:

.. code-block:: bash

    coverage run --source='.' --branch manage.py test
    coverage report

Selenium Troubleshooting
~~~~~~~~~~~~~~~~~~~~~~~~

**500 Internal Server Error**

Receiving an **500 Internal Server Error** in Selenium's Chromium web browser during
testing indicates an error serving the page.

If error is received only on some tests, the testing framework has located a legitimate problem
rendering that page that needs to be corrected.

If the error occurs rendering every page, the probable cause is missing static files. Correct this problem
by re-fetch vendor resources, check your ``static`` setting in the ``local/environment.json`` file
and re-run Django ``collectstatic`` admin command.

.. code-block:: bash

    ./fetch-vendor-resources.sh
    python manage.py collectstatic

To debug further, set the verbosity of the tests to level 3 for increased log output and
look for ``Missing staticfiles manifest entry for`` or other error messages detailing problems
with serving the page.

.. code-block:: bash

    python manage.py test -v 3


**IDE Test Configuration - Settings File Not Found**

When executing tests through an IDE, the test configuration may error due to a lack of specifying a settings file.
In the default test interpreter, set the settings file to siteapp/settings.py.
This file contains the Django settings for the GovReady-Q project.


Windows(WSL) users
~~~~~~~~~~~~~~~~~~~~~

Aside from downloading (i.e. chromium-chromedriver) the executable to the system Windows needs a PATH that points to where the executable chromedriver file is located. This is also true for Windows Subsystem for Linux. A standard location to move the chromedriver executable to is `/usr/local/bin`. With that the program can just be named in your section of code without any other path parts needed. Below is an example of adding chromedriver as a parameter of the selenium chrome webdriver:

.. code-block:: bash

    driver = selenium.webdriver.Chrome(executable_path='chromedriver.exe')

.. note::
   The above applies for WSL 1 and for WSL 2 there are a few more steps to properly use chromedriver. If you ever want to set your Ubuntu install to use a different WSL version then in a windows terminal run ``wsl --set-version Ubuntu <new_version_number>``


WSL version 2
---------------

For WSL 2 on Ubuntu you need to do these steps even if you have Chrome installed in Windows.

Dependencies:

.. code-block:: bash

    sudo apt-get update
    sudo apt-get install -y curl unzip xvfb libxi6 libgconf-2-4

Chrome itself:

.. code-block:: bash

    wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
    sudo apt install ./google-chrome-stable_current_amd64.deb

Ensure it worked:

.. code-block:: bash

    google-chrome --version

Find the chromedriver_ url for the ChromeDriver version that matches your Chrome version (e.g. **https://chromedriver.storage.googleapis.com/86.0.4240.22/chromedriver_linux64.zip**)

Download, unzip, and put in your local bin directory:

.. code-block:: bash

    wget https://chromedriver.storage.googleapis.com/86.0.4240.22/chromedriver_linux64.zip
    unzip chromedriver_linux64.zip
    sudo mv chromedriver /usr/local/bin/chromedriver
    sudo chown root:root /usr/bin/chromedriver
    sudo chmod +x /usr/local/bin/chromedriver

chromedriver should now point to the newly installed chromedriver

.. code-block:: bash

    which chromedriver # /usr/local/bin/chromedriver

Last but not least we need to download and install VcXsrv_. Then run **xlaunch.exe** from the programs files folder (for VcXsrv). Leave most settings as default but check the "Disable access control". In Linux the DISPLAY environment variable tells GUI applications at which IP address the X Server is that we want to use. Since in WSL 2 the IP address of Windows land is not ``localhost`` anymore, we need to set DISPLAY to the correct IP address:

.. code-block:: bash

    export DISPLAY=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2; exit;}'):0.0

.. note::

    You can put it anywhere but I recommend **.bashrc**.

Now if you run ``echo $DISPLAY`` you should get something like ``172.17.35.177:0.0``.
