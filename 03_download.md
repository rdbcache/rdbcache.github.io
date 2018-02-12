---
layout: page
title : Download
header : Download
tagLine: install
group: navigation
---
{% include JB/setup %}

## Install rdbcache

rdbcache is a java application. It requires Java version 1.8+ runtime environment.

For linux:

    wget https://raw.githubusercontent.com/rdbcache/rdbcache/master/download/install
    sh install
    check if OK
    rdbcache -v
    rm install

For Windows:

    click https://raw.githubusercontent.com/rdbcache/rdbcache/master/download/rdbcache.tar.gz
    click and open rdbcache.tar.gz
    run in command prompt, check if OK
    java -jar rdbcache.jar -v

## Setup

we need to setup environment variables and database

Put following environment variables in your /root/.bash_profile.
Please replace the values with the proper ones for your environment.

    export RDBCACHE_PORT=8181
    export REDIS_SERVER=localhost
    export DATABASE_NAME=testdb
    export DATABASE_SERVER=localhost
    export DB_USER_NAME=dbuser
    export DB_USER_PASS=rdbcache

run from console

    rdbcache
