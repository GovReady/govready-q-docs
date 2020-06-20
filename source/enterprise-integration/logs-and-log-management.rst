.. Copyright (C) 2020 GovReady PBC

Logs and Log Management
=======================

.. meta::
  :description: Description of GovReady-Q Application Logs.

GovReady-Q logging depends upon the configuration of your system.

* GovReady-Q application logs
* Default Django logs
* Gunicorn (or other WSGI) logs
* NGINX (or other reverse proxy) logs

GovReady-Q Application Logs
---------------------------

As of version 0.9.1.21, GovReady-Q Application logs in a hybrid format with the datetime,
source and level information space delimited and the log message in a JSON object format.
The end goal is to use JSON object format as the default format for the entire log message.
The following events are currently logged in this hybrid format.

**"update_permissions portfolio assign_owner_permissions"**

Assign portfolio owner permissions.

.. code:: text

    (pending example)

**"update_permissions portfolio remove_owner_permissions"**

Remove portfolio owner permissions.

.. code:: text

    (pending example)

**"portfolio_list"**

View list of portfolios.

.. code:: text

    2020-06-03 23:53:19,274 siteapp.views level INFO {"user": {"id": 7, "username": "me"}, "event": "portfolio_list"}

**"new_portfolio"**

Create new portfolio.

.. code:: text

    2020-06-03 23:53:20,647 siteapp.views level INFO {"object": {"object": "portfolio", "id": 5, "title": "Security Projects"}, "user": {"id": 7, "username": "me"}, "event": "new_portfolio"}

**"new_portfolio assign_owner_permissions"**

Assign portfolio owner permissions of newly created portfolio to creator.

.. code:: text

    2020-06-03 23:53:20,663 siteapp.views level INFO {"object": {"object": "portfolio", "id": 5, "title": "Security Projects"}, "receiving_user": {"id": 7, "username": "me"}, "user": {"id": 7, "username": "me"}, "event": "new_portfolio assign_owner_permissions"}


**"send_invitation portfolio assign_edit_permissions"**

Assign portfolio edit permissions to a user.

.. code:: text

    2020-06-03 23:53:34,435 siteapp.views level INFO {"object": {"object": "portfolio", "id": 13, "title": "me"}, "receiving_user": {"id": 21, "username": "me2"}, "user": {"id": 20, "username": "me"}, "event": "send_invitation portfolio assign_edit_permissions"}

**"send_invitation project assign_edit_permissions"**

Assign edit permissions to a project when inviting a user to a project.

.. code:: text

    (pending example)

**"send_invitation system assign_edit_permissions"**

Assign edit permissions to a project's associated system when inviting a user to a project.

.. code:: text

    (pending example)

**"send_invitation element assign_edit_permissions"**

Assign edit permissions to a project's associated system's root_element when inviting a user to a project.
.. code:: text

    (pending example)

**"cancel_invitation"**

Cancel invitation to a project.

.. code:: text

    (pending example)


**"accept_invitation"**

Accept invitation to a project.

.. code:: text

    2020-06-17 23:20:29,782 siteapp.views level INFO {"object": {"object": "invitation", "id": 13, "to_email": "user2@gmail.com"}, "user": {"id": 15, "username": "User2"}, "event": "accept_invitation"}

**"accept_invitation project assign_edit_permissions"**

.. code:: text

    2020-06-17 23:20:29,752 siteapp.views level INFO {"object": {"object": "project", "id": 85, "title": "Awesome System"}, "sending_user": {"id": 14, "username": "User1"}, "user": {"id": 15, "username": "User2"}, "event": "accept_invitation project assign_edit_permissions"}

**"accept_invitation system assign_edit_permissions"**

.. code:: text

    2020-06-17 23:20:29,763 siteapp.views level INFO {"object": {"object": "system", "id": 183, "name": "Awesome System"}, "sending_user": {"id": 14, "username": "User1"}, "user": {"id": 15, "username": "User2"}, "event": "accept_invitation system assign_edit_permissions"}

**"accept_invitation element assign_edit_permissions"**

.. code:: text

    2020-06-17 23:20:29,772 siteapp.views level INFO {"object": {"object": "element", "id": 183, "name": "Awesome System"}, "sending_user": {"id": 14, "username": "User1"}, "user": {"id": 15, "username": "User2"}, "event": "accept_invitation element assign_edit_permissions"}

**"sso_logout"**

Single Sign On logout.

.. code:: text

    (pending example)

**"project_list"**

View list of projects.

.. code:: text

    2020-06-03 23:53:25,902 siteapp.views level INFO {"user": {"id": 14, "username": "portfolio_user"}, "event": "project_list"}

**"start_app"**

Start a questionnaire/compliance app.

.. code:: text

    2020-06-03 23:53:49,721 siteapp.views level INFO {"object": {"task": "project", "id": 23, "title": "My Project Name"}, "user": {"id": 28, "username": "me"}, "event": "start_app"}

**"new_project"**

Create a new project (e.g., questionnaire/compliance app that is a project).

.. code:: text

    2020-06-03 23:53:49,721 siteapp.views level INFO {"object": {"object": "project", "id": 16, "title": "My Project Name"}, "user": {"id": 28, "username": "me"}, "event": "new_project"}

**"new_element new_system"**

Create a new element (e.g., system component) that represents a new system.

.. code:: text

    2020-06-03 23:53:49,722 siteapp.views level INFO {"object": {"object": "element", "id": 3, "name": "My Project Name"}, "user": {"id": 28, "username": "me"}, "event": "new_element new_system"}

    2020-06-03 23:54:07,816 siteapp.views level INFO {"object": {"object": "invitation", "id": 3, "to_email": "user2@example.com"}, "user": {"id": 29, "username": "me2"}, "event": "accept_invitation"}

**"new_element new_system assign_owner_permissions"**

Assign ownership permission to a newly created element for a project's newly created associated system.

.. code:: text

    (pending example)

**"new_system assign_owner_permissions"**

Assign owernship permission to a project's newly created associated system.

.. code:: text

    (pending example)

**"assign_baseline"**

Assign a baseline set of controls to a project system (technically, assign the baseline set of controls to a system.root_element).

.. code:: text

    2020-06-03 23:53:49,721 controls.views level INFO {"object": {"object": "system", "id": 16, "title": "My Project Name"}, baseline={"catalog_key": "NIST_SP-800-53_rev4", "baseline_name": "low"}, "user": {"id": 28, "username": "me"}, "event": "assign_baseline"}
