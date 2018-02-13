---
layout: page
title : About
header : About
tagline: rdbcache
group: navigation
---
{% include JB/setup %}

I am Sam Wen - an engineer with a passion for writing code and building stuff on top of open source software. In recent multiple projects, when I worked with redis and relational database, I often had the feeling that we are missing something that should already exist. I have been searching for it on the Internet and have not found it. In most cases, I ended up with an unsatisfied, improvised and project specific solutions.

The reason I use redis is because it is in memory. It is fast and widely adapted in the open source community. The reason I am not giving up relational database is because it is on disk. It is safe and reliable. Especially, with the abundance of solutions and experiences available for relational database, no one can easily it throw away.

However, in many cases, database is not the main cause of delay. A big portion of time delay is RTT (round trip time) between application server host and redis or database server host. If without much careful coding, one user click on a web page can easily generate over 100 queries to redis or database. The total delay caused by data accessing will cumulate up to 100 x ( RTT + query time). 

The main idea to address this issue is piping, which is continuously sending multiple requests through a single connection and processing responses as it comes.  It reduces RTT dramatically for the right cases, but also can dramatically increase the complexity of coding flow for improper cases.

rdbcache is my proposal to address the issue. It is the missing piece I have been searching for. It is designed to run in the same host as the application server. The RTT for local loop is about 0.0x millisecond or less. If use unix socket, the number will be even smaller. With such small RTT, programmer can ignore RTT issue at all and coding as the nature logic flow goes. It should be simple, reliable and handy, but no dramatic.

rdbcache uses redis as cache to offers asynchronous database cache api service. It provides eventually consistency between redis and database. rdbcache attempts to bridge the gap between redis and database. rdbcache also helps to address the RTT issue if running at the same host as the application server.
