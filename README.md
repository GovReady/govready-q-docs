GovReady-Q Documentation
========================

This project's documentation is hosted at:

[http://govready-q.readthedocs.io](http://govready-q.readthedocs.io)

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

