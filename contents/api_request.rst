The API request
...............

Because the Nutanix REST APIs are designed to be simple to use, it's very easy to understand what the request itself is doing.

In the **Nutanix API Intro** section of this lab, we looked at the various Nutanix API versions that are available to you.  In the example above, we are using Nutanix API v2.0 to get a count of VMs running on our cluster.  The JavaScript making the AJAX call and the Python executing the API request, are constructing the following GET request.

.. note::

  The request coming from the JavaScript to our Python view is an HTTP POST request.  The request to the API itself, in this example, is an HTTP GET request.

.. code-block:: html

   https://<cluster_virtual_ip>:9440/api/nutanix/v2.0/vms
 
If you were to change `<cluster_virtual_ip>` to your cluster IP address and browse to that URL, you would probably see an error saying `"An Authentication object was not found in the SecurityContext"`.  That's because we haven't specified the credentials that should be used for the request.

.. note::

  If you have an open browser tab where you are already logged in and authenticated with an active Nutanix Prism session, it is possible the request may succeed, depending on your browser's cookie settings.

Now that we have our API request URL, we can add HTTP Basic Authentication in the form of a username and password, then simulate the entire request using cURL.  For this quick test we will assume the following:

- **Cluster virtual IP address** - *your Cluster IP address*
- **Cluster username** - <your cluster username>
- **Cluster password** - <your cluster password>>

.. code-block:: bash

   curl -X GET \
     https://<cluster_ip>:9440/api/nutanix/v2.0/vms \
     -H 'Accept: application/json' \
     -H 'Content-Type: application/json' \
     --insecure \
    --basic --user <your cluster username>:<your cluster password>

Please be mindful of the `--insecure` parameter, as detailed in the lab intro.
