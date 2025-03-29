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

LinkedIn resumé example:
- Could store in a relational database, but for some fields where there could be many values and this complicates the structure that is needed to store this.
- One option is the store data in a `user` table, with the other data (e.g. education), in a separate `education` table using a foreign key reference to the `user` table.
- Another option is the store the data in a JSON model.
- Tables is better if you want to be able to perform analysis on the data at a later stage. However, the impedence mismatch here is apparent.
- JSON representation is better for locality; easier to fetch all the information related to a user in one query. The tree structure of the data is explicit.
