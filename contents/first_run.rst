Application first run
.....................

Since we are developing a simple demo application, we only have two requirements to run our app.  Run these commands in the application's main folder i.e. **not** in the `lab/` folder.

**Important note**: If you are running this lab on a non-US keyboard, you may require an additional step to ensure Flask runs properly.  The commands below are temporary and won't permanently modify your system.

.. code-block:: bash

  export LC_ALL=en_US.UTF-8
  export LANG=en_US.UTF-8

- Tell Python Flask where to find our application and what sort of environment we're running in.

  Create a file in the lab's root folder named `.flaskenv` and set the file's contents as follows:

  .. code-block:: bash

     FLASK_ENV=development
     FLASK_APP=lab

- Run the application:

  **Note**: If you are on Mac OS X and are prompted to allow incoming connections (see screenshot below), please click **Allow**.

  .. figure:: images/first_run_prompt.png

.. figure:: images/linux_logo_32x32.png
.. figure:: images/osx_logo_32x32.png

.. code-block:: bash

  flask run --host 0.0.0.0

.. figure:: images/windows_logo_32x32.png

.. code-block:: bash

  flask run

At this point, Python Flask tells us exactly what to do in order to test the beginnings of our application:

.. code-block:: bash

   * Running on http://<ip_address>:5000/ (Press CTRL+C to quit)

- Browse to http://127.0.0.1:5000 on your local machine

  If everything is working, you'll get an error saying the requested URL was not found on the server.  This is expected, since we haven't yet told Flask how to handle requests for the root (`/`) URL.

  Check the output in your console/terminal and you'll also see the 404 error reflected there, as expected.

  .. code-block:: bash

     127.0.0.1 - - [7/Apr/2021 16:41:06] "GET / HTTP/1.1" 404 -

  This is a good test as it verifies everything is setup and working.  It also verifies that the dependencies are installed, along with Python Flask being ready to serve your application.

- Stop the application (Press CTRL+C to quit)

Now let's start building our application by adding the application's supporting files.