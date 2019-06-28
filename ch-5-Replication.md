https://learning.oreilly.com/library/view/designing-data-intensive-applications/9781491903063/ch05.html

Replication means keeping a copy of the same data on multiple machines that are connected via a network. 

There are several reasons why you might want to replicate data:

1) To keep data geographically close to your users (and thus ***reduce access latency***)

2) To allow the system to continue working even if some of its parts have failed (and thus ***increase availability***)

3) To scale out the number of machines that can serve read queries (and thus ***increase read throughput***)


In this chapter we will assume that your dataset is so small that each machine can hold a copy of the entire dataset. In Chapter 6 we will relax that assumption and discuss ***partitioning (sharding) of datasets*** that are too big for a single machine. In later chapters we will discuss various kinds of faults that can occur in a replicated data system, and how to deal with them.


***If the data that you’re replicating does not change over time, then replication is easy: you just need to copy the data to every node once, and you’re done***.

***All of the difficulty in replication lies in handling changes to replicated data***, and that’s what this chapter is about. 

***three popular algorithms for replicating changes between nodes: single-leader, multi-leader, and leaderless replication***. Almost all distributed databases use one of these three approaches. They all have various pros and cons.


***There are many trade-offs to consider with replication: for example, whether to use synchronous or asynchronous replication, and how to handle failed replicas. Those are often configuration options in databases, and although the details vary by database***, the general principles are similar across many different implementations. We will discuss the consequences of such choices in this chapter.

--------------------------------------------------------------------------------------------------------------------------

### Leaders and Followers

Each node that stores a copy of the database is called a replica. With multiple replicas, a question inevitably arises: how do we ensure that all the data ends up on all the replicas?

Every write to the database needs to be processed by every replica; otherwise, the replicas would no longer contain the same data. ***The most common solution for this is called leader-based replication (also known as active/passive or master–slave replication)***. It works as follows:

1) One of the replicas is designated the leader (also known as master or primary). When clients want to write to the database, they must send their requests to the leader, which first writes the new data to its local storage.

2) The other replicas are known as followers (read replicas, slaves, secondaries, or hot standbys).i Whenever the leader writes new data to its local storage, it also sends the data change to all of its followers as part of a replication log or change stream. Each follower takes the log from the leader and updates its local copy of the database accordingly, by applying all writes in the same order as they were processed on the leader.

3) When a client wants to read from the database, it can query either the leader or any of the followers. However, writes are only accepted on the leader (the followers are read-only from the client’s point of view).


 Leader-based (master–slave) replication.
 
This mode of replication is a built-in feature of many relational databases, such as PostgreSQL (since version 9.0), MySQL, Oracle Data Guard [2], and SQL Server’s AlwaysOn Availability Groups [3]. It is also used in some nonrelational databases, including MongoDB, RethinkDB, and Espresso [4]. Finally, leader-based replication is not restricted to only databases: distributed message brokers such as Kafka [5] and RabbitMQ highly available queues [6] also use it. Some network filesystems and replicated block devices such as DRBD are similar.
 
 
 

### Synchronous Versus Asynchronous Replication

An important detail of a replicated system is whether the replication happens synchronously or asynchronously. (In relational databases, this is often a configurable option; other systems are often hardcoded to be either one or the other.)




