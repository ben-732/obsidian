## Basic SQL
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
EMPLOYEE AS E(Fn, Mi, Ln, Ssn, Bd, Addr, Sex, Sal, Sssn, Dno)  
```

Note that the relation `EMPLOYEE` now has a variable name `E` which corresponds to a tuple variable 

We can use alias-naming or renaming mechanism in any SQL query to specify tuple variables. E.g.,
```sql
SELECT E.Fname, E.Lname, E.Address
FROM EMPLOYEE AS E, DEPARTMENT as D
WHERE D.Dname = 'research' AND D.Dnumber = E.Dno;
```

Attributes can also be renames with `AS`

```sql
SELECT E.Fname as Employee_name, E.Lname as Employee_surname, E.Address
FROM EMPLOYEE AS E, DEPARTMENT as D
WHERE D.Dname = 'research' AND D.Dnumber = E.Dno;
```

#### Unspecified WHERE clause
If no `WHERE` clause is supplied then it indicates that there is no condition on tuple selection. The effect of this is a cross product of all possible tuple combination results.


#### Select Asterisk *
Retrieve all the attribute values of the selected tuples .
The `*` can be prefixed by the relation name, e.g., `EMPLOYEE.*` refers to all attributes of `EMPLOYEE` table

#### Select Sets
SQL does not automatically eliminate duplicate tuples in query results. For aggregate operations, duplicates must be accounted for using the `DISTINCT` keyword in the `SELECT` statement. After this, only distinct tuples should remain in the result. 

To retrieve every distinct salary value. 
```sql
SELECT DISTINCT Salary FROM EMPLOYEE
```


#### Set operations
SQL has directly incorporated some set operations, `UNION`, `EXCEPT`, and `INTERSECT`.

Corresponding multiset operations: `UNION ALL`, `EXCEPT ALL`, `INTERSECT ALL`

Type compatibility is needed for these operations to be valid.

#### Substring pattern matching
`LIKE` Comparison operator is used for string pattern matching. `%` Relaces an arbitrary number of zero or more characters, while `_` represents a single character. 
```sql
SELECT * FROM EMPLOYEES WHERE Address LIKE ‘%Houston,TX%’; 
SELECT * FROM EMPLOYEES WHERE Ssn LIKE ‘_ _ 1_ _ 8901’
```

#### Arithmetic Operations
`BETWEEN` comparison operator:
```sql
SELECT * FROM EMPLOYEES WHERE (Salary BETWEEN 30000 AND 40000) AND Dno = 5; 
```

Provides Standard arithmetic operators, may be included as a part of `SELECT` statement
- Addition `+`
- Subtraction `–`
- Multiplication `*` 
- Division `/` 

Show the resulting salaries if every employee working on the ‘Product’ project is given a 10 percent raise:
```sql
SELECT E.Fname, E.Lname, 1.1 * E.Salary AS Increased_sal 
FROM EMPLOYEE AS E, WORKS_ON ASW, PROJECT AS P 
WHERE E.Ssn=W.Essn ANDW.Pno=P.Pnumber AND P.Pname=‘ProductX’;
```

#### Ordering Results
`ORDER BY` clause orders results, it is typically placed at the end of the query
```sql
SELECT * 
FROM EMPLOYEE AS E
ORDER BY E.Lname, E.FName
```

They are ordered in ascending order by default
- Keyword `DESC` to set a descending order of values 
- Keyword `ASC` to specify ascending order explicitly  

```sql
SELECT * 
FROM EMPLOYEE AS E
ORDER BY D.Dname DESC, E.Lname ASC, E.Fname ASC
```

#### Basic retrieval block
A simple retrieval query in SQL can consists of up to four clauses, only the first two `SELECT` and `FROM` are mandatory.

`SELECT`  lists the attributes to be retrieved 
`FROM`  specifies all relations (tables) needed
`WHERE`  identifies conditions for the selection
`ORDERBY`  specifies an order for displaying the results

### Modifying Data
There are three commands used to modify the database. These are `INSERT`, `DELETE`, and `UPDATE`.

- `INSERT` typically inserts a single tuple (row) into a relation (table) 
- `UPDATE` modifies attribute values of a number of tuples (rows) in a relation (table) that satisfy the condition.
- `DELETE` removes a number of tuples (rows) in a relation (table) that satisfy the condition 

Both `UPDATE` And `DELETE` may propagate to tuples in other relations if referential triggered actions are specified in the referential integrity constraints of the DDL.

#### Inserting Data
In its simplest form, `INSERT` is used to add one or more tuples to a relation 

Attribute values should be listed in the same order as the attributes were specified in the `CREATE TABLE` command. 

Constraints on data types are observed automatically and Any integrity constraints as a part of the DDL specification are enforced.

To use the command, Specify the relation name and a list of values for the tuple. All values including nulls are supplied

```sql
INSERT INTO EMPLOYEE VALUES ('richard', 'K' 'Marini', '653298653', '1962-12-30', '98 Oak Forest Road, Katy, TX', 'M', 37000, '653298653', 4)
```

You can also insert multiple values from the result of a query:
```sql
INSERT INTO WORKS_ON_INFO (Emp_name, Proj_name, Hours_Per_Week)
SELECT  E.Lname, P.Pname, W.Hours
FROM PROJECT P, WORKS_ON W, EMPLOYEE E
WHERE P.Pnumber=W.Pno AND W.Essn=E.Ssn
```

Another variation of `INSERT` is used for bulk-loading of several tuples into tables 

A new table `TNEW` can be created with the same attributes as `T` and using `LIKE` and `DATA` in the syntax, it can be loaded with entire data. 

For example: 
```sql
CREATE TABLE D5EMPS LIKE EMPLOYEE (
	SELECT E.* 
	FROM EMPLOYEE AS E 
	WHERE E.Dno=5) 
