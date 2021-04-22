.. title:: Nutanix Python API Lab v1.3

.. toctree::
  :maxdepth: 2
  :caption:     Required Labs
  :name: _req-labs
  :hidden:

  .. example/index
  contents/intro
  contents/setup
  contents/structure
  contents/classes
  contents/initialisation
  contents/first_run
  contents/supporting
  contents/visuals
  contents/javascript
  contents/api_request
  contents/testing
  contents/thoughts  

.. _welcome:

Welcome
#######

Welcome to Nutanix Prism REST API Getting Started Lab (Python) - v1.3.

.. _getting_started:

What We Are Doing
#################
  
The Nutanix Python API Lab will cover a couple of key points.

- Creation of a simple Python Flask web application.
- Creation of a single basic view to display Prism Central environment details for the user.
- A backend model to talk to the Nutanix Prism Central v3 REST APIs.
- JavaScript to create the interface between the front- and back-end parts of the application.

.. _requirements:

What We Aren't Doing
####################

This lab is not intended as a guide that can be used to learn Python development from scratch.  While the copy & paste steps will allow you to create a working application, previous experience with Python may aid you in understanding what each section does.

However, the lab will include links to valuable explanation and learning resources that can be used at any time for more information on each section.  For example, the general structure of this application is almost identical to the one provided by the official Python Flask tutorial and will often link to resources there.

Requirements
############

To successfully complete this lab, you will need an environment that meets the following specifications.

- Previous experience with Python may be beneficial but is not strictly mandatory.
- An installation of Python 3.8 or later.  For OS-specific information, please see the next section.
- Python `pip` for Python 3.8.
- Python Flask.  The lab will walk you through creating an easy to use installation file, allowing dependency installation on all supporting operating systems.
- The text editor of your choice.  A good suggestion is `Visual Studio Code <https://code.visualstudio.com/>`_ as it is free and supports Python development via plugin.
- cURL
- cURL (for Windows - see below).

Python 3.8 on OS X
------------------

- Install Python 3.8 on OS X by downloading the `OS X installation package <https://www.python.org/downloads/mac-osx/>`_.

Python 3.8 on Ubuntu 20.04 or later
-----------------------------------

No manual steps are required as Python 3.8 is the default version of Python bundled with Ubuntu 20.04 (at the time of writing this lab) 

Python 3.8 and cURL on Windows
------------------------------

- Install Python 3.8 by downloading the `Python 3.8 installer <https://www.python.org/downloads/release/python-380/>`_.
- Install cURL from the `cURL website <https://curl.haxx.se/windows/>`_.

Note that cURL is not required to create the demo app.  cURL command samples are provided throughout the lab and may be used for reference at any time.  This is due to its cross-platform nature vs supporting platform-specific commands (e.g. PowerShell).

**Note re Windows systems:** As at April 2021, a **default** installation of Python 3.8 will be installed in the following folder:

.. code-block:: bash

    C:\Users\<username>\AppData\Local\Programs\Python\Python38

.. _optional_components:

Optional Components
###################

In addition to the requirement components above, the following things are "nice to have".  They are not supplementary only and are not required this lab.

- A `Github <https://github.com/>`_ account.  This can be created by signing up directly through GitHub.
- The `GitHub Desktop <https://desktop.github.com/>`_ application (available for Windows and Mac only)
- `Postman <https://www.getpostman.com/>`_, one of the most popular API testing tools available.

.. _cluster_details:

Cluster Details
###############

In a presenter-led environment you will be using a shared Nutanix cluster.  Please use this cluster when carrying out your cURL and application testing.

In a self-paced environment you will need access to a Nutanix cluster along with the credentials required to access it.
