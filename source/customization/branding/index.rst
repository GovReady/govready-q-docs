.. Copyright (C) 2020, 2021 GovReady PBC

.. _Applying Custom Organization Branding:

Applying Custom Organization Branding
=====================================

The look and feel of GovReady-Q can be customized a bit by overriding
the Django templates used to construct the site’s pages and by
serving additional static assets.

Custom branding can contain static assets (such as a logo image) and
HTML template overrides. Branding is packaged into a directory with a
particular directory layout and some Python boilerplate code that allows
GovReady-Q to find the branding files. The directory is placed inside
the main GovReady-Q directory, and an application setting is used to
activate it.

Storing the branding files in a single, separate directory insures
maintaining and updating your branding files and GovReady-Q
files from the official repository are independent activities.

Creating the branding directory
-------------------------------

You’ll need a working setup of GovReady-Q to create the branding directory
and to test your changes. Make sure you have
GovReady-Q :ref:`set up for development on your
workstation <Developing for Govready-Q>`.

Custom branding is packaged inside what Django calls an
`application <https://docs.djangoproject.com/en/2.1/ref/applications/>`__,
but it is just a packaged sub-component of a website. To create a new
branding package directory, change to the directory where you have
GovReady-Q set up. Then run:

.. code:: sh

   python3 manage.py startapp branding

This command creates a new directory called ``branding`` with
Python boilerplate code to make it a valid Django "application."

Make directories for storing the custom static assets and templates:

.. code:: sh

   mkdir branding/static
   mkdir branding/templates

.. note::
   The directory with custom branding is called ``branding`` by convention,
   but you can chose any name.

   You will only need to go through the above process to initially
   create your custom branding Django application folder. Once created,
   you only need to include and activate your custom branding directory in other instances
   of GovReady-Q for your branding to be applied.

Activate the branding package
-----------------------------

Next, let your development installation of GovReady-Q know that you want
to use the custom branding package.

    - Development: :ref:`Keys and Description`
    - Production:  :ref:`Production Deployment`.  Keep in mind it will direct you to the deployments repo for further instructions on how to set it for your deployment method of choice.


Overriding templates
--------------------

Any of the templates that make up GovReady-Q’s frontend can be
overridden. The full list of templates can be browsed in GovReady-Q’s
GitHub repository at

https://github.com/GovReady/govready-q/tree/master/templates

Start by trying to override the ``navbar.html`` template, which is
inserted at the top of every page. Use your favorite text editor to
create a file at ``branding/templates/navbar.html``. Copy the
content of GovReady-Q’s stock ``navbar.html`` from
https://github.com/GovReady/govready-q/blob/master/templates/navbar.html
into it. (GitHub’s “Raw” button is handy for getting a clean version to
save or copy/paste.)

At the bottom of the file, add some custom HTML, such as:

.. code:: html

   <div>
     <b>Welcome to my organization&rsquo;s custom site!</b>
   </div>

Start GovReady-Q on your workstation (see the :ref:`development
docs <Developing for Govready-Q>`) and visit a page. You should see your
new content below the navbar at the top of every page.

Adding custom CSS
-----------------

You can also add a custom CSS stylesheet to your branded GovReady-Q by
taking the following steps:

a) Add the CSS file as a static asset.
b) Insert a ``<link rel="stylesheet" href="...">`` tag into the
   ``<head>`` section of each page’s HTML by overriding the
   ``head.html`` template.

To create the static asset, make a new file named
``branding/static/custom.css``. Let’s say you want to make the
background color of each page red. The file should contain:

.. code:: css

   body {
       background: red !important;
   }

Then override the ``head.html`` template. GovReady-Q’s base for
``head.html`` is empty — its purpose is only to allow you to add to the
``<head>`` element. So create a new file at
``branding/templates/head.html`` and put in it:

.. code:: jinja

   {% load static %}
   <link rel="stylesheet" href="{% static "custom.css" %}">

See the `Django documentation for static
files <https://docs.djangoproject.com/en/2.1/howto/static-files/>`__ for
more information about the ``static`` template tag.

Open any page in your locally running GovReady-Q and you should see that
the background color of every page has changed.


Keeping your templates up to date
---------------------------------

With each new released version of GovReady-Q, there is the possibility
that the stock templates have changed. Some changes may require you to
re-engineer your template overrides to preserve functionality.

The deeper your customization, the more you will need to look at new
releases of GovReady-Q for changes that update pages and page elements you
have customized (like a new menu item) and new pages and section to which
you may want to customize with your branding.

If you are able to implement all your branding in CSS, you will rarely
need to change your branding files.
