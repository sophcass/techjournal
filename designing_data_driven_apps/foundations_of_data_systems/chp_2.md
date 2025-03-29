# Data Models and Query Languages
## Realtional Model Versus Document Model
**SQL**: Structured Query Language

**RDBMS**: Relational Database Management System

***Polygot persistence*** is a data architecture approach where an application uses multiple data storage technologies to best fit the specific needs of different data types and operations rather than relying on a single database for everything.

***Impedance mismatch*** is the disparity or misalignment between the data structures and models used by different software components or systems.

- It is a term borrowed from electronics.
- Argued to be true in the awkward translation layer that often exists between application code and the relational database model of tables, rows, and columns.

Document-oriented databases:
- MongoDB
- RethinkDB
- CouchDB
- Espresso

A **document database** is a type of NoSQL database that can be used to store and query data as JSON-like documents

LinkedIn resumé example:
- Could store in a relational database, but for some fields where there could be many values and this complicates the structure that is needed to store this.
- One option is the store data in a `user` table, with the other data (e.g. education), in a separate `education` table using a foreign key reference to the `user` table.
- Another option is the store the data in a JSON model.
- Tables is better if you want to be able to perform analysis on the data at a later stage. However, the impedence mismatch here is apparent.
- JSON representation is better for locality; easier to fetch all the information related to a user in one query. The tree structure of the data is explicit.

**Relational database versus document databases?**

Relational database:
- Pros: Good support for data joins and many-to-one and many-to-many relationships.

Document database:
- Pros: Schema flexibility, good performance due to locality, and for some applications the data is in a structure closer to what is used in the application.

Document databases are often called *schemaless*. However, a more accurate term is *schema-on-read* as the structure of the data is implicit, i.e. the code that reads it assumes a structure.

## Query Languages for Data

**Declaritive query language** requires the user to tell it what pattern of data you want. i.e. what conditions must be met and how the data should be transformed (e.g. grouped, aggregated, sorted).
- SQL

```sql
SELECT * From animals WHERE family = 'Sharks'; 
```

**Imperative code** tells the computer to perform certain operations in a certain order.
- IMS (IBM's Information Management System)
- CODASYL (Conference on DAta SYstems Languages)
```javascript
function getSharks() {
    var sharks = [];
    for (var i = 0; i< animals.length; i++>) {
        sharks.push(animals[i]);
    }
}
```

### MapReduce Querying
**MapReduce** is a programming model for processing large amounts of data in bulk across many machines, popularised by Google.

A limited form of MapReduce is supported by some NoSQL databases, including MongoDB and CouchDB.

It is somewhere between an imperative and declartive query language.

```JavaScript
db.observations.mapReduce(
    function map() {
        var year = this.observationTimestamp.getFullYear();
        var month = this.observationTimestamp.getMonth() + 1;
        emit(year + "-" + month, this.numAnimals);
    },
    function reduce (key, values) {
        return Array.sum(values);
    },
    {
        query: { family: "Sharks" },
        out: "monthlySharkReport"
    }    
)
```

## Graph-Like Data Models
Graph data models are suitable when there are many-to-many complex relationships.

A **graph** consists of two kinds of objects;
1. vertices (aka nodes or entities)
2. edges (aka relationships or arcs)

### Property Graphs

In a property graph each vertex consists of:
- A unique identifier
- A set of outgoing edges
- A set of incoming edges
- A collection of properties (key-value pairs)

Each edge consists of:
- A unique identifier
- The vertex at which the edge starts (the tail vertex)
- The vertex at which the edge ends (the head vertex)
- A label to describe the kind of relationship between the two vertices
- A collection of properties (key-value pairs)

**Pros**:
- Good flexibility. i.e. different kinds of data structure is easy to express (e.g. how different countries call "states" a different name) and granualarity of data (e.g. you can go into less detail on some data paths compared to others).
- Easy to extend to other data types.

**Cypher** is a declartive query language for property graphs.

### Triple Store
A **triple store** is a type of database designed to store and retrieve data in the form of triples (subject-predicate-object) following the Resource Description Framework (RDF) model.
E.g. (Jim, likes, bananas)
- *Jim* is the subject
- *likes* in the predicate
- *bananas* is the object

**SPARQL** is pronounced "sparkle" and is a query language used for triple-stores using the RDF data model.
It stands for SPARQL Protocol and RDF Query Language.

**Datalog**
- Older than Cypher or SPARQL. Provides the foundation that they were built.
- Used in Datomic and Cascalog (used in Hadoop).
- predicate(subject, object)
