
SQL is a practical language. 

SQL stands for Structure Query Language. It is considered one of the major reasons for the commercial success of relational databases. 

The origin of SQL is relational predicate calculus called tuple calculus which was proposed initially as the language SQUARE.

SQL is an informal or practical rendering of the relational data model with syntax.

The language has features for data definition, data manipulation, transaction control, indexing, security specifications, active databases, multi-media, distributed databases etc. 
#### History

SQL Has gone through many standards starting with `SQL-86` (`SQL 1`), `SQL-92`, (`SQL 2`). 

Later standards from `SQL-1999` are divided into core specification and specialized extensions. The extensions are implemented for different applications. Such as data mining, data warehousing, multimedia etc.

`SQL-2006` added XML features, in 2008 Object Oriented features were added. 

`SQL-3` Is the current standard which started with `SQL-1999` It has not yet been fully implemented by any RDBMS. 


### Data Definition and Types
Table, row and column are used for relational model terms. `relation`, `tuple`, `attribute`.

`CREATE` statement - The main SQL statement for data definition. Used to create schemas, tables, types, domains,  views, assertions, triggers, and more. 

#### SQL Schema
Identified by a schema name, includes an authorisation identifier and descriptors for each element. Schema elements include: Tables, types, constraints, views, domains, and other constructs (Such as auth grants)

Example:
```sql
CREATE SCHEMA COMPANY AUTHORIZATION 'Jsmith';
```

Each SQL statement should end with a **semicolon**

The privilege to create schemas, tables and other constructs must be explicitly granted to the relevant user accounts by the system administrator (DBA).

#### Catalogue Concepts
A catalogue is a collection of schemas in an SQL environment. 

A database installation typically has a default environment and schema. 

A catalogue aways contains a special schema called `INFORMATION_SCHEMA` which provides information on all schemas in the catalogue and all the element descriptors in these schemas

Schemas within the same catalogue can share certain elements such as a type and domain definitions. 

SQL also has the concept of a cluster of catalogues. 

#### Create Table 
This specifies a new relation, you provide:
- The name of the table
-  Attributes, their types and initial constraints. 

When the attributes are specified. Each one is given a name and a datatype to specify its domain of values. It is also given possible attributes constraints, such as `NOT NULL`

The key and entity integrity and referential integrity constraints can be specified with the `CREATE TABLE` statement, after the attributes are declared. They can also be specified later with the `ALTER TABLE` statement.

Can attach the schema name to the relation name `CREATE TABLE COMPANY.EMPLOYEE ...`
Or if you are in the context of a schema already, just do `CREATE TABLE EMPLOYEE ...`
##### Base tables (Base Relations)
Relation and its tuples are actually created and stored as a file by the DBMS
##### Virtual Relations (Views)
Created through the `CREATE VIEW` Statement. May or may not correspond to an actual physical file. 
##### Company Example
```sql
CREATE TABLE EMPLOYEE(  
	Fname VARCHAR(15) NOT NULL,  
	Minit CHAR,  
	Lname VARCHAR(15) NOT NULL,  
	Ssn CHAR(9) NOT NULL,  
	Bdate DATE,  
	Address VARCHAR(30),  
	Sex CHAR,  
	Salary DECIMAL(10, 2),  
	Super_ssn CHAR(9),  
	Dno INT NOT NULL ,  
	PRIMARY KEY (Ssn)  
);  

CREATE TABLE DEPARTMENT(  
	Dname VARCHAR(15) NOT NULL,  
	Dnumber INT NOT NULL,  
	Mgr_ssn CHAR(9),  
	Mgr_start_date DATE,  
	PRIMARY KEY (Dnumber),  
	UNIQUE (Dname),  
	FOREIGN KEY (Mgr_ssn) REFERENCES EMPLOYEE(Ssn)  
);  

CREATE TABLE DEPT_LOCATIONS (  
	Dnumber INT NOT NULL ,  
	Dlocation VARCHAR(15) NOT NULL,  
	PRIMARY KEY (Dnumber, Dlocation),   
	FOREIGN KEY (Dnumber) REFERENCES DEPARTMENT(Dnumber)  
);  

CREATE TABLE PROJECT (  
	Pname VARCHAR(15) NOT NULL,  
	Pnumber INT NOT NULL,  
	Plocation VARCHAR(15),  
	Dnum INT NOT NULL,  
	PRIMARY KEY (Pnumber),  
	UNIQUE (Pname),  
	FOREIGN KEY (Dnum) REFERENCES DEPARTMENT(Dnumber)  
);  
CREATE TABLE WORKS_ON (  
	Essn CHAR(9) NOT NULL,  
	Pno INT NOT NULL,  
	Hours DECIMAL(3, 1) NOT NULL,  
	PRIMARY KEY (Essn, Pno),  
	FOREIGN KEY (Essn) REFERENCES EMPLOYEE(Ssn),  
	FOREIGN KEY (Pno) REFERENCES PROJECT(Pnumber)  
);  
CREATE TABLE DEPENDENT(  
	Essn CHAR(9) NOT NULL,  
	Dependent_name VARCHAR(15) NOT NULL,  
	Sex CHAR,
	
	Bdate DATE,  
	Relationship VARCHAR(8) ,  
	PRIMARY KEY (Essn, Dependent_name) ,  
	FOREIGN KEY (Essn) REFERENCES EMPLOYEE(Ssn)  
);
```

