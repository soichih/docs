****
Configuration
****

Most of the configuration files for MCA can be found in **/opt/mca/mca/api/config** directory.

Data Sources
============

**datasource.js** lists all sLS and global lookup service that host information is pulled from. 

Global SLS
----------

A sample datasource entry for Global SLS

::

    "osg": {
        label: 'OSG',
        type: 'global-sls',
        activehosts_url: 'http://ps1.es.net:8096/lookup/activehosts.json',
        query: '?type=service&group-communities=OSG',
    },

* "osg" is the internal key that needs to be unique across all datasources. 
* label is used to show the datasource name in the web UI
* type needs to be "global-sls" for Global SLS datasource.
* activehosts_url should always point to http://ps1.es.net:8096/lookup/activehosts.json unless you know a different global registry.
* query: This query is passed to all sLS instances that are member of the global registry. For more detail on sLS query, please refer to `sLS API Spec <https://github.com/esnet/simple-lookup-service/wiki/APISpec#query>`_

So, the above sample datasource entry instructs mca-cache service to pull all service records (used as "hosts" for MCA) from the global lookup service (ps1.es.net) with community registered to "OSG". If you don't want any hosts from OSG, simply remove this section, or update the label and group-communities to something other than OSG.

As you may find in the default datasource.js, you can list as many datasources as you want. Make sure to use a unique key, and label.

sLS
--------

If you have your private sLS instance, you can cache service ("hosts" for MCA) entries from it by entering something like following

::

    "wlcg": {
        label: 'WLCG',
        type: 'sls',
        url: 'https://soichi7.ppa.iu.edu/sls/lookup/records/?type=service&group-communities=WLCG',
        //exclude: [], //TODO - allow user to remove certain service from appearing in the UI
        cache: 1000*60*5, //refresh every 5 minutes (default 30 minutes)
    },





Authentication / Profile Service
============

MCA uses authentication and profile microservices developed by SCA (Scalable Computing Archive) group at IU. Please refer to <doesn't exist yet> for more information on authentication and profile services.