WITH DATA
```

#### Deleting Data
`DELETE` removes tuples from a relation. It includes a `WHERE` Clause to select the tuples to be deleted. 

Referential integrity should be enforced, Tuples are deleted from only one table at a time (unless CASCADE is specified on a referential integrity constraint) 

A missing `WHERE`-clause specifies that all tuples in the relation are to be deleted; the table then becomes an empty table 

The number of tuples deleted depends on the number of tuples in the relation that satisfy the `WHERE`-clause

```sql
DELETE FROM EMPLOYEE WHERE Lname='Brown';
DELETE FROM EMPLOYEE WHERE Ssn='123456789';
DELETE FROM EMPLOYEE WHERE Dno=5;
DELETE FROM EMPLOYEE;
```

#### Updating Data
`UPDATE` is used to modify attribute values of one or more selected tuples. A `WHERE`-clause selects the tuples to be modified and an additional `SET`-clause specifies the attributes to be modified and their new values. 

Each command modifies tuples in the same relation, Referential integrity specified as part of DDL specification is enforced.

Change the location and controlling department number of project number 10 to 'Bellaire' and 5, respectively
```sql
UPDATE PROJECT
SET Plocation = 'Bellaire', Dnum = 5
WHERE Pnumber = 10;
```

Give all employees in the 'Research' department a 10% raise in salary.
```sql
UPDATE EMPLOYEE 
SET SALARY = SALARY * 1.1 
WHERE DNO IN (SELECT DNUMBER FROM DEPARTMENT WHERE DNAME='Research');
```

In this request, the modified `SALARY` value depends on the original `SALARY` value in each tuple.
The reference to the `SALARY` attribute on the right of `=` refers to the old `SALARY` value before modification. The reference to the `SALARY` attribute on the left of `=` refers to the new `SALARY` value after modification.

### Additional SQL Features
Techniques for specifying complex retrieval queries 
Writing programs in various programming languages that include SQL statements: 
- Embedded and dynamic SQL, SQL/CLI (Call Level Interface) 
- and its predecessor ODBC, SQL/PSM (Persistent Stored Module) 

Set of commands for specifying physical database design parameters, file structures for relations, and access paths, e.g., `CREATE INDEX`

Transaction control commands 
Specifying the granting and revoking of privileges to users 
Constructs for creating triggers 
Enhanced relational systems known as object-relational define relations as classes. 
Abstract data types (called User Defined Types- UDTs) are supported with `CREATE TYPE` 
New technologies such as XML and OLAP are added to versions of SQL

## More advance SQL
### Null & Null Comparisons
`NULL` can represent multiple things:
- An unknown value. Value exists but is not known, or it is not known whether or not the value exists.
- Unavailable or withheld value: Value exists but is purposely withheld
- Not applicable attribute: The attribute does not apply to this tuple, or is undefined for this tuple

SQL does not distinguish among different meanings of `NULL`

When a `NULL` value is involved in a comparison, the result is considered to be `UNKNOWN`

#### Three valued logic
In situations where one of the values in a Boolean comparison is `UNKNOWN`, SQL Evaluates according to the following table:
![[Pasted image 20240320194016.png | center | 600]]

#### Null Checking
SQL allows queries that check whether an attribute value is `NULL`.

Rather than using `=` or `<>` to compare an attribute value to `NULL`, SQL uses the comparison operator: `IS NULL` or `IS NOT NULL`

SQL considers each individual `NULL` value to be distinct from every other `NULL` value

### Nested queries
Complete select-from-where blocks within `WHERE` clause of another query 
Outer query and nested subqueries 

Comparison operator `IN` compares value $v$ with a set (or multiset) of values $V$.
It evaluates to `TRUE` if $v \in V$

Some queries require that existing values in the database be fetched and then used in a comparison condition. These can be formulated using nested queries, the other query is called the outer query.

```SQL
SELECT DISTINCT Pnumber
FROM PROJECT
WHERE Pnumber IN (
	SELECT Pnumber
	FROM PROJECT, DEPARTMENT, EMPLOYEE
	WHERE Dnum=Dnumber AND Mgr_ssn=Ssn AND Lname='Smith'
) OR (
	SELECT Pno
	FROM WORKS_ON, EMPLOYEE
	WHERE Essn=Ssn AND Lname='Smith'
);
```

#### Tuples in nested queries
SQL allows the use of tuples of values in comparison by placing them within parentheses
```sql
SELECT DISTINCT Essn
FROM WORKS_ON
WHERE (Pno, Hours) IN (
	SELECT Pno, Hours
	FROM WORKS_ON
	WHERE Essn='123456789'
);
```

#### Set/Multiset Comparisons
In addition to the `IN` operator, a number of other comparison operators can be used to compare a single value $v$ to a set (or multiset) of values $V$.

` = ANY` and ` = SOME` operators compare a multiset to a single value. 
- Returns `TRUE` if the value $v$ is equal to some value in the set `V`, and is hence equivalent to `IN`

You can also use other comparison operators to compare a single value $v$  

Other operators that can be combined with `ANY` or `SOME` include `>`, `>=`, `<`, `<=`, and `<>` 

The keyword `ALL` can also be combined with each of these operators where value must exceed all values from nested query.

```SQL
SELECT LName, Fname
FROM EMPLOYEE
WHERE Salary > ALL (SELECT Salary FROM EMPLOYEE WHERE Dno=5)
```

#### Naming Ambiguity
In the case of several levels of nested queries, we may have possible ambiguity among the attribute names (if attributes of the same name exist)

To Avoid potential errors and ambiguities, Create tuple variables (aliases) for all tables referenced in SQL query


#### Correlated Nested Queries
Two queries are correlated: when A condition in the `WHERE` clause of a nested query references some attributes of a relation declared in the outer query. 

Is evaluated once for each tuple in the outer query.

Queries that are nested using the `=` or `IN` comparison operator can be collapsed into one single block

#### Corelated Helper functions
`EXISTS` and `UNIQUE` are Boolean functions that return `TRUE` or `FALSE`, hence can be used in the `WHERE` clause. 

The `EXISTS` and `NOT EXISTS` functions check whether the result of a correlated nested query is empty or not. 

The `UNIQUE(Q)` function Returns `TRUE` if there are no duplicate tuples in the result of query `Q`.

`EXIST(Q)` returns `TRUE` if there is at least one tuple in the results of the nested query `Q`, and returns `FALSE` otherwise.

#### For all quantifier 
To achieve the “for all” (universal quantifier) effect, we can use double negation:
List first and last name of employees who work on ALL projects controlled by Department 5.
![[Pasted image 20240320195819.png | center | 500]]

The inner query here is saying: Select all projects by department 5, do set subtraction to remove every project that is worked on by a particular employee. This means that if that specific employee works on all projects from department 5, then they will return an empty set and fulfill the `NOT EXISTS` requirement. 

#### Explicit Set values
We can use explicit set of values in `WHERE` clause. 

retrieve the SSN of all employees who work on project numbers 1, 2, or 3
```sql
SELECT DISTINCT Essn 
FROM WORKS_ON 
WHERE Pno IN (1, 2, 3);
```

### Joining Tables
Joined table (or joined relation) allows you specify a table to result from a join operation int eh `FROM` clause of a query. 

The `FROM` clause in below returns a single joined table, which has all the attributes of the first table `EMPLOYEE` followed by all the attributes of the second table `DEPARTMENT`.

```SQL
SELECT FName, LName, Address
FROM (EMPLOYEE JOIN DEPARTMENT ON Dno=DNumber)
WHERE Dname='Research';
```

SQL allows different types of joins with different behaviours: 
- `NATURAL JOIN`
- Various `OUTER` joins `OUTER JOIN (LEFT/RIGHT/FULL)`
- `INNER` Join
#### Natural Join
A join on two relations $R$ and $S$ where no join condition is specified. It is equivalent to an implicit `EQUIJOIN` condition for each pair of attributes with the same name from $R$ and $S$ . 

Each such pair of attributes is included only once in the resulting relation

If the names of the join attributes are not the same in the base relations, it is possible to rename attributes so that they match, and then to apply the `NATURAL JOIN`:

```sql
SELECT Fname, Lname, Address 
FROM (EMPLOYEE NATURAL JOIN (DEPARTMENT AS DEPT(Dname, Dno, Mssn, Msdate))) 
WHERE Dname=‘Research’;
```

The above works with `EMPLOYEE.Dno = DEPT.Dno` as an implicit join condition.

#### Inner and Outer Joins
##### Inner Join
The Default type of join in a joined table. A Tuple is included in the result only if a matching tuple exists in the other relation.

##### Left Outer Join
Every tuple in left table must appear in result. If no matching tuple, added with `NULL` values for attributes of right table

##### Right Outer Join 
Same as left, but the right (Joined table) must appear in the result.  If no matching tuple, padded with NULL values for attributes of left table


##### Full Outer Join
Combines results of the LEFT and RIGHT OUTER JOIN, 


#### Multiway Join
Can nest JOIN specifications for a multiway join,

```sql
SELECT A, B, C, D FROM (P JOIN Q ON P.id=Q.id) JOIN R ON Q.id=R.id
```

### Aggregate Functions
Used to summarise information from multiple tuples into a single-tuple summary

Built-in aggregate functions `COUNT`, `SUM`, `MAX`, `MIN`, and `AVG`

```sql
SELECT SUM(Salary) AS Total_Salary, 
	MIN(Salary) AS Min_Salary, 
	MAX(Salary) AS Max_Salary, 
	AVG(Salary) AS Average_Salary,
