API Intro
#########

This section is optional reading but is recommended for those not already familiar with the Nutanix Prism REST APIs.

If you are already familiar with the Nutanix Prism REST APIs, their structure and usage, please scroll to the bottom of this page and use the provided button to move forward.

Nutanix API Intro
.................

Before we start creating our app, let's take a look at how Nutanix describes the APIs we'll use today.

**Those familiar with the Nutanix APIs may wish to skip this section.**

The Nutanix Prism Element and Prism Central REST APIs allow you to create scripts that run system administration commands against both Nutanix clusters and Prism Central instances.

These APIs enable the use of HTTP requests to get information about the cluster/Prism Central as well as make changes to the configuration.

Nutanix Prism Element and Prism Central API requests will respond with JSON-formatted output.

The Nutanix Prism Central v3 REST API, supported in Prism Central **only**, is the latest intentful API released by Nutanix. All users are advised to migrate to v3, unless there is a requirement to use a v2.0 API.  Please note the v2.0 API is only supported via Prism Element, not via Prism Central.

Authentication
..............

Authentication against the Nutanix Prism Element and Prism Central REST APIs is done using HTTP Basic Authentication.
Requests on HTTP port 80 are automatically redirected to HTTPS on port 9440.
This requires that a valid cluster/Prism Central or configured directory service credential is passed as part of the API request.

For the purposes of this lab, we will assume you have access to at least a Nutanix Prism Central username with a minimum of READ access.
Note that a Cluster Admin account is not required to read information via Nutanix Prism API request.

For those attendees or readers following this lab in a presenter-led environment, please use the Prism Central IP address and credentials given to you by your presenter.

Prism Element "vs" Prism Central
................................

The Prism Element v2.0 REST APIs are cluster-specific i.e. designed to manage and manipulate entities on an individual Nutanix cluster.

The Prism Central v3 REST APIs include a larger set of APIs designed to manipulate entities that aren't necessarily specific to a single cluster.  This includes Nutanix Calm and many other Nutanix products that are available via Prism Central.

API Versions
............

As at January 2020, there are two main/publicly available Nutanix APIs.  Note that while Nutanix API v1 may be available via the REST API Explorer in Nutanix Prism, all users are advised to use v3 APIs instead, or v2.0 if the required endpoint is only available via an individual cluster.  v1 is mentioned here for completeness reasons only and should only be used in specific situations where v1 is the only choice.

The API versions available today are as follows.

- v2.0 (Prism Element)
- v3 (Prism Central)

**Note re security:** In the sample commands below you'll see use of the `--insecure` cURL parameter.  This is used to get around SSL/TLS verification issues when using self-signed certificates.  Please consider the potential pitfalls and security implications of bypassing certificate verification before using `--insecure` in a production environment.  The same precautions apply when providing a username and password on the command-line.  This should be avoided when possible, since this method shows both the username and password in clear text.

.. note::

   **Windows systems:** When running the cURL sample commands on Windows 10, single-quote characters (`'`) may need to be replaced with double-quote characters (`"`).

Prism Element REST API v1
~~~~~~~~~~~~~~~~~~~~~~~~~

Status: Available but should only be used in specific situations (e.g. gathering VM performance metrics)

This set of APIs was originally used to manipulate and view VMs, storage containers, storage pools etc.  As at January 2020, the v1 APIs should only be used in specific situations, e.g. to gather VM or storage container performance metrics.  All other API functions should be carried out via API v2.0 or v3.

So as not to confuse the content in this lab, please see the following blog article for information on performance metrics via API v1: `Gathering VM Performance Metrics via API <https://www.nutanix.dev/2019/09/23/getting-vm-performance-metrics-via-api>`_.

Prism Element REST API v2.0
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Status: Available

An over-simplification would be to say that the v2.0 APIs are where the older v0.8 and v1 APIs came together. Many of the entities and endpoints available in v0.8 and v1 were made available in v2.0, along with a huge amount of backend cleanup, endpoint renaming and general API improvements. The v2.0 APIs were also the first officially GA API made available by Nutanix.

If you have some exposure to the previous API versions, moving to the v2.0 APIs will highlight a number of differences. For example:

- “containers” got renamed to “storage_containers”
- “storagePools” got renamed to “storage_pools”

The difference? A consistent naming convention in the form of snake-case across all entities.

Here’s a basic example of a v2.0 API request to list all **storage_containers** on a cluster:

