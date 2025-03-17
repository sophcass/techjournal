# Encoding and Evolution
In large applications code changes to align with data format changes often do not happen instantaneously.

Server-side:
* Rolling upgrades / Staged upgrades: Deploying new versions a few nodes at a time.

Client-side:
* User decides when to upgrade.

**Backward compatibility**: Newer code can read data that was written by older code.

**Forward compatibility**: Older code can read data that was written by newer code.

## Formats for Encoding Data
The translation from the in-memory representation of data to a byte sequence is called ***encoding***.

Encoding / serialisation / marshalling

Decoding / parsing / deserialisation / unmarshalling

### Language Specific Encoding Formats
E.g. pickle in Python, Marshal in Ruby

Problems:
* Tied to specific language
* Securrity risks (arbitrary code execution)
* Little versioning
* Bad efficiency

### JSON, XML, and Binary Variants
Examples of standarised encoding:
* JSON: Javascript Object Notation
* XML: Extensible Markup Language
* CSV: Comma Separated Values

Problems:
* Ambiguity around encoding of numbers, resulting in a potential loss in precision.
* Lack of support for binary strings, resulting in workarounds (encoding binary strings as text using Base64), but this is hacky and inflates the data size.
* Schema languages for both XML and JSON are optional, complicated to learn and implement.
* CSV does not have a schema

Example binary encoding languages:
* Apache Thrift
* Protocol Buffers (protobuf)

### Thrift and Protocol Buffers
Both Thrift and Protocol Buffers require a schema.

Using an 81 byte JSON object as an example...

Thrift:
* Developed by Facebook.
* 2 formats:
    1. BinaryProtocol - 59 bytes
    2. CompactProtocol - 34 bytes

Protocol Buffers:
* Developed by Google.
* 33 bytes

#### Schema Evolution

Adding a new field:
* Forward compatibility - add a new field with new tag (no name changes).
* Backward compatibility - new fields must be `optional`.

Removing a field:
* Forward compatibility - only remove an optional field.
* Backward compatibility - You cannot reuse tags.


Changing datatype:
* Possible, but with a few risks, such as truncation of data.
* Protocol Buffers does not have a list/array datatype, but instead has a `repeated` marker. This has the nice effect that you can easily change an `optional` field into a `repeated` field.

### Avro
Avro:
* Developed by Apache.
* Started in 2009 as a subproject of Hadoop**.
* 32 bytes

** Hadoop is an open-source framework for storing & analysing big data for applications.

The main difference between Avro and Thrift/Protocol Buffers is that Avro does not have field tags. This means that the binary data can only be decoded correctly if the code reading the data is using the *exact same schema* as the code that wrote the data. However the key idea with Avro is that the writer's schema and the reader's schema *don't have to be the same*, they only have to be compatible.

#### Schema Evolution
Adding/Removing a field: To maintain compatibility, you may only add or remove a field with a default value.

Changing datatype: Changing the datatype of a field is possible, if Avro can convert the type.

Changing the name: Possible, but tricky. The reader schema can use aliases for field names, so that it can match the old writer's schema names against aliases. i.e. backward compatible, but not forward compatible.

Avro can **dynamically generate schemas** - This is a major advantage over Thrift and Protocol Buffers.

SSL: Secure Sockets Layer
TSL: Transport Layer Security

## Modes of Dataflow
The most common ways how data flows between processes:
* Via databases
* Via service calls
* Via asynchronous message passing

Asynchronous: Happens one after the other.

### Dataflow Through Databases
In a database, the process that writes to the databaase encodes the data, and the process that reads from the database decodes it.

In general, it is commnon for several different processes to be accessing a database at the same time. This could be:
1. Different applications or services.
2. Several instances of the same services.

These difference processes could be using different versions of code. E.g. in a rolling upgrade deployment some instances will have been updated while others haven't yet at any one time.

*Data outlives code* is an observation that code gets deployed and rewritten often, whereas database content and its encoding gets rewritten less often and it needs to be done explicitly.

Rewriting (migrating) data into a new schema is possible, but it is expensive to do on a large dataset. The answer? Schema evolution!

### Dataflow Through Services
Communicating over the network si done through the arrangement of clients & servers. The server exposes an API over the network, which the client connects to, The API exposed by the server is called the *service*.

**AJAX**: Asynchronous Javascript and XML

&rarr; Allows web pages to be updated asynchronously by exchanging data with a web server behind the scenes.

&rarr; Doesn't do a full page reload like regular web requests.

HTTP: Hypertext Transfer Protocol

APIs provide ***encapsulation***. Services impose fine grained restrictions on what the client can or cannot do.

**SOA**: Service-oriented architecture. A.K.A. microservices.

When HTTP is used as the underlying protocol for talking to the service, it is called a **web service**.

**REST**: Representational State Transfer
* A set of architectural principles for designing APIs.


**SOAP**: Simple Object Access Protocol
* An XML-based protocol for exchanging structured messages between systems.

**RPC**: Remote Procedure Calls
* Allows the system to excecute a functions if it were a local function call
* Often used for inter-service communication
* gRPC uses Protocol Buffers

There is no agreement in how API versioning should work. For RESTful APIs common approaches include:
1. Use a version number in the URL
2. Include a version number in the HTTP Accept header

If API keys are used another option is to store the client's API version on the server and allow this version selection to be updated through a separate administration interface.

### Message-Passing Dataflow
Message broker / message queue / message-oriented middleware

**Message broker**: One process sends a message to a naamed queue or topic and the broker ensures that the message is delivered to one or more consumer of or subscribers to that queue or topic.

Examples of open-source message brokers:
* RabbitMQ
* ActiveMQ
* HornetQ
* NATS
* Apache Kafka

What is a distributed actor framework?

A programming model for concurrency in an application that is across multiple nodes.
