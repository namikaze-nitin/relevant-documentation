
# OrientDB Implementation in java

## Document API
This documentation will progress with `How to work with Document API` concept which will include basic CRUD operation and database handling. Rest of the code and details can be found in the project [`orientdb_poc`](https://github.com/namikaze-nitin/orientdb-neurosys-poc).

### Relevant Package Locations in project `orientdb_poc`
* Class : `DocumentCRUDDao.java`
	* Location : `src/main/java` > `com.neurosys.orientdb.poc.dao.document`
	* Purpose : Code consist of functions performing CRUD operations  on Document database.
* Class : `TestDocumentCRUDDao.java`
	* Location : `src/test/java` > `com.neurosys.orientdb.poc.document`
	* Purpose : Code to test `DocumentCRUDDao.java`

### Steps involved in working with Document API
#### Step 1 : Create Document Database
Orientdb provides `ODatabaseDocumentTx` class to create document databases. Different ways of creating Document databases :
* Creating `in-memory` database
```
ODatabaseDocumentTx documentDatabase=new ODatabaseDocumentTx("memory:test-native-document");
documentDatabase.create();
```
* Using via localhost through the `remote` interface
```
ODatabaseDocumentTx db = new ODatabaseDocumentTx("remote:localhost/test-native-document").open("admin", "admin_passwd");
```
* Using `plocal` database from the local file system
```
ODatabaseDocumentTx db = new ODatabaseDocumentTx("plocal:/tmp/database/test-native-document").create();
```
>More on database creation... [click here](https://orientdb.com/docs/2.2/Document-API-Database.html#creating-new-databases)

#### Step 2 : Creating new Class and its Properties
Classes can be created in `Schema-Less`, `Schema-Full` or `Schema-Hybrid` form. `Schema-Less` and `Schema-Full` methods are mentioned here.
>More about schema and classes... [click here](https://orientdb.com/docs/3.0.x/gettingstarted/Tutorial-Classes.html#working-with-classes)

##### Schema-Full Mode
>Using `Schema-Full` mode is necessary while using `Indexes` or `Constraints` on a class as this mode allows us to create properties.

* *Class creation*:	
	To access the schema API in Java, you need the Schema instance of the database you are using.
	```
	OSchema schema = db.getMetadata().getSchema();
	```
	Create a persistent class in Java using the method `createClass()`. 
	``` xml
	<!--This method creates the class `Restaurant` in the database. It simultaneously creates the physical cluster `restaurant` where records related to this class will be stored  -->
	OClass oClass = schema.createClass("ClassName");
	```

* *Properties creation*:
	```
	class1.createProperty("Field1", OType.INTEGER);
	class1.createProperty("Field2", OType.STRING);
	```

##### Schema-Less Mode
For schema-less mode, no additional steps are required to create class as it gets automatically created while saving the document record into database.

#### Step 3 : Creating `Document` record
* Creating a document for a particular class
```
ODocument doc = new ODocument("ClassName");
```
* Assigning field values to this document
```
doc.field("Field1", 1);
doc.field("Field1", "test");
```

#### Step 4 : Saving Document into database
* When working with `Schema-Less` class, we are not defining any class during database creation or after that. So, as soon as document is saved into database, a class gets created with the same name as mentioned in ODocument constructor argument.
* When working with `Schema-Full` mode, passed field data gets saved in the properties specified during the class creation.
```
doc.save();
```

#### Step 5 : CRUD operations
CRUD operations are performed in `DocumentCRUDDao.java` class of project `orientdb_poc`. Basic syntax of those operations are been mentioned here.

##### Create
Similarly as mentioned above we can store another record belonging to to a different class.
```
ODocument animal = new ODocument("Animal");
animal.field( "name", "Gaudi" );
animal.field( "location", "Madrid" );
animal.save();
```

##### Retrieve
* Browse all the documents in a cluster
```
for (ODocument doc : db.browseCluster("ClusterName")) {
	System.out.println( doc.field("Field1") );
}
```
* Browse all records of a class
```
for (ODocument animal : database.browseClass("ClassName")) {
      System.out.println( animal.field("Field1"));
}
```
* Count records of a class
```
long count = database.countClass("ClassName");
```
* Count records of a cluster
```
long count = database.countCluster("ClusterName");
```
* Browse using Query execution
>Orientdb supports a subset of [SQL](https://github.com/sslavic/orientdb-wiki/blob/master/Document-Database.md#execute-a-query) that allows it to process links to documents and graphs.

```
 List<ODocument> result = db.query(new OSQLSynchQuery<ODocument>("select * from ClassName"));
```

##### Update
You can [update](https://orientdb.com/docs/2.2/Document-Database.html#updating-documents) persistent documents through the Java API by modifying the document instance and then calling save() on the database.
```
doc.field( "Field1", 2 );
doc.save();
```

##### Delete
Through the Java API, you can [delete](https://orientdb.com/docs/2.2/Document-Database.html#deleting-documents) documents through the delete() method on the loaded document instance. 
* Delete single record 
```
doc.delete();
```
* Deleting all records
```
for (ODocument doc : database.browseClass("ClassName"))
	doc.delete();
```

#### Step 6 : Drop database
```
db.drop();
```
> For other database related queries [click here](code-orientdb-api.md#database-queries) 

> End of Document API basics, will update it in case something new is found.

## Object API

This documentation will progress with `How to work with Object API` concept which will include basic DAO operations and database handling. Rest of the code and details can be found in the project `orientdb_poc`.

For Official documentation of Object API go [here](http://orientdb.com/docs/2.2.x/Object-Database.html#object-api)

### Relevant Package Locations in project `orientdb_poc`
* Package : `com.neurosys.orientdb.poc.dao.object`
	* Location : `src/main/java`
	* Purpose : Code consist of functions performing basic DAO operations on Object database.
* Package : `com.neurosys.orientdb.poc.object`
	* Location : `src/test/java` 
	* Purpose : Code to test DAO operations on Object Database

### Steps involved in working with Object API

#### Step 1 : Create Object Database
Orientdb provides `ODatabaseObjectTx` class to create object databases. Different ways of creating Object databases :
* Creating `in-memory` database
```
ODatabaseObjectTx objectDatabase=new ODatabaseObjectTx("memory:test-object");
objectDatabase.create();
```
* Using via localhost through the `remote` interface
```
ODatabaseObjectTx db = new ODatabaseObjectTx("remote:localhost/test-object").open("admin", "admin_passwd");
```
* Using `plocal` database from the local file system
```
ODatabaseObjectTx db = new ODatabaseObjectTx("plocal:/tmp/database/test-object").create();
```

More on [Object Database Creation](http://orientdb.com/docs/2.2.x/Object-Database.html#use-the-database)

#### Step 2 : Creating new classes and properties 

Classes can be created in `Schema-Less`, `Schema-Full` or `Schema-Hybrid` form. `Schema-Less` and `Schema-Full` methods are mentioned here.

##### Schema Full

* Create the Class

```
	OClass className = database.getMetadata().getSchema().createClass("className");
```	

* Define properties of the class : Depending on the property type the syntax for creating a property is different
	* Property which is not of a relationship type
	Example:
	```
	className.createProperty("id", OType.INTEGER);
	```
	* Property which is of a relationship
	Example:
	```
	className.createProperty("customer", OType.LINK, customer);
	```

##### Schema Less

* Create an Entity class . Ex: Person.java

* Register this entity class in the database
```
database.getEntityManager().registerEntityClass(className);
```	

##### Schema Hybrid

* Create and register an entity class
```
database.getEntityManager().registerEntityClass(className);
```	
* Get the registered class from the database
```
database.getMetadata().getSchema().getClass(className);
```		

#### Step 3 :  CRUD Operations	

The functions are defined within the package `com.neurosys.orientdb.poc.dao.object` within the source folder `src/main/java`

##### Persist Data

```
className object = db.newInstance(className.class,<list of class data>);  		
database.save(object);
```

##### Retrieve all records

```
for (className object : database.browseClass(className.class)) 
	objectlist.add(object);
return objectlist;
```

##### Retrieve certain record

```
String sql = "select * from className where classProperty = '"+classPropertyValue+"'";
List<className> s =database.query(new OSQLSynchQuery<className>(sql));
return s.get(0);
```


#### Step 4 : Testing of the DAO Layer

* Create an In-memory database for testing
    ```
     OObjectDatabaseTx db = new OObjectDatabaseTx("memory:test");
    ```
 
* Create some database classes preferably some with relation
	* Classes with no relationships need only be [registered.](#schema-less)
	* Classes with relationships will require [dynamic property creation after registering.](#schema-hybrid)
* Make data entries using the Object DAO Layer
* Test the data persisted in the database 	
    ```
    classObject.getRecordByName("recordName");
    ```
Example
``` sub1.getSubjectByName("Math") ```


## Database Queries
### Java Classes Used
Basic structure of java classes and their data-members used for querying :
* Class : `Student`
    ```
    public class Student{
    	private Integer stuId;
    	private String name;
    	private String surname;
    	private Subject subject;
    }
    ```
* Class : `Teacher`
    ```
    public class Teacher{
    	private Integer tId;
    	private String name;
    	private String surname;
    	private Subject subject;
    }
    ```
* Class : `Subject`
    ```
    public class Subject{
    	private Integer subId;	
    	private String name;
    }
    ```

### Querying from Document Database
* [Create a database](code-orientdb-api.md#steps-involved-in-working-with-document-api) instance before trying any querying operations. Example of an in-memory database : 
    ```
    ODatabaseDocumentTx db = new ODatabaseDocumentTx("memory:test").create();
    ```

* `ODatabaseDocument` provides some methods to interact with the database via SQL statements and scripts:
	* `query()` methods are inteded to execute idempotent statements only (eg. SELECT, MATCH, TRAVERSE).
	* `command()` methods can execute any type of statements, idempotent (SELECT, MATCH...), non idempotent (INSERT, UPDATE, DELETE...) and DDL (CREATE CLASS, CREATE PROPERTY...)

	```
	OResultSet query(String query, Object... positionalParamas)
	OResultSet query(String query, Map namedParams);

	OResultSet command(String query, Object... positionalParams);
	OResultSet command(String query, Map namedParams);

	```
* `ODocument` type records saved in database for Sample Classes mentioned above are created in [Schema Less Mode](code-orientdb-api.md#step-2-creating-new-class-and-its-properties).

#### SELECT
Sql statement to query: `String selectQuer="SELECT FROM " + Student.class.getSimpleName();`

##### Using `db.query()` : 
```
List<ODocument> resultSet = db.query(new OSQLSynchQuery(selectQuer));
```

##### Using `db.command()`:
Method 1 : Using [`OCommandSQL`](http://orientdb.com/javadoc/latest/com/orientechnologies/orient/core/sql/OCommandSQL.html) : SQL command request implementation
```
OCommandSQL commandQuer = new OCommandSQL(selectQuer);
List <ODocument> resultSet = db.command(commandQuer).execute();
```

Method 2 : Using [`OSQLSynchQuery`](http://orientdb.com/javadoc/latest/com/orientechnologies/orient/core/sql/query/OSQLSynchQuery.html) : SQL synchronous query
```
OSQLSynchQuery<ODocument> syncQuer=new OSQLSynchQuery<ODocument>(selectQuer);
List <ODocument> resultSet = db.command(commandQuer).execute();
```

> NOTE : No need to use projection '*' for selecting all the records in OrientDB.
> Relevant Links : 
* [SELECT Command](https://orientdb.com/docs/2.2/SQL-Query.html) 
* [Querying database in SQL](https://orientdb.com/docs/3.0.x/java/Java-Query-API.html#querying-the-database-in-sql)


#### INSERT
* Insert operation returns the `same` record which is persisted using the query.

```
String insertObj="INSERT INTO " + Student.class.getSimpleName() + " (sid,name,surname,subject) VALUES (?,?,?,?)";
OCommandSQL commandQuer = new OCommandSQL(insertQuer);
ODocument result = db.command(commandQuer).execute(1111,"Nitin","Sharma",null);
```

> Relevant Links : 
* [INSERT Command](https://orientdb.com/docs/2.2/SQL-Insert.html)

#### UPDATE
* Update operation returns 1 if query is successfully executed.

```
String updateQuer = "UPDATE Student SET name='Rajat' WHERE name='Nitin'";
OCommandSQL commandQuer = new OCommandSQL(updateQuer);
Integer updateResult = db.command(commandQuer).execute();
```

> Relevant Links : 
* [UPDATE Command](https://orientdb.com/docs/2.2/SQL-Update.html)

#### DELETE
* Update operation returns 1 if query is successfully executed.

```
/*
 * DELETE a record saved in class 'Student' of database db
 */
String deleteQuer="Delete from Student WHERE sid='3333'";
int deleteResponse = db.command(new OCommandSQL(deleteQuer)).execute();
```
> Relevant Links : 
* [DELETE Command](https://orientdb.com/docs/2.2/SQL-Delete.html)

#### COUNT
```
String countQuer = "SELECT COUNT(*) from Student WHERE surname='Sharma'";
List <ODocument>resultSet = db.command(new OCommandSQL(count)).execute();				
```

#### COUNT with GROUPBY clause
```
String countQuer = "SELECT COUNT(stuId),subject.name FROM Student GROUP BY subject";
List <ODocument>resultSet = db.command(new OCommandSQL(countQuer)).execute();
```	
> Relevant Links : 
* [Group-By clause reference](https://orientdb.com/docs/2.2/Tutorial-SQL.html#group-by)

#### Inserting Document into Document
In following query example, aim is to insert a record of `Subject` class as a `class property` into a record of Student class.  
```
/*
 * Inserting Document into Document
 * 
 * ODocument record which is to be inserted as a property(here it is of class Subject), needs to be persisted first.
 * After persisting, this record can be passed as a 'value' into field of an another record(Student).
 */
ODocument docSubject=new ODocument("Subject");
docSubject.field("subId","1111");
docSubject.field("name","Networking");
docSubject.save();

String insertStudent="INSERT INTO STUDENT (stuId,name,surname,subject) VALUES (?,?,?,?)";
db.command(new OCommandSQL(insertStudent)).execute("12346","John","Targeryn",docSubject);
```

Access this subject property using command:
```
OCommandSQL docInDoc=new OCommandSQL("SELECT subject.name AS subName FROM Student WHERE sid='12346'");
List <ODocument>resultSet = db.command(docInDoc).execute();
Assert.assertEquals("Networking",resultSet.get(0).field("subName"));
```

#### JOIN 3 Tables

Here, a new set of Classes will be created to explain the `JOIN` operation better.
* Class : `Country`, Properties : (`String countryId`,`String name`,`String continent`)
* Class : `City`, Properties : (`String cityId`,`String name`,`ODocument country`)
* Class : `Employee`, Properties : (`String empId`,`String name`,`String surname`,`ODocument city`)

Important Notes :
* Most important difference between OrientDB and a Relational Database is that relationships are represented by `LINKS` instead of `JOINs` in OrientDB. 
* For this reason, the classic JOIN syntax is not supported. 
* OrientDB uses the "dot (.) notation" to navigate LINKS.

In OrientDB a `Join` operation would be:

```
String joinStatement= "SELECT FROM Employee WHERE city.country.name='Pakistan'";
List<ODocument> rs = db.command(new OCommandSQL(joinStatement)).execute();
```

In SQL, you might create a join like:
```
SELECT *
FROM Employee A, City B, Country C,
WHERE A.city = B.cityId
AND B.country = C.countryId
AND C.name = 'Pakistan' 
```

> For details on Links and Joins, [...click here](https://orientdb.com/docs/2.2/SQL.html#joins)

> For other query related references [...click here](code-orientdb-api.md#references)




## Transactions in Orientdb
* Create a Database
```
ODatabaseDocumentTx database = new ODatabaseDocumentTx("memory:test");
database.create();
```

* Create an Orientdb database Class and enter some record
```
ODocument teach=new ODocument("Teacher");
teach.field("tId","1111");
teach.field("firstName","Rajat");
teach.field("sirName","Nayak");
teach.field("subject",null);
```

* Transaction must be carried out within a `try-catch`
```
try{
	database.begin(TXTYPE.OPTIMISTIC);
	...
	// WRITE HERE YOUR TRANSACTION LOGIC
	...
	database.commit();
}catch( Exception e ){
	e.printStacktrace();
	database.rollback();
}
```

> Relevant Links : 
* [Transactions](https://orientdb.com/docs/2.2/Transactions.html)
* [Transaction Propagation](https://orientdb.com/docs/2.2/Transaction-propagation.html)









## Geo-Location
### Set-UP
* **Eclipse** : 
Other than already added dependencies in pom.xml, add following :
	* Lucene Spatial index : indexing when using sql
		
	```
	<dependency>
	    <groupId>com.orientechnologies</groupId>
	    <artifactId>orientdb-spatial</artifactId>
	    <version>2.2.22</version>
	</dependency>
	```
	* OrientDB Lucene Full Text Index : supports special query functions
 	```
	<dependency>
	    <groupId>com.orientechnologies</groupId>
	    <artifactId>orientdb-lucene</artifactId>
	    <version>2.2.22</version>
	</dependency>
	```
* **Command Line** : 
Download plugin jar of same version as your orientdb server from :  
```
http://central.maven.org/maven2/com/orientechnologies/orientdb-spatial/2.2.22/orientdb-spatial-2.2.22-dist.jar
```

All Set : Lets Code
### Code
#### Step 1 : Create a `ODatabaseDocumentTx` instance
```
ODatabaseDocumentTx databaseDocumentTx = new ODatabaseDocumentTx("memory:testmycode").create();
		
``` 
#### Step 2 : Create Schema For Your Class
* To access the schema API in Java, you need the Schema instance of the database you are using.
```
OSchema schema = databaseDocumentTx.getMetadata().getSchema();
```
* Create a persistent class in Java using the method `createClass()`. 

``` xml
<!--This method creates the class `Restaurant` in the database. It simultaneously creates the physical cluster `restaurant` where records related to this class will be stored  -->
OClass oClass = schema.createClass("Restaurant");
```

* Create Properties and assign their types
```	
oClass.createProperty("name",OType.STRING);
oClass.createProperty("location",OType.EMBEDDED);
```
>Details on [Embedded relationship](http://orientdb.com/docs/2.0/orientdb.wiki/Concepts.html#embedded-relationships)

#### Step 3 : Create some test records
* Create record of class "OPoint" which will act as [embedded](http://orientdb.com/docs/2.0/orientdb.wiki/Concepts.html#embedded-relationships) objects for storing location.
	* Class `OPoint` is by default created in your database if you have correctly done your setup.

	* This class has a field called `location` which is used to store an ArrayList consisting of two DOUBLE type value: (latitude,longitude).
	* REMEMBER, embedded records are not saved and can only be accessed by the class(eg:here we are using `Restaurant`) using them.
```
ODocument locationDoc1 = new ODocument("OPoint");
locationDoc1.field("coordinates", Arrays.asList(12.4684735, 41.8914114));		
```
```
ODocument locationDoc2 = new ODocument("OPoint");
locationDoc2.field("coordinates", Arrays.asList(12.5684735, 41.8914114));
```
```	
ODocument locationDoc3 = new ODocument("OPoint");
locationDoc3.field("coordinates", Arrays.asList(0, 1));
```

* Create `ODocument` type records for class(eg:`Restaurant`) whose SCHEMA was generated earlier and store location in them.
```
ODocument hotel1= new ODocument("Restaurant");
hotel1.field("name","Dar Poeta");
hotel1.field("location",locationDoc1);
hotel1.save();		
```
```
ODocument hotel2= new ODocument("Restaurant");
hotel2.field("name","ye bhi Dar Poeta");
hotel2.field("location",locationDoc2);
hotel2.save();
```
```	
ODocument hotel3= new ODocument("Restaurant");
hotel3.field("name","fir se Dar Poeta");
hotel3.field("location",locationDoc3);
hotel3.save();
```	

* Create [spatial-index](https://orientdb.com/docs/2.2/Spatial-Index.html#lucene-spatial) on `location` field
```
databaseDocumentTx.command(new OCommandSQL("CREATE INDEX Restaurant.location ON Restaurant(location) SPATIAL ENGINE LUCENE"));
```
> **Note** : Spatial-Index can [also be created](https://orientdb.com/docs/2.2/Spatial-Index.html#how-to-create-a-spatial-index) while creating schema for `Restaurant` class. But there are issues which people are facing and are yet not resolved. 

#### Step 4 : Try Out Some Queries
Here is just syntax mentioned for quering records. Each one will return `ODocument` type results, access them as per your need.

* **ST_Within** : all points(i.e. Restaurants) within Polygon boundary (sent using coordinates)
>`execute.size()` will return no of records(Restaurants) which came out as a resultant of this query execution.

```
String within="select * from Restaurant where  ST_WITHIN(location,'POLYGON ((12.314015 41.8262816, 12.314015 41.963125, 12.6605063 41.963125, 12.6605063 41.8262816, 12.314015 41.8262816))') = true";

execute=databaseDocumentTx.query(new OSQLSynchQuery<ODocument>(within));
log.info("how many points within polygon : " + execute.size());

``` 
Result : Will exclude `hotel3` record since it does not lie inside polygon
```
how many points within polygon : 2
```

* **ST_Distance** : Distance of Restaurants from location sent as parameter. `ST_GEOMFROMTEXT` converts text to a geometric object.

```
String distance="select ST_Distance(location,ST_GEOMFROMTEXT('POINT(12.4664632 41.8904382)')) as distance from Restaurant"";

execute=databaseDocumentTx.query(new OSQLSynchQuery<ODocument>(distance));	
```
Result : Distance of all the three points from Point(12.4664632 41.8904382)

>For other query examples... [click here](https://orientdb.com/docs/2.2/Spatial-Index.html#functions) : Modify according to your database.

For Concept Documentation for Geo-Location... [click here](concept-orientdb.md#geolocation-geospatial-data-)

## Object to Document Transformation
### Relevant Package Locations in project `orientdb_poc`
* Class :`ObjectToDocumentTransform.java`
	* Location : `src/test/java` > `com.neurosys.orientdb.poc.transform`
	* Purpose  : Code for converting record stored using ObjectAPI to an `ODocument`

### Steps involved in Transformation
#### Step 1 : Object database creation and Persisting some data
* Create database
```
objectDatabase=new OObjectDatabaseTx("memory:objecttodocument");
objectDatabase.create();
```
* Register Entity classes
```
objectDatabase.getEntityManager().registerEntityClass(className.class);
```
Example : 
```
OEntityManager em=objectDatabase.getEntityManager();
em.registerEntityClass(Subject.class);
em.registerEntityClass(Teacher.class);
```

* Persist data into classes

```
classNameObjectDao classObject = new classNameObjectDao(odb);
classObject.persist("Object Data");  
```
Example : 

```
//Create new Subject and Teacher using their constructors
Subject subject=new Subject("Ninjutsu");
Teacher teacher=new Teacher("Hashirama","Senju",subject);

/*
 * Create DAO class object for Subject and Teacher.
 * DAO classes are responsible for interacting with databases
 */

SubjectObjectDao subjectDao = new SubjectObjectDao(objectDatabase);
TeacherObjectDao teacherDao = new TeacherObjectDao(objectDatabase);

//Persist data into database
subjectDao.saveSubject(subject);
teacherDao.saveTeacher(teacher);
```

#### Step 2 : Extract data in Document Format

OrientDB natively provides [getRecordByUserObject()](http://orientdb.com/javadoc/latest/com/orientechnologies/orient/core/db/OUserObject2RecordHandler.html#getRecordByUserObject-java.lang.Object-boolean-) for this conversion.
Example : 
```
//Extract Teacher using 'name' from database
Teacher extractedTeacher=teacherDao.getTeacherByName(teacher.getName());

//Convert extracted Teacher into ODocument format
//@Todo : reason behind name "getRecordByUserObject"
ODocument doc=objectDatabase.getRecordByUserObject(extractedTeacher, true);
log.info("Transformed...");
```

Now if test for ClassName of extracted object, result should be true.
```
Assert.assertTrue("Object coming out is not of class \"Teacher\"","Teacher".equals(doc.getClassName()));
    	
```

## Document to Object Transformation
OrientDB natively does not support Document to Object Transformation. Hence, we will convert the extract the stored Document object into JSON format which than can be converted to required Object type.
### Relevant Package Locations in project `orientdb_poc`
* Class :`DocumentToJSONTransform.java`
	* Location : `src/test/java` > `com.neurosys.orientdb.poc.transform`
	* Purpose  : Code for converting record stored using Document API into JSON format.

### Steps involved in Transformation
#### Step 1 : Create database and store some records
* Create a Document database

```
documentDatabase=new ODatabaseDocumentTx("memory:document_to_json");
documentDatabase.create();
```

* Store some document type records

```
//Create new documents for class="Subject" and "Teacher"
ODocument docSubject=new ODocument("Subject");
docSubject.field("name","Ninjutsu");

ODocument docTeacher=new ODocument("Teacher");
docTeacher.field("firstName","Hashirama");
docTeacher.field("sirName","Senju");
docTeacher.field("subject",docSubject);
```

* Save these records into database

```
//Save both the documents in database
docTeacher.save();
docSubject.save();
```

#### Step 2 : Extract records from database

```
List <ODocument>listOfDocument = db.query(new OSQLSynchQuery("select from ".toString().concat(className)));

//Get First record
ODocument doc=listOfDocument.get(0);
```

#### Step 3 : Convert this document to JSON format

```
String jsonTeacher=doc.toJSON();
```

## TroubleShooting
Sample OrientDB Code [...here](sample-orientdb)

>**Note** : While working with Object API we will be creating Object type databases which will create conflict if `Lucene` related dependencies are present in `pom.xml`.

### Error : 
```com.orientechnologies.orient.core.exception.ODatabaseException: Cannot create database 'objecttodocument' ```

### Cause :
#### Case 1 
There is a conflict while working with Object database and using Lucene Indexing in the same project which is not been solved by orientdb community.

### Stack Trace : 
```
com.orientechnologies.orient.core.exception.ODatabaseException: Cannot create database 'objecttodocument'
	at com.orientechnologies.orient.core.db.document.ODatabaseDocumentTx.create(ODatabaseDocumentTx.java:509)
	at com.orientechnologies.orient.core.db.document.ODatabaseDocumentTx.create(ODatabaseDocumentTx.java:398)
	at com.orientechnologies.orient.core.db.ODatabaseWrapperAbstract.create(ODatabaseWrapperAbstract.java:76)
	at com.neurosys.orientdb.poc.transform.ObjectToDocumentTransform.before(ObjectToDocumentTransform.java:35)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.junit.runners.model.FrameworkMethod$1.runReflectiveCall(FrameworkMethod.java:50)
	at org.junit.internal.runners.model.ReflectiveCallable.run(ReflectiveCallable.java:12)
	at org.junit.runners.model.FrameworkMethod.invokeExplosively(FrameworkMethod.java:47)
	at org.junit.internal.runners.statements.RunBefores.evaluate(RunBefores.java:24)
	at org.junit.runners.ParentRunner.runLeaf(ParentRunner.java:325)
	at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:78)
	at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:57)
	at org.junit.runners.ParentRunner$3.run(ParentRunner.java:290)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:71)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:288)
	at org.junit.runners.ParentRunner.access$000(ParentRunner.java:58)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:268)
	at org.junit.internal.runners.statements.RunAfters.evaluate(RunAfters.java:27)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:363)
	at org.eclipse.jdt.internal.junit4.runner.JUnit4TestReference.run(JUnit4TestReference.java:86)
	at org.eclipse.jdt.internal.junit.runner.TestExecution.run(TestExecution.java:38)
	at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.runTests(RemoteTestRunner.java:459)
	at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.runTests(RemoteTestRunner.java:678)
	at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.run(RemoteTestRunner.java:382)
	at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.main(RemoteTestRunner.java:192)
Caused by: java.lang.ClassCastException: com.orientechnologies.orient.object.db.OObjectDatabaseTx cannot be cast to com.orientechnologies.orient.core.db.document.ODatabaseDocumentTx
	at com.orientechnologies.spatial.OLuceneSpatialIndexFactory.onCreate(OLuceneSpatialIndexFactory.java:135)
	at com.orientechnologies.orient.core.db.document.ODatabaseDocumentTx.create(ODatabaseDocumentTx.java:476)
	... 27 more

```

### Solution : Remove following dependencies from `pom.xml` : 

```
<dependency>
    <groupId>com.orientechnologies</groupId>
    <artifactId>orientdb-spatial</artifactId>
    <version>2.2.22</version>
</dependency>

	
<dependency>
    <groupId>com.orientechnologies</groupId>
    <artifactId>orientdb-lucene</artifactId>
    <version>2.2.22</version>
</dependency>	
```



## References
* [Sample OrientDB Code](https://github.com/namikaze-nitin/orientdb-neurosys-poc)
* [Official Website](http://orientdb.com/orientdb/#)
* [Official Documentation](http://orientdb.com/docs/last/)
* [TutorialsPoint](https://www.tutorialspoint.com/orientdb/orientdb_overview.htm)
* [Installation guide from digital ocean](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-orientdb-on-ubuntu-14-04)
* [More Gyan from Petter's random thought](http://pettergraff.blogspot.in/2013/12/orientdb-thanks.html)
* [GeoSpatial Data in OrientDB](https://dzone.com/articles/spatial-module-in-orientdb-22)
* [Schema](http://orientdb.com/docs/2.2.x/Schema.html#schema)
* [Relationships](http://orientdb.com/docs/2.2.x/Schema.html#relationships)	
* [Object API](http://orientdb.com/docs/2.2.x/Object-Database.html#object-api)
* [Object Database Creation](http://orientdb.com/docs/2.2.x/Object-Database.html#use-the-database)
* Querying in Orientdb : 
	* [Java Query API](https://orientdb.com/docs/3.0.x/java/Java-Query-API.html)
	* [Working with Documents](https://orientdb.com/docs/2.2/Document-API-Documents.html#working-with-documents)
	* [Pagination](https://orientdb.com/docs/2.2/Pagination.html)
	* [SQL Reference](https://orientdb.com/docs/2.2/SQL.html)
	* [SQL : Batch of Commands](https://orientdb.com/docs/2.2/SQL-batch.html#sql-batch)
	* [SQL : Commands and their structures](https://orientdb.com/docs/2.2/Commands.html)
	* [SQL : Clauses](https://orientdb.com/docs/2.2/Tutorial-SQL.html#where)
	* [SQL : Parser syntax](https://orientdb.com/docs/2.2/SQL-Syntax.html)
	* [Console Commands Reference](https://orientdb.com/docs/2.2/Console-Commands.html)
* Transaction :	
	* [Transactions](https://orientdb.com/docs/2.2/Transactions.html)
	* [Transaction Propagation](https://orientdb.com/docs/2.2/Transaction-propagation.html)