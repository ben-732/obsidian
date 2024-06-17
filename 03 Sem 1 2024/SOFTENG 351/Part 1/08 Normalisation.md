### Informal design guidelines
Relational database design is the grouping of attributes to form "good" relation schemas, There are two levels of relation schemas:

**Guidelines 1:**
Informally, each tuple in a relation should represent one entity or relationship instance. (Applies to individual relations and their attributes).
- Attributes of different entities (`EMPLOYEE`s, `DEPARTMENT`s, `PROJECT`s) should not be mixed in the same relation
- Only foreign keys should be used to refer to other entities
- Entity and relationship attributes should be kept apart as much as possible
- Design a schema that can be explained easily relation by relation.The semantics of attributes should be easy to interpret.

#### Redundant information and Anomalies
When redundant information is stored in databases, it causes space to be wasted and issues to occur on update anomalies. 

![[Pasted image 20240418140200.png | center | 300]]
For this table schema. 
Update anomalies: You have to update every employee to change the name of a project, potentially 100s of rows. 

Insert Anomaly: You cannot insert a project without inserting an employee

Delete Anomaly: You cannot delete a project without deleting all associated employees. 

**GUIDELINE 2:**  
Design a schema that does not suffer from the insertion, deletion and update anomalies.
- If there are any anomalies present, then note them so that applications can be made to take them into account.

#### Null Values
**GUIDELINE 3:**
Relations should be designed such that their tuples will have as few `NULL` values as possible
- Attributes that are NULL frequently could be placed in separate relations (with the primary key)


Common reasons for nulls:  
- Attribute not applicable or invalid  
- Attribute value unknown (may exist) 
- Value known to exist, but unavailable

#### Spurious Tuples
Bad designs for a relational database may result in erroneous results for certain `JOIN` operations. They should be avoided at all costs. 

**GUIDELINE 4:**
Design relation schemas so that they can be joined with equality conditions on attributes that are appropriately related (primary key, foreign key) pairs in a way that guarantee that no spurious tuples are generated.

Avoid relations that contain matching attributes that are not (foreign key, primary key) combinations because joining on such attributes may produce spurious tuples.

#### Summary
Anomalies that cause redundant work to be done during insertion into and modification of a relation, and that may cause accidental loss of information during a deletion from a relation.
    
Waste of storage space due to NULLs and the difficulty of performing selections, aggregation operations and joins due to NULL values
    
Generation of invalid and spurious data during joins on base relations with matched attributes that may not represent a proper(foreign key, primary key) relationship.