```sql
ALTER TABLE EMPLOYEE ADD (  
	FOREIGN KEY (Super_ssn) REFERENCES EMPLOYEE(Ssn),  
	FOREIGN KEY (Dno) REFERENCES DEPARTMENT(Dnumber)  
);  
```

##### Foreign Keys
When Creating tables, like the company example, sometimes foreign keys might cause errors. This is from either:
- Circular references
- References to tables that have not been created. 

DBAs have ways to stop referential integrity enforcement to get around this problem

These constraints can be left out of the initial `CREATE TABLE` statement and then added later using the `ALTER TABLE` Statement. (This is what is done in the company example)


#### Attribute Datatypes and Domains
##### Numeric data types  
**Integer numbers:** `INTEGER`, `INT`, and `SMALLINT`  
**Floating-point (real) numbers:** `FLOAT` or `REAL`, and  `DOUBLE PRECISION`  
**Formatted numbers:** `DECIMAL(i,j)` or `DEC(i,j)`, or `NUMERIC(i,j)`  
- `i` stands for Precision, the total number of digits in the value, i.e., on both sides of the decimal point  
- `j` stands for Scale, number of digits after the decimal point
##### Boolean data type  
Values of `TRUE` or `FALSE` (or `NULL`)
##### Character-string types
**Fixed length:** `CHAR(n)`, `CHARACTER(n)`  
**Varying length:** `VARCHAR(n)`, `CHAR VARYING(n)`, `CHARACTER VARYING(n)`\
##### Bit-string data types  
**Fixed length:** `BIT(n)`
**Varying length:** `BIT VARYING(n)`
##### Date/Time data types  
**`DATE` Data type:** 
10 positions, made of `YEAR`, `MONTH`, and `DAY` in the form `YYYY-MM-DD`  

**`TIME` Data type:** 
At least 8 positions, made of `HOUR`, `MINUTE`, and `SECOND` in the form `HH:MM:SS`

**`Timestamp` Data type:** 
- Includes the DATE and TIME fields  
- Plus a minimum of six positions for decimal fractions of seconds  
- Optional `WITH TIME ZONE` qualifier  

**`INTERVAL` Data type:**  
- Specifies a relative value that can be used to increment or decrement an absolute value of a `date`, `time`, or `timestamp`  

`DATE`, `TIME`, `Timestamp`, `INTERVAL` data types can be cast or converted to string formats for comparison.

##### Additional
These are not supported by MySQL
**`Domain` Data type:**
Name used with the attribute specification, Makes it easier to change the data type for a domain that is used by numerous attributes to improve schema readability.  
Example: `CREATE DOMAIN SSN_TYPE AS CHAR(9);`

**`TYPE`**  
User Defined Types (UDTs) are supported for object-oriented applications. Uses the command: `CREATE TYPE`

### Constraints
Relational Model has 3 basic constraint types that are supported in SQL:  
- Key constraint: A primary key value cannot be duplicated  
- Entity Integrity constraint: A primary key value cannot be null  
- Referential integrity constraints: The foreign key must have a value that is already present as a primary key, or may be null.

#### Basic Constraints
- Restrictions on attribute domains and NULL  
- Default value of an attribute `DEFAULT <value>`  
- `NOT NULL` constraint  
	- `NULL` is not permitted for a particular attribute

