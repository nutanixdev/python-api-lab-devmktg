---------
Lab Setup
---------

Overview
++++++++

**Estimated time to complete: 60-90 MINUTES**

The Nutanix Python API Lab will cover a few key points.

- Creation of a simple Python Flask web application.
- Creation of a single basic view to display Prism Central environment details for the user.
- A backend model to talk to the Nutanix Prism Central v3 REST APIs.
- JavaScript to create the interface between the front- and back-end parts of the application.

Lab Setup
+++++++++

For this lab your laptop or workstation will need to be configured with Python Flask and all required dependencies.  In an upcoming section, you will be guided through creating an easy installation method to satisfy all dependencies.

Project Location
................

You can store your project files anywhere you like.  To keep things consistent, we will use a folder named `python-lab`.  This folder will be referred to as the **lab root** throughout this lab.

- Create a folder named `python-lab`, making sure you have write permissions to that folder.  If you are using the command line, some examples for creating the folder are as follows:

  .. figure:: images/linux_logo_32x32.png
  .. figure:: images/osx_logo_32x32.png

  .. code-block:: bash

     cd ~
     mkdir python-lab
     cd python-lab

  .. figure:: images/windows_logo_32x32.png

  .. code-block:: bash

     cd %HOMEPATH%
     mkdir python-lab
     cd python-lab

Please ensure you have a terminal (Linux/Mac) or command prompt (Windows) open and have changed to the above directory before continuing.

Virtual Environments
....................

It is strongly recommended that your Python development is done inside a Python virtual environment.  Developing in a virtual environment can help replicate and test things like Python setup scripts and ensure all dependencies are met before running a Python script or application.  When installing dependencies, it is also beneficial to know a previously tested and verified dependency version has been installed.

- Even though Python virtual environments (venv) are now included with Python 3, we'll use the following command to make sure they work.  Please run these commands from the directory your project will be stored in.

  .. figure:: images/linux_logo_32x32.png
  .. figure:: images/osx_logo_32x32.png

  .. code-block:: bash

     python3.8 -m venv nutanix
     . nutanix/bin/activate

  .. figure:: images/windows_logo_32x32.png

  .. code-block:: bash

     python.exe -m venv nutanix
     nutanix\Scripts\activate.bat

  .. note::

     Windows systems: As at April 2021, a **default** installation of Python 3.8 will be installed in the following folder:

     .. code-block:: bash

        C:\Users\<username>\AppData\Local\Programs\Python\Python38

  Running these commands to setup and activate a new virtual environment will look similar to the following screenshot.

  .. note::

     The `(nutanix)` designation indicates we are now developing inside the new virtual environment.

  .. figure:: images/venv_activated_linux.png
  .. figure:: images/venv_activated_windows.png

  If you need to leave the virtual environment, use the following command:

  .. figure:: images/linux_logo_32x32.png
  .. figure:: images/osx_logo_32x32.png

  .. code-block:: bash

     deactivate

  .. figure:: images/windows_logo_32x32.png

  .. code-block:: bash

     nutanix\Scripts\deactivate.bat

  .. note::

     Even though the commands above run .bat files, PowerShell .ps1 scripts are included, too.
     If you prefer to use PowerShell, replace `activate.bat` with `Activate.ps1`.
     To deactivate, simply enter `deactivate`.  There is no `Deactivate.ps1` as a script is created in memory for this purpose.

  If at any stage you wish to delete the virtual environment, simply delete the `nutanix` virtual environment directory and all its contents.

  .. note::

     For more information on virtual environments, please see the official Python virtual environment `documentation <https://docs.python.org/3/tutorial/venv.html>`_.

App Dependencies
................

Our application will require additional Python modules beyond those that are included in the `Python Standard Library <https://docs.python.org/3.8/library/>`_.

The easiest way to ensure these are available is by using a Python Setup Script, typically named `setup.py`, or by using a specially-formatted file typically named `requirements.txt`.

In our case, we will use the `requirements.txt` method.  This will ensure our dependencies are met, while also installing versions that are known to be compatible with our dashboard app.

- Make sure you have created and activated a virtual environment, as outlined above.  For this lab, your virtual environment should be called `nutanix`.
- Create a plain text file in the project folder named `requirements.txt`.

  This requirements file will handle the installation of dependencies required for this Python Flask application:

  .. code-block:: python

     flask==1.1.2
     flask_assets==2.0
     flask-wtf==0.14.3
     jsmin==2.2.2
     cssmin==0.2.0
     requests==2.25.1
     urllib3==1.26.4
     wtforms==2.3.3
     python-dotenv==0.17.0
     black==20.8b1

- Now, to ensure our dependencies are available, let's install the dependencies for the first time.  This process will now make use of our `requirements.txt` dependencies file.

  .. figure:: images/linux_logo_32x32.png
  .. figure:: images/osx_logo_32x32.png

  .. code-block:: bash

     pip3 install -r requirements.txt

  .. figure:: images/windows_logo_32x32.png

  .. code-block:: bash

     <python_install_folder>\Scripts\pip3.8.exe install -r requirements.txt

  If all dependencies have been found and installed correctly, the end of the output will look something like this.

  .. note::

     Note that if you are installing packages you don't have, the messages regarding installing from cache may be slightly different.

  .. figure:: images/dependencies_installed.png

  Our environment is now setup to run our Python Flask application using Nutanix Prism Central REST APIs.
