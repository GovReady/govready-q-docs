.. Copyright (C) 2020 GovReady PBC

.. _Database Support:

Database Support
================

.. meta::
  :description: These pages describe GovReady-Q' Database Support.

The supported database types are:

 - PostgreSQL
 - MySQL/MariaDB.


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




Connect webapp server to the database
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Next, let your development installation of GovReady-Q know that you want
to use the custom branding package.

    - Development: :ref:`Keys and Description`
    - Production:  :ref:`Production Deployment`.  Keep in mind it will direct you to the deployments repo for further instructions on how to set it for your deployment method of choice.

.. note::
   You may configure the database to use ssl certificates.  An example connection string would look like:

   - ``postgresql://govready_q@dbserver.example.com/govready_q?sslmode=verify-full&sslrootcert=/usr/src/app/govready-q/pgsql.crt"``
