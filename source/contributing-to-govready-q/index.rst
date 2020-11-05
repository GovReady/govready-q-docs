.. Copyright (C) 2020 GovReady PBC

.. _Contributing to Govready-Q:

Contributing to GovReady-Q
==========================

.. meta::
  :description: As an open source platform, you can contribute to GovReady-Q.

As an open source platform, you can contribute to GovReady-Q by submitting bug reports, suggestions, documentation improvements, and source code.  The GovReady-Q source code and GovReady-Q Dcumentation source files are hosted on GitHub.

You can file issues with the application by clicking "New Issue" on the `GovReady-Q issue tracker <https://github.com/GovReady/govready-q/issues>`__, or you can create your own forked repository by clicking "Fork" on the main `GovReady-Q source code repository <https://github.com/GovReady/govready-q>`__.  You can make changes in your forked repository, and then submit a pull request back to the main repository.

For documentation, you can file issues on the `GovReady-Q Documentation issue tracker <https://github.com/GovReady/govready-q-docs/issues>`__, or you can create your own forked repository by clicking "Fork" on the `GovReady-Q Documentation repository <https://github.com/GovReady/govready-q-docs>`__.  You can make changes in your forked repository, and then submit a pull request back to the main repository.

We suggest that you read this Contributing guide, the project’s
`README <https://github.com/GovReady/govready-q/blob/master/README.md>`__
 and its `LICENCE <https://github.com/GovReady/govready-q/blob/master/LICENSE.md>`__.

Policies
--------

*TODO* - insert references to any policies that the project follows.
E.g., a code of conduct.

Public domain *P2*
------------------

*TODO* - simple reiteration of licensing

Team principles
---------------

Release engineering *P2*
~~~~~~~~~~~~~~~~~~~~~~~~

*TODO* What RE principles do we adhere to?

Application development *P1*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

*TODO* E.g., do we follow the 12 Factor app pattern? Any other
architectural principles that all developers need to follow?

Story lifecycle *P1*
--------------------

*TODO* - describe story lifecycle. We may not want this section here
unless the stories are tracked publically.

Definition of “done” *P1*
~~~~~~~~~~~~~~~~~~~~~~~~~

*TODO*

Column exit criteria *P1*
~~~~~~~~~~~~~~~~~~~~~~~~~

*TODO* again, if the story lifecycle is private, maybe we don’t want
this section.

Development practices
---------------------

Pairing
~~~~~~~

*TODO*

Flow
~~~~

Create a private branch, typically based off the *master* branch for
your work.

Create a pull request to request that your work be reviewed and then
merged from your branch to the *master* branch.

We recommend that you push your private branch to the GitHub repo and
create pull requests early (use the *draft* feature in Github to label
your pull request as not yet ready to merge) so that other developers
can see what you are up to and offer comments.

When your pull request is ready to be reviewed and merged, remove the
draft attribute and *TODO* ping that you need a reviewer? assign a
reviewer?

*TODO* how is a reviewer selected?

Pull requests *P1*
~~~~~~~~~~~~~~~~~~

Any developer on the team can review any PR.

What should you do when you review a PR?

-  Review the code for quality and consistency
-  Call out any breaking changes
-  Assert the **Definition of Done** (above) is met

   -  Tests are written and running in CI
   -  Documentation is written, if applicable
   -  Code is in a deployable state

*TODO* once approved, only *maintainers* (Greg, Peter, and Josh) can
merge pull requests to master. How does that workflow get kicked off?

*TODO* are there any CI checks run against PR branches?

Automated tests *P1*
~~~~~~~~~~~~~~~~~~~~

*TODO*

Pre-Commit hooks *P2*
~~~~~~~~~~~~~~~~~~~~~

*TODO* we should enforce standard pre-commit hooks (clean line endings)
and add at least *black* for formatting.

*TODO* for extra credit, maybe automatically invoke pip-compile when the
requirements.in file changes?

Release practices *P2*
----------------------

*TODO*

Security practices
------------------

Use of branch protection *P1*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Branch protection affords the following advantages:

-  branch commit history cannot be rewritten
-  pushes to significant branches can be restricted to a subset of
   maintainers
-  merges to protected branches can require a status check (e.g.,
   passing tests, pull request reviews, etc.)

Branch protection is enabled on the *master* branch.

*TODO* is it enabled on any other branches?

Static vulnerabilty detection *P2*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

*TODO* CircleCI pipeline runs *safety*

Static code analysis *P2*
~~~~~~~~~~~~~~~~~~~~~~~~~

*TODO* CircleCI pipeline runs bandit for a small number of tests

Other team practices
--------------------

Onboarding *P1*
~~~~~~~~~~~~~~~

Use this text to create an issue to onboard a new contributor.

-  ☐ Developer has read this document
-  ☐ Add developer as contributor to GitHub repo
   https://github.com/govready/govready-q

*TODO* probably more steps!

CI/CD *P2*
----------

*TODO*
 