FROM EMPLOYEE;
```

`NULL` values are discarded when aggregate functions are applied to a particular column.

Use the `COUNT` function to count values in a column (attribute) rather than tuples, e.g.,

```sql
SELECT COUNT(*) AS Num_Employees FROM EMPLOYEE;

SELECT COUNT(Distinct Salary) as Num_Salaries FROM EMPLOYEE;
```

We can specify a correlated nested query with an aggregate function, e.g., to retrieve the names of all employees who have two or more dependents 

```sql
SELECT Lname, Fname 
FROM EMPLOYEE
WHERE (SELECT COUNT(*) FROM DEPENDANT WHERE SSN = ESSN) >= 2;
```

#### Grouping
- Create subgroups of tuples before summarising
- To select entire groups, `HAVING` clause is used
- Aggregate functions can be used in the `SELECT` clause or in a `HAVING` clause

Partition the relation into non-overlapping subsets (or groups) of tuples

Each group (partition) consists of the tuples that have the same value of some attributes, called grouping attribute(s)
  
Apply function to each such group independently to produce summary information about each group
  
`GROUP BY` clause specifies grouping attributes

`COUNT (*)` counts the number of rows in the group

```sql
SELECT Dno, COUNT(*), AVG(salary)
FROM EMPLOYEE
GROUP BY Dno;
```

If the grouping attribute has `NULL` as a possible value, then a separate group is created for the null value (e.g., null `Dno` in the above query).

`GROUP BY` may be applied to the result of a `JOIN`:
```sql
SELECT Pname, Pnumber, COUNT(*)
FROM PROJECT, WORKS_ON
WHERE Pnumber=Pno
GROUP BY Pnumber,Pname
```

The `HAVING` clause can be applied to specify a condition on groups. 

For each project on which more than two employees work, retrieve the project number, the project name, and the number of employees who work on the project:

```sql
SELECT Pnumber, Pname, COUNT (*)
FROM PROJECT,WORKS_ON 
WHERE Pnumber=Pno
GROUP BY Pnumber, Pname 
HAVING COUNT (*) > 2;
```

`WHERE` clause is executed first to select individual or joined tuples 
`HAVING` clause is applied later to select individual groups of tuples.

Correct Specification of the Query: 
Note: the `WHERE` clause applies tuple by tuple whereas `HAVING` applies to entire group of tuples

For each department that has more than five employees, retrieve the department member and the number of its employees who are making more than $40 000
```sql
SELECT Dno, COUNT(*) 
FROM EMPLOYEE 
WHERE Salary > 40000 AND Dno IN 
	(SELECT Dno FROM EMPLOYEE GROUP BY Dno HAVING COUNT(*) > 5) 
