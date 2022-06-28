.. Copyright (C) 2020 GovReady PBC

.. _Production Deployment:

Production Deployment
=====================

.. meta::
  :description: This document will guide you through the GovReady-Q production installation process.

Automated production deployments of GovReady-Q can be accomplished using the separate repository `govready-deployments <https://github.com/GovReady/govready-deployments>`__.

The `govready-deployments <https://github.com/GovReady/govready-deployments>`__ repo provides a runner and Python module skeletons for different automated deployments.  Currently, only the *docker-compose* is functions fully.

1. Deployment Code Structure
----------------------------

Deployments are Python modules that reside in `deployments`.  Each deployment module has the following structure:

::

   .
   └── https://github.com/GovReady/govready-deployments
       └── deployments
           ├── docker_compose
           │   └── deploy.py
           │   └── init.py
           │   └── undeploy.py
           │   └── config-validator.json
           ├── aws
           │   └── deploy.py
           │   └── init.py
           │   └── undeploy.py
           │   └── config-validator.json
           ...


The deployment runner commands are:

- `deploy.py` - The script that deploys the stack
- `undeploy.py` - The script that removes the stack
- `init.py` - The script that allows overriding of the `configuration.json` values
- `config-validator.json` - JSON config that validates the user provided configuration.


2. Requirements:
----------------
- Server with Internet access

