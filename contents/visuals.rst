Views, Blueprints and Templates
+++++++++++++++++++++++++++++++

For our application to function correctly we need a few more things.  Primarily:

- **Views**, the code that handles requests to specific URLs in your application.
- **Blueprints**, a logical grouping of related code and views.
- **Templates**, files that contain static data e.g. HTML for layout/display purposes as well as placeholders that will be replaced later with dynamic data.

Views
.....

Because we are writing a single-page application that is accessed via the root (`/`) URL, we have a view named `index` whose contents are in `lab/index.py`.  It will handle all requests to the root URL.

However, that view references other views that we haven't created, yet.  To prepare for the main application view, let's first create the `forms` view.  It handles the creation of the form that accepts input from the user.

- Create `lab/forms.py`.
- Add the following content to `lab/forms.py`:

  .. code-block:: python

     from flask_wtf import FlaskForm
     from wtforms import StringField, PasswordField, BooleanField, SubmitField
     from wtforms.validators import DataRequired

     """
     The clusterForm class is used to identify the properties used when submitted cluster details
     """
     class clusterForm(FlaskForm):
         cvmAddress = StringField('cvmAddress', validators=[DataRequired()])
         username = StringField('username', validators=[DataRequired()])
         password = PasswordField('password', validators=[DataRequired()])
         submit = SubmitField('Go!', id="goButton")

The resources below are for learning more about forms management in Python Flask:

- `wtforms <https://wtforms.readthedocs.io/en/stable/>`_ (Forms management for Python Flask)
- `Flask-WTF <https://flask-wtf.readthedocs.io/en/stable/>`_ (Simple integration of Flask and WTForms, including CSRF, file upload, and reCAPTCHA.)

With the forms view created, we look at the main view for our application.  Let's do that now.

- Create `lab/index.py`
- Add the following content to `lab/index.py`:

  .. code-block:: python

     from lab.forms import clusterForm

     from flask import (
         Blueprint, flash, g, redirect, render_template, request, session, url_for
     )
     from werkzeug.security import check_password_hash, generate_password_hash

     bp = Blueprint('index', __name__, url_prefix='/')

     @bp.route('/')
     def index():
         # make sure we are using the form that's been generated in forms.py
         form = clusterForm()
         return render_template('index.html', form=form)

You'll notice that the first thing the `index` view does is reference the `forms` view we created a moment ago.

The `index` view does a few things:

- Registers the '/' URL.  Do you recall the 404 error we received when first running our app?  This is where we are instructing Flask how to handle requests to the root (`/`) URL.
- Creates an instance of our `form` view
- Renders the view based on the `index.html` template (which we will create shortly)

However, for this view to function correctly, we now need to make it available via the application initialisation script.

- Open `lab/__init__.py`
- Below the line that says `    pass`, add the following content, remembering to indent the code correctly:

  .. code-block:: python

     from . import index
     app.register_blueprint(index.bp)

     from . import ajax
     app.register_blueprint(ajax.bp)

The `index` view (and `ajax` view, which we will create shortly) are now available to our app.

**Quick question**.  What will happen if we now run our application?  Correct - we will be shown an error saying the `ajax` view can't be imported.  To fix that and prepare for template creation, let's create our `ajax` view now.

