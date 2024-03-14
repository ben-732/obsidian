![[Pasted image 20240309111829.png | center | 300]]
### Types of database and Applications

Traditional Applications: 
- Numeric and Textual Databases 

More Recent Applications: 
- Multimedia Databases 
- Geographic Information Systems (GIS) 
- Social Networks started capturing a lot of information about people and about communications among people-posts, tweets, photos, videos in systems such as:  
	- Facebook 
	- Twitter 
	- Linked-In 
- Search Engines 
	- Google, Bing, Yahoo: collect their own repository of web pages for searching purposes

#### Applications of databases
- Businesses: Banking, Insurance, Retail, Transportation, Healthcare, Manufacturing 
- Service Industries: 
- Financial, Real-estate, Legal, Electronic Commerce, Small businesses 
- Education: Resources for content and Delivery 
- More recently: Social Networks, Environmental and Scientific Applications, Medicine and Genetics
- Personalised Applications: based on smart mobile devices


### Why databases
Before databases, everything is stored in files. This is suboptimal, there are lots of issues around how things are accessed, written and stored etc. 

### Example of educational DB
![[Pasted image 20240227152237.png | center | 500]] 


### How to store data
If Everything is stored in files, lots of files are needed
- How many files are needed?

You could try store everything in one file, but this has lots of limitations:
- Redundancies and inconsistencies 
- Integrity problems, atomicity of updates
![[Pasted image 20240227152622.png | center | 400]]

Alternatively you could create individual "files" for each entity. This has it's own set of issues
- Lengthy development times 
- Difficult of getting quick answers 
- Complex system administration 
- Lack of security and limited data sharing, extensive programming
![[Pasted image 20240227152741.png | center | 400]]
These files are all linked together, but this creates issues around having to update multiple files at once. 

## Database systems
This is an example of a simplified database system environment
![[Pasted image 20240227153045.png | center | 300]]
The DBMS is a software system that handles a lot of the complex parts of data storage like user access, security, data processing/manipulation, saving etc

### Basic definitions
- **Database** - A collection of related data
- **Data** - Known facts that can be recorded and have an implicit meaning
- **Mini-world** - Some part of the real world about which data is stored in a database. For example, student grades and transcripts at a university
- **Database Management System** - A software package/ system to facilitate the creation and maintenance of a computerised database.
- **Database System** - The DBMS software together with the data itself. Sometimes, the applications are also included.

### Application actions
Define a particular database in terms of its data types, structures, and constraints 
- Construct or Load the initial database contents on a secondary storage medium 
- Manipulation of the database: 
	- Retrieval: Querying, generating reports
	- Modification: Insertions, deletions and updates to its content 
	- Accessing the database through Web applications 
- Processing and Sharing by a set of concurrent users and application programs – yet, keeping all data valid and consistent
- Applications interact with a database by generating  
- Queries: 
	- access different parts of data and formulate the result of a request  
- Transactions: 
	- may read some data and “update” certain values or generate new data and store that in the database 
- Applications must not allow unauthorised users to access data 
- Applications must keep up with changing user requirements against the database

### Example with conceptual data model

**Mini-world for the example:** 
Part of a UNIVERSITY environment.  
**Some mini-world entities:** 
- Students 
- Courses 
- Sections of courses
- Academic departments 
- Instructors

Relationships in the Miniworld
- SECTIONs are of specific COURSEs 
- STUDENTs take SECTIONs 
- COURSEs have prerequisite COURSEs 
- INSTRUCTORs teach SECTIONs 
- COURSEs are offered by DEPARTMENTs 
- STUDENTs major in DEPARTMENTs

![[Pasted image 20240227154459.png | center | 300]]

### Characteristics of the Database Approach
**Self describing nature of a DB system**
- A DBMS *catalogue* stores the description of a particular database )data structures, types and constraints etc)
- The description is called **Meta data**
- this allows the DBMS software to work with different database applications

