x
Relationships are references between two entity type

The initial design in the previous page is not complete as some of the the individual entities are all required to be related to each other. 

An ER model has three main concepts
- Entities (and their entity types and entity sets) 
- Attributes (simple, composite, multivalued) 
- *Relationships* (and their relationship types and relationship sets)

A relationship relates two or more distinct entities with a specific meaning
- `EMPLOYEE` John Smith works on the ProductX `PROJECT`, or `EMPLOYEE` Franklin Wong manages the Research `DEPARTMENT`

Relationships of the same type are grouped or typed into a relationship type
- the `WORKS_ON` relationship type is between `EMPLOYEE`s and `PROJECT`s 
- the `MANAGES` relationship type in between `EMPLOYEE`s and `DEPARTMENT`

The degree of a relationship type is the number of participating entity types.
- Both `MANAGES` and `WORKS_ON` are binary relationships (They are between two things, degree 2)


WORKS_FOR is a N:1 relationship between EMPLOYEE and DEPARTMENT

![[Pasted image 20240305150534.png | center | 400]]

WORKS_ON is a M:N  relationship between EMPLOYEE and PROJECT
![[Pasted image 20240305150716.png | center | 400]]

### Relationship Types and Sets

Relationship Type:  
- Is the schema description of a relationship
- Identifies the relationship name and the participating entity types 
- Also identifies certain relationship constraints

Relationship Set:
- The current set of relationship instances represented in the database
- The current state of a relationship type

Previous figures displayed the relationship sets. Each instance in the set relates individual participating entities – one from each participating entity type
In ER diagrams, we represent the relationship type as follows:
- Diamond-shaped box is used to display a relationship type
- Connected to the participating entity types via straight lines
- Note that the relationship type is not shown with an arrow. The name should be typically be readable from left to right and top to bottom.


#### In the company example
By examining the requirements, six relationship types are identified, All are binary relationships. 
- `WORKS_FOR` (between `EMPLOYEE`, `DEPARTMENT`)
- `MANAGES` (also between `EMPLOYEE`, `DEPARTMENT`)
- `CONTROLS` (between `DEPARTMENT`, `PROJECT`)
- `WORKS_ON` (between `EMPLOYEE`, `PROJECT`)
- `SUPERVISION` (between `EMPLOYEE` (as subordinate), `EMPLOYEE` (as supervisor))
- `DEPENDENTS_OF` (between `EMPLOYEE`, `DEPENDENT`)
- 
![[Pasted image 20240305151151.png | center | 500]]


#### Constraints of relationship types

Cardinality Ratio (specifies maximum participation) - also known as ratio constraints
- One-to-one (1:1)  
- One-to-many (1:N) or Many-to-one (N:1) 
- Many-to-many (M:N)

Existence Dependency Constraint (specifies minimum participation) - also called participation constraint
- zero (optional participation, not existence-dependent)  
- one or more (mandatory participation, existence-dependent)

#### Recursive Relationship types 
A relationship type between the same participating entity type in distinct roles, Also called a self-referencing relationship type.
    
Example: the `SUPERVISION` relationship, the `EMPLOYEE` participates twice in two distinct roles: 
- supervisor (or boss) role supervised (or subordinate) role

Each relationship instance relates two distinct `EMPLOYEE` entities:
- One employee in supervisor role 
- One employee in supervised role

In a recursive relationship type both participations are same entity type in different roles.

For example, `SUPERVISION` relationships between `EMPLOYEE` (in role of supervisor or boss) and (another) `EMPLOYEE` (in role of subordinate or worker).

- In following figure, first role participation labeled with 1 and second role participation labeled with 2.
- In ER diagram, need to display role names to distinguish participations.

![[Pasted image 20240305152246.png | center | 400]]

#### Attributes of Relationship types
A relationship type can have attributes:

For example, `HoursPerWeek` of `WORKS_ON`
- Its value for each relationship instance describes the number of hours per week that an `EMPLOYEE` works on a `PROJECT`.
- A value of HoursPerWeek depends on a particular (employee, project) combination
    
Most relationship attributes are used with M:N relationships, In 1:N relationships, they can be transferred to the entity type on the N-side of the relationship


#### Relationship constraint notations
**Cardinality ratio** (of a binary relationship): 
Can be one of 1:1, 1:N, N:1, or M:N
- This is shown by placing appropriate numbers on the relationship edges.


**Participation constraint** on each participating entity type: 
Can be either Total (called existence dependency) or Partial.
- Total participation is shown by a double line, partial is denoted by a single line.

NOTE: These are easy to specify for Binary Relationship Types.

