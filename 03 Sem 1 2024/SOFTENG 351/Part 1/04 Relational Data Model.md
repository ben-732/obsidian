The Relational Model of data is based on the concept of a Relation
  
  The strength of the relational approach to data management comes from the formal foundation provided by the theory of relations
  
  The Relational Model has been implemented in many commercial and open source DBMSs, such as BD2 (IBM), Oracle, Sybase DBMS (SAP), SQL Server, Microsoft Access, MySQL, PostgreSQL, etc.
    
A Relation is a mathematical concept based on the ideas of sets
Informally, a relation looks like a table of values.
A relation typically contains a set of rows .

The data elements in each row represent certain facts that correspond to a real-world entity or relationship In the formal model, rows are called *tuples*

Each column has a column header that gives an indication of the meaning of the data items in that column. In the formal model, the column header is called an attribute name (or just *attribute*)

![[Pasted image 20240307101435.png | center | 500]]

####  Definitions

##### Key of a Relation
Each row has a value of a data item (or set of items) that uniquely identifies that row in the table called the *key*. 

 In the `STUDENT` table, `SSN` is the key

 Sometimes row-ids or sequential numbers are assigned as keys to identify the rows in a table, these are called artificial keys or surrogate keys.

##### Schema
The description of a relation. 
- Denoted by R(A1,A2, .....An)  
- R is the name of the relation  
- The attributes of the relation are A1,A2, ...,An

Example:
`CUSTOMER (Cust-id, Cust-name,Address, Phone#)`

- `CUSTOMER` is the relation name
- Defined over the four attributes: `Cust-id`, `Cust-name`,`Address`, `Phone#`

Each attribute has a domain or a set of valid values.  
For example, the domain of `Cust-id` is 6 digit numbers.

