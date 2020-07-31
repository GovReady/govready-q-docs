.. Copyright (C) 2020 GovReady PBC

.. _Setting Up a Database for Production Workloads:

Setting Up a Database for Production Workloads
==============================================

The preferred production database for Q is PostgreSQL, but MySQL/MariaDB
is also supported.

SQLite is supported in development environments, but it is not recommended for
production use.

Character Encoding
------------------

For proper operation, ensure that databases created for GovReady use UTF-8 encoding.

For convenience, we summarize database settings here.  Consult your database documentation to ensure you properly set up your database with UTF-8 encoding.

**PostgreSQL**

   .. code-block:: sql

      CREATE DATABASE "govready_q"
      ENCODING 'UTF8'
      LC_COLLATE = 'en_US.UTF-8' LC_CTYPE = 'en_US.UTF-8';

**MySQL/MariaDB**

   .. code-block:: sql

      CREATE DATABASE govready_q
      CHARACTER SET utf8mb4
      COLLATE utf8mb4_0900_ai_ci;

Setting Up Postgres
-------------------

These instructions assume a separate database server and webapp server.

On the database server
~~~~~~~~~~~~~~~~~~~~~~

On the database server, install Postgres. If using RHEL, CentOS, or
similar:

::

   yum install postgresql-server postgresql-contrib
   postgresql-setup initdb

In ``/var/lib/pgsql/data/postgresql.conf``, enable TLS connections by
changing the ``ssl`` option to

::

   ssl = on 

and enable remote connections by binding to all interfaces:

::

   listen_addresses = '*'

Enable remote connections to the database *only* from the webapp server
and *only* encrypted with TLS by editing
``/var/lib/pgsql/data/pg_hba.conf`` and adding the line (replacing the
hostname with the hostname of the Q webapp server):

::

   hostssl all all webserver.example.com md5

Generate a self-signed certificate (replace ``db.govready-q.internal``
with the database server’s hostname if possible):

::

   openssl req -new -newkey rsa:2048 -days 365 -nodes -x509 -keyout /var/lib/pgsql/data/server.key -out /var/lib/pgsql/data/server.crt -subj '/CN=db.govready-q.internal'
   chmod 600 /var/lib/pgsql/data/server.{key,crt}
   chown postgres.postgres /var/lib/pgsql/data/server.{key,crt}

Copy the certificate to the webapp server so that the webapp server can
make trusted connections to the database server:

::

   cat /var/lib/pgsql/data/server.crt
   # place on webapp server at /home/govready-q/pgsql.crt

Then restart the database:

::

   service postgresql restart

Then set up the user and database (both named ``govready_q``):

::

   sudo -iu postgres createuser -P govready_q
   # paste a long random password

   sudo -iu postgres createdb --encoding UTF8 --lc-collate 'en_US.UTF-8' --lc-ctype 'en_US.UTF-8' govready_q

Postgres’s default permissions automatically grant users access to a
database of the same name.

And if necessary, open the Postgres port:

::

   firewall-cmd --zone=public --add-port=5432/tcp --permanent
   firewall-cmd --reload

On the webapp server
~~~~~~~~~~~~~~~~~~~~

On the webapp server, now check that secure connections can be made:

::

   psql "postgresql://govready_q@dbserver.example.com/govready_q?sslmode=verify-full&sslrootcert=/home/govready-q/pgsql.crt"

(It should fail if the TLS certificate file is not provided, if sslmode
is set to ``disable``, if a different user or database is given, or if
the wrong password is given.)

Then in our GovReady-Q ``local/environment.json`` file, configure the
database (replace ``THEPASSWORDHERE``) by setting the following key:

::

       "db": "postgresql://govready_q:THEPASSWORDHERE@dbserver.example.com/govready_q?sslmode=verify-full&sslrootcert=/home/govready-q/pgsql.crt",

Then initialize the database content:

::

   python3 manage.py migrate
   python3 manage.py load_modules

And generate static files:

::

   python3 manage.py collectstatic
