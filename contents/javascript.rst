JavaScript and AJAX
+++++++++++++++++++

The JavaScript Function
.......................

The `ajax` view is a key part of our application.  It gets used via various JavsScript functions, stored in `lab/static/js/ntnx.js` file.  When the user enters their cluster or CVM IP address, credentials and hits the 'Go!' button, JavaScript makes various calls to the Nutanix APIs.  These calls are handled via `AJAX <https://en.wikipedia.org/wiki/Ajax_(programming)>`_ so the user's browser doesn't get refreshed every time.

As an example, let's first take a look at the JavaScript function that gets a count of VMs running on our cluster.

.. code-block:: JavaScript

   vmInfo: function( cvmAddress, username, password )
   {

       vmData = $.ajax({
           url: '/ajax/vm-info',
           type: 'POST',
           dataType: 'json',
           data: { _cvmAddress: cvmAddress, _username: username, _password: password },
       });

       vmData.success( function(data) {
           NtnxDashboard.resetCell( 'vmInfo' );
           $( '#vmInfo' ).addClass( 'info_big' ).append( '<div style="color: #6F787E; font-size: 25%; padding: 10px 0 0 0;">VM(s)</div><div>' + data['metadata']['count'] + '</div><div></div>');
       });

       vmData.fail(function ( jqXHR, textStatus, errorThrown )
       {
           console.log('error getting vm info')
       });
   },

Here are the most important steps carried out by this function:

- `vmInfo: function( cvmAddress, username, password )` - the name of the function, accepting the cluster/CVM IP address & and credentials.
- `vmData = $.ajax({` - use `jQuery <https://jquery.com/>`_ to initiate an AJAX request.
- `url: '/ajax/vm-info',` - the URL of the AJAX call that will be made.
- The block beginning with `vmData.success( function(data) {` - the JavaScript that will run when the AJAX call is successful.
- The block beginning with `vmData.fail(function ( jqXHR, textStatus, errorThrown )` - displays a message in the browser console when any error is encountered.  Of course, this "catch-all" approach is something that should be avoided before deploying to or developing for a production environment, but provides information that can be used to diagnose an issue.

The AJAX
........

Now that we are familiar with the simple JavaScript code that will make the AJAX call, let's look at the Python code that carries out the first part of the API request.

This Python code is stored in `lab/ajax.py`.

.. code-block:: python

   """
   disable insecure connection warnings
   please be advised and aware of the implications of doing this
   in a production environment!
   """
   urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

The code block above will prevent Python from throwing warnings about insecure connections over HTTPS.  This should only be done in development or testing environments; please be aware of the security implications of doing this in production.

.. code-block:: python

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

Here are the most important steps carried out by this function:

- `@bp.route('/vm-info',methods=['GET','POST'])` - Specify the URL that will respond to the AJAX call and allow both GET and POST methods.  During testing it can be useful to allow both methods, although production apps would only allow the methods that are explicitly required.
- `get_form()` - Get the user data available in the POST request.  This includes the CVM/Cluster IP address, username and password.
- `client = apiclient.ApiClient('get', cvmAddress,'vms','',username,password,'v2.0')` - Create an instance of our `ApiClient` class and set the properties we'll need to execute the API request.

- `results = client.get_info()` - Execute the actual API request.
- `return jsonify(results)` - Convert the API request results to JSON format and return the JSON back to the calling JavaScript, where it will be processed and displayed in our app.

.. note::

  You'll notice a few parameters being passed during instantiation of the ApiClient class.  As an optional step, open `lab/util/apiclient/__init__.py` and look at the other parameters that can be passed.  For example, you can specify the API endpoint we're interested and the API version.  These are useful options for using the same ApiClient class with different versions of the APIs.