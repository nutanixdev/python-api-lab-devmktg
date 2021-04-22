JavaScript and AJAX
+++++++++++++++++++

The JavaScript Function
.......................

The `ajax` view is a key part of our application.  It gets used via various JavsScript functions, stored in the `lab/static/js/ntnx.js` file.  When the user enters their Prism Central IP address and credentials then hits the 'Go!' button, JavaScript makes various calls to the Nutanix Prism Central v3 REST APIs (plus one other that we'll talk about shortly).  These calls are handled via `AJAX <https://en.wikipedia.org/wiki/Ajax_(programming)>`_ so the user's browser doesn't get refreshed every time.

So that we're following a very well-known principle known as DRY (don't repeat yourself), almost all requests to the Nutanix Prism Central v3 REST APIs in our app are handled via a single function named **pcListEntities**.  This function accepts a number of parameters, the most important of which is the **entity** parameter.  By altering the **entity** parameter, we can instruct the Prism Central v3 REST APIs to return a list of entities of that type.  For example, can pass "vm", "cluster" (etc).

Here's what the **pcListEntities** function looks like.

.. code-block:: JavaScript

   pcListEntities: function( cvmAddress, username, password, entity, pageElement, elementTitle ) {

       pcEntityInfo = $.ajax({
           url: '/ajax/pc-list-entities',
           type: 'POST',
           dataType: 'json',
           data: { _cvmAddress: cvmAddress, _username: username, _password: password, _entity: entity, _pageElement: pageElement, _elementTitle: elementTitle },
       });

       pcEntityInfo.done( function(data) {

           NtnxDashboard.resetCell( pageElement );
           $( '#' + pageElement  ).addClass( 'info_big' ).append( '<div style="color: #6F787E; font-size: 25%; padding: 10px 0 0 0;">' + elementTitle + '</div><div>' + data.metadata.total_matches + '</div><div></div>');

           switch( entity ) {
               case 'project':

                   $( '#project_details' ).addClass( 'info_big' ).html( '<div style="color: #6F787E; font-size: 25%; padding: 10px 0 0 0;">Project List</div>' );

                   $( data.entities ).each( function( index, item ) {
                       $( '#project_details' ).append( '<div style="color: #6F787E; font-size: 25%; padding: 10px 0 0 0;">' +  item.status.name + '</div>' );
                   });

                   $( '#project_details' ).append( '</div><div></div>' );

               default:
                   break;
           }

       });

   },
   /* pcListEntities */

Here are the most important steps carried out by this function:

- `pcListEntities: function( cvmAddress, username, password, entity, pageElement, elementTitle ) {` - the name of the function, accepting the parameters that define the type of entity info being requested.
- `pcEntityInfo = $.ajax({` - use `jQuery <https://jquery.com/>`_ to initiate an AJAX request.
- `url: '/ajax/pc-list-entities',` - the URL of the AJAX call that will be made.
- The block beginning with `pcEntityInfo.done( function(data) {` - the JavaScript that will run when the AJAX call has finished.
- The switch block beginning with `switch( entity ) {` - only step into that code block if the `pcListEntities` function has been used to specifically request information on Prism Central projects.

In addition to the **pcListEntities** function, we have another function named **storagePerformance** that begins as follows:

.. code-block:: javascript

   storagePerformance: function( cvmAddress, username, password ) {

       /* AJAX call to get some container stats */
       request = $.ajax({
           url: '/ajax/storage-performance',
           type: 'POST',
           dataType: 'json',
           data: { _cvmAddress: cvmAddress, _username: username, _password: password, _entity: "containers" },
       });

This function is a special one that deals specifically with getting storagec container performance information for the *first storage container in the first cluster registered to Prism Central*.  In a real-world situation you would request storage performance info for a specific cluster and container.

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

   @bp.route("/pc-list-entities", methods=["POST"])
   def pc_list_entities():
       # get the request's POST data
       get_form()
       client = apiclient.ApiClient(
           method="post",
           cluster_ip=cvmAddress,
           request=f"{entity}s/list",
           entity=entity,
           body=f'{{"kind": "{entity}"}}',
           username=username,
           password=password,
       )
       results = client.get_info()
       return jsonify(results)

Here are the most important steps carried out by this function:

- `@bp.route("/pc-list-entities", methods=["POST"])` - Specify the URL that will respond to the AJAX call and allow the POST method **only**.
- `get_form()` - Get the user data available in the POST request.  This includes the CVM/Cluster IP address, entity, username and password.
- Block beginning with `client = apiclient.ApiClient(` - Create an instance of our `ApiClient` class and set the properties we'll need to execute the API request.
- `results = client.get_info()` - Execute the actual API request.
- `return jsonify(results)` - Convert the API request results to JSON format and return the JSON back to the calling JavaScript, where it will be processed and displayed in our app.

In addition to the **pc_list_entities** function, `ajax.py` contains another function named **storage_performance**.  Note the code block that begins as shown below:

.. code-block:: python

   @bp.route("/storage-performance", methods=["POST"])
   def storage_performance():
       # get the request's POST data
       get_form()

       # etc

This function is used only when the app needs to request storage container performance information.  Why?  This function makes a number of API requests to different API endpoints:

- Prism Central v3 REST APIs are used to request information about registered clusters.
- Prism Element v2.0 REST APIs are used when:
  
  - Requesting a list of storage containers found on the first registered cluster
  - Requesting performance information for a specific storage container

.. note::

  You'll notice a few parameters being passed during instantiation of the ApiClient class.  As an optional step, open `lab/util/apiclient/__init__.py` and look at the other parameters that can be passed.  For example, you can specify the API endpoint and the API version.  These are useful options for using the same ApiClient class with different versions of the Nutanix Prism REST APIs.