A Total participation of an entity set represents that **each entity** in entity set must have ==at least one== relationship in a relationship set. 
- For example: In the below diagram each college must have at-least one associated Student

![[Pasted image 20240305153553.png | center | 240]]
#### Min Max Notation
This is an Alternative notation for relationship structural constraints.

Specified on each participation of an entity type $E$ in a relationship type $R$

Specifies that each entity $e \in E$ participates in at least $\text{min}$ and at most $\text{max}$ relationship instances in $R$

Default (no constraint): $\text{min}=0$, $\text{max}=n$ (meaning there is no limit) and must always satisfy: $$\text{min}\leq \text{max},\, \text{min}\geq 0,\, \text{max}\geq 1$$
Derived from the knowledge of mini-world constraints

##### Examples
A department has exactly one manager and an employee can manage at most one department.
- Specify (0,1) for participation of `EMPLOYEE` in `MANAGES`
- Specify (1,1) for participation of `DEPARTMENT` in `MANAGES`

An employee can work for exactly one department but a department can have any number of employees.
- Specify (1,1) for participation of `EMPLOYEE` in `WORKS_FOR`
- Specify (0,n) for participation of `DEPARTMENT` in `WORKS_FOR`

![[Pasted image 20240305154134.png | center | 500]]

Read the (min, max) numbers next to the entity type and looking away from the entity type. An employee can work for one department but a department can have any amount of people (>1) working for it.

##### Company using min max notation
![[Pasted image 20240305154413.png | center | 500]]


### Weak entities
An entity that does not have a key attribute and that is identification-dependent on another entity type. It must participate in an identifying relationship type with an owner or identifying entity type.

Entities are identified by the combination of:
- A partial key of the weak entity type, which is denoted by *underlined with a dashed or dotted line*.
- The particular entity they are related to in the identifying relationship type

#### Example 
A `DEPENDENT` entity is identified by the dependent’s first name, and the specific `EMPLOYEE` with whom the dependent is related.
- Name of `DEPENDENT` is the partial key
- `DEPENDENT` is a weak entity type
- `EMPLOYEE` is its identifying entity type via the identifying relationship type `DEPENDENT_OF`

![[Pasted image 20240305152851.png | center | 300]]


### Higher Degree Relationships
The relationship types covered so far have all been of degree 2 and are called binary

It is possible to have relationships of a higher degree, relationship types of degree 3 are called ternary and of degree n are called `n-ary`

> In general, an `n-ary` relationship is not equivalent to n binary relationships

Constraints are harder to specify for higher-degree relationships (n > 2) than for binary relationships

In general, 3 binary relationships represents different information than a single ternary relationship (a and b). 
    
 If needed, the binary and `n-ary` relationships can all be included in the schema design (see a and b, where all relationships convey different meanings)
    
In some cases, a ternary relationship can be represented as a weak entity if the data model allows a weak entity type to have multiple identifying relationships (and hence multiple owner entity types) (see c)

![[Pasted image 20240307094608.png | center | 250]]

If a binary relationship can be derived from a higher order relationship at all times, it is then redundant. 

For example, the `TAUGHT_DURING` relationship can be derived from the ternary relationship `OFFERS`
![[Pasted image 20240307095339.png | center | 300]]
#### Constraints on higher degree relationships
This is very messy to do. 

$(\text{min, max})$ relationships can be displayed on the edged, however these don't fully describe the constraints. 

Displaying a 1, M or N indicated additional constraints:
- M or N indicates no constraint
- 1 Indicates that an entity can participate in at most one relationship instance that has a particular combination of other participating relationships

In general, both (min, max) and 1, M or N are needed to fully describe constraints, however it is difficult and possible ambiguous with a degree higher than 2. 

##### Example
To keep track of the enrolments in classes and student grades, another database is to be designed.

  It keeps track of the COLLEGEs, DEPARTMENTs within each college, the COURSEs offered by departments, and SECTIONs of courses, INSTRUCTORs who teach the sections etc.
  
  ![[Pasted image 20240307100529.png | center | 400]]
## Other Tools
A number of popular tools that cover conceptual modelling and mapping into relational schema design.

Examples: ERWin, S- Designer (Enterprise Application Suite), ER- Studio, etc.

POSITIVES:  
- Serves as documentation of application requirements, easy user interface - mostly graphics editor support

NEGATIVES:  
- Most tools lack a proper distinct notation for relationships with relationship attributes
- Mostly represent a relational design in a diagrammatic form rather than a conceptual ER-based design

Automated Database Design tools
![[Pasted image 20240307101134.png | center | 400]]

