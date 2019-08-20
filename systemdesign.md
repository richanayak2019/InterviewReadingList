## System design

https://github.com/donnemartin/system-design-primer
https://www.slideshare.net/jboner/scalability-availability-stability-patterns/
http://www.haproxy.org/download/1.2/doc/architecture.txt
https://www.nginx.com/resources/glossary/reverse-proxy-vs-load-balancer/
https://www.puncsky.com/blog/2016-02-13-crack-the-system-design-interview
https://docs.google.com/document/d/1dNKjHICogW5f94MKoBr8wDA3TASbhP0nrcy4eKiuA8Q/edit#

## design uber :- https://puncsky.com/notes/120-designing-uber
https://puncsky.com/hacking-the-software-engineer-interview/?isAdmin=true

## unique id across cluster and unique sequnce id across cluster https://instagram-engineering.com/sharding-ids-at-instagram-1cf5a71e5a5c
https://stackoverflow.com/questions/2671858/distributed-sequence-number-generation/5685869



## sharding in mongo https://docs.mongodb.com/manual/sharding/
mongo replication https://docs.mongodb.com/manual/replication/
https://docs.mongodb.com/manual/applications/replication/

## mongo atomicity
https://docs.mongodb.com/manual/core/transactions/#transactions-and-atomicity
write concern and ackoledge  https://docs.mongodb.com/manual/core/transactions/#read-concern-write-concern-read-preference
mongo is CP , C can we eventualor strong based on reading from secondry or primary. during P mongo elect another primary and so become A hense it is not consistent due to roleback . also can become unavaialbe for 10 seconds


## RDBMS
Why isn't RDBMS Partition Tolerant in CAP Theorem and why is it Available?
there is no concept of P in non distributed sustems. in case primary and secondry relplica secondry become primary in rds.


## kafka is CA https://stackoverflow.com/questions/51375187/why-kafka-is-not-p-in-cap-theorem/51379079
due to arbbitartay node failure it can handle upto n-1 failure . It  can handle 1 billion writes per sond due to no copy feature from kernel to user memeory. consistancy can be controled like mongo to have ack from All isr. in case of failure it has to elect other leader which may not in icr and stopped servicng due to voilation of liner ordering.

## Hbase
Hadoop web site, http://hadoop.apache.org/
HBase web site, http://hbase.apache.org/
HBase wiki, http://wiki.apache.org/hadoop/Hbase HBase Reference Guide http://hbase.apache.org/book/book.html
HBase: The Definitive Guide, http://bit.ly/hbase-definitive-guide
Google Bigtable Paper, http://labs.google.com/papers/bigtable.html or http://research.google.com/archive/bigtable.html
Fallacies of Distributed Computing, http://en.wikipedia.org/wiki/Fallacies_of_Distributed_Computing
