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

