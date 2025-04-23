# Replication
Replication means keeping a copy of the same data on multiple machines that are connected via a network.

Why use replication?
- Reduce data latency.
- Increase availability.
- To increase throughput (horizontal scaling).

Replication algorithms include:
- Single-leader
- Multi-leader
- Leaderless

## Leaders and Followers
Each node that stores a copy of the database is called a replica.

Every right to the database needs to be processed by every replica.

**Solution**: **Leader-based** replication (A.K.A active/passive or master-slave replication).
1. Clients sends the write requests to the leader.
2. Leader sends data change to its followers.
3. Clients can read from any replica.

Single leader replication is used in:
- PostgreSQL.
- MySQL
- Oracle Data Guard.
- SQL Server's AlwaysOn Availability Groups.
- MongoDB
- RethinkDB
- Espresso
- Kafka
- RabbitMQ

### Sychronous Versus Asynchronous Replication
Synchronous replication &rarr; Waiting for a response from the follower before reporting success.

Asynchronous replication &rarr; The leader sends a message to its followers, but does not wait for a response.

Main considerations:
- Data consistency - Synchronous replication guarantees this. 
- Durability - In synchronous replication, if one node goes down the whole system goes down.

### Setting Up New Followers
1. Take a consistent snapshot of the leader's database.
2. Copy snapshot to the new follower node.
3. The follower requests all data changes since the snapshot using the leader's replication log.
4. Once the follower has processed the backlog of data changes, it has *caught up*.

### Handling Node Outages
How do you achieve high availability with leader-based replication?
1. Follower failure: Catch-Up Recovery.
    - The follower requests from the leader all the requests that occurred while it was disconnected. 
2. Leader failure: Failover.
    - A folloer is promoted to become a leader.

*Split-brain* is the situation when two nodes both believe they are the leader

### Implementation of Replication Logs
**Statement-based replication**:
* The leader logs every write request (statement) that it executes and sends that statement log to its followers.
* Problems:
    * Non-deterministic transactions (e.g. NOW() could have different values on each replica).
    * Transactions that depend on database data (e.g. auto-incrementing column need to happen in the exact same order).
    * Side effects can produce different values if they are not totally deterministic.

**Write-ahead log (WAL) shipping**:
* The log is an append only sequence of bytes containing all writes to the database.
* Advantages:
    * Avoids sequencing issues.
* Problems:
    * Replication is closely coupled to the storage engine.
    * Multiple versions of storage engines not possible.
    * Upgrades require downtime.

**Logical (row-based) replication**:
* The log is a sequence of records describing writes to database tables at the granularity of rows.
* Advantages:
    * Replication is decoupled from the storage engine internals, allowing for backward compatibility and changed data capture (i.e. sending contents of a database to external systems).

**Trigger-based replication**:
* Replication occurs in the application layer by using database triggers and stored procedures.
* A *trigger* lets you register custom application code that is automatically executed when a data change occurs in a database system.
* Problems:
    * Greater overheads.
    * Prone to bugs and limitations.
* Advantages:
    * Flexibility.

## Problems with Replication Lag
**Eventual consistency**: If you stop writing to a database, the followers will eventually catch up and become consistent with the leader,

**Replication Lag**: The delay between a write happening on the leader and being reflected on a folower.

Approaches for solving replication lag:
1. Reading your own writes
2. Monotonic reads
3. Consistent prefix reads

**Read-After-Write Consistency**:
* If the user is reading something they may have modified, read from the leader.
* For a short length (e.g. a minute) of time after the user modifies the data, read from the leader.
* Client remembers the time of the last write and ensures the replica they are reading from is up to date to the last write.

To be continued... page 164
