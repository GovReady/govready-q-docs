.. Copyright (C) 2020 GovReady PBC

.. _Enterprise Single Sign-On (SSO):

Enterprise Single Sign-On (SSO)
-------------------------------

.. _Okta OpenID Connect:

Okta OpenID Connect
~~~~~~~~~~~~~~~~~~~~~~~~~~~

GovReady-Q can be configured to use Okta OpenID for authentication and authorization.

::


   {
        "domain": "https://XXX.okta.com",
        "client_id": "MyClientId",
        "client_secret": "MyClientSecret",
        "roles_map": {
            "admin": "GroupNameThatMapsToAdminUser",
            "normal": "GroupNameThatMapsToRegularUser"
        },
        "claims_map": {
            "email": "email",
            "groups": "groups",
            "first_name": "given_name",
            "last_name": "family_name",
            "username": "preferred_username"
        }
    }

+---------------+-------------------------------------------------------------------------------+
| Key           | Description                                                                   |
+---------------+-------------------------------------------------------------------------------+
| domain        | Okta domain for your application                                              |
+---------------+-------------------------------------------------------------------------------+
| client_id     | Okta app's client id                                                          |
+---------------+-------------------------------------------------------------------------------+
| client_secret | Okta app's client secret                                                      |
+---------------+-------------------------------------------------------------------------------+
| roles_map     | This map is meant to map Okta User Groups to the application groups.          |
+---------------+-------------------------------------------------------------------------------+
| claims_map    | This map is meant to map Okta Claims to the application's User model columns. |
+---------------+-------------------------------------------------------------------------------+

This object should be set under the `okta` section of the environments.json.

Note: Deployment will slightly differ by setting this under `OKTA`.

.. _Proxy Authentication Server:

Proxy Authentication Server
~~~~~~~~~~~~~~~~~~~~~~~~~~~

GovReady-Q can be deployed behind a reverse proxy that authenticates
users and passes the authenticated user’s username and email address in
HTTP headers. In this configuration:

-  The user points their browser to the reverse proxy authentication
   server.
-  The proxy authenticates users and proxies the request to GovReady-Q
   if and only if the user is authenticated and authorized to access
   GovReady-Q. The proxy passes the user’s username and email address in
   HTTP headers of the proxy’s choosing.
-  GovReady-Q will create a user account for a new user or treat the
   user as logged in as soon as the user requests a page. Therefore,
   there is no sign-up or log-in process within GovReady-Q when a proxy
   authentication server is used.
-  All other authentication methods to GovReady-Q are disabled when
   proxy authentication is enabled. Therefore you should ensure that the
   Django admin’s username matches the admin’s username as provided by
   the proxy server. Otherwise, you will lose access to the admin page.
   However, if there is a mismatch, you may disable proxy
   authentication, log in to the Django admin with your admin username
   and password, and change your admin username to match the username
   sent by the proxy server.
-  GovReady-Q must be run at a private address that cannot be accessed
   except through the proxy server.

To activate reverse proxy authentication, add the header names used by the proxy e.g.:


In Development set the ``trust-user-authentication-headers`` key following :ref:`Developer Environment`

::


   {
     "trust-user-authentication-headers": {
       "username": "X-Authenticated-User-Username",
       "email": "X-Authenticated-User-Email"
     },
   }

In Production see:  :ref:`Production Deployment`

The proxy server must be configured to proxy to GovReady-Q’s private
address. But the ``address`` settings in GovReady-Q must reflect the host and protocol used
in the URL the *end user* uses to access GovReady-Q. They do *not* need
to match the address that the proxy server uses to reach the GovReady-Q
server.

Per the `Django
Documentation <https://docs.djangoproject.com/en/dev/howto/auth-remote-user/>`__
on authentication using REMOTE_USER, you must be sure that your proxy
server always sets or strips the special username and email headers,
including headers that normalize to the same Django key (in particular
headers with underscores), from the client request and **does not permit
an end-user to submit a fake (or “spoofed”) header value**.

We have an example reverse proxy authentication server,
`simple_iam_proxy_server
<https://github.com/GovReady/govready-q/tree/master/tools/simple_iam_proxy_server>`__,
which can be used for debugging purposes.  Some users have also
reported success in using `mitmproxy <https://mitmproxy.org/>`__ for
setting HTTP headers.
