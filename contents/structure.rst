App Structure
+++++++++++++

If you read through the API Intro earlier in this lab, you'll now we have a good understanding of the history and progression of the Nutanix Prism REST APIs.  It is now time to use those APIs inside our Python Flask application.

Key Directories
...............

The key directories of our app are as follows.

- A folder called `lab`.  This folder contains our project's code and all associated files.  Please note this directory is **inside** our **lab root** i.e. **python-lab/lab**
- `nutanix/`, the folder containing our virtual environment files.

Let's get started!

- Create the `/lab` directory now.

  .. figure:: images/linux_logo_32x32.png
  .. figure:: images/osx_logo_32x32.png
  .. figure:: images/windows_logo_32x32.png

  .. code-block:: bash

     mkdir lab

We'll create these files as we go through the lab, but here is some info about what each file does.

- `lab/__init__.py`, the application's "main" entry point.
- `lab/static/`, the folder containing our JavaScript, CSS and JSON files that describe our main view's layout.
- `lab/static/css` and `lab/static/js`, the folder containing our custom CSS and Javascript files
- `lab/static/js/lib/` and `lab/static/css/lib/`, collections of third-party JavaScripts and CSS to support our app.
- `lab/templates/`, our HTML layouts.
- `lab/util/apiclient/__init__.py`, our custom Python class that describes what an API request looks like and the functions associated with it.
- `lab/ajax.py`, the Python class that carries out AJAX requests called via JavaScript.
- `lab/forms.py`, the Python script that describes the user input form responsible for collecting credentials and cluster IP address.
- `config.py`, the Python script that contains configuration information.  Note that this file is **not** in the `lab` folder!
- `lab/index.py`, the Python blueprint responsible for handling the `/` route (URL).

.. note::
   
   Please pay special attention to the locations of each file above.  Some files are in the **lab root** i.e. `python-lab`, while others are in the sub-directory called **lab**.

Git "ignore" File
.................

In addition to the actual project files, the recommended Git `.gitignore` file for this application is as follows.
This specific `.gitignore` file is often used with Python projects.  For our app, the `instance/` line has been added.
If you are using Git to manage your project's source, this `.gitignore` is a good one to use.

.. code-block:: bash

   nutanix/

   *.pyc
   __pycache__/

   instance/

   .pytest_cache/
   .coverage
   htmlcov/

   dist/
   build/
   *.egg-info/

.. note::

  The virtual environment directory is referenced by `nutanix` above.  In your own projects, you should change the `nutanix` reference to the name of your virtual environment.
  