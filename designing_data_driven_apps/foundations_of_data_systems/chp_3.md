# Storage and Retrieval
## Data Structures That Power Your Database
### Hash Indexes
**A hashmap** is a data structure that pairs keys to values. It uses a hash function to calculate an index into an array of buckets, from which the desired value can be found. Simply put, it's like a real-world dictionary where you know the 'word' (key), and you quickly find its meaning' (value).

They work by taking a key (like a book title) and running it through a hash function, which gives us a unique 'address' where we can find our data (like the book). This means no matter how big our data set gets, we can access data instantly - it's like magic!

**Compaction** means throwing away duplicate keys in a log and keeping only the most recent update for each key.

### SSTables and LSM-Trees
SSTable = Sorted Strings Table

LSM-Trees = Log Structured Merge Trees

**SSTables** are immmutable, sorted, key-value storage files used in databases for efficient read and write operations. They are written sequentially and allow fast lookups with min. disk space.

**LSM-Trees** are a write optimised data structure that uses a hierarcy of increasingly larger SSTables. Data is first written to an in-memory (e.g. Memtable), periodically flushed to disk as SSTables, and merged during compaction.

**Bloom filters** are used to optimise database searches for keys that do not exist. It is a memory efficient data strucutre for approx. contents of a set.

**Write amplification**: One write to the database resulting in multiple writes to the disk over the course of the database's lifetime. 

### B-Trees

"ubiquitous" - present, appearing, or found everywhere.

Disk v. Memory &rarr;
* Memory is what your computer uses to store data temporarily, while storage (i.e. disk) is where you save files permenantly. 
* Analogy: Desk & a filing cabinet. When you're working on a task you keep the file on your desk (memory) and when you're done with it you put it in the filing cabinet (desk).

How to make B-Trees reliable?
* WAL: Write-ahead log (crash recovery)
* Latches: Light weight locks (concurrency)


### Other Indexing Structures
**Heap file**: The key in an index is the value you search for, and the value is a reference to the row (document, vertex) stored elsewhere (AKA nonclustered).

**Clustered index**: The key in an index is the value you search for and the value in this case is the indexed row.

**Covering index / Index with included columns**: Only some of the table's columns are stored within the index.

**Multi-column index**:
* Concatenated index combines several fiels by appending one column to another.
* Multi-dimensional index allows several columns to be queried on at onces. Commonly through R Trees.

**Levenshtein theorem**: The levenshtein distance between two words is the min. number of single character edits (insertions, deletions, or substitutions) required to change one word into the other.

Advantages of storing data on disk (in comparison to RAM):
* They are durable. Contents are not lost if the power is turned off.
* They have a lower cost per gigabyte than RAM.

**Anti-caching approach** evits the least recently used data from memory to disk when there is not enough memory and loads it back into memory when it is accessed again. This unlocks the ability to support databases larger than available memory.

**A swap file** is a file on a computers hard drive that acts as virtual memory. It works by the OS pretending it has more RAM by writing to a swap file tht can be moved between RAM and hard disk.

## Transaction Processing or Analytics?
**A transaction** refers to a group of reads and writes that form a logical unit.

ACID:
- A - Atomicity
- C - Consistency
- I - Isolation
- D - Durability

**OLTP**: Online Transaction Processing

**OLAP**: Online Analytic Processing

A **data warehouse** is a seperate database used to support OLAP queries. It contains a read-only copy of the data.

The data model of a data warehouse is most commonly relational.

## Column-Oriented Storage
**Column-oriented storage**: Store all the values from one column together. This way the files of the columns used in the query used to be parsed.

### Column Compression
**Vector processing** is a computer method that can process numerous data components at once. It operates on every element of the entire vector in one operation to avoid the overhead of the processing loop.

Way of sorting column-oriented data: Store replicas of column-oriented data in different sort orders (similar approach to optimising the data on adding secondary indexes to row-oriented data).

###Aggregation: Data Cubes and Materialized Views
Materialized views is creating a cache of common queries.
