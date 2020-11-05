.. Copyright (C) 2020 GovReady PBC

.. _Discussions:
.. _filetype: https://pypi.org/project/filetype/#file-header

Discussions
===========

The diagram below provides a summary representation of GovReady-Q’s
Django ``discussion`` data model that handles discussions, comments, and
invitations.

.. figure:: /assets/Discussion_Data_Model.png
   :alt: Discussion data model (not all tables represented)

   Discussion data model (not all tables represented)

A single discussion can be instantiated and associated to any task (task
~= “question”). A discussion can have multiple comments. Comments can
have multiple attachments. Users can be associated with any discussion as a guest via their discussion id.

Attachments and Validation
------------------------------------

All uploads are limited by file extension, size and type using filetype_. The valid extensions (**content types**) are: pdf (**application/pdf**), png (**image/png**), jpg (**image/jpeg**) with an upload limit of 2.5MB.
