# data-intensive-application-book-reading

https://learning.oreilly.com/library/view/designing-data-intensive-applications/9781491903063/ch01.html

![ch1 map](img/ch01-map-alt.png)

Many applications today are data-intensive, as opposed to compute-intensive. Raw CPU power is rarely a limiting factor for these applications—bigger problems are usually the ***amount of data, the complexity of data, and the speed at which it is changing***.


A data-intensive application is typically built from standard building blocks that provide commonly needed functionality. For example, many applications need to:

1) Store data so that they, or another application, can find it again later ***(databases)***

2) Remember the result of an expensive operation, to speed up reads ***(caches)***

3) Allow users to search data by keyword or filter it in various ways ***(search indexes)***

4) Send a message to another process, to be handled asynchronously ***(stream processing)***

5) Periodically crunch a large amount of accumulated data ***(batch processing)***