- Create `lab/ajax.py`
- Add the following content to `lab/ajax.py`. (We'll go through what the view does in an upcoming section).

  .. code-block:: python

     import os
     import json
     import base64
     import sys
     from datetime import datetime
     from datetime import timedelta
     import time
     import urllib3

     from flask import (
         Blueprint, flash, g, redirect, render_template, request, session, url_for, jsonify
     )
     from werkzeug.security import check_password_hash, generate_password_hash

     from .util import apiclient

     bp = Blueprint('ajax', __name__, url_prefix='/ajax')

     """
     disable insecure connection warnings
     please be advised and aware of the implications of doing this
     in a production environment!
     """
     urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

     """
     get the form POST data provided by the user
     """
     def get_form():
         global form_data
         global cvmAddress
         global username
         global password
         form_data = request.form
         cvmAddress = form_data['_cvmAddress']
         username = form_data['_username']
         password = form_data['_password']

     """
     load the default layout at app startup
     """
     @bp.route('/load-layout',methods=['POST'])
     def load_layout():
         site_root = os.path.realpath(os.path.dirname(__file__))
         layout_path = 'static/layouts'
         dashboard_file = 'dashboard.json'
         with open( f'{site_root}/{layout_path}/{dashboard_file}','r') as f:
             raw_json = json.loads(f.read())
             return base64.b64decode(raw_json['layout']).decode('utf-8')

     """
     get some high level cluster info
     """
     @bp.route('/cluster-info',methods=['POST'])
     def cluster_info():
         # get the request's POST data
         get_form()
         client = apiclient.ApiClient('post', cvmAddress,'clusters/list','{"kind":"cluster"}',username,password)
         results = client.get_info()
         return jsonify(results)

     """
     get the vm count
     """
     @bp.route('/vm-info',methods=['GET','POST'])
     def vm_info():
         # get the request's POST data
         get_form()
         client = apiclient.ApiClient('get', cvmAddress,'vms','',username,password,'v2.0')
         results = client.get_info()
         return jsonify(results)

     """
     get the cluster's physical info e.g. # of hosts, host serial numbers
     """
     @bp.route('/physical-info',methods=['POST'])
     def physical_info():
         # get the request's POST data
         get_form()
         client = apiclient.ApiClient('get', cvmAddress,'hosts','',username,password,'v2.0')
         results = client.get_info()
         return jsonify(results)

     """
     get the cluster's storage performance
     """
     @bp.route('/storage-performance',methods=['POST'])
     def storage_performance():
         # get the request's POST data
         get_form()

         # get the current time then substract 4 hours
         # this is used for the storage performance chart
         endTime = datetime.now()
         delta = timedelta(hours=-4)
         startTime = endTime + delta
         endTime = round(time.mktime(endTime.timetuple()) * 1000 * 1000)
         startTime = round(time.mktime(startTime.timetuple()) * 1000 * 1000)

         client = apiclient.ApiClient('get',cvmAddress,f'cluster/stats/?metrics=controller_avg_io_latency_usecs&startTimeInUsecs={startTime}&endTimeInUsecs={endTime}&intervalInSecs=30','',username,password,'v1','PrismGateway/services/rest')
         results = client.get_info()
         return jsonify(results)

     """
     get the container info e.g. # of containers
     """
     @bp.route('/container-info',methods=['POST'])
     def containers():
         # get the request's POST data
         get_form()
         client = apiclient.ApiClient('get',cvmAddress,f'storage_containers','',username,password,'v2.0')
         results = client.get_info()
         return jsonify(results)

Templates
.........

- Create the `lab/templates` folder.

  .. figure:: images/linux_logo_32x32.png
  .. figure:: images/osx_logo_32x32.png

  .. code-block:: bash

     mkdir lab/templates

  .. figure:: images/windows_logo_32x32.png

  .. code-block:: bash
  
     mkdir lab\templates  

Inside the `templates` folder we are going to create two templates.  These are as follows:

- `base`, the **master** template that our application's main view will be based on.
- `index`, the application's main view i.e. the one that we'll actually see.

Both templates are mostly HTML, with the exception of a few placeholders.
The placeholders are identified by being enclosed in `{{` and `}}` and will be replaced with dynamic data when the template is rendered.

- Create `lab/templates/base.html`
- Add the following content to `lab/templates/base.html`:

  .. code-block:: html

     <!doctype html>
     <html lang="en">
         <head>
             <meta charset="utf-8">
             <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
             <meta name="viewport" content="width=device-width, initial-scale=1">
             <title>{% block title %}{% endblock %} - Lab</title>

             {% assets 'home_css' %}
                 <link rel="stylesheet" href="{{ ASSET_URL }}">
             {% endassets %}

         </head>
         <body>
             <nav class="navbar navbar-default navbar-fixed-top main-nav">
                 <div class="container-fluid">
                     <div class="collapse navbar-collapse">
                         <ul class="nav navbar-nav">
                             <li><a href="#">Home</a></li>
                             <li><a href="#" class="defaultLayout">Revert to Default Layout</a></li>-->
                         </ul>
                         <form method="post" class="navbar-form navbar-left">
                             <div class="form-group">
                                 {{ form.hidden_tag() }}
                                 {{ form.cvmAddress(class="form-control",placeholder="Cluster/CVM IP") }}
                                 {{ form.username(class="form-control",placeholder="Cluster Username") }}
                                 {{ form.password(class="form-control",placeholder="Cluster Password") }}
                                 {{ form.submit(class="btn btn-primary") }}
                             </div>
                         </form>
                     </div>
                 </div>
             </nav>
             <section class="content">
                 {% for message in get_flashed_messages() %}
                     <div class="flash">{{ message }}</div>
                 {% endfor %}
                 {% block content %}{% endblock %}
             </section>

             <div style="height: 70px; clear: both;"></div>

             {% assets 'home_js' %}
                 <script src="{{ ASSET_URL }}"></script>
             {% endassets %}

         </body>

     </html>

- Create `lab/templates/index.html`.
- Add the following content to `lab/templates/index.html`:

  .. code-block:: html

     {% extends 'base.html' %}
     {% block header %}
         {% block title %}Home{% endblock %}
     {% endblock %}

     {% block content %}

     <div class="container" style="margin-top: 20px;">
         <div class="row">
             <div class="col-md-15">
                 <div class="container">
                     <div class="row">
                         <div class="col-md-15">

                             <div class="gridster">
                                 <ul>
                                     <!-- The grid layout will end up here, once it is generated -->
                                 </ul>
                             </div>

                         </div>
                     </div>
                 </div>
             </div>
         </div>
     </div>

     {% endblock %}

We already know that the `base` template will be used as the **master** template for all others in our app.  In our specific application we only have a single 'visible' view - the `index`.

Most of the content above will look very familiar, but with the addition of this line in particular:

.. code-block:: html

   {% extends 'base.html' %}

We can now tell that the `index` template is rendered using the `base` template.

Now let's look a little deeper into the `ajax` view and see how it works.