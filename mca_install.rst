Installation
######################################

Docker
============

<<<<<<< HEAD
The recommended way to install MCA is to use docker. You should use the docker-engine from the official docker repo 
(as oppose to a distirubtion provided one) as documented here https://docs.docker.com/engine/installation/linux/

* Step 1. Start a mongodb container

::

    docker run --name mca-mongo \
        --restart=always \
        -v /usr/local/mca/mongo:/data/db \
        -e AUTH=no \
        -d tutum/mongodb

Update /usr/local/mca/mongo to where you want to persist mongodb data.

* Step 2. Start MCA cache service container

:: 

    #download a sample config 
    mkdir config
    wget https://github.com/soichih/meshconfig/config/sample.cache.js -O config/index.js

    docker run --name mca-cache \
        --restart=always \
        --link mca-mongo:mongo \
        -v `pwd`/config:/config \
        -d soichih/mca-cache

* Step 3. Start MCA web service container

:: 

    #download a sample config 
    mkdir config
    wget https://github.com/soichih/meshconfig/config/sample.web.js -O config/index.js

    docker run --name mca-web \
        --restart=always \
        --link mca-mongo:mongo \
        -v `pwd`/config:/config \
        -v /usr/local/mca/sqlite:/data \
        -p 443:443 \
        -p 9443:9443 \
        -d soichih/mca-web

Update `/usr/local/mca/sqlite` to where you want to persist sqlite DB content (for user credentials and profile)

* Step 4. Start MCA publisher service container

:: 

    #download a sample config 
    mkdir config
    wget https://github.com/soichih/meshconfig/config/sample.pub.js -O config/index.js

    docker run --name mca-pub \
        --restart=always \
        --link mca-mongo:mongo \
        -v `pwd`/config:/config \
        -p 80:80 \
        -d soichih/mca-pub

You should now be able to access MCA UI at https://<yourhostname>/meshconfig
=======
Start a postgreSQL container for MCA.

::

    docker run --name mca-postgres \
        --restart=always \
        -d postgres


After postgreSQL container starts up, start the MCA container with default configuration / self-signed ssl cert.

:: 

    docker run --name mca \
        --restart=always \
        --link mca-postgres:postgres \
        -p 80:80 \
        -p 443:443 \
        -p 9443:9443 \
        -d soichih/mca

You should now be able to access MCA UI at https://<yourhostname>/meshconfig

RHEL6 / CentOS6 (x86)
==============================

MCA requires postgreSQL >9.2 provided by CentOS SCL, and nodejs from epel.

::

    yum install epel-release
    yum install centos-release-scl

Then, download the latest MCA RHEL6 RPM from `GitRepo <https://github.com/soichih/meshconfig-admin/releases>`_

::

    yum install mca-2.0-X.el6.x86_64.rpm

If this is the first time you have installed MCA, you can run following to initialize DB, generate access token, etc..

::

    service mca setup

Stacking MCA on existing toolkit instance on RHEL6
*****************************************************

.. note:: This instruction is still incomplete.. I need to reconcile with esmond httpd conf

Since MCA requires postgreSQL>9.2, and RHEL/CentOS6 provides postgreSQL8 which is used by toolkit(esmond), postgreSQL92 for MCA needs to be installed along side the postgreSQL8. To do this, you will need to adjust the port used by postgreSQL92 to something other than the default 5432 (5433 will do).

"service mca setup" will fail to create mca user and mcadmin database since it tries to use the postgreSQL8 instance. Do following to setup the postgreSQL92 for MCA.

* Step 1 - Start postgresql92 on port 5433

Create /opt/rh/postgresql92/root/etc/sysconfig/pgsql/postgresql92-postgresql
::
    mkdir /opt/rh/postgresql92/root/etc/sysconfig/pgsql
    echo "export PGPORT=5433" > /opt/rh/postgresql92/root/etc/sysconfig/pgsql/postgresql92-postgresql

Then start postgresql92..

::

    service start postgresql92-postgresql

* Step 2 - create mca user and mcadmin database

::

    su - postgres
    scl enable postgresql92
    psql -p 5433 -c "CREATE ROLE mca PASSWORD 'newpassword' CREATEDB INHERIT LOGIN;"
    psql -p 5433 -c "CREATE DATABASE mcadmin OWNER mca;"

* Step 3 - update mca db config

Edit /opt/mca/mca/api/config/db.js

::

    module.exports = 'postgres://mca:hogehoge@localhost:5433/mcadmin'


RHEL7 / CentOS7 (x86)
=======================

Download the latest MCA RHEL7 RPM from `GitRepo <https://github.com/soichih/meshconfig-admin/releases>`_. 

::

    yum install epel-release
    yum install mca-2.0-X.el7.x86_64.rpm

If this is the first time you have installed MCA, you can run following to initialize postgres DB, generate access token, start apache, MCA, etc..

::

    /opt/mca/mca/deploy/rhel7/setup.sh

If you are upgrading to a new version of MCA, MCA services should automatically restart. If you want to force restarting it anyway, you can run following.

::

    pm2 restart all

Debian
============

TODO.

Post Installation Configuration
###################################

HTTPS Certificate
========================

You will need to request and install a new HTTP/SSL certificate. By default, MCA comes with a self-signed certificate installed on /opt/mca/mca/deploy/conf/ssl/server/self.cert.pem. How to request the HTTP/SSL certificate is out of scope for this document. Please contact an administrator from your campus / institution to find out more.

Once you have obtained your HTTP/SSL certificate, you can install it on /etc/grid-security/http, and you will need to adjust the apache configuration (/etc/httpd/conf.d/apache-mca.conf) to point to your new certificate (for both port 443 and 9443 - if you are using X509 authentication)

::

    <VirtualHost _default:443>
        SSLCertificateFile /etc/grid-security/http/cert.pem
        SSLCertificateKeyFile /etc/grid-security/http/key.pem
        SSLCertificateChainFile /etc/grid-security/http/chain.pem

::

    <VirtualHost _default:9443>
        SSLCertificateFile /etc/grid-security/http/cert.pem
        SSLCertificateKeyFile /etc/grid-security/http/key.pem
        SSLCertificateChainFile /etc/grid-security/http/chain.pem

Firewall
========================

Open following ports on your firewall

* 80: Used to expose generated MeshConfig
* 443: Used for admin UI
* 9443: Used by SCA authentication service to allow X509 based login

For systemd
--------------------

::

    firewall-cmd --add-service=http --zone=public
    firewall-cmd --add-service=http --zone=public --permanent
    firewall-cmd --add-service=https --zone=public
    firewall-cmd --add-service=https --zone=public --permanent
    firewall-cmd --add-port=9443/tcp --zone=public
    firewall-cmd --add-port=9443/tcp --zone=public --permanent

MCA should now be running with the default configuration at https://<yourhostname>/meshconfig

Please see :doc:`mca_configure` next.

>>>>>>> 91d9b554521cf744a2a52213111422fded03324a

