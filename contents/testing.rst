Testing The App
+++++++++++++++

With our JavaScript, AJAX, CSS, views and templates now in place, it's a good time to run our application and see what happens.

If you don't currently have your virtual environment activated or if the application isn't running, these are the steps to do so.  Make sure you are in the application's directory before running these commands.

.. figure:: images/linux_logo_32x32.png
.. figure:: images/osx_logo_32x32.png

.. code-block:: bash

   . nutanix/bin/activate

  flask run --host 0.0.0.0

.. figure:: images/windows_logo_32x32.png

.. code-block:: bash

  nutanix\Scripts\activate.bat

  flask run

**Quick question**.  Why don't we need to specify the name of our app and instruct Flask to treat our environment as non-production?  Correct - these settings are specified in the file named `.flaskenv`

- Browse to http://127.0.0.1:5000 on your local machine to view your application.
- If everything is setup correctly, you will see a basic HTML form prompting for a **Cluster/CVM IP**, your **Cluster Username** and **Cluster Password**.

You'll also see a number of styled and labelled "containers", ready for our cluster info to be displayed.

Fill out the following fields:

- **Cluster/CVM IP** - The IP address of your cluster or CVM
- **Cluster Username** - Cluster username.  Note that this can be a read-only account.
- **Cluster Password** - Cluster password.

- Click **Go!**

If everything has been created and all parts of the application wired up correctly, the application will carry out our API requests via AJAX, process the results and display it all nicely on our page.

A successful test run will look similar to the screenshot below, although your cluster details will be different.

.. figure:: images/flask_app_run_final.png