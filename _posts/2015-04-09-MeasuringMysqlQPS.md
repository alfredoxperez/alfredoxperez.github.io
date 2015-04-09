---
title: "Measuring MySQL Queries Per Second through 'mysqladmin status'"
tags: [mysql qps mysqladmin status]
---

The status command for [mysqladmin](http://dev.mysql.com/doc/refman/5.6/en/mysqladmin.html) is a good way to see the qps in mysql but there are some pitfalls to be aware of...

I can easily run the command to get the qps along with some other data points:  

`mysqladmin -u user -p status`  



The output of this command looks like:  

`Uptime: 522  Threads: 5  Questions: 373562  Slow queries: 344  Opens: 73  Flush tables: 1  Open tables: 66  Queries per second avg: 715.636`  



... and I get an average QPS! Nice, but what does average QPS mean?  Is the average taken from a rolling window or for all time? The answer is in the output and it's since mysql has been up.  For example; we can take the number of "Questions", which is the count of queries to mysql, and divide it by the "Uptime" to get the avg QPS shown: 

`373562 / 522 = 715.6360153256705`



That's not so nice.  This can be a good way to measure the qps if my application is constantly running, but if it has downtime or if it runs only at certain times then the value really isn't accurate.  What's more is that the "flush-status" command does not flush the "Uptime" and "Questions" values so the only way to reset those values is to restart mysql.

Despite the caveats of this functionality it can be very helpful to track and diagnose performance in a testing environment where restarting mysql is possible for test runs.


Environment used:

  * Mac OS X 10.10.2
  * Java 1.7.0_80-ea
  * MySQL 5.6.20