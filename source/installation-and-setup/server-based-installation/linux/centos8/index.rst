.. Copyright (C) 2020 GovReady PBC

.. _CentOS / RHEL 8 from sources:

CentOS / RHEL 8 from sources
============================

.. meta::
  :description: This guide describes how to install the GovReady-Q server for CentOS 8 from source code.

This guide describes how to install the GovReady-Q server for CentOS 8 from source code.
This guide will take you through the following steps:

1. Installing required OS packages
2. Cloning the GovReady-Q repository
3. Installing desired database
4. Creating the local/environment.json file
5. Installing GovReady-Q
6. Starting and stopping GovReady-Q
7. Running GovReady-Q with Gunicorn HTTP WSGI
8. Monitoring GovReady-Q with Supervisor
9. Using NGINX as a reverse proxy
10. Additional options

1. Installing required OS packages
----------------------------------

GovReady-Q requires Python 3.6 or higher and several Linux packages to
provide full functionality. Execute the following commands as root:

.. code:: bash

   # Update package list
   dnf update

   # Install dependencies
   dnf install \
   python3 python3-devel gcc-c++.x86_64 \
   unzip git jq \
   graphviz

   # for pandoc, enable PowerTools repository (equivalent of CodeReady Linux Builder repo in RHEL 8)
   dnf install dnf-plugins-core
   dnf config-manager --set-enabled PowerTools
   dnf install pandoc

   # Upgrade pip to version 20.1+ - IMPORTANT
   pip install --upgrade pip

   # Optionally install supervisord for monitoring and restarting GovReady-q; and NGINX as a reverse proxy
   pip3 install supervisor
   dnf install nginx

   # To generate thumbnails and PDFs for export, you must install wkhtmltopdf
   # WARNING: wkhtmltopdf can expose you to security risks. For more information,
   # search the web for "wkhtmltopdf Server-Side Request Forgery"
   read -p "Are you sure (yes/no)? " ; if [ "$REPLY" = "yes" ]; then dnf install xorg-x11-server-Xvfb https://github.com/wkhtmltopdf/packaging/releases/download/0.12.6-1/wkhtmltox-0.12.6-1.centos8.x86_64.rpm ; fi

2. Cloning the GovReady-Q repository
------------------------------------

You now need to decide where to install the GovReady-Q files and whether to run GovReady-Q as root or as a dedicated
Linux user. Installing as root is convenient for initial testing and some circumstances. Creating a dedicated user and installing as that user is considered better practice.

2 (option a). Installing as root
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. note::
   These steps assume you are installing into the ``/opt/`` directory as root.

Clone the GovReady-Q repository from GitHub into the desired directory on your Ubuntu server.

.. code:: bash

   cd /opt

   # Clone GovReady-Q
   git clone https://github.com/govready/govready-q
   cd govready-q

   # GovReady-Q files are now installed in /opt/govready-q and owned by root

2 (option b). Installing as Linux user "govready-q"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. note::
   These steps assume you are installing into the ``/home/govready-q`` directory as user ``govready-q``.

While you are still root, create a dedicated Linux user ``govready-q`` and home directory. Change directory into the
created user's home directory and switch users to ``govready-q``. Clone the GovReady-Q repository from GitHub.

.. code:: bash

   # Create user
   useradd govready-q -m -c "govready-q"

   # Change permissions so that the webserver can read static files
   chmod a+rx /home/govready-q

   # Switch to the govready-q user
   cd /home/govready-q
   su govready-q

   # Clone GovReady-Q
   git clone https://github.com/govready/govready-q
   cd govready-q

   # GovReady-Q files are now installed in /home/govready-q/govready-q and owned by govready-q

3. Installing desired database
------------------------------

GovReady-Q requires a relational database. You can choose:

* SQLite3
* MySQL
* MariaDB
* PostgreSQL

GovReady-Q will automatically default to and use a SQLite3 database installed at ``local/db.sqlite3``
if you do not specify a database connection string in ``local/environment.json``.

.. note::
   All files in ``govready-q/local-examples/<local*>`` can be used as a template to create the local directory.

3 (option a). Installing SQLite3 (default)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

There is no setup necessary to use SQLite3. GovReady-Q will automatically install a local SQLite3 database
``local/db.sqlite3`` by default if no ``db`` parameter is set in ``local/environment.json``.

