.. Copyright (C) 2022 GovReady PBC

.. raw:: html

   <style>

   table, tr, td {
       text-align: center;
       background-color: white;
   }

   </style>

.. _Version 0.10.x:

Version 0.10.x
===============

What’s New in 0.10.x
---------------------

Welcome to GovReady-q v0.10.0 "Aspen".

The Aspen release provides major feature and stability improvements to the GovReady-Q GRC software.

Version 0.10 Aspen contains multiple, customer-driven improvements:

* Over 150 sample components based on DOD STIGs and SRGs.

* Private components, component usage approvals, and component responsible roles.

* An integrations framework for interacting with third-party APIs including other GRC software.

* Improved questionnaire editing screens.

* Major bug fixes.

* More generous MIT open source license.

    *******************************************************************************
    * IMPORTANT! RELEASES BETWEEN v0.9.11.2 and v0.10.0 CONTAIN BREAKING CHANGES! *
    *             PLEASE READ CHANGELOGS FOR ALL VERSIONS!                        *
    *******************************************************************************

**Feature changes**

* Support private components.
* Assign responsible roles to components and appointing parties to roles.
* Integrations framework for better inclusion of information from remote services.
* Component usage approval workflow.
* Single Sign On OIDC support.
* New questionnaire authoring and editing interface.
* Over 150 sample components created from DOD STIGS.
* Add form to create system from string or URLs.

**UI changes**

* Change label 'certified statement' to 'reference statement'.
* Warning Message appears at the top of home page and login page while using an Internet Explorer browser informing the user of Internet Explorer not being supported.
* Indicate private components with lock icon.
* Edit model for component in library supports marking component private.
* Add React component UI widget for setting and editing permissions on component editing.
* Add ability to change privacy of a component is given only to the owner of the component.
* Added tabs for coponent requests.
* Only Component owner can edit user permissions.
* Display the control framework along side of controls in component control listing page.
* Remove icons from project listing.
* Add Component search filter to filter results to components owned by user.
* Add form to create system from string or URLs.
* Change language in interface to 'system, systems' instead of 'project, projects'.
* Navigate users to new system form page as starting point to creating new systems.

**Developer changes**

