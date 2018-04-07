---
layout: page
title : API List
header : API List
tagline: restful application interface
group: navigation
order: 1
---
{% include JB/setup %}

## Basic

All API endpoints have the same simple pattern:

    /rdbcache/version/api_name/hash_key[/value][/table_name][/expiration][/sync][?query_string]

For example:

    curl http://rdbcache_server/rdbcache/v1/get/*/user_table?email=david@example.com
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

- \* (asterisk) in URL indicates that server will generate a hash key for the return data.
- user_table can be any table with primary key or unique key.
- timestamp is the server system time at before sending the response.
- duration is in seconds. It is the time that server used to accomplish the request.
- key is the hash key. It also maps to the data record through primary (or unique) key.
- trace_id is an id for late retrieving error(s) associated with this request.

By default, in order to response fast, rdbcache provides asynchronous API services as much as possible. It is also provide option (sync) to force an API call response synchronously. It means that the API call returns after database and redis updated.  This feature is useful when caller of the API wants to make sure the operation is completed before doing anything else. 

For example:

    curl http://rdbcache_server/rdbcache/v1/get/*/user_table/sync?email=david@example.com

## Summary

There are 11 API endpoints: get, set, getset, put, pull, push, select, save, delkey, delall and trace:

#### [1. /rdbcache/v1/get/hash_key](#api-1) 

&nbsp;&nbsp;&nbsp;&nbsp;Get data from redis or from database.

#### [2. /rdbcache/v1/set/hash_key](#api-2)

&nbsp;&nbsp;&nbsp;&nbsp;Set value in redis and in database.

#### [3. /rdbcache/v1/getset/hash_key](#api-3)

&nbsp;&nbsp;&nbsp;&nbsp;Get current value and set new value in redis and/or in database.

#### [4. /rdbcache/v1/put/hash_key](#api-4)

&nbsp;&nbsp;&nbsp;&nbsp;Update redis and database with partial data.

#### [5. /rdbcache/v1/pull](#api-5)

&nbsp;&nbsp;&nbsp;&nbsp;Get multiple entries from redis or database.

#### [6. /rdbcache/v1/push](#api-6)

&nbsp;&nbsp;&nbsp;&nbsp;Set multiple entries in redis and database.

#### [7. /rdbcache/v1/select](#api-7)

&nbsp;&nbsp;&nbsp;&nbsp;Select multiple entries from database and populate the redis.

#### [8. /rdbcache/v1/save](#api-8)

&nbsp;&nbsp;&nbsp;&nbsp;Save multiple entries into database and populate the redis.

#### [9. /rdbcache/v1/delkey](#api-9)

&nbsp;&nbsp;&nbsp;&nbsp;delete hash key(s) from redis.

#### [10. /rdbcache/v1/delall](#api-10)

&nbsp;&nbsp;&nbsp;&nbsp;delete hash key(s) and data from both redis and database.

#### [11. /rdbcache/v1/trace](#api-11)

&nbsp;&nbsp;&nbsp;&nbsp;get error messages by trace id.

## API Services

### 1. /rdbcache/v1/get/hash_key
<a id="api-1"></a>
* method: get
* option1: table name
* option2: expiration
* option3: sync
* option4: query_string

Get data from redis or from database.

This action finds the data by the hash key from the redis server. If no data is found, it queries the database by condition defined by the query string. Once the data is found, it returns to the client immediately. Then, it asynchronously set the hash key in redis server to the data found from database server if no data is found from redis server, and setups key expiration features.

Example:

    curl http://localhost:8181/rdbcache/v1/get/my-hash-key/tb1?id=1
    {
      "timestamp" : 1518035863297,
      "duration" : "0.009557",
      "key" : "my-hash-key",
      "data" : {
        "id" : 1,
        "name" : "name11",
        "age" : 10
      },
      "trace_id" : "96955ba6b7f34de680a1a9fa48b31c7e"
    }

### 2. /rdbcache/v1/set/hash_key
<a id="api-2"></a>
* method: get, post
* option1: value (required for get, invalid for post)
* option2: table name
* option3: expiration
* option4: sync
* option5: query_string

Set value in redis and in database.

This action returns to the client immediately, after it receives the request. Then, it asynchronously sets the hash key to the value in redis server, and updates/inserts the database with the value based on condition defined by the query string, and setups key expiration features.