.. note::
   All files in ``govready-q/local`` are git ignored so that you can safely pull git updates.

3 (option b). Installing MySQL
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

On the database server, install MySQL OS packages:

.. code:: bash

   # Install MySQL OS packages
    sudo yum install -y mysql-devel

Make a note of the MySQL's host, port, database name, user and password to add to GovReady-Q's configuration file at ``local/environment.json``.

.. code:: text

   {
      ...
      "db": "mysql://USER:PASSWORD@HOST:PORT/NAME",
      ...
   }

For proper operation, ensure that MySQL databases created for GovReady use UTF-8 encoding.

   .. code-block:: sql

      CREATE DATABASE govready_q
      CHARACTER SET utf8mb4
      COLLATE utf8mb4_0900_ai_ci;

3 (option c). Installing MariaDB
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

On the database server, install MariaDB related-packages:

.. code:: bash

   # Install MariaDB OS packages
    sudo yum install mariadb-server

   # Install MariaDB packages
    sudo yum install -y mysql-devel
    sudo mysql_install_db

Change ownership of a few key mariadb files and directories

.. code:: bash

    sudo chown mysql /var/log/mariadb
    sudo chown mysql /var/log/mariadb/mariadb.log
    sudo chown -R mysql /var/lib/mysql


The following should fail as the user will not have the right privileges.

.. code:: bash

    sudo systemctl start mariadb.service
    service mariadb status
   # Checking the current user (i.e. user)
    whoami
   # Start mysql with user
    mysql -user


Need to grant all privileges to the system user of your choice and set password for the user.

.. code-block:: sql

  USE mysql;
  SELECT User, Host, plugin FROM mysql.user;
  CREATE USER 'YOUR_SYSTEM_USER'@'localhost' IDENTIFIED BY '';
  GRANT ALL PRIVILEGES ON *.* TO 'YOUR_SYSTEM_USER'@'localhost';
  UPDATE user SET plugin='auth_socket' WHERE User='YOUR_SYSTEM_USER';
  UPDATE user set authentication_string=PASSWORD("") where User='YOUR_SYSTEM_USER';
  FLUSH PRIVILEGES;
  exit;

The following enables you to improve the security of your MariaDB installation in the following ways:

* You can set a password for root accounts.
* You can remove root accounts that are accessible from outside the local host.
* You can remove anonymous-user accounts.
* You can remove the test database, which by default can be accessed by anonymous users.

.. code:: bash

    sudo mysql_secure_installation

On the database server, install MariaDB OS packages:

.. code:: bash

   # Install MariaDB OS packages
    sudo yum install -y mysql-devel

The following should fail as the user will not have the right privileges.

.. code:: bash

    # start MariaDB and check its status
    sudo systemctl start mariadb.service
    service mariadb status

Make a note of the MariaDB's host, port, database name, user and password to add to GovReady-Q's configuration file at ``local/environment.json``.

.. code:: text

   {
      ...
      "db": "mysql://USER:PASSWORD@HOST:PORT/NAME",
      ...
   }

.. note::
   For mariaDB the default port is 3306.


For proper operation, ensure that MariaDB databases created for GovReady use UTF-8 encoding.

   .. code-block:: sql

      CREATE DATABASE govready_q
      CHARACTER SET utf8mb4



3 (option d). Installing PostgreSQL
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Install PostgreSQL OS packages either on the same server as GovReady-Q or on a different database server.

.. code:: bash

   sudo apt install -y postgresql postgresql-contrib
   # postgresql-setup initdb

Then set up the user and database (both named ``govready_q``):

.. code:: bash

   sudo -iu postgres createuser -P govready_q
   # Paste a long random password when prompted

   sudo -iu postgres createdb --encoding UTF8 --lc-collate 'en_US.UTF-8' --lc-ctype 'en_US.UTF-8' govready_q

Postgres’s default permissions automatically grant users access to a
database of the same name.

You must specify the database connection string in GovReady-Q's configuration file at ``local/environment.json``.

Make a note of the Postgres host, port, database name, user and password to add to GovReady-Q's configuration file at ``local/environment.json``.

.. code:: text

   {
      ...
      "db": "postgres://USER:PASSWORD@HOST:PORT/NAME",
      ...
   }