**Insulation between programs and data:**
- Called program-data independence. 
- Allows changing data structures and storage organisation without having to change the DBMS access programs.

**Data Abstraction:**
- A data model is used to hide storage details and present the users with a conceptual view of the database.
- Programs refer to the data model constructs rather than data storage details

**Support of multiple views of the data:**
Each user may see a different view of the database, which describes only the data of interest to that user, e.g., 
- A view that groups all the students who took each section and gives each student’s grade – useful for grade reporting purpose. 
- A view that gives the number of courses taken and the GPA for each student – useful for determine honours students.

**Sharing of data and multi-user transaction processing**
- Allowing a set of concurrent users to retrieve from and to update the database
- Concurrency control within the DBMS guarantees that each transaction is correctly executed or aborted
- Recovery subsystem ensures each completed transaction has its effect permanently recorded in the database
- *OLTP* (Online Transaction Processing) is a major part of database applications. This allows hundreds of concurrent transactions to execute per second.
#### Example of a simplified DBMS catalogue
![[Pasted image 20240227154834.png | center | 400]]

#### DBMS Users
Users may be divided into several categories:

Those who actually use and control the database content, and those who design, develop and maintain database applications (called “Actors on the Scene”)
- Database administrators
- Database Designers 
- End-users 
- System Analysts, Application Programmers and Business Analysts

Those who design and develop the DBMS software and related tools, and the computer systems operators (called “Workers Behind the Scene”).

- DBMS System Designers and Implementors, Database Tool Developers 
- Operators and Maintenance Personnel - running and maintenance of the database system hardware and software environment.
#### Pros of using a DBMS
- Controlling redundancy in data storage and in development and maintenance efforts. 
	- Sharing of data among multiple users. 
	- Provides backups etc,
- Restricting unauthorised access to data. Only the DBA staff uses privileged commands and facilities. 
- Providing Storage Structures (e.g., indexes) for efficient Query Processing 
- Providing backup and recovery services. 
- Providing multiple interfaces to different classes of users. 
- Representing complex relationships among data. 
- Enforcing integrity constraints on the database.

#### When not to use a DBMS

Main inhibitors (costs) of using a DBMS: 
- High initial investment and possible need for additional hardware. 
- Overhead for providing generality, security, concurrency control, recovery, and integrity functions.
When a DBMS may be unnecessary: 
- If the database and applications are simple, well defined, and not expected to change. 
- If access to data by multiple users is not required. 
When a DBMS may be infeasible: 
- In embedded systems where a general purpose DBMS may not fit in available storage

## Summary
- A database is a collection of related data (recorded facts). 
- A DBMS is a generalised software package for implementing and maintaining a computerised database. 
- The database and the software together form a database system. 
- Advantages of databases approaches verses traditional file-processing applications. 
- Different database users, such as administrators, designers, end-users, system analysts, software engineers, personals associated to DBMS software/environment, etc.




# E

### Data Models
#### Data Model
A set of concepts to describe the structure of a database, the operations for manipulating these structures and certain constraints that the database should obey.

#### Data model Structure and constraints 
- Constructs are used to define the database structure
- Constructs typically include elements (and their data types) as well as groups of elements (e.g. entity, record, table), and relationships among such groups
- Constraints specify some restrictions on valid data; these constraints must be enforced at all times

#### Data model operations
These operations are used for specifying database retrievals and updates by referring to the constructs of the data model

Operations on the data model may include both: 
- Basic model operations (e.g. generic insert, delete, update) 
- User-defined operations (e.g. compute_student_gpa, update_inventory)


#### Categories of data models
**Conceptual (high-level, semantic) data models**
These provide concepts that are close to the way many users perceive data, they are also called *entity-based* or *object-based* data models.

**Physical (low-level, internal) data models**
Provide concepts that describe details of how data is stored in the computer. These are usually specified in an ad-hoc manner through DBMS design and administration manuals.