##### Tuple
A tuple is an ordered set of values (enclosed in angled brackets ‘< ... >’)
- Each value is derived from an appropriate domain.
- A row in the CUSTOMER relation is a 4-tuple and would consist of four values, for example:
```
<632895,"JohnSmith","101MainSt.Atlanta,GA 30332", "(404) 894-2000">`
```

This is called a 4-tuple as it has 4 values  
- A tuple (row) in the `CUSTOMER` relation.
- A relation is a set of such tuples (rows)

##### Domain
A domain has a logical definition:  
Example: `USA_phone_numbers` are the set of 10 digit phone numbers valid in the U.S.

A domain also has a data-type or a format defined for it.
- The `USA_phone_numbers` may have a format: `(ddd)ddd-dddd` where each d is a decimal digit.
- Dates have various formats such as year, month, date formatted as `yyyy-mm-dd`, or as `dd mm,yyyy` etc.

The attribute name designates the role played by a domain in a relation:
- Used to interpret the meaning of the data elements corresponding to that attribute
- Example: The domain Date may be used to define two attributes named `Invoice-date` and `Payment-date` with different meanings

##### State
The relation state is a subset of the Cartesian product of the domains of its attributes
- Each domain contains the set of all possible values the attribute can take.
  
Example: attribute `Cust-name` is defined over the domain of character strings of maximum length 25
  `dom(Cust-name)` is `varchar(25)`
  
The role these strings play in the `CUSTOMER` relation is that of the name of a customer.


##### Summary
Given $R(A_{1}, A_{2}, \dots, A_{n})$
- $r(R) \subset \text{dom}(A_{1})\, \times \text{ dom}(A_{2})\, \times \dots \,\times \text{ dom} (A_{n)}$

$R(A_{1}, A_{2}, \dots, A_{n})$ is the schema of the relation
$R$ is the name of the relation
$A_{1}, A_{2}, \dots, A_{n}$ are the attributes of the relation
$r(R)$ is a specific state or "value"/"population"/ of relation $R$ - this is a set of tuples (rows)
$r(R) = \{ t_{1}, t_{2}, \dots, t_{n} \}$ where each $t_{i}$ is a n-tuple
$t_{i} = <v_{1}, v_{2}, \dots v_{n}>$ where each $v_{j}$ is an element of $\text{dom}(A_{j})$

##### Example
Let $R(A_{1}, A_{2})$ be a relation schema:
- Let $\text{dom}(A_{1}) = \{ 0, 1 \}$
- Let $\text{dom}(A_{2}) = \{ a, b, c \}$

Then $\text{dom}(A_1) \times \text{dom}(A_{2})$ is all possible combinations. $\{ <0, a>, <0,b>, \dots, <1, c> \}$
The relation state $r(R) \subset \text{dom}(A_{1}) \times \text{dom}(A_{2})$

For example $r(R)$ could be $\{ <0, a>, <1, b> \}$
This is one possible state $r$ of the relation $R$ defined over $A_{1}$ and $A_{2}$. It has two tuples $<0, a>$ and $<1, b>$

| Informal term              | Formal term        |
| -------------------------- | ------------------ |
| Table                      | Relation           |
| Column Header              | Attribute          |
| All possible Column Values | Domain             |
| Row                        | Tuple              |
|                            |                    |
| Table Definition           | Schema of relation |
| Populated Table            | State of relation  |
### Characteristics of relations
#### Ordering 
Ordering of tuples in a relation $r(R)$
- The tuples are not considered to be ordered, even through they appear in tabular form. 

Ordering of attributes in a relation schema $R$ (and of values within each tuple):
- We will consider the attributes in $R(A_{1},A_{2}, \dots,A_{n}$) and the values in $t=\, <v_{1}, v_{2}, .., v_{n}>$ to be ordered 

However, a more general alternative definition of relation does not require this ordering. It includes both the name and the value for each of the attributes. 
- Example: `t = { <name,“John” >, <SSN, 123456789> }`
- This representation may be called as “self-describing”


#### Values in a tuple:
All values are considered atomic (indivisible). Each value in a tuple must be from the domain of the attribute for that column

If tuple $t = <v_{1},v_{2},...,v_{n}>$ is a tuple (row) in the relation state $r$ of $R(A_{1},A_{2}, ...,A_{n})$ Then each $v_{i}$ must be a value from $\text{dom}(A_{i})$

A special null value is used to represent values that are unknown or not available or inapplicable in certain tuples.

#### Component Values
We refer to component values of a tuple $t$ by `t[Ai]` or `t.Ai`. This represents the value $v_{i}$ of Attribute $A_{i}$ for tuple $t$. 

Similarly, `t[Au, Av, ..., Aw]` refers to the sub-tuple of $t$ containing the values of  the respective attributes in $t$

### Constraints
Constraints determine which values are permissible and which are not in the database. They are of three main types. 

**Inherent or Implicit constraints:** These are based on the data model itself. 
- A relational model does not allow a list as a value for any attribute. 

**Schema-based or Explicit constraints:** These are expressed in the schema by using the facilities provided by the model. 
- Max cardinality ration constraint in the ER model.

**Application based or semantic constraints:** These are beyond the expressive power of the model and must be specified and enforced by the application programs.

Constraints are *conditions* that must hold on all valid relation states

There are three main types of (explicit schema-based) constraints that can be expressed in the relational model:
- Key constraints
- Entity Integrity constraints
- Referential  integrity constraints

Another schema-based constraint is the domain constraint.  Every value in a tuple must be from the domain of its attribute (or it could be null, if allowed for that attribute)

#### Key constraints

**Superkey** of $R$ - A set of attributes $SK$ of $R$ with the condition that no two tuples in any valid relation state $r(R)$ will have the same value for $SK$. 

That is, for any distinct tuples, $t_{1}$ and $t_{2}$ in $r(R)$, $t_{1}[SK] \neq t_{2}[SK]$. This uniqueness property must hold in any valid state $r(R)$. 

**Key** of $R$ - A "minimal" superkey. The key is a superkey $K$ such that removal of any attribute from $K$ results in a set of attributes that is not a superkey (does not possess the superkey uniqueness property). IE Contains the minimum required attributes to provide uniqueness. 


A key is a superkey but a superkey is not always a key 

Example: Consider the `CAR` relation schema: 
- `CAR(State, Reg#, SerialNo, Make, Model, Year)` 

`CAR` has two keys:  
- `Key1 = {State, Reg#} `
- `Key2 = {SerialNo}` 

Both are also superkeys of `CAR` 
- `{SerialNo, Make}` is a superkey but not a key as `Make` does not contribute to uniqueness?

In general: Any key is a superkey (but not vice versa), Any set of attributes that includes a key is a superkey. A minimal superkey is also a key.

##### Primary Key
If a relation has several candidate keys, one is chosen arbitrarily to be the primary key.
- The primary key attributes are underlined. 
- `CAR(State, Reg#, __SerialNo__, Make, Model, Year)` Here serial number is the primary key. 

The primary key value is used to uniquely identify each tuple in a relation
- Provides the tuple identity

Also used to reference the tuple from another tuple 
- General rule: Choose as primary key the smallest of the candidate keys (in terms of size). This is always applicable, the choice is sometimes subjective

![[Pasted image 20240312210937.png | center | 400]]

> The `CAR` relation with two candidate keys. `License_number` and `Enginer_serial_number`

#### Odd Seague
##### Relational Database Schema
A set $S$ of relation schemas that belong to the same database. 

$S$ is the name of the whole database schema  $S = {R_{1}, R_{2}, \dots, R_{n}}$ and a set $IC$ of integrity constraints. 

$R_{1}, R_{2}, \dots, R_{n}$ are the names of the individual relation schemas within the database $S$ 

A `COMPANY` database schema with 6 relation schemas

![[Pasted image 20240312211256.png| center | 500]]

##### Relational Database Schema

A relational [[01 Intro to Databases#Database State|database state]] $DB$ of $S$ is a set of relation states $DB = {r_{1}, r_{2}, \dots, r_{m}}$ such that each $r_{i}$ is a state of $R_{i}$ and such that the $r_{i}$ relation states satisfy the integrity constraints specified in $IC$

A relational database state is sometimes called a relational database snapshot or instance. However, We will not use the term instance since it also applies to single tuples.

A database state that does not meet the constraints is an invalid state.

##### Populated Database state
Each relation will have many tuples in its current relation state 
The relational database state is a union of all the individual relation states 
Whenever the database is changed, a new state arises 

Basic operations for changing the database: 
- `INSERT` a new tuple in a relation 
- `DELETE` an existing tuple from a relation 
- `MODIFY` an attribute of an existing tuple

![[Pasted image 20240312212259.png | center | 500]]

> An example state of a `COMPANY` database schema

#### Entity Integrity
The primary key attributes $PK$ of each relation schema $R$ in $S$ cannot have `null` values in any tuple of $r(R)$

This is because primary key values are used to identify the individual tuples.  $t[PK] \neq \text{null}$ for any tuple $t$ in $r(R)$ 

If $PK$ has several attributes, `null` is not allowed in any of these attributes

Other attributes of R may be constrained to disallow null values, even though they are not members of the primary key.

#### Referential Integrity (Foreign Key)
It is a constraint involving two relations whereas the previous constraints involve a single relation.  

Used to specify a relationship among tuples in two relations, i.e. The referencing relation and the referenced relation. 

Tuples in the referencing relation $R1$ have attributes $FK$ (called foreign key attributes) that reference the primary key attributes $PK$ of the referenced relation $R2$. 
A tuple $t_{1}$ in $R_{1}$ is said to reference a tuple $t_{2}$ in $R_{3}$ if $t_{1}[FK] = t_{2}[PK]$.


A referential integrity constraint can be displayed in a relational database schema as a directed arc from `R1.FK` to `R2.PK`

The value in the foreign key column (or columns) `FK` of the the referencing relation `R1` can be either: 
-  a value of an existing primary key value of a corresponding primary key `PK` in the referenced relation `R2`, 
- null.

If null, the `FK` in `R1` should not be a part of its own primary key.

#### Displaying a RDB schema and its constraints
Each relation schema can be displayed as a row of attribute names, The name of the relation is written above the attribute names

The primary key attribute (or attributes) will be underlined

A foreign key (referential integrity) constraints is displayed as a directed arc (arrow) from the foreign key attributes to the referenced table (Can also point the the primary key of the referenced relation for clarity)

![[Pasted image 20240312214429.png | center | 500]]


#### Semantic Integrity Constraints
Based on application semantics and cannot be expressed by the model itself. 

Example: “the max. no. of hours per employee for all projects he or she works on is 56 hours per week”

A constraint specification language may have to be used to express these } SQL-99 allows `CREATE TRIGGER` and `CREATE ASSERTION` to express some of these semantic constraints.

Keys, Permissibility of Null values, Candidate Keys (Unique in SQL), Foreign Keys, Referential Integrity etc. are expressed by the `CREATE TABLE` statement in SQL.

### Operations on Relations
- `INSERT` a tuple. 
- `DELETE` a tuple. 
- `MODIFY` a tuple. 

Integrity constraints should not be violated by any of the update operations.  Several update operations may have to be grouped together. Updates may propagate to cause other updates automatically. This may be necessary to maintain integrity constraints.

If an operation causes an integrity violation, several actions can be taken: 
- Cancel the operation that causes the violation (RESTRICT or REJECT option) 
- Perform the operation but inform the user of the violation 
- Trigger additional updates so the violation is corrected (CASCADE option, SET NULL option) 
- Execute a user-specified error-correction routine

#### Possible violations by operation
##### Insert
**Domain constraint:** 
If one of the attribute values provided for the new tuple is not of the specified attribute domain

**Key constraint:** 
If the value of a key attribute in the new tuple already exists in another tuple in the relation 

**Referential integrity:** 
If a foreign key value in the new tuple references a primary key value that does not exist in the referenced relation 

**Entity integrity:** 
If the primary key value is null in the new tuple

##### Update
May violate domain constraint and NOT NULL constraint on an attribute being modified

Any of the other constraints may also be violated, depending on the attribute being updated:

Updating the primary key (PK):
- Similar to a `DELETE` followed by an `INSERT` 
- Need to specify similar options to `DELETE`

Updating a foreign key (FK):
- May violate referential integrity

Updating an ordinary attribute (neither PK nor FK): 
- Can only violate domain constraints
##### Delete
**Referential integrity:** 
If the primary key value of the tuple being deleted is referenced from other tuples in the database

Can be remedied by several actions: `RESTRICT`, `CASCADE`, `SET NULL` (see next topic for more details)

`RESTRICT` - reject the deletion  
`CASCADE` - propagate the new primary key value into the foreign keys of the referencing tuples
`SET NULL` - set the foreign keys of the referencing tuples to NULL

One of the above options must be specified during database design for each foreign key constraint

