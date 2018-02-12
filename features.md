---
layout: page
title : Features
header : Features List
tagline: expiration and query string
group: navigation
---
{% include JB/setup %}

## Expiration

Expiration is an optional path variable. The default value is configurable.

### X: expiration with no sign

It schedules an event in X seconds, only if no event with the same key exists. It will not change a previous setup event.

Example: 

    curl http://localhost:8181/v1/get/my-expiration-test-key/tb2/10?id=id21
    {
      "timestamp" : 1518130073042,
      "duration" : "0.033897",
      "key" : "my-expiration-test-key",
      "data" : {
        "id" : "id21",
        "name" : "name21",
        "dob" : "1977-01-01"
      },
      "trace_id" : "ee4439a4e71b4933bbbf9da7895a517a"
    }

It gets the data from table tb2 and set expiration of the hash key in redis to 10 seconds.

### +X: expiration with position sign

It schedules an event in X seconds and remove any previously event with the same key. It delays previous set event, if use it before the event happens.

Example: 

    curl http://localhost:8181/v1/get/my-expiration-test-key/tb2/+30?id=id21
    {
      "timestamp" : 1518130362535,
      "duration" : "0.005958",
      "key" : "my-expiration-test-key",
      "data" : {
        "id" : "id21",
        "name" : "name21",
        "dob" : "1977-01-01"
      },
      "trace_id" : "915a4d0ceec24a9992c5273a4c894069"
    }

It gets the data and forces to set the expiration to 30 seconds.

### -X: expiration with negative sign

It schedules an event that occurs every X seconds and remove any previously event with the same key.

Example:

    curl http://localhost:8181/v1/get/my-expiration-test-key/tb2/-3?id=id21
    {
      "timestamp" : 1518130552044,
      "duration" : "0.007691",
      "key" : "my-expiration-test-key",
      "data" : {
        "id" : "id21",
        "name" : "name21",
        "dob" : "1977-01-01"
      },
      "trace_id" : "953294b4e7054eb98b6039833b05c7ff"
    }

It forces to set the expiration is 3 seconds and change the expiration behavior to refresh redis with data from database. 

### 0: expiration is zero

It removes existing event with the same key.

Example:

    curl http://localhost:8181/v1/get/my-expiration-test-key/0
    {
      "timestamp" : 1518130741906,
      "duration" : "0.000798",
      "key" : "my-expiration-test-key",
      "data" : {
        "name" : "name21",
        "dob" : "1977-01-01",
        "id" : "id21"
      },
      "trace_id" : "5fff44317205470ba465ea4caa70e0f1"
    }

It removes any expiration event with hash key my-expiration-test-key. 

## Query String

Query string in request URL will be translated to SQL clauses and constraints. To facilitate such translation, following keywords are introduced:

* \_IS_NOT_NULL\_
* \_IS_NULL\_
* \_IS_NOT_FALSE\_
* \_IS_NOT_TRUE\_
* \_IS_TRUE\_
* \_IS_FALSE\_
* \_NOT_LIKE\_
* \_LIKE\_
* \_NOT_REGEXP\_
* \_REGEXP\_
* \_GT\_
* \_GE\_
* \_LT\_
* \_LE\_
* \_EQ\_
* \_NE\_

SQL limit clause is supported.

Also the conventional operators: =, !=, >, >=, <, <=, <> are supported, url encoding may need.

Examples:

### 1) limit

    curl http://localhost:8181/v1/select/tb1?limit=3
    =>
    select * from tb1 limit 3

### 2) =

    curl http://localhost:8181/v1/select/tb1?id=1&id=2&id=3
    =>
    select * from tb1 where (id = "1" OR id = "2" OR id = "3")

### 3) \_REGEXP\_

    curl http://localhost:8181/v1/select/tb1?name_REGEXP_name1
    =>
    select * from tb1 where (name REGEXP "name1")