### Functional Dependencies
Functional dependencies (FDs) Are used to specify formal measures of the quality ("goodness”) of relational designs. Keys are used to define normal forms for relations.

Are constraints that are derived from the meaning and interrelationships of the data attributes

A set of attributes `X` functionally determines a set of attributes `Y`, if the value of `X` determines a unique value for `Y`

$X \to Y$  means that if two tuples have the same value for $X$, they must have the same value for $Y$

>For any two tuples `t1` and `t2` in any relation instance $r(R)$: If `t1[X]=t2[X]`, then `t1[Y]=t2[Y]`

$X \to Y$ in $R$ specifies a *constraint* on all relation instances $r(R)$. It can be displayed graphically on the relation schema as denoted by the arrow

![[Pasted image 20240418142603.png | center | 400]]

FDs are derived from the real-world constraints on the attributes.

For example:
Social security number determines employee name: $\text{Ssn} \to \text{Ename}$

Project number determines project name and location: $\text{Pnumber} \to \text{Pname, Plocation}$

Employee ssn and project number determines the hours per week that the employee works on the project

A Functional Dependency (FD) is a property of the attributes in the relation schema R the constraint must hold on every relation instance $r(R)$
  
If $K$ is a key of $R$, then $K$ functionally determines all attributes in R since we NEVER have two distinct tuples with `t1[K]=t2[K]`

To define FDs we need to understand the meaning of the attributes involved and the relationship between them. FD is a property of the attributes in the schema $R$.

Given the instance (population) of a relation, all we can conclude is that an FD may exist between certain attributes. We can however conclude that certain FDs do not exist because there are tuples that show a violation of those dependencies. 
#### Example
![[Pasted image 20240418143828.png | Center | 400]]
Given the state of the `TEACH` relation, we can say that the FD :Text → Course may exist. However, the FDs Teacher → Course,Teacher → Text and Couse → Text are ruled out.

![[Pasted image 20240418144215.png | center | 300]]
FDs may hold for: B → C; C → B; {A,B} → C; {A,B} → D; {C,D} → B.

#### Full Functional Dependency: 
A FD `X -> Y` where the removal of any attribute from `X` means that the FD will not hold anymore. 

`{Ssn, Pnumber} -> Hours` is a full FD since neither `Ssn -> Hours` or `Pnumber -> Hours` holds.
`{Ssn, Pnumber} -> Ename` is a partial dependency as `Ssn -> Ename` holds.


#### Transitive Dependencies
A functional dependency `X -> Y` in `R` is a transitive dependency if there exists a set of attributes `Z` in `R` that is neither a candidate key nor a subset of any key of `R` and both `X -> Z` and `Z -> Y` hold.

For example:
- `Ssn -> Dmgr_ssn` is a transitive FD as `Ssn -> Dnumber` and `Dnumber -> dmgr_ssn` hold
- `Ssn -> Ename` is non-transitive as there is no set of attributes `X` where `Ssn -> X` and `X -> Ename`
### Normalisation of Data
A process of analysing the given relation schemas based on their FDs and primary keys to achieve the desired properties by minimising redundancy and anomalies.

**Normal form:**  
Condition using keys and FDs of a relation to certify whether a relation schema is in a particular form. Refers to the highest normal form condition that it meets, indicate the degree of normalisation.

The higher the normal form the better the structure. 

**2NF, 3NF, BCNF** based on keys and FDs of a relation schema

**4NF** based on keys, multi-valued dependencies (MVD)s;

**5NF** based on keys, join dependencies (JD)s

Additional properties may be needed to ensure a good relational design (lossless join, dependency preservation. There are two important properties of normalisation through decomposition:

##### Non-additive join 
Aka lossless join, property guarantees that the spurious tuples generation problem does not occur with respect to the relation schema after decomposition.
- Is extremely important and cannot be sacrificed.

##### Dependency preservation
Property ensures that each functional dependency is represented in some individual relation resulting after the decomposition.
- Is less stringent and may be sacrificed.

#### Practical use of Normal Form
Normalisation is the carried out in practice so that the resulting designs are of high quality and meet desirable properties. 

The practical utility of higher normal forms (>4NF) becomes questionable when the constraints on which they are based become hard to understand or detect. 

Database designers don't need to achieve the highest possible level of normalisation
- Usually only up to 3NF and BCNF. 4NF is rarely used in practice. 
- Sometimes relations may be left in lower states for performance reasons. 

**Denormalization** is the process in which the a join of higher normal form is stored as a base relation in a lower normal form. 

#### Keys
[[04 Relational Data Model#Key constraints|Keys]] are an important part of normalisation,  a superkey is set of attributes that is a subset of $R$. A key is a minimal superkey.

If a relation schema has more than one key, each is called a candidate key. One of the candidate keys is arbitrarily designated to be the primary key, the other s are called secondary keys. 

An attribute of $R$ is called a prime attribute of $R$ if it is a member of some candidate key of $R$.

A nonprime attribute is not a prime attribute, it is not a member of any candidate key. 

#### First normal form
1NF is considered to be part of the formal definition of a relation in a basic (flat) relational model. It was defined to disallow:
- Multi-valued attributes
- Composite attributes
- Their combinations like nested relations - Attributes whose values for an individual tuple are non-atomic

The only attributes permitted in 1NF are single atomic (indivisible) values. Most RDBMSs allow only those relations to be defined in the first normal form. 


![[Pasted image 20240422201749.png | Center | 400]]
This creates redundancy but allows first normal form to be achieved by creating a key that is `DLocation` and `Dnumber`.


##### Normalising into 1NF
There are three ways to achieve 1NF in the previous example
1. Remove the attribute `DLocation` that violates 1NF and place into a separate relation `DEPT_LCOATIONS` along with the primary key `DNumber` of the `DEPARTMENT`
2. Expand the key to `(DNumber, DLocation)` so that there will be a separate tuple in the original `DEPARTMENT` relation for each location of a department
3. If a maximum number of values is known for attribute, replace `DLocation` with n atomic attributes `DLocation1`, `DLocation2`, `DLocation3`

The first solution is the most optimal as it generic and does not create redundancy. 


#### Second Normal Form
Uses Functional Dependencies and Primary Keys'

A relation schema $R$ is in second normal form if every non-prime attribute $A \in R$ is fully functionally dependent on the primary key.

You can test for 2NF by checking for functional dependencies whose left-hand side attributes are part of the primary key (Or the full primary key)

##### Normalising into 2NF
If a relation schema $R$ is not in 2NF, it can be "second normalised" (2nf Normalised) into multiple separate 2NF Relations

>Nonprime attributes are associated only with the part of the original primary key on which they are fully functional dependent in the decomposed 2NF relations. 

![[Pasted image 20240422204858.png | center | 400]]
#### Third Normal form
A relation in schema $R$ is in 3NF if it satisfies 2NF and has no non-prime attributes of $R$ transitively dependant on the primary key. 

We only consider it a problem if `X -> Y` and `Y -> Z` if `Y` is not a candidate key - Not a prime attribute. 

##### Normalising into 3NF
![[Pasted image 20240422205801.png | center | 500]]

#### Summaries so far
![[Pasted image 20240425110509.png | center | 600]]

The Normal forms defined informally:
- 1NF: All (nonprime?) attributes depend on the key  
- 2NF: All (nonprime?) attributes depend on the whole key  
- 3NF: All (nonprime?) attributes depend on nothing but the key

These definitions consider the primary key only, more general definitions take into account multiple candidate keys

##### 2NF    
A relation schema $R$ is in second normal form (2NF) if every non-prime attribute $A$ in $R$ is fully functionally dependent on every key of $R$.

![[Pasted image 20240425111049.png | center | 500]]


##### 3NF
Super key of relation schema $R$ – a set of attributes $S$ of $R$ that contains a key of $R$

A relation schema $R$ is in third normal form (3NF) if whenever a nontrivial functional dependency `X -> A` holds in R, then either:
1. X is a super key of R, or
2. A is a prime attribute of R (member of candidate key)

![[Pasted image 20240425111746.png | center | 400]]
 

Condition a catches two types of violations:
1. A non-prime attribute functionally determines another non-prime attribute.This catches 3NF violations due to a transitive dependency.
2. A proper subset of a key of R functionally determines a non-prime attribute.This catches 2NF violations due to non-full functional dependencies.

This definition can be further refined as:
A relation schema R is in third normal form (3NF) if every non-prime attribute in R meets both of these conditions:
- It is fully functionally dependent on every key of R
- It is non-transitively dependent on every key of R
    
Note that stated this way, a relation in 3NF also meets the requirements for 2NF.

#### Boyce-Codd Normal Form 
A relation schema R is in Boyce-Codd Normal Form (BCNF) if whenever a nontrivial functional dependency X->A holds in R, then X is a super key of R.

Each normal form is strictly stronger than the previous one, i.e.,  
-  Every 2NF relation is in 1NF  
-  Every 3NF relation is in 2NF
- Every BCNF relation is in 3NF

![[Pasted image 20240425114921.png | center | 600]]
It is 3NF because B is a prime attribute even though it is not a super key of $R$

#### Decomposition Checks
When using decomposition to satisfy normality constraints, you have to ensure that you are fulfilling the lossless ([[#Non-additive join]]) relation.  To test binary decompositions for this property:

**Binary Decomposition:** Decomposition of a relation R into two relations.
**Property NJB** (non-additive join test for binary decompositions): 
A decomposition $D = {R_{1}, R_{2}}$ of $R$ has the lossless join property with respect to a set of functional dependencies $F$ on $R$ if and only if either:
- The FD:  $((R_{1} \cap R_{2}) \to (R_{1}-R_{2}))$ is in $F^+$
- The FD:  $((R_{1} \cap R_{2}) \to (R_{2}-R_{1}))$ is in $F^+$

(Whats in both minus whats in the first but not the second)

The notion of $F^+$ (call closure of $F$) refers to the set of all functional dependencies that include $F$ as well as all dependencies that can be inferred from $F$.

For the example:
- D1: {<u>Student</u>, <u>Instructor</u>} and {<u>Student</u>, <u>Course</u>}  
- D2: {Course, <u>Instructor</u>} and {<u>Course</u>, <u>Student</u>}  
- D3: {<u>Instructor</u>, Course} and {<u>Instructor</u>, <u>Student</u>}

D1 gives Student $\to$ Instructor or Student $\to$ Course, which are both false.
D2 gives Course $\to$ Instructor or Course $\to$ Student, both also false.

However, in D3 we get Instructor $\to$ Course or Instructor $\to$ Student.

Since Instructor $\to$ Course is true, the NJB property is satisfied and D3 is determined as a non- additive (good) decomposition.

#### 4th and 5th
These are non-examinable, not often used and quite complex?

4NF is about multi variate dependencies