**Encrypting your connection to PostgreSQL running on a separate database server**

If PostgreSQL is running on a separate host, it is highly recommended you follow the instructions below
to configure a secure connection between GovReady-Q and PostgreSQL.

In ``/var/lib/pgsql/data/postgresql.conf``, enable TLS connections by
changing the ``ssl`` option to

.. code:: bash

   ssl = on

and enable remote connections by binding to all interfaces:

.. code:: bash

   listen_addresses = '*'

Enable remote connections to the database *only* from the webapp server
and *only* encrypted with TLS by editing
``/var/lib/pgsql/data/pg_hba.conf`` and adding the line (replacing the
hostname with the hostname of the Q webapp server):

.. code:: bash

   hostssl all all webserver.example.com md5

Generate a self-signed certificate (replace ``db.govready-q.internal``
with the database server’s hostname if possible):

.. code:: bash

   openssl req -new -newkey rsa:2048 -days 365 -nodes -x509 -keyout /var/lib/pgsql/data/server.key -out /var/lib/pgsql/data/server.crt -subj '/CN=db.govready-q.internal'
   chmod 600 /var/lib/pgsql/data/server.{key,crt}
   chown postgres.postgres /var/lib/pgsql/data/server.{key,crt}

Copy the certificate to the webapp server so that the webapp server can
make trusted connections to the database server:

.. code:: bash

   cat /var/lib/pgsql/data/server.crt
   # Place on webapp server at /home/govready-q/pgsql.crt

Restart PostgreSQL:

.. code:: bash

   service postgresql restart

And if necessary, open the PostgreSQL port:

.. code:: bash

   firewall-cmd --zone=public --add-port=5432/tcp --permanent
   firewall-cmd --reload

4. Creating the local/environment.json file
-------------------------------------------

Create the ``local/environment.json`` file with appropriate parameters. (Order of the key value pairs is not significant.)

**SQLite (default)**

.. code:: json

      {
         "govready-url": "http://localhost:8000",
         "debug": false,
         "secret-key": "long_random_string_here"
      }

**MySQL**

.. code:: json

      {
         "db": "mysql://USER:PASSWORD@localhost:PORT/NAME",
         "govready-url": "http://localhost:8000",
         "debug": false,
         "secret-key": "long_random_string_here"
      }

**MariaDB**

.. code:: json

      {
         "db": "mysql://USER:PASSWORD@localhost:3306/govready_q",
         "govready-url": "http://localhost:8000",
         "debug": false,
         "secret-key": "long_random_string_here"
      }

**PostgreSQL**

.. code:: json

      {
         "db": "postgres://govready_q:PASSWORD@localhost:5432/govready_q",
         "govready-url": "http://localhost:8000",
         "debug": false,
         "secret-key": "long_random_string_here"
      }


.. note::
   As of 0.9.1.20, the "govready-url" environment parameter is preferred way to set Django internal security, url,
   ALLOWED_HOST, and other settings instead of deprecated environment parameters "host" and "https".
   The "host" and "https" deprecated parameters will continue to be supported for a reasonable period for legacy installs.

   Deprecated (but supported for a reasonable period):

   .. code:: json

      {
         "db": "mysql://USER:PASSWORD@HOST:PORT/NAME",
         "host": "localhost:8000",
         "https": false,
         "debug": false,
         "secret-key": "long_random_string_here"
      }

   Preferred:

   .. code:: json

      {
         "db": "mysql://USER:PASSWORD@HOST:PORT/NAME",
         "govready-url": "http://localhost:8000",
         "debug": false,
         "secret-key": "long_random_string_here"
      }

   See :ref:`Configuration with Environment Variables` for a complete list of configuration options.

5. Installing GovReady-Q
------------------------

At this point, you have installed required OS packages; cloned the GovReady-Q repository; configured your preferred database option of SQLite3, MySQL, or PostgreSQL; and created the ``local/environment.json`` file with appropriate settings.

Make sure you are in the base directory of the GovReady-Q repository. (Execute the following commands as the dedicated Linux user if you set one up.)

Run the install script to install required Python libraries, initialize GovReady-Q's database and create a superuser. This is the same command for all database backends.