.. code-block:: html

   https://<prism_element_ip>:9440/api/nutanix/v2.0/storage_containers

Alternatively, this HTTPS API request can be carried out using the `curl` command:

.. code-block:: bash

   curl -X GET \
     https://<cluster_virtual_ip>:9440/api/nutanix/v2.0/storage_containers \
     -H 'Accept: application/json' \
     -H 'Content-Type: application/json' \
     --insecure \
     --basic --user <cluster_username>:<cluster_password>

Quick Summary
~~~~~~~~~~~~~

Before moving forward, note that all the APIs above return a JSON response that is easily consumable by many programming or scripting languages.

Also, all the requests above are basic HTTP GET requests and do not require a payload (POST body).

Prism Central REST API v3
~~~~~~~~~~~~~~~~~~~~~~~~~

Status: Available

The Prism Central v3 REST APIs, released as GA on April 17th 2018, are the first departure from how things were done before.

We had standard GET requests to get data from a cluster and standard POST methods to make changes - the v3 APIs are slightly different.  All the previous Prism Element APIs still required the developer to tell the system what to do and how to do it. The Prism Central v3 REST APIs, on the other hand, are the first APIs built around an Intentful paradigm, that is, `move the programming from the user to the machine`.  Instead of writing a ton of code to get something done, we tell the system what the desired state is and the system will “figure out” the best way to get there.  This will sound somewhat familiar to those using configuration management frameworks like Salt, Puppet, Chef, Ansible, PowerShell DSC etc.

How this all happens is somewhat beyond the scope of this particular lab but, as an example, take a look at the request below. It’s still getting similar information to the previous requests but with a few key differences.

1. It is an HTTP **POST** request, not **GET**.
2. A JSON payload (POST body) is required so that the cluster knows what type of entity to return. In this example, we’re requesting information about VMs.
3. We’re telling the system what we want to do with the data. In this case, we want to list all VMs.
4. The Prism v3 REST APIs are supported on **Prism Central only**.

.. code-block:: html

   https://<prism_central_ip>:9440/api/nutanix/v3/vms/list

And the post body:

.. code-block:: JSON

   {"kind": "vm"}

Alternatively, this HTTPS API request can be carried out using the `curl` command:

.. code-block:: bash

   curl -X POST \
     https://<prism_central_virtual_ip>:9440/api/nutanix/v3/vms/list \
     -H 'Accept: application/json' \
     -H 'Content-Type: application/json' \
     -d '{"kind": "vm"}' \
     --insecure \
     --basic --user <prism_central_username>:<prism_central_password>

cURL Command Analysis
.....................

As an extra step, let's take the v3 API request above and look at what each part of the command is doing.  If you are familiar with using cURL to make API requests, please feel free to skip this section.

- curl -X POST \ - Run cURL and specify that we will be making an HTTP POST request (as opposed to HTTP GET).
- https://<prism_central_virtual_ip>:9440/api/nutanix/v3/vms/list \ - Specify the complete URL to send the request to.
- -H 'Accept: application/json' \ - Specify the content types the client is able to understand.
- -H 'Content-Type: application/json' \ - Tell the server what type of data is actually sent.
- -d '{"kind":"vm"}' \ - For our POST request, specify the request **body** i.e. the parameters to send along with the request.
- --insecure \ - Tell the cURL command to ignore SSL certificate verification errors (please see the note above re what this means).
- --basic - Tell the cURL command that we will authenticate using **Basic Authentication**.
- --user <prism_central_username>:<prism_central_password> - Specify the username and password to use during basic authentication.

Version Use Cases
.................

With what we know about the various API versions now, let's take a look at why you might use each API.

- **v1**: Legacy application support and entity-specific performance metrics.  Without specific requirements, the v1 APIs should not be used.
- **v2.0 via Prism Element**: Migration away from legacy APIs, combination of older v0.8 and v1 APIs into single GA API, **cluster-specific** tasks e.g. storage container information & management.
- **v3 via Prism Central**: Latest supported API aimed at managing **environment-wide** configuration and entities.  Unlike API v2.0 on Prism Element, this includes a vast array of entities such as Nutanix Calm Blueprints, RBAC, Applications and Nutanix Flow Network Security Rules.  The v3 APIs are supported on Prism Central only.

Summary
.......

At this point, you know what the available APIs versions are and what some of the differences are between them.

Now let’s move on to the lab itself.
