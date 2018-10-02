NDN Common Client Libraries API
===============================

This is the source code of NDN Common Client Libraries API, as published on
https://named-data.net/doc/ndn-ccl-api/

Please submit any bugs or issues to the NDN-CCL issue tracker:
https://redmine.named-data.net/projects/ndn-ccl/issues

Prerequisites
-------------

To "compile" documentation into html you need the Sphinx documentation package.

If you're on macOS:

    sudo easy_install pip
    sudo pip install sphinx
    sudo pip install sphinxcontrib-fulltoc

If you're on Ubuntu Linux:

    sudo apt-get install python-pip
    sudo pip install sphinx
    sudo pip install sphinxcontrib-fulltoc

Compilation
-----------

In a terminal, enter:

    make html

And a set of HTML pages will be built under ``_build/html``


If you have Latex installed, in a terminal you can also enter:

    make latexpdf

This way Sphinx will prepare a .tex file and a .pdf document under ``_build/latex``.