**Implementation (representational) data models**
Provide concepts that fall between the above two, used by many commercial DBMS implementations (e.g. relational data models used in many commercial systems).

**Self-Describing Data Models**
Combine the description of data with the data values. Examples include XML, key-value.


### Schemas
A database schema is a description of a database. Includes descriptions of the database structure, data types, and the constraints on the database.

A schema diagram is an illustrative display of (most aspects of) a database schema. 
A scheme construct is a component of the schema or an object within the schema, like `STUDENT` or `COURSE`.

#### Database State
The state (instance/occurrence/snapshot) of a database is the actual data stored in a database at a particular moment in time. This includes the collection of all the data in the database.

The database schema changes very infrequently, whereas the database state changes every time the database is updated. 

A *valid state* is a database state that satisfies the structure and constraints of the database. 

The *initial state* refers to the state of the database when the data is initially loaded into the system.


#### Three-Schema Architecture
Proposed to support DBMS characteristics of:
- Program-data independence
- Support multiple views of the data

DBMS Schemas defined at three lvels


**Internal Schema** at the internal level to describe physical storage structures and access paths. This typically uses a physical data model. 

**Conceptual schema** at the conceptual level to describe the structure and constraints for the whole database for a community of users. Uses a conceptual or an implementation data model.

**External schemas** at the external level to describe the various user views. Usually uses the same data model as the conceptual schema.

![[Pasted image 20240228163740.png | center | 400]]

Mappings among schema levels are needed to transform requests and data. 
- Programs refer to an external schema, and are mapped by the DBMS to the internal schema for execution.
- Data extracted from the internal DBMS level is reformatted to match the user’s external view (e.g. formatting the results of an SQL query for display in a Web page)
##### Data independence 
Logical Data Independence:
- The capacity to change the conceptual schema without having to change the external schemas and their associated application programs.

Physical Data Independence:
- The capacity to change the internal schema without having to change the conceptual schema
- For example, the internal schema may be changed when certain file structures are reorganised or new indexes are created to improve database performance.

When a schema at a lower level is changed, only the mappings between this schema and higher-level schemas need to be changed in a DBMS that fully supports data independence. The higher-level schemas are unchanged


### DBMS Languages
#### Data Definition Language (DDL)
The languages used by the DBA and database designers to specify the conceptual schema of a database. In many DBMSs, the DDL is also used to define internal and external schemas (views).

In some DBMSs, separate storage definition language (SDL) and view definition language (VDL) are used to define internal and external schemas. 

SDL is typically realised via DBMS commands provided to the DBA and database designers

#### Data Manipulation Language (DML)
Used to specify database retrievals and updates

DML commands (data sublanguage) can be embedded in a general purpose programming language (host language), such as COBOL, C, C++, or Java. A library of functions can also be provided to access the DBMS from a programming language.

Alternatively, stand-alone DML commands can be applied directly (called a query language).

##### Types of DML
###### High Level or non procedural languages
- These include the SQL relational language 
- Are “set”-oriented and specify what data to retrieve rather than how to retrieve it. 
- Also called declarative languages. 
- May be used in a standalone way or may be embedded in a programming language
###### Low Level or Procedural Language:
- Retrieve data one record-at-a-time;
- Constructs such as looping are needed to retrieve multiple records, along with positioning pointers
- These must be embedded in a programming language

#### DBMS Interfaces
Standalone query language interfaces. Example: Entering SQL queries at the DBMS interactive SQL interface (SQL \*Plus in ORACLE)

- Programmer interfaces  
	- For embedding DML in programming languages
- User-friendly interfaces
	- Menu based (Web based) popular for browsing on the web
	- Forms-based, designed for naïve users used to filling in entries
	- Graphics-based: Point and Click/Drag and Drop used for specifying a query on a schema diagram
- Natural language
	- Requests in written English, free text as a query
