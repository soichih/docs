Installation
######################################

Docker
============

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