GROUP BY Dno;
```

### With Clause
The `WITH` clause allows a user to define a table that will only be used in a particular query (may not be available in all SQL based DBMS implementations) 

Used for convenience to create a temporary “View” and use that immediately in a query, Allows a more straightforward way of looking at a step-by step query 

This temporary “View” (or table) will be discarded after the query is executed. 

The previous example done using `WITH`
```sql
WITH BIGDEPTS(dno) AS 
	(SELECT Dno FROM EMPLOYEE GROUP BY Dno HAVING COUNT(*) > 5)
SELECT Dno, COUNT(*) 
FROM EMPLOYEE
WHERE Salary > 40000 AND Dno IN BIGDEPTS
GROUP BY Dno;
```

Here, we use the `WITH` clause to create a temporary table `BIGDEPTS` and then use this table in the subsequent query. 

### Case Clause
SQL Also provides the `CASE` construct. 

This is used when a value can be different based on certain conditions. Can be used in any part of an SQL query where a value is expected. 

For example, employees are receiving different raises in different departments.

```sql
UPDATE EMPLOYEE 
SET salary = CASE  
	WHEN Dno=5 THE Salary + 2000
	WHEN Dno=4 THE Salary + 1500
	WHEN Dno=1 THE Salary + 3000
	ELSE Salary+0;
