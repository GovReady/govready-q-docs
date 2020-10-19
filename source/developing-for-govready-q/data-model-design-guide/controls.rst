.. Copyright (C) 2020 GovReady PBC

.. _Controls:

Controls
===========

The diagram below provides a summary representation of GovReady-Q’s
Django ``controls`` data model that handles controls, components, and combined statements.

.. figure:: /assets/Controls_Data_Model.png
   :alt: Controls data model (not all tables represented)

   Controls data model (not all tables represented)

A single control can be instantiated and associated to any number of components. A discussion can have multiple comments. Comments can
have multiple attachments. Users can be associated with any discussion as a guest via their discussion id.