* Add support for OIDC SSO configuration separate from OKTA SSO configuration.
* Update Django, libraries.
* Remove debug-toolbar.
* Support for private components by adding 'private' boolean field to controls.models.Element.
* Support for hidden components by adding 'hidden' boolean field to controls.models.Element.
* Support for requiring approval components by adding 'require_approval' boolean field to controls.models.Element.
* Create new components as private and assign owner permissions to user who created the component.
* Added extensible Integrations Django appplication to support communication with third-party services via APIs, etc.
* Added initial support for DoJ's CSAM integration.
* Added ElementPermissionSerializer for component (element) permissions.
* Add tests for component creation form user interface.
* Add ElementPermissionSerializer, UpdateElementPermissionSerializer, RemoveUserPermissionFromElementSerializer for component (element) permissions.
* Add ElementWithPermissionsViewSet for component (element) permissions.
* Add more permission functions to element model: assigning a user specific permissions, removing all permissions from a user, and checking if a user is an owner of the element.
* Updated User model to include search by 'username' and exclusion functionality to queryset.
* Add model Roles, Party, and Appointments to siteapp to support identifying roles on Components (Element).
* Assign owners to components imported via OSCAL. If no user is identified during component (element creation) assign first Superuser (administrator) as component owner.
* Support navigating to specific tab on component library component page using URL hash (#) reference.
* Protype integrations System Summary page.
* Refactor and OIDC authentication for proper testing of admin and not admin roles.
* Create a new system via name given by a string in URL.
* Add a large set of sample components (150+) generated from STIGs.
* Detect Apple ARM platform (e.g. 'M1 chip') and use alternate backend Dockerfile with Chromium install commented out.
* Added SystemEvent object in controls to track system events.

**Bug fixes**

* Fix permissions for non-admin members of projects to edit control implementation statements.
* Fix User lookup to properly query search results and exclude specific users
* Resolve components not displaying the tag widget by properly setting existingTags default for new component.
* Footer fixes.
* Assign owners to default components (elements) created during install first_run script.
* Correctly display POA&M forms with left-side menu.
* Refactor and OIDC authentication for proper testing of admin and not admin roles.

v0.9.13 (January 23, 2022)
--------------------------

**UI changes**

* Add sign-in warning message to which users need to agree.
* Reduce number of Group Django messages from question actions into single message for adding actions.
* Simplify new authoring tool. Move prompt from right to left. Only show first line of question prompt.
* Display all project modules in a single group on project.html.
* Display project root_task's module summary on project page.
* Add ability to search projects.

**Bug fixes**

* Properly close CSV export modal after exporting.

**Developer changes**

* Comment out deprecated queries in SiteApp.models.Project.get_projects_with_read_priv.
* Require login to view projects list.

v0.9.11.11 (January 15, 2022)
-----------------------------

**Feature changes**

* Ability to add modules in new authoring tool.
* Allow deleting of questions, modules in new authoring tool by removing protection on foreign key references.
* Superusers can see all projects.

**UI changes**

* Simplify task progress history. Only display questions of current module. Only colorize to glyphicons.
* Enable adding component control statement from System selected component.
* Enable adding component control statement from System selected controls.
* Switch to "I want to..." language on landing page.
* Align module text left and add numbers to project page.
* Add big button back to project home page on module summary page.
* Edit AppVersion title, version, and description in new authoring tool.
* Reinstate Database Administration opening in new browser tab.
* Display pagination control btm of component page.
* Add 'Things to do' text to project.html.
* Display links to previous and next selected control on System selected control editor page.
* Fix sizing of catalog listing panel in app store to keep rows clean.

**Bug fixes**

* Stop scrunching of progress-project-area-wrapper on question page.
* Always make sure output param exists in all modules that get edited.
* Fix adding statements to components in library.
* Correctly escape carriage returns in multi-line component descriptions in edit component modal.

**Developer changes**

* Superusers can see all projects.

v0.9.11.10-dev (December 14, 2021)
----------------------------------

Introuduce new authoring tool. Remove authoring tool modal from task question page.

**Feature changes**

* Enabling batch viewing of questions for easier questionnaire authoring.
* Enable editing of artifacts.
* Enabling cloning entire templates in template library.

**Developer changes**

* Add Django `nlp` app to system to support Natural Language Processing of SSPs and statements.
* Include spaCy libraries as part of build.
* Include initial, simplified candidate entity recognition script.
* Remove full text search of statements from component library search because it was slow and returned to many results.
* Add serializers for Modules and ModuleQuestions.
* Refactor siteapp.views.project and templates/project.html to remove vestigial column vs row layout code and previous authoring tools.
* Remove authoring tool modal from task question page.

**UI changes**

* Use left side vertical React navigation menu for project.
* Improve toast message appearance by limiting width.
* Improve styling of project page rollovers make module actions clearer
* Improve styling of template library. Use bootstrap panels for each item.
* Remove authoring tool modal from task question page.

**Bug fixes**

* Fix permissions to allow non-administrator to clone project templates in project template.
* Fix crash when restoring a previous version of a statement.
* Fix setting control baseline by proper use of `update_or_create` in `System.set_security_impact_level`.

v0.9.11.6 (October 13, 2021)
----------------------------

Remove GPL3 License from repository.

**UI changes**

* Use left side vertical nav menu for project.
* Improve appearance of statement editing forms: better shading, better setting of textarea height, overall appearance.
* Remove adding component or new control from a project's control listing.

v0.9.11.5 (October 9, 2021)
---------------------------

Merge and synchronize api-tag work and supporting REACT structures from GovReady-Q-SPA into latest version GovReady-Q-Private (0.9.11.3)

**Feature changes**

* Enable REACT-based api-tags.

**Developer changes**

* Switch from `ElementRole` to `Tag` as value for dynamic actions in questions.
* Provide `root_element` information for `System` SimpleSystemSerializer to make it easier to identify systems by name.

**Data changes**

* Add `created`, `updated` fields to `controls.System` to better align with base serializer.

v0.9.11.4.2 (October 8, 2021)
-----------------------------

**UI changes**

* Fix component status and type to be set only in library rather than in systems.
* Hide impact levels, POA&M status box from project mini-dashboard until UI can be improved.
* Improve look of modules.

v0.9.11.4.1 (October 7, 2021)
-----------------------------

**Feature changes**

* Insert new questions after current question in authoring tool.

v0.9.11.3 (September 28, 2021)
------------------------------

**Feature changes**

* Add new question types `choice-from-data` and `multiple-choice-from-data` to get display choice with options created from dynamic data.
* Enable downloading of a compliance app directory.

**Developer changes**

* Add new question types `choice-from-data` and `multiple-choice-from-data` to get display choice with options created from dynamic data.
* Improve DRY-ness of module serialization.
* Enable downloading of a compliance app directory.