Examples:

    curl -X POST -H "Content-Type: application/json" \
    'http://localhost:8181/rdbcache/v1/set/set-test-key/tb1' \
    -d '{"name":"mike","age":"20"}'
    {
      "timestamp" : 1518035902257,
      "duration" : "0.004225",
      "key" : "set-test-key",
      "trace_id" : "b47293d08ea44f739d13fe6f7d7110b0"
    }

    curl 'http://localhost:8181/rdbcache/v1/get/set-test-key/tb1'
    {
      "timestamp" : 1518035935168,
      "duration" : "0.007012",
      "key" : "set-test-key",
      "data" : {
        "id" : 152,
        "name" : "mike",
        "age" : 20
      },
      "trace_id" : "2b7f32ac3c1c4bbc85361b83da3991d9"
    }

### 3. /rdbcache/v1/getset/hash_key
<a id="api-3"></a>
* method: get, post
* option1: value (required for get, invalid for post)
* option2: table name
* option3: expiration
* option4: sync
* option5: query_string

Get current value and set new value in redis and/or in database.

This action finds the data by the hash key from the redis server. If no data is found, it queries the database by condition defined by the query string. Once the current value is found, it returns to the client immediately. Then, it asynchronously sets the hash key to the new value in redis server, and updates the database with the new value, and setups key expiration features.

Examples:

    curl -X POST -H "Content-Type: application/json" \
    'http://localhost:8181/rdbcache/v1/getset/set-test-key' \
    -d '{"id":152,"name":"mike","age":"29"}'
    {
      "timestamp" : 1518036014387,
      "duration" : "0.0119",
      "key" : "set-test-key",
      "data" : {
        "id" : 152,
        "name" : "mike",
        "age" : 20
      },
      "trace_id" : "2720821d17d1400bb2289d12829a61fa"
    }

    curl 'http://localhost:8181/rdbcache/v1/get/set-test-key'
    {
      "timestamp" : 1518036166498,
      "duration" : "0.007404",
      "key" : "set-test-key",
      "data" : {
        "id" : 152,
        "name" : "mike",
        "age" : 29
      },
      "trace_id" : "b747722de6a54899842b21777802cf34"
    }

### 4. /rdbcache/v1/put/hash_key
<a id="api-4"></a>
* method: post
* option1: table name
* option2: expiration
* option3: sync
* option4: query_string

Update redis and database with partial data.

This action returns to the client immediately, after it receives the request. Then, it asynchronously updates the hash key with the posted partial data in redis server, and updates the database server with the partial data based on condition defined by the query string, and setups key expiration features.

Examples:

    curl -X POST -H "Content-Type: application/json" \
    'http://localhost:8181/rdbcache/v1/put/set-test-key' \
    -d '{"age":"30"}'
    {
      "timestamp" : 1518036233773,
      "duration" : "0.005936",
      "key" : "set-test-key",
      "trace_id" : "025e44bbb6aa429cb5b897d4e155653f"
    }

    curl 'http://localhost:8181/rdbcache/v1/get/set-test-key'
    {
      "timestamp" : 1518036261586,
      "duration" : "0.007767",
      "key" : "set-test-key",
      "data" : {
        "id" : 152,
        "name" : "mike",
        "age" : 30
      },
      "trace_id" : "8b1c26c84c6047feb7b4d68557a12ae7"
    }

### 5. /rdbcache/v1/pull
<a id="api-5"></a>
* method: post
* option1: table name
* option2: expiration
* option3: sync

Get multiple entries from redis or database.

It checks the redis and the database.  Once result is concluded for every key, it returns the results to the client immediately. Then, it asynchronously synchronizes redis server by the list of hash keys and database server based on the condition previously saved, and setups key expiration features.

Examples:

    curl 'http://localhost:8181/rdbcache/v1/select/tb2?id=id22&id=id23'
    {
      "timestamp" : 1518037011556,
      "duration" : "0.002116",
      "data" : {
        "2c27a9441ad345f1911e97a9cc61aa02" : {
          "id" : "id22",
          "name" : "name22",
          "dob" : "2010-03-19"
        },
        "caf8aa3e449b41198ab05f43efc8d030" : {
          "id" : "id23",
          "name" : "name23",
          "dob" : null
        }
      },
      "trace_id" : "3b62e1d56a914188bdef86ebc0196647"
    }

    curl -X POST -H "Content-Type: application/json" \
    'http://localhost:8181/rdbcache/v1/pull/tb2' \
    -d '["2c27a9441ad345f1911e97a9cc61aa02","caf8aa3e449b41198ab05f43efc8d030"]'
    {
      "timestamp" : 1518037298293,
      "duration" : "0.007802",
      "data" : {
        "2c27a9441ad345f1911e97a9cc61aa02" : {
          "id" : "id22",
          "name" : "name22",
          "dob" : "2010-03-19"
        },
        "caf8aa3e449b41198ab05f43efc8d030" : {
          "id" : "id23",
          "name" : "name23",
          "dob" : null
        }
      },
      "trace_id" : "f9d12826d6d74d309c9e929164b88bf8"
    }

