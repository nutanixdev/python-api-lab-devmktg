The API request
...............

Because the Nutanix REST APIs are designed to be simple to use, it's very easy to understand what the request itself is doing.

In the **Nutanix API Intro** section of this lab, we looked at the various Nutanix Prism API versions that are available to you.  In the example from the previous section, we are using Nutanix Prism Central v3 APIs to get a list of entities of a specific type.  The JavaScript making the AJAX call and the Python executing the API request are constructing the following POST request.

.. code-block:: html

   https://<prism_central_ip>:9440/api/nutanix/v3/vms/list

.. code-block:: json

   {"kind": "vm"}
 
If you were to change `<prism_central_ip>` to your Prism Central IP address browse to that URL, you would probably see an error as follows:

.. code-block:: json

   {"state": "ERROR", "code": 401, "message_list": [{"reason": "AUTHENTICATION_REQUIRED", "message": "Authentication required.", "details": "Basic realm=\"Intent Gateway Login Required\""}], "api_version": "3.1"}

That's because we are missing a few pieces of vital information:

- We haven't specified the credentials that should be used for the request.
- We haven't supplied the required JSON POST body that must be sent with the request.
- We have submitted an HTTP GET request, whereas requests of this type must be HTTP POST requests.

.. note::

  If you have an open browser tab or window where you are already logged in and authenticated with an active Nutanix Prism Central session, it is possible the request may be authenticated but fail with the following error:

.. code-block:: json

   {
       "api_version": "3.1",
       "code": 400,
       "kind": "vm",
       "message_list": [{
           "message": "Invalid UUID passed. list", 
           "reason": "INVALID_UUID"
       }],
       "state": "ERROR"
   }

While the connection itself was successful, the browser is sending an HTTP GET request, while these requests need to be POST.  Furthermore, the error message tells us that **list** is not a valid VM UUID.  For those reasons, we can't use that URL without the POST body as specified earlier.

Now that we have our API request URL and JSON POST body, we can add HTTP Basic Authentication in the form of a username and password, then simulate the entire request using cURL.

.. code-block:: bash

   curl -X POST \
     https://<prism_central_ip>:9440/api/nutanix/v3/vms/list \
     -H 'Accept: application/json' \
     -H 'Content-Type: application/json' \
     --data '{"kind": "vm", "length": 1}' \
     --insecure \
     --basic \
     --user "<prism_central_username>:<prism_central_password>"

Please be mindful of the `--insecure` parameter, as detailed in the lab intro.     

This exact POST request structure, including HTTP Basic Authentication, the URL and the JSON POST body are exactly what the **pcListEntities** JavaScript function uses to get lists of VMs, images, etc.
