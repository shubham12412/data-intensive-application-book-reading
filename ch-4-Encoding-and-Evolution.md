This means that old and new versions of the code, and old and new data formats, may potentially all coexist in the system at the same time. In order for the system to continue running smoothly, we need to maintain compatibility in both directions:

### Backward compatibility
Newer code can read data that was written by older code.

### Forward compatibility
Older code can read data that was written by newer code.

Backward compatibility is normally not hard to achieve: as author of the newer code, you know the format of data written by older code, and so you can explicitly handle it (if necessary by simply keeping the old code to read the old data). Forward compatibility can be trickier, because it requires older code to ignore additions made by a newer version of the code.


***In this chapter we will look at several formats for encoding data, including JSON, XML, Protocol Buffers, Thrift, and Avro. In particular, we will look at how they handle schema changes and how they support systems where old and new data and code need to coexist. We will then discuss how those formats are used for data storage and for communication: in web services, Representational State Transfer (REST), and remote procedure calls (RPC), as well as message-passing systems such as actors and message queues.***


## Formats for Encoding Data

Programs usually work with data in (at least) two different representations:

1) In memory, data is kept in objects, structs, lists, arrays, hash tables, trees, and so on. These data structures are optimized for efficient access and manipulation by the CPU (typically using pointers).

2) When you want to write data to a file or send it over the network, you have to encode it as some kind of self-contained sequence of bytes (for example, a JSON document). Since a pointer wouldn’t make sense to any other process, this sequence-of-bytes representation looks quite different from the data structures that are normally used in memory

Thus, we need some kind of translation between the two representations. The translation from the in-memory representation to a byte sequence is called encoding (also known as serialization or marshalling), and the reverse is called decoding (parsing, deserialization, unmarshalling)

TERMINOLOGY CLASH \
Serialization is unfortunately also used in the context of transactions (see Chapter 7), with a completely different meaning. To avoid overloading the word we’ll stick with encoding in this book, even though serialization is perhaps a more common term.
 
As this is such a common problem, there are a myriad different libraries and encoding formats to choose from. Let’s do a brief overview.

----------------------------------------------------------------------------------------------------------------------

## Language-Specific Formats 

Many programming languages come with built-in support for encoding in-memory objects into byte sequences. For example, Java has java.io.Serializable [1], Ruby has Marshal [2], Python has pickle [3], and so on. ***Many third-party libraries also exist, such as Kryo for Java*** [4].

***These encoding libraries are very convenient, because they allow in-memory objects to be saved and restored with minimal additional code. However, they also have a number of deep problems:***

1) The ***encoding is often tied to a particular programming language***, and reading the data in another language is very difficult. If you store or transmit data in such an encoding, you are committing yourself to your current programming language for potentially a very long time, and precluding integrating your systems with those of other organizations (which may use different languages).

2) In order to restore data in the same object types, the decoding process needs to be able to instantiate arbitrary classes. This is frequently a source of security problems [5]: if an attacker can get your application to decode an arbitrary byte sequence, they can instantiate arbitrary classes, which in turn often allows them to do terrible things such as remotely executing arbitrary code [6, 7].

3) Versioning data is often an afterthought in these libraries: as they are intended for quick and easy encoding of data, ***they often neglect the inconvenient problems of forward and backward compatibility.***

4) ***Efficiency (CPU time taken to encode or decode, and the size of the encoded structure)*** is also often an afterthought. For example, Java’s built-in serialization is notorious for its bad performance and bloated encoding [8].

For these reasons it’s generally a bad idea to use your language’s built-in encoding for anything other than very transient purposes.

-----------------------------------------------------------------------------------------------------------------------

### JSON, XML, and Binary Variants

Moving to standardized encodings that can be written and read by many programming languages, JSON and XML are the obvious contenders. They are widely known, widely supported, and almost as widely disliked. XML is often criticized for being too verbose and unnecessarily complicated [9]. JSON’s popularity is mainly due to its built-in support in web browsers (by virtue of being a subset of JavaScript) and simplicity relative to XML. CSV is another popular language-independent format, albeit less powerful.

JSON, XML, and CSV are textual formats, and thus somewhat human-readable (although the syntax is a popular topic of debate). Besides the superficial syntactic issues, they also have some subtle problems:


