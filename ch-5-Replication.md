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