.. code:: bash

   # If you created a dedicated Linux user, be sure to switch to that user to install GovReady-Q
   # su govready-q
   # cd /home/govready-q/govready-q

   # Run the install script to install Python libraries,
   # initialize database, and create Superuser
   ./install-govready-q.sh


.. note::
   The command ``install-govready-q.sh`` creates the Superuser interactively allowing you to specify username and password. However, if there already is a superuser it will not prompt you to create one.

   The command ``install-govready-q.sh --non-interactive`` creates the Superuser automatically for installs where you do
   not have access to interactive access to the command line. The auto-generated username and password will be output (only once) to the stdout log.

6. Starting and stopping GovReady-Q
-----------------------------------

**Starting GovReady-Q**

You can now start GovReady-Q Server. GovReady-Q defaults to listening on localhost:8000, but can easily be run to listen on other host domains and ports.

.. code:: bash

   # Run the server on the default localhost and port 8000
   python3 manage.py runserver

Visit your GovReady-Q site in your web browser at: http://localhost:8000/

.. code:: bash

   # Run the server to listen at a different specific host and port
   # python manage.py runserver host:port
   python3 manage.py runserver 0.0.0.0:8000
   python3 manage.py runserver 67.205.167.168:8000
   python3 manage.py runserver example.com:8000

**Stopping GovReady-Q**

Press ``Ctrl-C`` in the terminal window running GovReady-Q to stop the server.

7. Running GovReady-Q with Gunicorn HTTP WSGI
---------------------------------------------

In this step, you will configure your deployment to use a higher performing, multi-threaded gunicorn (Green Unicorn) HTTP WSGI server
instead of GovReady-Q using Django's built-in server. This will serve you pages faster, with greater scalability.
You will start gunicorn server using a config file which has settings to start GovReady-Q.

8. Monitoring GovReady-Q with Supervisor
----------------------------------------

In this step, you will configure your deployment to use Supervisor to monitor and restart Gunicorn automatically if GovReady-Q
should unexpectedly crash.

9. Using NGINX as a reverse proxy
---------------------------------

In this step, you will configure your deployment to use NGINX as a reverse proxy in front of Gunicorn as an extra layer of performance and security.

10. Additional options
----------------------

Installing GovReady-Q Server command-by-command
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For situations in which more granular control over the install process is required, use the commands below to install GovReady-Q.

.. code:: bash

   # Clone GovReady-Q
   git clone https://github.com/govready/govready-q
   cd govready-q

   # Install Python 3 packages
   pip3 install --user -r requirements.txt

   # Install Bootstrap and other vendor resources locally
   ./fetch-vendor-resources.sh

   # Initialize the database by running database migrations (sqlite3 database used by default)
   python3 manage.py migrate

   # Load a few critical modules
   python3 manage.py load_modules

   # Create superuser with initial account interactively with prompts
   python3 manage.py first_run
   # Reply to prompts interactively

   # Alternatively, create superuser with initial account non-interactively
   # python3 manage.py first_run --non-interactive
   # Find superuser name and password in output log

.. note::
   The command ``python3 manage.py first_run`` creates the Superuser interactively allowing you to specify username and password.

   The command ``python3 manage.py first_run --non-interactive`` creates the Superuser automatically for installs where you do
   not have access to interactive access to the command line. The auto-generated username and password will be output (only once) to
   to the stdout log.


Enabling PDF export
~~~~~~~~~~~~~~~~~~~

To activate PDF and thumbnail generation, add ``gr-pdf-generator`` and
``gr-img-generator`` environment variables to your
``local/environment.json`` configuration file:

.. code:: text

   {
      ...
      "gr-pdf-generator": "wkhtmltopdf",
      "gr-img-generator": "wkhtmltopdf",
      ...
   }

Deployment utilities
~~~~~~~~~~~~~~~~~~~~

GovReady-Q can be optionally deployed with NGINX and Supervisor. There's also a script for updating GovReady-Q.

Sample ``nginx.conf``, ``supervisor.conf``, and ``update.sh`` files can
be found in the source code directory ``deployment/ubuntu``.

Notes
~~~~~

Instructions applicable for RHEL 8 and CentOS 8 and tested on a `CentOS 8.1.1911 Docker image <https://hub.docker.com/_/centos>`__.