```

### Recursive Queries
An example of a recursive relationship between tuples of the same type is the relationship between an employee and a supervisor.

This relationship is described by the foreign key `Super_ssn` of the `EMPLOYEE` relation.

A example of a recursive operation is to retrieve all supervisees of a supervisory employee `e` at all levels:
> All employees e' directly supervised by e, all employees e'' directly supervised by each employee e', all employees e''' directly supervised by each employee e'', and so on. Thus the CEO would have each employee in the company as a supervisee in the resulting table. Example shows such table SUP_EMP with 2 columns (Supervisor, Supervisee (any level)):


```sql
WITH RECURSIVE SUP_EMP (SupSsn, EmpSsn) AS (
	SELECT Super_ssn, Ssn 
	FROM EMPLOYEE 
	UNION 
	SELECT S.SupSsn, E.Ssn 
	FROM EMPLOYEE AS E, SUP_EMP AS S 
	WHERE E.Super_ssn = S.EmpSsn 
) 
SELECT * FROM SUP_EMP;
```

The above query starts with an empty `SUP_EMP` and successively builds `SUP_EMP` table by computing immediate supervisees first, then second level supervisees, etc. until a fixed point is reached and no more supervisees can be added. It references itself while building.


### Summary of Expanded block Structure

```
SELECT <attibute and function list>
FROM <table list>
[WHERE <condition>]
[GROUP BY <grouping attributes>]
[HAVING <group condition>]
[ORDER BY <attribtue list>]
```

- `SELECT` clause lists the attributes or functions to be retrieved
- `FROM` clause specifies all the tables needed 
- `WHERE` clause specifies the conditions for selection 
- `GRROUP BY` specifies grouping attributes 
- `HAVING` specifies a condition on the grouping
- `ORDER BY` specifies an order for displaying the result

### Assertions and Triggers
`CREATE ASSERTION`
- Specify additional types of constraints that are outside scope of the built-in relational model constraints (i.e., primary and unique keys, entity integrity, and referential integrity). 

`CREATE TRIGGER` 
- Specify automatic actions that database system will perform when certain events and conditions occur 
- This type of functionality is generally referred as active databases


#### Assertions
Specify a query that selects any tuples that violate the desired condition. Use only in cases where it goes beyond a simple CHECK which applies to individual attributes and domains. 

E.g. Constraint specifies that the salary of an employee must not be greater than that of the manager in the same department.

```sql
CREATE ASSERTION SALARY_CONSTRAINT
CHECK  (NOT EXISTS (
	SELECT * 
	FROM EMPLOYEE E, EMPLOYEE M, DEPARTMENT D
	WHERE E.SALARY> M.SALARY
		AND E.Dno = D.Dnumber
		AND D.Mgr_ssn = M.Ssn ));