- Mobile Interfaces
	- interfaces allowing users to perform transactions using mobile apps
- Speech
	- Input query and Output response
- Web Browser with keyword search
- Parametric interfaces, e.g., bank tellers using function keys
- Interfaces for the DBA:
	- Creating user accounts, granting authorisations
	- Setting system parameters
	- Changing schemas or access paths

#### DBMS Programming Language Interfaces

Programmer interfaces for embedding DML in a programming languages
**Embedded Approach**:
embedded SQL (for C, C++, etc.), SQLJ (for Java)

**Procedure Call Approach**
DBC for Java, ODBC (Open Databse Connectivity) for other programming languages as API’s (application programming interfaces)

**Database Programming Language Approach**
ORACLE has PL/SQL, a programming language based on SQL; language incorporates SQL and its data types as integral components

**Scripting Languages**
PHP (client-side scripting) and Python (server-side scripting) are used to write database programs.

![[Pasted image 20240228165206.png | center | 400]]
The top part refers to the various users of the database environment
The lower part shows the internal modules of the DBMS responsible for storage of data and processing of transactions.


### Database System Utilities
For preforming certain actions like:
- Loading data stored in files into a database. Includes data conversion tools
- Backing up the database periodically on tape. 
- Reorganising database file structures. 
- Performance monitoring utilities. 
- Report generation utilities. 
- Other functions, such as sorting, user monitoring, data compression, etc.

## Centralised / Client-Server DBMS Architectures

**Centralised DBMS**: 
Combines everything into single system including- DBMS software, hardware, application programs, and user interface processing software.
User can still connect through a remote terminal – however, all processing is done at centralised site.

![[Pasted image 20240228165447.png | center | 500]]
### Basic 2-tier Client-Server Architectures
There are specialised Servers with specialised functions available

Print server, File server, DBMS Server, Web Server, Email Server etc
![[Pasted image 20240228165621.png | center | 400]]
Clients access the specialised servers as required

### Clients and Servers
**Clients**
Provide appropriate interfaces through a client software module to access and utilize the various server resources.
Clients may be diskless machines or PCs or Workstations with disks with only the client software installed
Connected to the servers via some form of a network (LAN: local area network, wireless network, etc.)

**Server**
Provides database query and transaction services to the clients
Relational DBMS servers are often called SQL servers, query servers, or transaction servers
Applications running on clients utilise an Application Program Interface (API) to access server databases via standard interface such as: ODBC (Open Database Connectivity standard) or JDBC - for Java programming access

### Two Tier Client-Server Architecture
Client and server must install appropriate client module and server module software for ODBC or JDBC.
A client program may connect to several DBMSs, sometimes called the data sources
In general, data sources can be files or other non-DBMS software that manages data.
Details will be discussed in the late sessions on Database Programming

### Three Tier Client-Server Architecture
Common for Web applications  
Intermediate Layer called Application Server or Web Server:

- Stores the web connectivity software and the business logic part of the application used to access the corresponding data from the database server
- Acts like a conduit for sending partially processed data between the database server and the client.

Three-tier Architecture Can Enhance Security:
- Database server only accessible via middle tier
- Clients cannot directly access database server
- Clients contain user interfaces and Web browsers
- The client is typically a PC or a mobile device connected to the Web

![[Pasted image 20240229161339.png | center | 400]]
### Classification of DBMS
Based on the data model used
- Legacy: Network, Hierarchical.
- Currently Used: Relational, Object-oriented, Object-relational
- Recent Technologies: Key-value storage systems, NOSQL systems: document based, column-based, graph-based and key-value based. Native XML DBMSs.

Based on the data model used
- Legacy: Network, Hierarchical.
- Currently Used: Relational, Object-oriented, Object-relational
- Recent Technologies: Key-value storage systems, NOSQL systems: document based, column-based, graph-based and key-value based. Native XML DBMSs.