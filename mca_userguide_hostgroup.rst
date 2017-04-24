******************
Host Group
******************

.. |editbutton| image:: images/mca/editbutton.png

.. image:: images/mca/hostgroups.png

To define a meshconfig, you will need 2 basic ingredients. Hostgroups, and Test specs.  Host group is a logical grouping of toolkit instances, and you can reuse a single host group for multile meshconfigs. 

Click button "Add New ..." button to open a new host group dialog.

.. image:: images/mca/hostgroup.png

* Name: Name of the hostgroup
* Service Type: You need to pick a service type for this hostgroup. 
* Admins: List of users who are *admin* of this hostgroup. Only admins can edit the hosts.

* Hosts/Static
    Enter a hostname / service name to search from all available hosts that are currently cached from your service datasources. You can only select from hosts that provides the service that you have selected above.

* Hosts/Dynamic
    Dynamic hostgroup allows you to construct a hostgroup that automatically updated based on your custom criteria and current list of hosts registered in your datasource. Please see below for more detail.

You can use host groups created by other users, but you can only edit ones with your name listed under Admins.

You can click on a small pencil button |editbutton| to edit an existing host group.

Dynamic Host Groups
-----------------------

Dynamic Host Group allows you to enter host selection criteria to be evaluated at runtime. This is useful if you want to construct a host group that changes its member based on the latest information from the global registry.

.. image:: images/mca/dynamichosteditor.png

As you can see, dynamic Host Group editor will show a *current* list of hosts that matches the criteria - which may change over time.

Available Attributes inside the criteria editor
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

<<<<<<< HEAD
Inside the criteria editor, you can access host object as well as service object. You can construct an arbitrary complex criteria in Javascript using this object.
=======
Inside the criteria editor, you can access service, and host object for each host. You can construct an arbitrary complex criteria in Javascript using this object (please see below for samples).
>>>>>>> 91d9b554521cf744a2a52213111422fded03324a

*service* object has following attributes

.. code-block:: javascript

    {
        "location": {
            "longitude": "9.9356",
            "latitude": "51.5339",
            "city": "Goettingen",
            "state": "Niedersachsen",
            "postal_code": "37077",
            "country": "DE"
        },
        "admins": ["lookup/person/b600b85a-f344-4f48-b8c8-4ea3016283d6"],
        "id": 13,
        "uuid": "7584634b-0434-4b1d-8d28-23e2cb065f7a.bwctl",
        "name": "GoeGrid bwctl",
        "type": "bwctl",
        "locator": "tcp://perfsonar01.goegrid.gwdg.de:4823",
        "lsid": "wlcg",
        "client_uuid": "7584634b-0434-4b1d-8d28-23e2cb065f7a",
        "sitename": "GoeGrid",
        "createdAt": "2016-01-23T03:18:28.253Z",
        "updatedAt": "2016-01-26T19:14:54.934Z",
        "ma": null,
    }

<<<<<<< HEAD
*host* object has following attributes, although *host* objects are sparsely populated. Please see the Hosts tab for list of all information available.
=======
You can also access host object which is sparsely populated (not all attributes are populated). Please see the Hosts tab in MCA UI for list of all information available.
>>>>>>> 91d9b554521cf744a2a52213111422fded03324a

.. code-block:: javascript

    {
        "info": {
<<<<<<< HEAD
            "hardware_processorcount": "2",
            "hardware_processorspeed": "2666.674 MHz",
            "os_version": "Scientific Linux 6.6 (Carbon)",
            "hardware_memory": "8192 MB",
            "toolkitversion": "3.5.0.6-1.pSPS"
=======
            "hardware_processorcore": "1",
            "os_kernel": "Linux 2.6.32-573.8.1.el6.web100.x86_64",
            "os_name": "Scientific Linux",
            "hardware_processorspeed": "2500.000 MHz",
            "os_version": "6.7 (Carbon)",
            "bundle": "perfsonar-toolkit",
            "net_tcp_maxbuffer_recv": "124928 bytes",
            "net_tcp_autotunemaxbuffer_send": "4194304 bytes",
            "toolkitversion": "3.5.0.6",
            "hardware_cpuid": "Intel(R) Xeon(R) CPU E5-2670 v2 @ 2.50GHz",
            "hardware_processorcount": "1",
            "bundle_version": "3.5.0.6",
            "net_tcp_congestionalgorithm": "cubic",
            "net_tcp_autotunemaxbuffer_recv": "4194304 bytes",
            "hardware_memory": "1877 MB",
            "net_tcp_maxbuffer_send": "124928 bytes"
>>>>>>> 91d9b554521cf744a2a52213111422fded03324a
        },
        "communities": ["ATLAS", "GridKa", "WLCG"],
        "admins": null,
        "hostname": "se4.goegrid.gwdg.de",
        "toolkit_url": "auto",
        "no_agent": false,
        "ip": "134.76.97.128"
    }

Sample Criterias
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Select all hosts from ALGT2 site**

.. code-block:: javascript

    if(service.sitename == "AGLT2") return true;

**Select all hosts from US or Canada** 

.. code-block:: javascript

    if(service.location.country == "US" || service.location.country == "CA") return true;

**Select all hosts from wlcg datasource** 

"wlcg" is from the datasource id that you have defined in the datasource.js

.. code-block:: javascript

    if(service.lsid == "wlcg") return true;

**Select all hosts with hostname containing "cern.ch"** 

.. code-block:: javascript

    if(~host.hostname.indexOf("cern.ch")) return true;

<<<<<<< HEAD
.. note:: Not all hosts has hostname parameter. If you try to access an attribute that does not exist for a particular service, the criteria will throw an exception, and it will not return in the final result.
=======
.. note:: Not all hosts has hostname parameter. If you try to access an attribute that does not exist for a particular service, the criteria will throw an exception for that particular host, and such host will not be included in the hostgroup.
>>>>>>> 91d9b554521cf744a2a52213111422fded03324a

**Select all hosts with toolkit version that starts with "3.5"**

.. code-block:: javascript

<<<<<<< HEAD
    if(~host.info.toolkitversion.indexOf("3.5") === 0) return true;

**Select all hosts**

Simply returning true will select *all* hosts (that provides specified service type from all datasources)
=======
    if(~host.info.toolkitversion.indexOf("3.5")) return true;

**Select all hosts**

Simply returning true will select *all* hosts with a specified service type from all datasources.
>>>>>>> 91d9b554521cf744a2a52213111422fded03324a

.. code-block:: javascript

    return true;

**Select all hosts with more than 2GB of memory**

.. code-block:: javascript

    var memory = parseInt(host.info.hardware_memory);
    if(memory > 2000) return true;

Converting from Dynamic Host Group to Static Host Group
<<<<<<< HEAD
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
=======
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
>>>>>>> 91d9b554521cf744a2a52213111422fded03324a

If you'd like to *freeze* the current search result produced by the dynamic hostgroup, you can simply click *Static* tab and it will copy the current criteria results to static list. 


Please see :doc:`mca_userguide_testspec` next.