### 6. /rdbcache/v1/push
<a id="api-6"></a>
* method: post
* option1: table name
* option2: expiration
* option3: sync

Set multiple entries in redis and database.

It returns to the client immediately, after it receives the request. Then, it asynchronously sets the posted key and value map in redis server, and updates the database server with the data based on the condition previously saved, and setups key expiration features.

Examples:

    curl -X POST -H "Content-Type: application/json" \
    'http://localhost:8181/rdbcache/v1/save/tb1' \
    -d '[{"name":"new_name001","age":11}, {"name":"new_name002","age":12},{"name":"new_name003","age":13}]'
    {
      "timestamp" : 1518038120506,
      "duration" : "0.000471",
      "data" : [ "145e31830c3c4c9f876c8293a1d526d2", "d129d4b9e1a549bd832f9c454b118388", "ad09ca20fa2d453faf4bbe3a6beda2e3" ],
      "trace_id" : "04bde76dae594e1eb4c1ff10f8f723de"
    }

    curl -X POST -H "Content-Type: application/json" \
    'http://localhost:8181/rdbcache/v1/pull/tb1' \
    -d '[ "145e31830c3c4c9f876c8293a1d526d2", "d129d4b9e1a549bd832f9c454b118388", "ad09ca20fa2d453faf4bbe3a6beda2e3" ]'
    {
      "timestamp" : 1518038361008,
      "duration" : "0.013523",
      "data" : {
        "145e31830c3c4c9f876c8293a1d526d2" : {
          "id" : 156,
          "name" : "new_name001",
          "age" : 11
        },
        "d129d4b9e1a549bd832f9c454b118388" : {
          "id" : 157,
          "name" : "new_name002",
          "age" : 12
        },
        "ad09ca20fa2d453faf4bbe3a6beda2e3" : {
          "id" : 158,
          "name" : "new_name003",
          "age" : 13
        }
      },
      "trace_id" : "e3068c903458432a9b1f1a0dfead95c2"
    }

### 7. /rdbcache/v1/select
<a id="api-7"></a>
* method: get
* option1: table name
* option2: expiration
* option3: sync
* option4: query_string

Select multiple entries from database and populate the redis.

It selects data from the database by condition defined by the query string. Once the query is done, it returns to the client immediately. Then, it asynchronously saves the data into redis server, and setups key expiration features.

Examples:

    curl 'http://localhost:8181/rdbcache/v1/select/tb2?id=id21&id=id22&id=id23'
    {
      "timestamp" : 1518039068206,
      "duration" : "0.003632",
      "data" : {
        "16a282c3dc09427ca5987b90c81c47be" : {
          "id" : "id21",
          "name" : "name21",
          "dob" : "1977-01-01"
        },
        "eab936ebd21341a79626ee634c96932f" : {
          "id" : "id22",
          "name" : "name22",
          "dob" : "2010-03-19"
        },
        "bec73523101e4b8da7ff577fb587afec" : {
          "id" : "id23",
          "name" : "name23",
          "dob" : null
        }
      },
      "trace_id" : "a16838c58a9149c0b41468dff9dc26ff"
    }

### 8. /rdbcache/v1/save
<a id="api-8"></a>
* method: post
* option1: table name
* option2: expiration
* option3: sync

Save multiple entries into database and populate the redis.

It returns to the client immediately, after it receives the request. Then, it asynchronously saves the posted data to database, and saves the data into redis server, and setups key expiration features.

Examples:

    curl -X POST -H "Content-Type: application/json" \
    'http://localhost:8181/rdbcache/v1/save/tb1' \
    -d '[{"name":"new_name011","age":11}, {"name":"new_name012","age":12},{"name":"new_name013","age":13}]'
    {
      "timestamp" : 1518039226016,
      "duration" : "0.000583",
      "data" : [ "d1ad378409d54f0db8a31beaeca9e005", "38a6d4f5cb3c43f8bf6bbbf9972957b1", "4378ec1afcb044918efb25c64599c825" ],
      "trace_id" : "190a3cb0449b476f9d7bb4ef0199b685"
    }

    curl -X POST -H "Content-Type: application/json" \
    'http://localhost:8181/rdbcache/v1/pull/tb1' \
    -d '[ "d1ad378409d54f0db8a31beaeca9e005", "38a6d4f5cb3c43f8bf6bbbf9972957b1", "4378ec1afcb044918efb25c64599c825" ]'
    {
      "timestamp" : 1518039407381,
      "duration" : "0.011928",
      "data" : {
        "d1ad378409d54f0db8a31beaeca9e005" : {
          "id" : 169,
          "name" : "new_name011",
          "age" : 11
        },
        "38a6d4f5cb3c43f8bf6bbbf9972957b1" : {
          "id" : 170,
          "name" : "new_name012",
          "age" : 12
        },
        "4378ec1afcb044918efb25c64599c825" : {
          "id" : 171,
          "name" : "new_name013",
          "age" : 13
        }
      },
      "trace_id" : "3e70f6684745489f9cd3ffdb2d39917c"
    }

