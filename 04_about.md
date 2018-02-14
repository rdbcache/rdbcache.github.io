---
layout: page
title : About
header : About
tagline: rdbcache
group: navigation
---
{% include JB/setup %}

I am Sam Wen - an engineer with passion for writing code and building stuff on top of open source software. For the past few years, when I worked with in-memory cache and relational database, I often had the feeling that we are missing something that should already exist. I have been searching for it on the Internet and have not found it.


### Use both redis and database at the same time in an easy way

The reason I use in-memory cache is because it is fast. When redis becomes widely adapted in the open source community, I switched from memcache to redis. The reason I am still use relational database (i.e., mysql) is because it is on disk and safe. Also there are abundance of open source solutions that use mysql. How can I take advantage of the powers and benefits of both redis and database at the same time in an easy way.


### Code as the business logic flow goes without worrying about RTT

The RTT (round trip time) between application host and database (or redis) server can become a significant portion of request response time. If without much careful design and coding, one user click on a web page can easily generate over 100 queries to database (or redis). The total delay caused by data accessing can cumulate up to 100 x ( RTT + query time). One popular approach to address RTT issue is pipelining. It can reduces RTT  for some cases, but also can dramatically increase the coding complexity for other cases. How can I code as the business logic flow goes without worrying about RTT.


### rdbcache is built to address the two issues. It should be handy and reliable.

rdbcache uses redis as cache to offer asynchronous database cache api service. It provides eventually consistency between redis and database.  

rdbcache also helps to address the RTT issue if running it within the same host as the application server. The RTT for local loop or unix socket is 0.0x millisecond or less. With such small RTT, programmer can ignore the RTT issue at all.