```

#### Triggers
Triggers specify the type of action to be taken when certain events occur and when certain conditions are satisfied.

>E.g. A manager may want to be informed if an employee’s travel expenses exceed a certain limit in the database state

Used to monitor the database, and implement such actions in DBMS

Typical trigger has three components which make it a rule for an “active database” : 
**Event(s):** usually database update operations 
**Condition:** determines whether rule actions should be executed 
**Action:** to be taken, usually SQL statements or transactions, etc.

Suppose we want to check whenever an employee’s salary is greater than the salary of his or her direct supervisor in the `COMPANY` database.  
- Several events can trigger this rule: inserting a new employee record, changing an employee’s salary, or changing an employee’s supervisor. } 

Suppose that the action to take would be to call an external stored procedure `SALARY_VIOLATION,5` which will notify the supervisor.

```sql
CREATE TRIGGER SALARY_VIOLATION
BEFORE INSERT OR UPDATE 
	OF SALARY, SUPERVISOR_SSN ON EMPLOYEE
FOR EACH ROW
	WHEN (NEW.SALARY > (
		SELECT SALARY 
		FROM EMPLOYEE 
		WHERE SSN = NEW.SUPERVISOR_SSN))
	INFORM_SUPERVISOR(NEW.Supervisor_ssn, NEW.Ssn);
```

### Views (Virtual Tables)
In SQL, A view is a single table that is derived from other tables. This can be a base table or a previously defined view. 

It does not necessarily exist in physical form, is considered to be a virtual table (In contrast to base tables)

As a way of specifying a table that we need to reference frequently, even though it may not exist physically. 

Once a View is defined, SQL queries can use the View relation in the FROM clause. The View is always kept up-to-date, this is the responsibility of the DBMS and not the user.

To create a view use the `CREATE VIEW` Command. 
- Give the table name, list of attributes and a query to specify the contents of the view.

In this view, the attributes retain the names from the base tables. 
```sql
CREATE VIEW WORKS_ON1
AS SELECT Fname, Lname, Pname, Hours
FROM EMPLOYEE, PROJECT, WORKS_ON
WHERE Ssn = Essn AND Pno = Pnumber;
```

```sql
CREATE VIEW DEPT_INFO(Dept_name, No_of_emps, Total_Sal)
AS SELECT DName, COUNT(*), SUM(Salary)
	FROM DEPARTMENT, EMPLOYEE
	WHERE DNumber = Dno
	GROUP BY Dname;
