.. Copyright (C) 2020 GovReady PBC

.. _Production Deployment:

Production Deployment
=====================

.. meta::
  :description: This document will guide you through the GovReady-Q production installation process.

All deployment methods can be found in the https://github.com/GovReady/govready-deployments repo.

Follow the directions for the deployment method you would like to use. The goal is to have a diversity of deployment methodologies to support everyone's needs.

.. note::
   Documentation will not be held here since the production deployment implementations are in constant flux.  Make sure
   to goto https://github.com/GovReady/govready-deployments for the lastest information.



Deployment Code Structure
~~~~~~~~~~~~~~~~~~~~~~~~~

Deployments are python modules that reside in `deployments`.  Each deployment module will have the following:

::

   .
   └── https://github.com/GovReady/govready-deployments
       └── deployments
           ├── onprem
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


- `deploy.py` - The script that deploys the stack
- `undeploy.py` - The script that removes the stack
- `init.py` - The script that allows overriding of the `configuration.json` values
- `config-validator.json` - JSON config that validates the user provided configuration.


Deployment Init
~~~~~~~~~~~~~~~

This will generate a skeleton JSON configuration file for the deployment you choose to use.  It will output `configuration.json`  to override and set values for stack.

Example:

.. code-block:: bash

    # Builds configuration.json based on the config-validator.json
    python run.py init

    # Builds configuration.json based on the config-validator.json and skips the prompt
    python run.py init --type onprem


Deploy
~~~~~~

For a deployment to run, it must have it's `config-validator.json` satisfied.

To satisfy those requirements you can:

- Set Environment variables that match the keys from the `config-validator.json`
- Set values in the configuration file created via the `init`.

.. note::
   If both the configuration file and the environment variables are set, the configuration file takes precedence.


+-------------------------+-------------------------------------------------------+
| **Arguments & Flags**   | **Description**                                       |
+-------------------------+-------------------------------------------------------+
|`--config <config-file>` | JSON formatted file required to deploy                |
+-------------------------+-------------------------------------------------------+
|`--type <type>`          | (Optional) Skip prompt and provide deployment type    |
+-------------------------+-------------------------------------------------------+

Example:

.. code-block:: bash

    # Deploys using `configuration.json` using the `onprem` deployment solution
    python run.py deploy --type onprem --config configuration.json


Remove Deployment
~~~~~~~~~~~~~~~~~

Tears down specified deployment.

+-------------------------+-------------------------------------------------------+
| **Arguments & Flags**   | **Description**                                       |
+-------------------------+-------------------------------------------------------+
|`--type <type>`          | (Optional) Skip prompt and provide deployment type    |
+-------------------------+-------------------------------------------------------+

Example:

.. code-block:: bash

    # Removes deployment using the `onprem` deployment solution
    python run.py undeploy --type onprem
