Supporting Files
++++++++++++++++

In this part of the lab we're going to add the supporting JavaScript and CSS files.  These files are critical to the layout and functionality of the app.

Checking directory structure
............................

- Create the following directories and files:

  - **lab/static**
  - **lab/static/css**
  - **lab/static/css/lib**
  - **lab/static/css/fonts**
  - **lab/static/js**
  - **lab/static/js/lib**
  - **lab/static/layouts**

  .. figure:: images/linux_logo_32x32.png
  .. figure:: images/osx_logo_32x32.png

  .. code-block:: bash

     mkdir lab/static
     mkdir lab/static/css
     mkdir lab/static/css/lib
     mkdir lab/static/css/fonts
     mkdir lab/static/js
     mkdir lab/static/js/lib
     mkdir lab/static/layouts

  .. figure:: images/windows_logo_32x32.png

  .. code-block:: bash  

     mkdir lab\static
     mkdir lab\static\css
     mkdir lab\static\css\lib
     mkdir lab\static\css\fonts
     mkdir lab\static\js
     mkdir lab\static\js\lib
     mkdir lab\static\layouts

Adding Third Party Files
........................

- From the URLs below, grab the relevant file, make sure the name is correct and extract it into the appropriate directory.

  - `CSS <https://github.com/nutanixdev/lab-assets/blob/master/python-lab-v1/css-lib.zip?raw=true>`_ - extract to **lab/static/css/lib/**
  - `Fonts <https://github.com/nutanixdev/lab-assets/blob/master/python-lab-v1/fonts.zip?raw=true>`_ - extract to **lab/static/css/fonts/**
  - `JavaScript <https://github.com/nutanixdev/lab-assets/blob/master/python-lab-v1/js-lib.zip?raw=true>`_ - extract to **lab/static/js/lib/**

.. note::

  If extracting the ZIP files from the command line, ensure they are extracted **directly** to the directories above and not into subdirectories.

  Once the files are downloaded and extracted to the correct locations, your directory structure should be as follows:

  .. figure:: images/public_structure.png

Adding Custom Files
...................

- From the URLs below, grab the relevant file, make sure the name is correct and copy it into the appropriate directory.

  - `ntnx.css <https://raw.githubusercontent.com/nutanixdev/lab-assets/master/python-lab-v1/ntnx.css>`_ - copy to **lab/static/css**
  - `ntnx.js <https://raw.githubusercontent.com/nutanixdev/lab-assets/master/python-lab-v1/ntnx.js>`_ - copy to **lab/static/js**
  - `dashboard.json <https://raw.githubusercontent.com/nutanixdev/lab-assets/master/python-lab-v1/dashboard.json>`_ - copy to **lab/static/layouts**

Referencing Supporting Files
............................

- Open `lab/__init__.py` and, under the line that reads `assets = Environment(app)`, add the following Python code.

  **Important note:** Python has strict indentation_ requirements.  For the code below, make sure the indentation begins at the same point as the `assets = Environment(app)` line.

  .. _indentation: https://docs.python.org/3.6/reference/lexical_analysis.html

  .. code-block:: python

     home_css = Bundle(
             'css/lib/reset.css',
             'css/lib/built-in.css',
             'css/lib/jquery-ui-custom.css',
             'css/lib/jq.gridster.css',
             'css/lib/jq.jqplot.css',
             'css/ntnx.css'
     )
     home_js = Bundle(
             'js/lib/jquery-2.1.3.min.js',
             'js/lib/classie.min.js',
             'js/lib/ntnx-bootstrap.min.js',
             'js/lib/modernizr.custom.min.js',
             'js/lib/jquery.jqplot.min.js',
             'js/lib/jqplot.logAxisRenderer.js',
             'js/lib/jqplot.categoryAxisRenderer.js',
             'js/lib/jqplot.canvasAxisLabelRenderer.js',
             'js/lib/jqplot.canvasTextRenderer.js',
             'js/lib/jquery.gridster.min.js',
             'js/ntnx.js'
     )

     assets.register('home_css',home_css)
     assets.register('home_js',home_js)

  This code block registers two 'bundles' that allow our app to correctly load and access the JavaScript and CSS files.  Firstly, the bundles are created as `home_css` and `home_js`, then registered as application assets using `assets.register`.

With this done, we can continue with working on our application.