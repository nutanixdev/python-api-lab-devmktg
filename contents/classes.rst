The ApiClient class
...................

The first file we'll create is one of the most supporting files in the app.

This file is the **ApiClient** class and describes what an API request looks like as well as the functions associated with it.

- Create the `lab/util/apiclient/` folders.

  .. figure:: images/linux_logo_32x32.png
  .. figure:: images/osx_logo_32x32.png

  .. code-block:: bash

     mkdir lab/util
     mkdir lab/util/apiclient

  .. figure:: images/windows_logo_32x32.png

  .. code-block:: bash

     mkdir lab\util
     mkdir lab\util\apiclient

- Create the file `lab/util/apiclient/__init__.py`

  The `__init__.py` file is a reserved filename that Python looks for when instantiating a class.

  The contents of `__init__.py` should be as follows:

  .. code-block:: python

        #!/usr/bin/env python3.8

        import sys
        import requests
        from requests.auth import HTTPBasicAuth
        import json

        class ApiClient():

            def __init__(self, method, cluster_ip, request, body, username, password, version='v3',root_path='api/nutanix'):
                self.method = method
                self.cluster_ip = cluster_ip
                self.username = username
                self.password = password
                self.base_url = f"https://{self.cluster_ip}:9440/{root_path}/{version}"
                self.entity_type = request
                self.request_url = f"{self.base_url}/{request}"
                self.body = body

            def get_info(self, show_info=False):

                if show_info == True:
                    print(f"Requesting '{self.entity_type}' ...")
                headers = {'Content-Type': 'application/json; charset=utf-8'}
                try:
                    if(self.method == 'post'):
                        r = requests.post(self.request_url, data=self.body, verify=False, headers=headers, auth=HTTPBasicAuth(self.username, self.password), timeout=60)
                    else:
                        r = requests.get(self.request_url, verify=False, headers=headers, auth=HTTPBasicAuth(self.username, self.password), timeout=60)
                except requests.ConnectTimeout:
                    print(f'Connection timed out while connecting to {self.cluster_ip}. Please check your connection, then try again.')
                    sys.exit()
                except requests.ConnectionError:
                    print(f'An error occurred while connecting to {self.cluster_ip}. Please check your connection, then try again.')
                    sys.exit()
                except requests.HTTPError:
                    print(f'An HTTP error occurred while connecting to {self.cluster_ip}. Please check your connection, then try again.')
                    sys.exit()

                if r.status_code >= 500:
                    print(f'An HTTP server error has occurred ({r.status_code}, {r.text})')
                else:
                    if r.status_code == 401:
                        print(f'An authentication error occurred while connecting to {self.cluster_ip}. Please check your credentials, then try again.')
                        sys.exit()

                return(r.json())

A few things to note about this class:

- The `__init__` function runs when the class is instantiated and describes **how** it should be instantiated.
- In our `ApiClient` class, we are setting some properties of the class, such as the IP address of our cluster, the cluster credentials (etc).
- The `get_info` function is called on-demand after the class is instantiated and carries out the actual API request.
- The `try` section of the `get_info` function attempts to complete the API request and get an HTTP response from the Nutanix API.
- The remaining `except` sections specify various exceptions that can be caught and dealt with accordingly.  For example, looking for `r.status_code >= 500` will catch any HTTP 500 errors.  This type of catch-all is bad practice in production environments but suits our basic demo requirements well enough.
- If no exceptions are caught, the JSON response from the API request is returned via `return(r.json())`.

With the basic application structure and main supporting class created, we can move forward with creating the other parts of our app.