3. Install:
-----------
- Docker installed on Server (https://docs.docker.com/engine/installation/)
- Docker Compose installed on Server (https://docs.docker.com/compose/install/)
- Python (3+) installed on server (https://www.python.org/downloads/)
- Git installed on server (https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)

Make sure Docker is running before continuing.

The basic steps of deploying GovReady-Q on a remote Ubuntu 20.04 server running at the 'govready.example.com' are provided below.
Adjust these steps appropriately for your server and environment.

4. Clone govready-deployments
-----------------------------
Clone the `govready-deployments <https://github.com/GovReady/govready-deployments>`__ repo.

.. code-block:: bash

    ssh root@govready.example.com
    cd <your/preferred/install/directory>
    git clone https://github.com/GovReady/govready-deployments
    cd govready-deployments

.. note::
    You may install/move the 'govready-deployments` to any desired location on your server.

5. Deployment Init
------------------
For a deployment to run, it must have it's `config-validator.json` satisfied.

To satisfy those requirements you can:

- Set Environment variables that match the keys from the `config-validator.json`
- Set values in the configuration file created via the `init`.

Example:

.. code-block:: bash

    # cd <your/preferred/install/directory>/govready-deployments
    # Builds configuration.json based on the config-validator.json and skips the prompt
    python3 run.py init --type docker_compose

.. note::
   If both the configuration file and the environment variables are set, the configuration file takes precedence.


6. Configure
------------
Edit the required paramenters in the generated configuration.json file. The below example shows common settings.

    .. code-block:: json

        {
            "ADMINS": [{"username":"admin", "email":"admin@example.com", "password":"SecretPassword"}],
            "ALLOWED_HOSTS": "",
            "BRANDING": "",
            "DATABASE_CONNECTION_STRING": "",
            "EMAIL_DOMAIN": "",
            "EMAIL_HOST": "",
            "EMAIL_PORT": "",
            "EMAIL_PW": "",
            "EMAIL_USER": "",
            "GIT_URL": "",
            "GR_IMG_GENERATOR": "",
            "GR_PDF_GENERATOR": "",
            "HOST_ADDRESS": "govready.example.com",
            "HOST_PORT_HTTP": "80",
            "HOST_PORT_HTTPS": "443",
            "MAILGUN_API_KEY": "",
            "MOUNT_FOLDER": "",
            "NGINX_CERT": "",
            "NGINX_KEY": "",
            "OIDC": "",
            "OKTA": "",
            "PERSIST_STACK": false,
            "PROXY_AUTHENTICATION_EMAIL_HEADER": "",
            "PROXY_AUTHENTICATION_USER_HEADER": "",
            "SECRET_KEY": "gl$3y#j-2vsm)-!4-)!_cj8$^6h^y9(@+&p0n%vig-po7u)tb5",
            "VERSION": ""
        }

Keys and Description - environment.json
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. list-table:: Title
   :header-rows: 1

   * - Key
     - Data Type
     - Default
     - Description
   * - ``ADMINS``
     - Object[]
     - []
     - Used to configure a display point of contact “Administrator” on site and unrelated to the configuration of actual administrators configured in the database Ex: ``[{"username": "username", "email":"first.last@example.com", "password": "REPLACEME"}]``. Will auto-create an admin, you need to find it in the logs docker-compose logs.
   * - ``ALLOWED_HOSTS``
     - Object[]
     - []
     - GovReady-Q's approved list of host names provided as an array. If not provided, will default to HOST_ADDRESS. Example: ``["localhost", "182.34.56.3"]``.
   * - ``BRANDING``
     - string
     - ""
     - Full file path to GovReady-Q branding directory on Host :ref:`custom branding<Applying Custom Organization Branding>`. GovReady default branding will be used.
   * - ``DATABASE_CONNECTION_STRING``
     - string
     - "postgres://postgres:PASSWORD@postgres_dev:5432/govready_q"
     - If supplied, this is the DB connection used. See :ref:`Database Support`. Default will create a Postgres server in the docker-compose deployment for you. It will not have snapshots.
   * - ``EMAIL_DOMAIN``
     - string
     - ""
     - The email domain for interacting with a mail server.
   * - ``EMAIL_HOST``
     - string
     - ""
     - The email domain for interacting with a mail server.
   * - ``EMAIL_DOMAIN``
     - string
     - ""
     - The email domain for interacting with a mail server.
   * - ``EMAIL_PORT``
     - string
     - ""
     - The email port for interacting with a mail server.
   * - ``EMAIL_PW``
     - string
     - ""
     - The email user password for interacting with a mail server.
   * - ``EMAIL_USER``
     - string
     - ""
     - The email user for interacting with a mail server.
   * - ``GIT_URL``
     - string
     - "http://github.com/GovReady/govready-q"
     - The git url for for retrieving the GovReady-Q repository to deploy.
   * - ``GR_IMG_GENERATOR``
     - string
     - Disabled
     - Image generator binary name. Default is to disable this feature.
   * - ``GR_PDF_GENERATOR``
     - string
     - Disabled
     - PDF generator binary name. Default is to disable this feature.
   * - ``HOST_ADDRESS``
     - string
     - "govready.example.com"
     - GovReady-Q's public address as would be entered in a web browser.
   * - ``HOST_PORT_HTTP``
     - string
     - "80"
     - GovReady-Q's public address HTTP port; defaults to 80.
   * - ``HOST_PORT_HTTPS``
     - string
     - "443"
     - GovReady-Q's public address HTTPS port; defaults to 443.
   * - ``MAILGUN_API_KEY``
     - string
     - Disabled
     - Mailgun API key to send emails if set.
   * - ``MOUNT_FOLDER``
     - string
     - Current directoy
     - Mount folder to put artifacts, logs, etc.
   * - ``NGINX_CERT``
     - string
     - ""
     - Full file path to Nginx cert.pem on Host server to copy into NGINX container.
   * - ``NGINX_KEY``
     - string
     - ""
     - Full file path to Nginx key.pem on Host server to copy into NGINX container.
   * - ``OIDC``
     - string
     - Disabled
     - OIDC configuration object.
   * - ``OKTA``
     - string
     - Disabled
     - OIDC OKTA configuration object.
   * - ``PERSIST_STACK``
     - string
     - false
     - Persist stack between runs.
   * - ``PROXY_AUTHENTICATION_EMAIL_HEADER``
     - string
     - Disabled
     - Proxy Authentication User header.    
   * - ``PROXY_AUTHENTICATION_USER_HEADER``
     - string
     - Disabled
     - Proxy Authentication Email header.
   * - ``SECRET_KEY``
     - string
     - "gl$3y#j-2vsm)-!4-)!_cj8$^6h^y9(@+&p0n%vig-po7u)tb5"
     - Django Secret.
   * - ``VERSION``
     - string
     - "main"
     - GovReady-Q git branch version/tag to deploy.  

.. note::
   These parameters can be set in the configuration.json file or as environmental parameters. For a complete list of configuration settings, visit:

   https://github.com/GovReady/govready-deployments/blob/main/deployments/docker_compose/README.md.

7. Deploy
---------
Deploy:

+-------------------------+-------------------------------------------------------+
| **Arguments & Flags**   | **Description**                                       |
+-------------------------+-------------------------------------------------------+
|`--config <config-file>` | JSON formatted file required to deploy                |
+-------------------------+-------------------------------------------------------+
|`--type <type>`          | (Optional) Skip prompt and provide deployment type    |
+-------------------------+-------------------------------------------------------+

Example:

.. code-block:: bash

    # Deploys using `configuration.json` using the `docker_compose` deployment solution
    python3 run.py deploy --type docker_compose --config configuration.json



8. (Optional) Set up SSL from Let's Encrypt
-------------------------------------------
The default deployment will create a self-signed SSL certificate. You can optionally install a valid SSL Certificate from Let's Encrypt if your server is reachable from the public Internet. (Follow these steps each time you deploy.)

.. code-block:: bash

    # exec into nginx docker docker
    exec -it govready-q_nginx_1 /bin/sh
    # install certbot
    apk add certbot certbot-nginx
    # run certbot specifying your domain and respond to prompts
    certbot --nginx -d govready.example.com

.. note::
   To install your own certificates, specify the path on the Host server to your certificates in the configuration.json file so that your certificates will be copied into the NGINX container and used.


9. Remove Deployment
--------------------

Tears down specified deployment.

+-------------------------+-------------------------------------------------------+
| **Arguments & Flags**   | **Description**                                       |
+-------------------------+-------------------------------------------------------+
|`--type <type>`          | (Optional) Skip prompt and provide deployment type    |
+-------------------------+-------------------------------------------------------+

Example:

.. code-block:: bash

    # Removes deployment using the `docker_compose` deployment solution
    python run.py undeploy --type docker_compose
