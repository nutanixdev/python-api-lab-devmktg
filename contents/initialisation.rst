Initialisation
++++++++++++++

For this section we'll build the app based on the structure seen earlier.

Configuration Options
.....................

`config.py` is where our app stores app-specific configuration.
For this basic application we are only storing a single static configuration item - the `SECRET_KEY` required for CSRF protection.
For more information on `CSRF <https://en.wikipedia.org/wiki/Cross-site_request_forgery>`_, please see the `CSRF <https://en.wikipedia.org/wiki/Cross-site_request_forgery>`_ Wikipedia article.

CSRF protection isn't strictly required for demo or isolated applications, but is a good habit to get into when developing web applications.

- Create `config.py`

  .. note::

     Please note that `config.py` should be in the project's root directory i.e. `~/python-lab`, and should **not** be in the `lab/` folder.

  - Add the following content to `config.py`:

    .. code-block:: python

       import os

       class Config(object):
           SECRET_KEY = os.environ.get('SECRET_KEY') or 'some strong secret string'

  While we won't be adding CSRF protection to this lab, we could import this key later to ensure our app is protected against CSRF (Cross-Site Request Forgery).

Initialisation Script
.....................

`lab/__init__.py` is our application's main initialisation script.  This file contains the application **factory** and instructs Python to treat our `lab` folder as a package.  In our app, configuration and setup, for example, will be carried out inside the factory function and the app returned afterwards.  For a more detailed explanation, please see the official `factory tutorial <http://flask.pocoo.org/docs/1.0/tutorial/factory/>`_.

- Create a file named `__init__.py` in the `lab/` folder.
- For now, our application's `__init__.py` should be as follows:

  .. code-block:: python

     import os
     from flask import Flask
     from flask_assets import Environment, Bundle

     from .util import apiclient
     from config import Config


     def create_app(test_config=None):
         # create and configure the app
         app = Flask(__name__, instance_relative_config=True)
        
         assets = Environment(app)
        
         app.config.from_object(Config)
        
         if test_config is None:
             # load the instance config, if it exists, when not testing
             app.config.from_pyfile("config.py", silent=True)
         else:
             # load the test config if passed in
             app.config.from_mapping(test_config)
        
         # ensure the instance folder exists
         try:
             os.makedirs(app.instance_path)
         except OSError:
             # do something here if the directory can't be created
             pass
        
         return app
  

  Key lines to look for in `__init__.py`:

  - `from flask import Flask` makes the Flask web framework available to our application.
  - `from flask_assets import Environment, Bundle` enables us to store configuration and bring together our static files as **bundles**.
  - `from .util import apiclient` and `from config import Config` imports our ApiClient package and grabs our configuration from `config.py`.
  - `os.makedirs(app.instance_path)` attempts to create our instance path that can be used to store local data, e.g. files that won't be committed to source control.  We aren't explicitly using this folder in our app but it is useful to know about for future projects.

  At this point our application will actually "work".  It won't do anything particularly useful, although now is a good time to see how to run a Python Flask application.