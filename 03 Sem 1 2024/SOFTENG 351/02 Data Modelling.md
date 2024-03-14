### Database Design
The database design process has two main activities
- Database Design
- Applications design

In this course we will focus on the conceptual database design, to design a conceptual schema for a database application

![[Pasted image 20240229161646.png | center | 400]]

#### Conceptual Design methods
- Entity Relationship (ER) Diagrams 
- Enhanced Entity Relationship (EER) Diagrams 
- Use of Design Tools in industry for designing and documenting large scale designs 
- The UML (Unified Modelling Language) Class Diagrams are popular in industry to document conceptual database designs


### ER Models
#### Entities and Attributes
An **entity** is a basic concept for the ER model. Entities are specific things or objects in the mini-world that are represented in the database.
For example, the `EMPLOYEE` John Smith, the Research `DEPARTMENT`, the ProductX `PROJECT`

**Attributes** are properties used to describe an entity
For example an `EMPLOYEE` entity may have the attributes `Name`, `SSN`, `Address`, `Sex`, `BirthDate`

A specific entity will have a value for each of its attributes
For example a specific employee entity may have `Name='John Smith'`, `SSN='123456789'`, `Sex='M'`, `BirthDate='09-01-55‘`

Each attribute has a value set (or data type) associated with it – e.g. integer, string, date, enumerated type

##### Types of Attributes
**Simple:** Each entity has a single atomic value for the attribute. 
- SSN  
- Sex

**Composite:** The attribute may be composed of several components. The Composition may form a hierarchy where some components are themselves composite
- Address(Apt#, House#, Street, City, State, ZipCode, Country)
- Name(FirstName, MiddleName, LastName)

**Multi-valued:** An entity may have multiple values for that attribute. Denoted as {Color} or {PreviousDegrees}.
- Colour of a `CAR`  
- PreviousDegrees of a `STUDENT`

**Derived** Attributes whose value is dynamic and derived from another attribute. It is represented by dashed oval in an ER Diagram
- Person's age is a derived attribute as it changes over time and can be derived from another attribute (Date of birth).

 In general, composite and multi-valued attributes may be nested arbitrarily to any number of levels, although this is rare
 - `PreviousDegrees` of a `STUDENT` is a composite multi-valued attribute denoted by `{PreviousDegrees (College, Year, Degree, Field)}`
	- Here, Multiple PreviousDegrees values can exist. Each has four subcomponent attributes: `College`, `Year`, `Degree`, `Field`
![[Pasted image 20240229163316.png | center | 500]]

Example of composite attributes
![[Pasted image 20240229163702.png | center | 400]]

#### Entity Types / Key Attributes
Entities with the same basic attributes are grouped or typed into an entity type
- For example, the entity type `EMPLOYEE` and `PROJECT`

An attribute of an entity type for which each entity must have a unique value is called a key attribute of the entity type.
- `SSN` of `EMPLOYEE`

A key attribute may be composite
- `VehicleTagNumber` is a key of the `CAR` entity type with components `(Number, State)`.

An entity type may have more than one key
- The `CAR` entity type may have two keys: 
	- `VehicleIdentificationNumber` (popularly called VIN) } 
	- `VehicleTagNumber` (Number, State), aka license plate number

Each key is underlined (Note: this is different from the relational schema where only one “primary key" is underlined).


#### Entity Sets
Each entity type will have a collection of entities stored in the database, e.g., CAR entity instances, also known as the entity set or sometimes entity collection. 
![[Pasted image 20240229164446.png | center | 400]]

The entity set is the current state of the entities of that type that are stored in the database

#### Value Sets (Domains) of Attributes
Each simple attribute is associated with a value set, these value sets are also known as *data types* 
- `Lastname` has a value which is a character string of up to 15 characters
- `Date` has a value consisting of MM-DD-YYYY where each letter is an integer

A value set specifies the set of values associated with an attribute, Value sets are similar to *data types* in most programming languages.


Mathematically, an attribute $A$ for an entity type $E$ whose value set is $V$ is defined as a function

$$A: E \to P(V)$$
We refer to the value of attribute $A$ for entity $e$ as $A(e)$.

#### Displaying an entity type
In ER diagrams, an entity type is displayed in a rectangular box
Attributes are displayed in ovals
- Each attribute is connected to its entity type 
- Components of a composite attribute are connected to the oval representing the composite attribute 
- Each key attribute is underlined 
- Multivalued attributes displayed in double ovals

![[Pasted image 20240229165055.png | center | 300]]

##### Examples
Entity Type `CAR` with two keys and a corresponding Entity Set:
(a) is the ER diagram. (b) is the entity set.
![[Pasted image 20240229165140.png | center | 600]]

Project example
![[Pasted image 20240229165349.png | center | 500]]



#### Relationships
Joins between entities

Described in [[03 Modelling Relationships]]

#### Summary of notation
![[Pasted image 20240305154523.png | center | 280]]


### UML class diagrams
ER diagrams are one popular example for displaying database schemas however there are many other notations that exist in the literature and in various database design and modelling tools

UML class diagrams is representative of another way of displaying ER concepts that is used in several commercial design tools.

They represent classes (similar to entity types) as large rounded boxes with three sections:  
- Top section includes entity type (class) name  
- Second section includes attributes
- Third section includes class operations (operations are not in basic ER model)

Relationships are called associations represented as lines connecting the classes. 

It is commonly used in database design and object-oriented software design, UML has many other types of diagrams for software design.


#### Company Schema example
![[Pasted image 20240305155032.png | center | 500]]