### 9. /rdbcache/v1/delkey
<a id="api-9"></a>
* method: get, post
* option1: hash_key (required for get, invalid for post)
* option2: table
* option3: sync

delete hash key(s) from redis.

It returns to the client immediately, after it receives the request. Then, it asynchronously deletes hash key(s) from redis, and related key info from database, and remove associated key expiration features.

Examples:

    curl -X POST -H "Content-Type: application/json" \
    'http://localhost:8181/rdbcache/v1/delkey/tb1' \
    -d '[ "d1ad378409d54f0db8a31beaeca9e005", "38a6d4f5cb3c43f8bf6bbbf9972957b1", "4378ec1afcb044918efb25c64599c825" ]'
    {
      "timestamp" : 1518039518088,
      "duration" : "0.000872",
      "data" : [ "d1ad378409d54f0db8a31beaeca9e005", "38a6d4f5cb3c43f8bf6bbbf9972957b1", "4378ec1afcb044918efb25c64599c825" ],
      "trace_id" : "eaaf1b30e9ca40b992780bf053464ded"
    }

    curl 'http://localhost:8181/rdbcache/v1/get/d1ad378409d54f0db8a31beaeca9e005/tb1'
    {
      "timestamp" : 1518039631228,
      "status" : 404,
      "error" : "Not Found",
      "exception" : "com.doitincloud.rdbcache.exceptions.NotFoundException",
      "message" : "data not found",
      "path" : "/rdbcache/v1/get/d1ad378409d54f0db8a31beaeca9e005"
    }

    curl 'http://localhost:8181/rdbcache/v1/get/*/tb1?id=169'
    {
      "timestamp" : 1518040004299,
      "duration" : "0.005519",
      "key" : "2a3069babaa9459b97d42d3951f534c4",
      "data" : {
        "id" : 169,
        "name" : "new_name011",
        "age" : 11
      },
      "trace_id" : "7628cede4a14482db2f5e729ff7f40b7"
    }

### 10. /rdbcache/v1/delall
<a id="api-10"></a>
* method: get, post
* option1: hash_key (required for get, invalid for post)
* option2: table
* option3: sync

delete hash key(s) and data from both redis and database.

It returns to the client immediately, after it receives the request. Then, it asynchronously deletes hash key(s) from redis, and related key info and data from database, and remove associated key expiration features.

Examples:

    curl 'http://localhost:8181/rdbcache/v1/delall/e245408bd7ae439a81fe4b79394dfdac/tb1'
    {
      "timestamp" : 1518040295477,
      "duration" : "0.000707",
      "key" : "e245408bd7ae439a81fe4b79394dfdac",
      "trace_id" : "cee544086b724b0e84863fc028fe64d0"
    }

    curl 'http://localhost:8181/rdbcache/v1/get/e245408bd7ae439a81fe4b79394dfdac/tb1'
    {
      "timestamp" : 1518040397510,
      "status" : 404,
      "error" : "Not Found",
      "exception" : "com.doitincloud.rdbcache.exceptions.NotFoundException",
      "message" : "data not found",
      "path" : "/rdbcache/v1/get/e245408bd7ae439a81fe4b79394dfdac"
    }

    curl 'http://localhost:8181/rdbcache/v1/get/*/tb1?id=168'
    {
      "timestamp" : 1518040476732,
      "status" : 404,
      "error" : "Not Found",
      "exception" : "com.doitincloud.rdbcache.exceptions.NotFoundException",
      "message" : "data not found",
      "path" : "/rdbcache/v1/get/*/tb1"
    }

### 11. /rdbcache/v1/trace
<a id="api-11"></a>
* method: get, post
* option1: trace_id (required for get, invalid for post)

get error messages by trace id.

A trace id is sent back in responses for all API calls. This trace id allows user to retrieve error messages if there is any error caused by the API call.

Example:

    curl 'http://localhost:8181/rdbcache/v1/trace/cee544086b724b0e84863fc028fe64d0'
    {
      "timestamp" : 1518040688356,
      "duration" : "0.00357",
      "key" : "cee544086b724b0e84863fc028fe64d0",
      "data" : {
        "0" : {
          "timestamp" : 1518040397485,
          "message" : "data not found",
          "trace" : "<init>@NotFoundException.java#36<-get@RedisDbaseCacheApis.java#105"
        }
      },
      "trace_id" : "148fc6dd67b343b082bd46615c73541f"
    }

