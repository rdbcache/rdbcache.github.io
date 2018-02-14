---
layout: page
title: rdbcache
tagline: redis database cache asynchronous api server
---
{% include JB/setup %}

## What is rdbcache?

rdbcache stands for redis database cache. It is an open source database cache server. rdbcache uses redis as cache to offer asynchronous database cache api service. It provides eventually consistency between redis and database. rdbcache attempts to bridge the gap between redis and database.

The asynchronous nature makes rdbcache very fast and useful in many scenarios. Through few simple restful API endpoints, rdbcache offers the convenience for developers to easily take advantage of the powers and benefits of both redis and database.

Links for complete [API List](/01_apis) and [Features](/02_features)

## Quick Start

rdbcache is a java application. It requires Java version 1.8+ runtime environment.

#### For Mac/Linux:

    curl https://raw.githubusercontent.com/rdbcache/rdbcache/master/download/install | sh

    # check if OK
    rdbcache -v

    Put following environment variables in your /root/.bash_profile.
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

## Why & How?

The answer for Why? is in [About page](/04_about). Let’s try to answer How? with examples.

### 1) Get user info from user_table by email david@example.com:

    curl http://rdbcache_server/v1/get/*/user_table?email=david@example.com
    {
      "timestamp" : 1518120211706,
      "duration" : "0.022995",
      "key" : "69766f6c4556450c85bfda45c4bbab0b",
      "data" : {
        "id" : 7,
        "email" : "david@example.com",
        "name" : "David C.",
        "dob" : "1979-11-08"
      },
      "trace_id" : "40337bdb704242b98b5830d8eee37a0a"
    }

A simple API request can query and get data from database.  
The duration is in seconds. It is the time that server used to accomplish the request.

### 2) Use the hash key to get the same user info:

    curl http://rdbcache_server/v1/get/69766f6c4556450c85bfda45c4bbab0b
    {
      "timestamp" : 1518120320262,
      "duration" : "0.00197",
      "key" : "69766f6c4556450c85bfda45c4bbab0b",
      "data" : {
        "dob" : "1979-11-08",
        "id" : "7",
        "name" : "David C.",
        "email" : "david@example.com"
      },
      "trace_id" : "633d1a87d3e748c4a1f27b9a8316a4ae"
    }

The duration goes down by ten fold due to the data is in redis.  
Once created, the hash key will always available until it is deleted by calling delkey or delall API.

### 3) Change the user name from “David C.” to “David Copper”:

    curl -X POST -H "Content-Type: application/json" \
    http://rdbcache_server/v1/put/69766f6c4556450c85bfda45c4bbab0b \
    -d '{"name" : "David Copper"}'
    {
      "timestamp" : 1518120953655,
      "duration" : "0.000495",
      "key" : "69766f6c4556450c85bfda45c4bbab0b",
      "trace_id" : "eb121832da72463ebdc44aa4dad3f28c"
    }

Since put API doesn’t have to send data back, it returns immediately after server receives it.  
The duration reduces to sub-millisecond.  
The put API allows to use partial data to update both redis and database.

### 4) Verify the name change

    curl http://rdbcache_server/v1/get/69766f6c4556450c85bfda45c4bbab0b
    {
      "timestamp" : 1518121599199,
      "duration" : "0.001314",
      "key" : "69766f6c4556450c85bfda45c4bbab0b",
      "data" : {
        "id" : 7,
        "email" : "david@example.com",
        "name" : "David Copper",
        "dob" : "1979-11-08"
      },
      "trace_id" : "5faffb5c973c4be7ad32457b29364ea9"
    }

Name is changed.

### 5) Verify data in database:

    mysql -h database_server -u dbuser -p testdb -e "select * from user_table where id = 7"
    +----+-------------------+--------------+------------+
    | id | email             | name         | dob        |
    +----+-------------------+--------------+------------+
    |  7 | david@example.com | David Copper | 1979-11-08 |
    +----+-------------------+--------------+------------+

Name is changed in database.

### 6) Select 3 rows from tb1

    curl http://rdbcache_server/v1/select/tb1?limit=3
    {
      "timestamp" : 1518122143868,
      "duration" : "0.00574",
      "data" : {
        "2692e593d2464f488f47973d24d8a38b" : {
          "id" : 1,
          "name" : "name11",
          "age" : 10
        },
        "b6122872a4b64da88dc926892bacce49" : {
          "id" : 2,
          "name" : "name12",
          "age" : 21
        },
        "c4ea1bad68db4b499a4ca548e27e7b4d" : {
          "id" : 3,
          "name" : "name13",
          "age" : 32
        }
      },
      "trace_id" : "e578efa6ca9d43a39273eb83bfc6581d"
    }

A simple API request can pull out multiple rows of data from database.  
Query string in URL will be translated to SQL clauses and constraints. 

More examples are available at [API List](/01_apis) and [Features](/02_features).

## Tests

Version 1.0 beta has passed the integration tests, please see [the test result](https://github.com/rdbcache/rdbcache-test/blob/master/rdbcache-test-result.txt). rdbcache test suite is open source and available at [github](https://github.com/rdbcache/rdbcache-test).

## Playing with source code

rdbcache uses maven and build on top of Java Spring Boot 1.5.10. It requires maven 3.5+ and JDK version 1.8+.

    git clone https://github.com/rdbcache/rdbcache.git
    cd rdbcache
    mvn clean spring-boot:run

rdbcache test suite is built on top of PHP Yii2 framework. It requires php 5.4.0+ and Yii2 framework.

    git clone https://github.com/rdbcache/rdbcache-test.git
    cd rdbcache-test
    composer install
    ./rdbcache-test

If you'd like to be added as a contributor to improve the software and add more test cases, please fork the code and/or the test suite.