##### `CHECK` Clause
Constraints on individual tuples within a relation using the CHECK clause  
-  `Dnumber INT NOT NULL CHECK (Dnumber > 0 AND Dnumber < 21);`
  
`CHECK` clause can also be used in conjunction with the `CREATE DOMAIN` statement
`CREATE DOMAIN D_NUM AS INTEGER CHECK (D_NUM > 0 AND D_NUM < 21);`
##### `PRIMARY KEY` Clause
Specifies one or more attributes that make up the primary key of a relation  
`Dnumber INT PRIMARY KEY;`

##### `UNIQUE` Clause 
Specifies alternate (secondary) keys (called `CANDIDATE` keys in the relational model).  `Dname VARCHAR(15) UNIQUE;`

  
##### `FOREIGN KEY` clause

```sql
FOREIGN KEY (Super_SSN) REFERENCES EMPLOYEES(Ssn) ON DELETE SET NULL ON UPDATE CASCADE;
```

> If the value for the primary key gets deleted, set the foreign key to null. If the thing gets updated, propagate the update to this. 

The default action for violation is to reject update operation  

Attach referential triggered action clause  
- Options include `SET NULL`, `CASCADE`, and `SET DEFAULT`  
- Action taken by the DBMS for `SET NULL` or `SET DEFAULT` is the same for both `ON DELETE` and `ON UPDATE`. 
- `CASCADE` option suitable for “relationship” relations 

#### Naming Constraints
Constraints can be named using the keyword `CONSTRAINT`. This is useful for altering the constraint later on. 

```sql
CONSTRAINT EMPPK
	PRIMARY KEY(SSN);

CONSTRAINT EMPSUPERPFK
	FOREIGN KEY(Super_ssn) REFERENCES EMPLOYEE(Ssn) 
		ON DELETE SET NULL ON UPDATE CASCADE;
	
```

#### Tuple Constraints
Additional constraints can be applied on individual tuples within a relation are also possible using `CHECK` clauses at the end of a `CREATE TABLE` statement

These apply to each tuple individually, `CHECK (Dept_Create_Date <= Mgr_Start_Date);`

These are row-based constraints because they apply to each row individually and are checked whenever a row is inserted or modified. 

### Querying Data
The `SELECT` Statement is a basic statement for retrieving information from a database. 

This is an important distinction between the practical SQL model and the formal relational model. 

SQL allows a table to have two or more or more tuples that are identical in all their attribute values. 
- Unlike a relational model which is strictly set-theory based
- SQL Tables are not a set of tuples, but a multiset (Like a bag)
- Some SQL Relations are constraints to be sets because a key constraint has been declared. 

Select statement syntax is 
```sql 
SELECT attributes FROM tables WHERE condition;
```

Basic logical comparison operators for comparing attribute values  
- `=`, `<`, `<=`, `>`, `>=`, and `<>`  

**Projection attributes**  
- Attributes whose values are to be retrieved  

**Selection condition**  
- Boolean condition that must be true for any retrieved tuple. Selection conditions include join conditions when multiple relations are involved.  

Consider an implicit tuple variable (or iterator) looping over individual tuples and evaluating against the condition.


#### Ambiguous Attribute Names  
The same name can be used for two (or more) attributes in different relations  
- As long as the attributes are in different relations  
- Must qualify the attribute name with the relation name to prevent ambiguity
#### Aliasing and Renaming
> Declare alternative relation names E and S to refer to the `EMPLOYEE` relation twice in a query


For each employee, retrieve the employee’s first and last name and the first and last name of his or her immediate supervisor.  
```sql
SELECT E.Fname, E.Lname, S.Fname, S.Lname  
FROM EMPLOYEE AS E, EMPLOYEE AS S  
WHERE E.Super_ssn = S.Ssn;  
```

It is recommended practice to abbreviate names and to prefix same or similar attribute from multiple tables.

The attribute names can also be renamed, e.g.,  
```sql
```EMPLOYEE AS E(Fn, Mi, Ln, Ssn, Bd, Addr, Sex, Sal, Sssn, Dno)  
```

Note that the relation `EMPLOYEE` now has a variable name `E` which corresponds to a tuple variable 

We can use alias-naming or renaming mechanism in any SQL query to specify tuple variables. E.g.,
```sql
SELECT E.Fname, E.Lname, E.Address
FROM EMPLOYEE AS E, DEPARTMENT as D
WHERE D.Dname = 'research' AND D.Dnumber = E.Dno;
```