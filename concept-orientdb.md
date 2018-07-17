# OrientDB Concepts

## OverView
* An open-source NoSQL DBMS, which contains DBMS + some addtional features : Document and Graph DBMS.
* [NoSQL Database](http://basho.com/resources/nosql-databases/) provides a mechanism for storing and retrieving NON-relational data that refers to data other than tabular data such as document data or graph data. Though they may support SQL-like languages.
* Written in java, so amazingly fast.
* Can store 220,000 records per second on [Commodity Hardware](http://whatis.techtarget.com/definition/commodity-hardware).
* `Pre-Requisite` : MySQL and NoSQL knowledge is gold.

[Official website](http://orientdb.com/orientdb/#)

## Advantage over MongoDB
* MogoDB is purely Document based while OrientDB is a hybrid of Document with graph Engine.
* Query language is built on SQL. while MongoDB has its own language based on JSON.
* Supports [ACID](https://en.wikipedia.org/wiki/ACID) transactions as well as atomic operations.
* MongoDB uses the RDBMS JOINS to create relationship between entities. It has high runtime cost and does not scale when database scale increases.

For comparison btw different NoSQL databases out there... [click here](https://www.arangodb.com/2015/10/benchmark-postgresql-mongodb-arangodb/)

### Problem with JOINS
With document and relational DBMS, the more data you have, the slower the database will perform. Joins have heavy runtime costs. In comparison, OrientDB handles relationships as physical links to the records, assigned only once when the edge is created O(1). 
Compare this to an RDBMS that “computes“ the relationship every single time you query a database O(LogN). With OrientDB, speed of traversal is not affected by the database size. It is always constant regardless if it has one record or 100 billion records. This is critical in the age of Big Data.


## Orientdb Architecture and API Comparison
In OrientDB, we have 3 different APIs: Object API, Document API and the Graph API. 

### Architecture
* The Graph API works on top of the Document API. 
* The Document API contains the Document, Key/Value and Object Oriented models. 
* The Graph API handles the Vertex and Edge relationships.

![](http://www.orientdb.org/images/orientdb-api-stack.png)

### API and their migration
* [Graph API](http://orientdb.com/docs/last/Graph-Database-Tinkerpop.html#graph-api) : Use this Java API if you work with graphs and want portable code across TinkerPop Blueprints implementations. It is easiest to switch to this when migrating from other Graph Databases, such as Neo4J or Titan. If you used TinkerPop standard on these, you can use OrientDB as a drop-in replacement.

* [Document API](http://orientdb.com/docs/last/Document-Database.html#document-api) : Use this Java API if your domain fits Document Database use case with schema-less structures. 
```
It is easiest to switch to this when migrating from other Document Databases, such as MongoDB and CouchDB.
```
* [Object API](http://orientdb.com/docs/last/Object-Database.html#object-api) : Use this Java API if you need a full Object Oriented abstraction that binds all database entities to POJO (that is, Plain Old Java Objects). 
```
It is easiest to switch to this when migrating from JPA applications.
```
## OrientDB as Multi-Model database
Oriendtb is an [multi-model](https://www.oreilly.com/ideas/data-modeling-with-multi-model-databases) which supports Graph, Document, Key/Value and Object models. Each model here is not just a layer, but coexists in one single engine.
### Document Model
* Belongs to NoSql database.
* In [Document model](http://orientdb.com/docs/last/Document-Database.html), data is stored in documents and group of these Documents is called Collection.
* Structure
	
|Relational Model|	Document Model|	OrientDB Document Model|
|-|-|-|
|Table|	Collection|	Class or Cluster|
|Row|	Document|	Document|
|Column|	Key/value pair|	Document field|
|Relationship|	not available|	Link|

### Graph Model
`Not much focus on this model right now.`
* Stores data in form of `Vertices` connected by `Edges`.  

### Key/Value Model
`Not much focus on this model right now.`
* Data can be stored in form of key-value pair.
* Value is generally complex type and supports documents and graph elements as values.


### Object Model
* [Object Model](https://orientdb.com/docs/2.2/Object-Database.html) is inherited from OOP's concept. Supports inheritance, polymorphism.
* Structure

|Relational Model| Object Model| OrientDB Object Model|
|-|-|-|
| table | Class | Class or Cluster|
| Row | Object | Document or Vertex |
| Column | Object Property | Document Field or Vertex/Edge property|
| Relationship | Pointer | Link|



## Basic Concepts in OrientDB
### Record ID(#RID)
* Database server automatically assigns a unit identifier to the `Record` in orientDB as soon as it is generated.
* This unit identifier is called Record ID(RID) and has structure like :

```
(cluster):(position)

(cluster) = cluster identification number

(position) = absolute position of the record in cluster
```

### Record 
* Smallest unit that can be loaded/stored in the database.
* Records can be stored in four types
	* Document
	* Record Bytes
	* Vertex
	* Edge

### Document
* Most flexible record type available in OrientDB.
* Supports [Schema](http://orientdb.com/docs/last/Java-Schema-Api.html) as well as schema-less classes.
* Can be easily handled by export/import in JSON format.
```
{ 
   "id"        : "1201", 
   "name"      : "Jay", 
   "job"       : "Developer", 
   "creations" : [ 
      { 
         "name"    : "Amiga", 
         "company" : "Commodore Inc." 
      }, 		
      { 
         "name"    : "Amiga 500", 
         "company" : "Commodore Inc." 
      } 
   ] 
} 
``` 

### RecordBytes
* Same as `BLOB` type in RDBMS.
	* BLOB is short for **B**inary **L**arge **OB**ject, a collection of binary data stored as a single entity in a database management systems (DBMS). 
	* BLOBs are used primarily to hold multimedia objects such as image, video, and sound, though they can also be used to store programs or even fragments of code.

### Vertex
* Used to store data in form of graph.
* In graph database most basic unit of data is a `node`, which in OrientDB is called a `Vertex`.

### Edge
* RecordType that connects one vertex to another.
* Types : Regular and Lightweight

Difference between these types [...click here](https://orientdb.com/docs/2.2/Lightweight-Edges.html)

### Schema
Orientdb supports Schema-less, Schema-Full and Schema-Hybrid solutions.

* `Schema-full` enables strict-mode at a class-level and sets all fields as mandatory.
* `Schema-less` enables classes with no properties. Default is non-strict-mode, meaning that records can have arbitrary fields.
* `Schema-hybrid` enables classes with some fields, but allows records to define custom fields. This is also sometimes called schema-mixed.

For details on Schema [...click here](http://orientdb.com/docs/last/Schema.html#schema)

### Class
* `Class` for OrientDB is what `Table` is for MySQL. 
* Similar to `Table` in relational database, but here it can be ```Schema-Less```, ```Schema-Full``` or ```mixed```.
* Inheritance is allowed and each class has its own cluster/clusters.
* No of cluster for a class can be more than one. Starting from v2.2, OrientDB automatically creates multiple clusters per each class (the number of clusters created is equals to the number of CPU's cores available on the server) to improve using of parallelism.
* Orientdb uses [strategies](https://orientdb.com/docs/2.2/Cluster-Selection.html) to select the cluster where data is to be stored.

For details on Class [...click here](http://orientdb.com/docs/last/Schema.html#class)

### Cluster
* A Place where group of records are stored.
* By-default : One [cluster](https://orientdb.com/docs/2.1/Tutorial-Clusters.html#clusters) per Class.
* All the records of a class are stored in the same cluster.
* [Types of clusters](https://orientdb.com/docs/2.1/Tutorial-Clusters.html#working-with-clusters) : Physical Cluster and Memory Cluster

For understanding working of clusters [...click here](https://orientdb.com/docs/2.1/Tutorial-Clusters.html#understanding-clusters)

### Relationships
OrientDB supports two types of relationships: referenced and embedded.

* **Referenced** : SOrientDB uses a direct link to the referenced record or records. This allows the database to avoid the costly JOIN operations used by Relational databases.
```
                  customer
  Record A     ------------->    Record B
CLASS=Invoice                 CLASS=Customer
  RID=5:23                       RID=10:2
```
In this example, Record A contains the reference to Record B in the property customer.


* **Embedded** : In the case of embedded relationships, OrientDB contains the relationship within the record. Embedded relationships are stronger than referenced relationships, but the embedded record does not have its own Record ID. Because of this, you cannot reference them directly through other records. The relationship is only accessible through the container record. If the container record is deleted, then the embedded record is also deleted.
```
                  address
  Record A     <>---------->   Record B
CLASS=Account               CLASS=Address
  RID=5:23                     NO RID!
```
Here, Record A contains the entirety of Record B in the property address. You can only reach Record B by traversing the container, Record A and no record ID (#RID) is assigned to the Record B.
```
orientdb> SELECT FROM Account WHERE Address.city = 'Rome'
```

For details on these relationships [...click here](http://orientdb.com/docs/last/Schema.html#relationships)

### Database
* Interface to access the real storage.
* OrientDB stores multiple database types.

For details on databases... [click here](https://orientdb.com/docs/2.2/Concepts.html#database)

### Concurrency and transactions
OrientDB uses Optimistic [Concurrency](https://orientdb.com/docs/2.2/Concurrency.html#concurrency) Control approach which assumes that multiple transactions can complete frequently without interfering with each other.

OrientDB supports optimistic [transactions](https://orientdb.com/docs/2.2/Concurrency.html#transactions). The database does not use locks when transactions are running, but when the transaction commits, each record (document or graph element) version is checked to see if there have been updates from another client. For this reason, you need to code your applications to be concurrency-proof.

For details on transaction and concurrency control... [click here](https://orientdb.com/docs/2.2/Concurrency.html#concurrency)


## Remote vs PLocal Databases
According to the OrientDB official docs, `PLOCAL` and `REMOTE` in the `<database-url> <mode>` argument specifies the type of the connection you're using to connect to the created db and it's different from `<storage-type>` argument which specifies the type of the storage that you want to use.

* `PLOCAL` : If you create a DB in this way, you automatically connect to it in PLOCAL mode. It uses a disk based storage to ensure data persistence. E.g:

```
create database plocal:/path/to/db/dbname document

Creating database [plocal:/path/to/db/dbname] using the storage type [plocal]...
Database created successfully.
```

* `REMOTE` : If you create a database by using REMOTE mode, you have to specify the storage type which can be PLOCAL or MEMORY. E.g.:
	* **PLOCAL** : it uses a disk based storage.
	```
	orientdb {db=dbname}> create database remote:localhost/dbname root root plocal

	Creating database [remote:localhost/dbname] using the storage type [plocal]...
	Connecting to database [remote:localhost/dbname] with user 'admin'...OK
	Database created successfully.
	```
	* **MEMORY** : It uses the volatile memory to create and save the db. It doesn't assure the data persistance and you'll lose all data when you restart the machine. E.g.:

	```
	orientdb {db=dbname}> create database remote:localhost/dbname1 root root memory
	
	Creating database [remote:localhost/dbname1] using the storage type [memory]...
	Connecting to database [remote:localhost/dbname1] with user 'admin'...OK
	Database created successfully.
	```

## Distributed Network configuration
A OrientDB cluster is composed by two or more servers that are the nodes of the cluster. All the server nodes that want to be part of the same cluster must to define the same Cluster Group. 
Note : `Change the name and password of the group to prevent foreign nodes from joining it!`

Setting-up details... [click here](setup-orientdb.md#distributed-server-set-up)

Details on different network configurations... [click here](https://orientdb.com/docs/2.2/Distributed-Configuration.html#automatic-discovery-in-lan-using-multicast) 



## In-Memory Database (HSQLDB substitution)
Orientdb does support in-memory databases which can be used for testing purposes.
* A database using memory storage is designated by a URL of the form `memory:<path>`, for example `memory:test`. 
* A hierarchical path is allowed, for example memory:subdir/test.

Implementation : 
```
ODatabaseDocumentTx databaseDocumentTx = new ODatabaseDocumentTx("memory:testmycode").create();
```
Details on in-memory databases... [click here](https://orientdb.com/docs/2.2/Memory-storage.html#memory-storage)

## Connection Pooling
One of the most common use cases is to reuse the database, avoiding to create it every time. It's also the typical scenario of the Web applications. Instead of creating a new ODatabaseDocumentTx instance all the times, get an available instance from the pool:
```
// OPEN DATABASE using ODatabaseDocumentPool
ODatabaseDocumentTx db = ODatabaseDocumentPool.global().acquire("remote:localhost/petshop", "admin", "admin");

try {
  // YOUR CODE
} finally {
  db.close();
}
```
Remember to always close the database instance using the close() database method like a classic non-pooled database. In this case the database will be not closed for real, but the instance will be released to the pool, ready to be reused by future requests.

## GeoLocation (GeoSpatial Data)
* Before version 2.2, OrientDB had very minimal support for storing and retrieving data and was limited to a pair of `Double` datatype coordinates stored in an OrientDB. Indexing was spatial indexing was created to speedup the quering.

* In version 2.2, support for different types of geometry objects was created, such as :
	* Point (OPoint).
	* Line (OLine).
	* Polygon (OPolygon).
	* MultiPoint (OMultiPoint).
	* MultiLine (OMultiline).
	* MultiPolygon (OMultiPlygon).
	* Geometry collections.

However, since Orientdb uses Lucene Spatial Index for indexing, we will be using Lucene itself for geolocation purpose.

Details on Lucene Spatial Indexing... [click here](https://orientdb.com/docs/2.2/Spatial-Index.html#lucene-spatial)

## Tasks
* Scaling across multiple machines (handled by chaitanya).
* HOW to interact with orientDB.
	* JPA Route : works on single entity manager.
	* Native OrientDB route : need to focus on Object only,not on graph.
* Connection Pooling
	* Does orientDB support it?
    * Can C3PO handle orientDB?
    * Is there any hsqldb substitution for orientDB? Can be used for testing purpose.
    * Are transaction possible?
    * document vs object API
    * JPA merge() vs persist()
    * hazelcast : multicast and ip-based distribution... 
    * Whether orientdb supports geolocations... like postgreDB supports it as native
    * Plocal vs remote databases.

## References
* [Sample OrientDB Code](sample-orientdb)
* [Orientdb Code Documentation](code-orientdb-api.md)
* [Official Website](http://orientdb.com/orientdb/#)
* [Official Documentation](http://orientdb.com/docs/last/)
* [TutorialsPoint](https://www.tutorialspoint.com/orientdb/orientdb_overview.htm)
* [Installation guide from digital ocean](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-orientdb-on-ubuntu-14-04)
* [More Gyan from Petter's random thought](http://pettergraff.blogspot.in/2013/12/orientdb-thanks.html)
* [GeoSpatial Data in OrientDB](https://dzone.com/articles/spatial-module-in-orientdb-22)