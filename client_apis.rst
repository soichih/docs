**********************
perfSONAR Client API 
**********************

perfSONAR includes APIs to retrieve perfSONAR data from the measurement archive (called esmond) and from the Lookup Service (called sLS).

Since all perfSONAR APIs are REST-based, standard tools like 'curl' can be used. We also
have a some libraries and scripts that make data retrieval easy.

Measure Archive APIs and clients: 
  * http://software.es.net/esmond/perfsonar_client_perl.html
  * http://software.es.net/esmond/perfsonar_client_python.html
  * https://pypi.python.org/pypi/esmond_client/ 

Lookup Service APIs and clients:
   * https://github.com/esnet/simple-lookup-service/wiki/ClientAPI
   * https://pypi.python.org/pypi/sls-client/

perfSONAR Dashboard (MaDDash) API:
   * http://software.es.net/maddash/index.html#api

Toolkit Host configuration API:
   * perfSONAR toolkit host configuration info can be retrieved using a URL of this format:
      * http://your-perfsonar-hostname/toolkit/index.cgi?format=json

API Support
===========

For support for using these APIs, send email to the perfSONAR user list.
