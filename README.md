GovReady-Q Documentation
========================

## Overview

Welcome to the source and build files for GovReady-Q's documentation.  The git repository for these files is located at:

https://github.com/GovReady/govready-q-docs.

The rendered documentation is hosted on the web at:

[http://govready-q.readthedocs.io](http://govready-q.readthedocs.io)

The main source GovReady-Q source code repository is hosted at:

https://github.com/GovReady/govready-q.

## Building The Documentation Locally

The  source files for the documentation are stored in the `source` subdirectory of this directory. A webhook is configured at readthedocs.io to publish the master branch of [govready/govready-q](https://github.com/GovReady/govready-q) when there are new commits.

To test documentation locally, install Sphinx and the theme:

	pip install sphinx sphinx_rtd_theme sphinxcontrib-contentui

If you are on macOS and prefer to install sphinx with homebrew:

	brew install sphinx-doc
	echo 'export PATH="/usr/local/opt/sphinx-doc/bin:$PATH"' >> ~/.bash_profile
	pip install sphinx_rtd_theme sphinxcontrib-contentui

Then build the documentation:

	make html

You can then browse the documentation locally at:

	build/html/index.html

## Style Guide

### Organizing the Documentation

Following the top-level outline, separate out different sections for different audiences.  (With the understanding that one person might be a member of different audiences when they're wearing a different hat.)  A person's role -- project sponsor, project manager, auditor, software evaluator, software developer, tester, devops -- will mean that they will be looking for one section's topic, but might be overwhelmed by the topics in another section.  Strive to make the documentation usable for people in different roles.

### File Type

Documentation files should natively be in reStructured text format rather than Markdown or HTML.  Consider using `pandoc` to convert incoming documents as necessary.

### File Hierarchy and File Naming

File names for leaf nodes in any section are constructed by taking the full text of the main header, converting to lowercase, and replacing any runs of non-alphanumeric characters with a single dash.  The file extension `.rst` is added at the end.

An exception is a section with a single file that comprises the entire section.  In that case the name of the file is `index.rst`.  The directory name is constructed as above from the header in `index.rst`.

Directories are used to separate each section or sub-section into its own directory.

### Asset Directories

In the current organization, most image assets are in the top-level `assets` directory.  For historical reasons, `installation-and-setup/container-based-installation` and `user-guide` each have their own `assets` directory.

We do not currently have a preference for one top-level directory, or multiple directories local to each section.  But at some point, one or the other should be chosen and implemented.

### Internal Links

Internal links should generally be Sphinx `:ref:` statements, rather than standard reStructuredText links to sections.

In other words, use this:

```
 :ref:`Section Title`
 
 :ref:`if different link text is needed<Section Title>`
```

rather than this:

```
`Section title`_

`if different link text is needed<Section Title>`_
```

The rationale, from the [Sphinx docs about ref](https://www.sphinx-doc.org/en/master/usage/restructuredtext/roles.html#ref-role): 

> Using ref is advised over standard reStructuredText links to sections (like \`Section title\`_) because it works across files, when section headings are changed, will raise warnings if incorrect, and works for all builders that support cross-references.

Reference labels should generally be placed just before section titles, and use the same text as the title, which simplifies the reference to that section.  For instance:

```
.. _Section Title:

Section Title
```

Since labels must be unique over all files, hopefully your section title can be made specific enough to be unique over all files.  If not possible, then it's okay to adjust the label to be unique by adding a suffix or similar.

### toctrees

In `toctree` sections, use a reference to the filename rather than a label.
