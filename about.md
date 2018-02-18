---
layout: page
title : About
header : About
tagline: rdbcache
group: navigation
order: 5
---
{% include JB/setup %}

I am Sam Wen - an engineer with a passion for writing code and building cool things on top of open source software. While working on my previous projects, I needed the in-memory cache for a relational database to improve performance. I felt like something like this should already exist but I couldn't find a robust project that fullfilled my needs, so I made rdbcache.


### Use both redis and database at the same time in an easy way

The reason I use the in-memory cache is because it is fast for both reads and writes. When redis became widely adapted in the open source community, I switched from memcache to redis. The reason I am still use relational database (ex. mysql) is because it is on disk and a safe way to store data. There is also an abundance of open source solutions that use mysql. The goal of rdbcache is to leverage the advantages of redis and the relational database.


### Code with business logic flow without worrying about RTT

The RTT (round trip time) between application host and database (or redis) server can become a significant portion of request response time. Without careful design and coding, a single user clicking on a web page can easily generate over 100 queries to the database (or redis). The total delay caused by data accessing can cumulate up to 100 x ( RTT + query time). One popular approach to address RTT issue is [pipelining](https://en.wikipedia.org/wiki/Software_pipelining). If it's properly implemented it can reduce RTT, but it also dramatically increases the coding complexity. With rdbcache, focus on business logic and worry less about performance.


### rdbcache is built to address the two issues. It should be powerful and reliable.

rdbcache uses redis as a cache to offer an asynchronous database cache api service. It provides [eventual consistency](https://en.wikipedia.org/wiki/Eventual_consistency) between redis and database.  

rdbcache also helps to address the RTT issue if it runs within the same host as the application server. The RTT for local loop or unix socket is less than a hundredth of millisecond. With such negligible RTT, a programmer can ignore RTT entirey.
