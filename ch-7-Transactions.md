https://learning.oreilly.com/library/view/designing-data-intensive-applications/9781491903063/ch07.html#ch_transactions

In the harsh reality of data systems, many things can go wrong:

1) The database software or hardware may fail at any time (including in the middle of a write operation).

2) The application may crash at any time (including halfway through a series of operations).

3) Interruptions in the network can unexpectedly cut off the application from the database, or one database node from another.

4) Several clients may write to the database at the same time, overwriting each other’s changes.

5) A client may read data that doesn’t make sense because it has only partially been updated.

6) Race conditions between clients can cause surprising bugs.


***In order to be reliable, a system has to deal with these faults and ensure that they don’t cause catastrophic failure of the entire system***. However, implementing fault-tolerance mechanisms is a lot of work. It requires a lot of careful thinking about all the things that can go wrong, and a lot of testing to ensure that the solution actually works.


For decades, transactions have been the mechanism of choice for simplifying these issues. ***A transaction is a way for an application to group several reads and writes together into a logical unit. Conceptually, all the reads and writes in a transaction are executed as one operation***: either the entire transaction succeeds (commit) or it fails (abort, rollback). If it fails, the application can safely retry. ***With transactions, error handling becomes much simpler for an application, because it doesn’t need to worry about partial failure—i.e., the case where some operations succeed and some fail (for whatever reason).***


If you have spent years working with transactions, they may seem obvious, but we shouldn’t take them for granted. Transactions are not a law of nature; ***they were created with a purpose, namely to simplify the programming model for applications accessing a database. By using transactions, the application is free to ignore certain potential error scenarios and concurrency issues, because the database takes care of them instead (we call these safety guarantees)***.



***Not every application needs transactions, and sometimes there are advantages to weakening transactional guarantees or abandoning them entirely (for example, to achieve higher performance or higher availability)***. Some safety properties can be achieved without transactions.

***How do you figure out whether you need transactions?*** In order to answer that question, ***we first need to understand exactly what safety guarantees transactions can provide, and what costs are associated with them***. Although transactions seem straightforward at first glance, there are actually many subtle but important details that come into play.

In this chapter, we will examine many examples of things that can go wrong, and explore the algorithms that databases use to guard against those issues. We will go especially deep in the area of concurrency control, discussing various kinds of race conditions that can occur and how databases implement isolation levels such as read committed, snapshot isolation, and serializability.

This chapter applies to both single-node and distributed databases; 

-------------------------------------------------------------------------------------------------------------------------

### The Slippery Concept of a Transaction

Almost all relational databases today, and some nonrelational databases, support transactions. 

In the late 2000s, nonrelational (NoSQL) databases started gaining popularity. They aimed to improve upon the relational status quo by offering a choice of new data models (see Chapter 2), and by including replication (Chapter 5) and partitioning (Chapter 6) by default. Transactions were the main casualty of this movement: many of this new generation of databases abandoned transactions entirely, or redefined the word to describe a much weaker set of guarantees than had previously been understood [4]

With the hype around this new crop of distributed databases, there emerged a popular belief that transactions were the antithesis of scalability, and that any large-scale system would have to abandon transactions in order to maintain good performance and high availability [5, 6]. On the other hand, transactional guarantees are sometimes presented by database vendors as an essential requirement for “serious applications” with “valuable data.” Both viewpoints are pure hyperbole.

The truth is not that simple: ***like every other technical design choice, transactions have advantages and limitations. In order to understand those trade-offs, let’s go into the details of the guarantees that transactions can provide***—both in normal operation and in various extreme (but realistic) circumstances.

### The Meaning of ACID
The safety guarantees provided by transactions are often described by the well-known acronym ACID, which stands for Atomicity, Consistency, Isolation, and Durability. ***It was coined*** in 1983 by Theo Härder and Andreas Reuter [7] ***in an effort to establish precise terminology for fault-tolerance mechanisms in databases.***

-----------------------------------------------------------------------------------------------------------------------

### ATOMICITY
Rather, ACID atomicity describes what happens if a client wants to make several writes, but a fault occurs after some of the writes have been processed—for example, a process crashes, a network connection is interrupted, a disk becomes full, or some integrity constraint is violated. If the writes are grouped together into an atomic transaction, and the transaction cannot be completed (committed) due to a fault, then the transaction is aborted and the database must discard or undo any writes it has made so far in that transaction.

Without atomicity, if an error occurs partway through making multiple changes, it’s difficult to know which changes have taken effect and which haven’t. The application could try again, but that risks making the same change twice, leading to duplicate or incorrect data. ***Atomicity simplifies this problem: if a transaction was aborted, the application can be sure that it didn’t change anything, so it can safely be retried.***

***The ability to abort a transaction on error and have all writes from that transaction discarded is the defining feature of ACID atomicity. Perhaps abortability would have been a better term than atomicity, but we will stick with atomicity since that’s the usual word.***


---------------------------------------------------------------------------------------------------------------------

### CONSISTENCY

