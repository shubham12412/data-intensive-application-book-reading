In Part I of this book, we discussed aspects of data systems that apply when data is stored on a single machine. Now, in Part II, we move up a level and ask: what happens if multiple machines are involved in storage and retrieval of data?


#### There are various reasons why you might want to distribute a database across multiple machines:

1) ***Scalability***
If your data volume, read load, or write load grows bigger than a single machine can handle, you can potentially spread the load across multiple machines.

2) ***Fault tolerance/high availability****
If your application needs to continue working even if one machine (or several machines, or the network, or an entire datacenter) goes down, you can use multiple machines to give you redundancy. When one fails, another one can take over.

3) ***Latency***
If you have users around the world, you might want to have servers at various locations worldwide so that each user can be served from a datacenter that is geographically close to them. That avoids the users having to wait for network packets to travel halfway around the world.

-------------------------------------------------------------------------------------------------------------------

## Scaling to Higher Load

If all you need is to scale to higher load, the simplest approach is to buy a more powerful machine (sometimes called ***vertical scaling or scaling up***). Many CPUs, many RAM chips, and many disks can be joined together under one operating system, and a fast interconnect allows any CPU to access any part of the memory or disk. In this kind of ***shared-memory architecture***, all the components can be treated as a single machine

The problem with a shared-memory approach is that the cost grows faster than linearly: a machine with twice as many CPUs, twice as much RAM, and twice as much disk capacity as another typically costs significantly more than twice as much. And due to bottlenecks, a machine twice the size cannot necessarily handle twice the load.

### Shared-Nothing Architectures

By contrast, shared-nothing architectures [3] (sometimes called ***horizontal scaling or scaling out***) have gained a lot of popularity. In this approach, each machine or virtual machine running the database software is called a node. Each node uses its CPUs, RAM, and disks independently. Any coordination between nodes is done at the software level, using a conventional network.

While a distributed shared-nothing architecture has many advantages, it usually also incurs additional complexity for applications and sometimes limits the expressiveness of the data models you can use.




### Replication Versus Partitioning

There are two common ways data is distributed across multiple nodes:

### Replication
Keeping a copy of the same data on several different nodes, potentially in different locations. Replication provides redundancy: if some nodes are unavailable, the data can still be served from the remaining nodes. Replication can also help improve performance. 

### Partitioning
Splitting a big database into smaller subsets called partitions so that different partitions can be assigned to different nodes (also known as ***sharding***). 


These are separate mechanisms, but they often go hand in hand


