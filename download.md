---
layout: page
title : Download
header : Download
tagline: install
group: navigation
order: 3
---
{% include JB/setup %}

## Install rdbcache

rdbcache is a Java application. It requires Java version 1.8+ runtime environment.

#### For Mac/Linux:

    curl https://raw.githubusercontent.com/rdbcache/rdbcache/master/download/install | sh

    # check if OK
    rdbcache -v

    Put following environment variables in your ~/.bash_profile.
    Please replace the values with the proper ones for your environment.

    export RDBCACHE_PORT=8181
    export REDIS_SERVER=localhost
    export DATABASE_NAME=testdb
    export DATABASE_SERVER=localhost
    export DB_USER_NAME=dbuser
    export DB_USER_PASS=rdbcache

    rdbcache

#### For Windows:

click [download rdbcache.zip](https://raw.githubusercontent.com/rdbcache/rdbcache/master/download/rdbcache.zip)

    # check if OK
    java -jar rdbcache.jar -v

    Please replace the values with the proper ones for your environment.

    SET RDBCACHE_PORT=8181
    SET REDIS_SERVER=localhost
    SET DATABASE_NAME=testdb
    SET DATABASE_SERVER=localhost
    SET DB_USER_NAME=dbuser
    SET DB_USER_PASS=rdbcache

    java -jar rdbcache.jar