```

`DROP VIEW` Disposes of a view. 

#### View Implementations
Complex problem of efficiently implementing a view for querying 

##### Query modification approach  
Compute the view as and when needed. Do not store permanently, Modify view query into a query on underlying base tables

Disadvantage: inefficient for views defined via complex queries that are time-consuming to execute

##### View materialization 
Physically create a temporary view table when the view is first queried or created, Keep that table on the assumption that other queries on the view will follow 

Requires efficient strategy for automatically updating the view table when the base tables are updated.

This requires an update strategy for materialized views where the DBMS determines what new tuples must be inserted, deleted, or modified in a materialized view table.

There are multiple ways to handle materialization:
- **Immediate update** strategy updates a view as soon as the base tables are changed 
- **lazy update** strategy updates the view when needed by a view query 
- **periodic update** strategy updates the view periodically (in the latter strategy, a view query may get a result that is not up-to-date). This is commonly used in Banks, Retail store operations, etc.

#### View Updates
Update on a view defined on a single table without any aggregate functions. 

Can be mapped to an update on underlying base table possible if the primary key is preserved in the view. 

Update not permitted on aggregate views eg:
```sql
UPDATE DEPT_INFO 
SET Total_Sal = 10000 
WHERE Dname = 'Research'
```

When views involve joins, it is often not possible for the DBMS to determine which of the update is intended.

The clause `WITH CHECK OPTION` must be added at the end of the view definition if a view is to be updated to make sure that tuples being updated stay in the view. 

#### In-line view
Defined in the `FROM` clause of a query. it is used in the `WITH` [[06 Server Query Language#With Clause|section]]

#### Views in Authorisation
SQL Query auth statements are discussed later (`GRANT` and `REVOKE`), but views can be used to hide certain attributes or tuples from unauthorised users.  

For a user who is only allowed to see employee department for those who work at department 5, they may only access the view DEPT5EMP:
```sql
CREATE VIEW DEPT5EMP AS
SELECT * 
FROM EMPLOYEE
WHERE Dno = 5;
```

### Schema evolution
Schema evolution commands can be used to alter a schema by adding or dropping tables, attributes, constraints and other schema elements. 

DBA May want to change the schema while database is operational, these command do not require recompilation of the database schema. 

Certain checks must be performed by the DBMS to ensure that the changes do not affect the rest of the database and cause inconsistencies. 


#### Drop command
The `DROP` Command is used to drop named schema elements such as tables, domains, constraints etc. 

The drop command has two behaviour options `CASCADE` and `RESTRICT`. 

`DROP SCHEMA COMPANY CASCADE` will remove the schema and all of its elements, including tables, views, constraints etc. 

`DROP TABLE DEPENDENT RESTRICT` The table will only be dropped if it is not referenced in any constraints. 


#### The alter command
Alter table actions include: 
- Adding or dropping a column (attribute) 
- Changing a column definition 
- Adding or dropping table constraints 

 ```sql
ALTER TABLE COMPANY.EMPLOYEE ADD COLUMN Job VARCHAR(12);
```

We must still enter a value for the new attribute Job for each individual `EMPLOYEE` tuple. This can be done by using the default clause or the `UPDATE` command.

##### Removing columns

When dropping a column, you must choose either `CASCADE` or `RESTRICT` 

`CASCADE`: all constraints and views that reference the column are dropped automatically 
`RESTRICT`: the drop is successful only when no views or constraints (or other schema elements) reference the column

```sql
ALTER TABLE COMPANY.EMPLOYEE DROP COLUMN Address CASCADE;
```

##### Default values
Default values can be dropped or altered. 

```sql
ALTER TABLE COMPANY.EMPLOYEE ALTER COLUMN Mgr_ssn
DROP DEFAULT;

ALTER TABLE COMPANY.EMPLOYEE ALTER COLUMN Mgr_ssn
SET DEFAULT "12312313;
```

##### Constraints
We can change the constraints specified on a table by adding or dropping a named constraint 

For example, to drop the constraint named `EMPSUPERFK` from the `EMPLOYEE` table

After removing the constraint, we can redefine a replacement constraint using the `ADD CONSTRAINT` keyword in the `ALTER TABLE` statement.

```sql
ALTER TABLE COMPANY.EMPLOYEE
DROP CONSTRAINT EMPSUPERFK CASCADE;

ALTER TABLE COMPANY.EMPLOYEE
ADD CONSTRAINT EMPSUPERFK
	FOREIGN KEY(Super_ssn) REFERENCES EMPLOYEE(Ssn)
	ON DELETE SET NULL ON UPDATE CASCADE;
```