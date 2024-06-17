The basis for relational database commands
- Unary Relational Operations 
- Relational Algebra Operations From Set Theory
- Binary Relational Operations
- Additional Relational Operations 
- Examples of Queries in Relational Algebra

Relational algebra is the basic set of operations for the relational model, they provides a formal foundation for relational model operations. These operations enable a user to specify basic retrieval requests (or queries) as relational algebra expressions

The result of an operation is a new relation, which may have been formed from one or more input relations. This property makes the algebra “closed” (all objects in relational algebra are relations)

The algebra operations thus produce new relations, These can be further manipulated using operations of the same algebra.

A sequence of relational algebra operations forms a relational algebra expression, The result of a relational algebra expression is also a relation that represents the result of a database query (or retrieval request) 

Used as a basis for implementing and optimizing queries in the query processing and optimization in RDBMSs.


### Notation overview
Relational Algebra consists of several groups of operations

Unary Relational Operations
- SELECT $\sigma$ (sigma)
- PROJECT $\pi$ (pi)
- RENAME $\rho$ (rho)

Relational Algebra operations from Set Theory
- UNION $\cup$, 
- INTERSECTION $\cap$, 
- DIFFERENCE (MINUS ) $-$

Binary Relational Operations
- JOIN (several variations of JOIN exist) 
- DIVISION

Additional Relational Operations 
- OUTER JOINS
- AGGREGATE FUNCTIONS (these compute summary of information: e.g., SUM, COUNT, AVG, MIN, MAX)

### Company DB Schema/State
![[Pasted image 20240411171818.png | center]]

![[Pasted image 20240411173501.png | center]]
### Unary Relational operations
#### Select
The SELECT operation $\sigma$ is used to select a subset of the tuples from a relation based on a select condition. 

The selection condition acts as a filter. Keeps only those tuples that satisfy the qualifying condition
Tuples satisfying the condition are selected whereas the others tuples are discarded (filtered out)

Select the `EMPLOYEE` tuples whose department number is 4:

$$
\LARGE\sigma\normalsize\,_{\text{DNO}\, =\, 4}\LARGE(\text{EMPLOYEE})
$$
$$
\LARGE\sigma\normalsize\,_{\text{SALARY}\, >\, 10,000}\LARGE(\text{EMPLOYEE})
$$
In general, the select operation is denoted by $\Large\sigma\normalsize_{\text{<Selection condition>}}(R)$ Where:
- The symbol $\sigma$ (sigma) is used to denote the select operator 
- The selection condition is a Boolean (conditional) expression specified on the attributes of relation $R$ 
- Tuples that make the condition true are selected, they appear in the result of the operation
- Tuples that make the condition false are filtered out,  discarded from the result of the operation

`SELECT` Operation Properties  
- The SELECT operation$\Large\sigma\normalsize_{\text{<Selection condition>}}(R)$ produces a relation $S$ that has the same schema (same attributes) as $R$ 
- SELECT $\sigma$ is commutative: $\large\sigma_{c_{1}}(\sigma _{c_{2}} (R)) = \sigma_{c_{2}} (\sigma_{c_{1}}  (R))$ 
- Because of commutativity property, a cascade (sequence) of SELECT operations may be applied in any order:  
- A cascade of SELECT operations may be replaced by a single selection with a conjunction of all the conditions:  $\large\sigma_{c_{1}}(\sigma_{c_{2}}(\sigma_{c_{3}}(R))) = \sigma_{c_{1} \text{ AND } c_{2} \text{ AND }c_{3}}(R)$  
- The number of tuples in the result of a SELECT is less than (or equal to) the number of tuples in the input relation $R$

#### Project
The project operation denoted by $\pi$ (pi) keeps certain columns (attributes) from a relation and discards all others. 

PROJECT Creates a vertical partitioning, the list of specified columns (attributes) is kept in each tuple. The other attributes in each tuple are discarded

To list each employees first and last name you would use:
$$
\Huge\pi\large_{\text{Lname, Fname,Salary}}(\text{EMPLOYEE})
$$
In general, the project operation is denoted by $\Large\pi\normalsize_{\text{<Selection condition>}}(R)$ Where:
- $\pi$ is the symbol used to represent the *project* operation
- \<attribute list\> is the desired list of attributes from the relation $R$

The project operation removes any duplicate tuples that are created as a result of the operation. This is because the result of the project operation must be a set of tuples 
> Mathematical sets do not allow duplicate elements.

Operation properties"
- The number of tuples in the result of projection $\pi_{c_{1}}(R)$ is always less or equal to the number of tuples in $R$
	- If the list of attributes contains a key of R then the number of tuples in the result of PROJECT is equal to the number of tuples in R. 
- PROJECT is not commutative
- $p_{l_{1}} (p_{l_{2}} (R) ) = p_{l_{1}} (R)$ as long as $l_{2}$ contains the attributes in $l_{1}$

#### Rename
The RENAME operator is denoted by $\rho$ rho.

In some cases, we may want to rename the attributes of a relation or the relation name or both. This is useful when a query requires multiple operations, which is necessary in some cases (see JOIN operation later)

There are three forms of renaming, attribute names, relation name or a combined one that does both:
- $\large\rho_{(B_{1}, B_{2}, \dots, B_{N})}(R)$ changes the column (attribute) names to $B1, B2, \dots$
- $\large\rho_{s}(R)$ Changes the relation name to $S$
- $\large\rho_{s(B_{1}, B_{2}, \dots, B_{N})}(R)$ Combines the two operations above. 

For convenience there is also shorthand that is used for renaming attributes in an intermediate relation:
![[Pasted image 20240411175559.png | center | 500]]
### Expressions
We may want to apply several relational algebra operations one after the other. Either we can write the operations as a single relational algebra expression by nesting the operations, or we can apply one operation at a time and create intermediate result relations.

In the latter case, we must give names to the relations that hold the intermediate results.

For example, To retrieve the first name, last name, and salary of all employees who work in department number 5, we must apply a select and a project operation.

We could write it as a single relational algebra expression:
$$
\Large\pi\normalsize_{\text{Fname, Lname, Salary}}(\sigma _{\text{Dno}=5}(\text{EMPLOYEE}))
$$
or alternatively we can explicitly show the sequence of operations, giving a name to each intermediate relation:
$$
\text{DEP5\_EMPS} \leftarrow \sigma  _{\text{Dno}=5}(\text{EMPLOYEE})
$$
$$
\text{RESULT} \leftarrow \pi_{\text{Fname, Lname, Salary}}(\text{DEP5\_EMPS})
$$
The Left arrow symbol $\leftarrow$ is the assignment operation.



### Set theory operations
#### Union operation
Binary operation is denoted by $\cup$. The result of $R \cup S$ is a relation that includes all tuples that are in either or both of $R$ or $S$ . Duplicate tuples are eliminated in this process.

The two operand relations $R$ and $S$ must be “type compatible” (or `UNION` compatible):
- R and S must have same number of attributes 
- Each pair of corresponding attributes must be type compatible (have same or compatible domains)

#### Intersection
`INTERSECTION` is denoted by $\cap$, The result of the operation $R \cap S$, is a relation that includes all tuples that are in both $R$ and $S$ 

The attribute names in the result will be the same as the attribute names in R Two operand relations R and S must be ‘type compatible’
#### Difference
`SET DIFFERENCE` (also called `MINUS` or `EXCEPT`) is denoted by $–$ 
The result of $R – S$, is a relation that includes all tuples that are in $R$ but not in $S$ 

The attribute names in the result will be the same as the attribute names in $R$, Two operand relations $R$ and $S$ must be ‘type compatible’


#### Properties
Both union and intersect are commutative operations. 
$$
R \cup S = S \cup R , R \cap S = S \cap R
$$
Both union and intersection can be treated as n-ary operations applicable to any number of relations as both are associative operations; that is:
$$
R\cup(S\cup T) = (R \cup S) \cup T
$$
$$
R\cap(S\cap T) = (R \cap S) \cap T
$$

 
 The minus operation is not commutative; that is, in general $R - S \neq S-R$
#### Cartesian Product
The cartesian product (or cross join) operation is used to combine tuples from two relations in a combinatorial fashion. 

Denoted by $R(a_{1}, a_{2}, \dots) \times S(b_{1}, b_{2}, \dots)$, it results in a relation $Q$ with degree of $m+n$ attributes where $m$ and $n$ are the number of attributes in $R$ and $S$ respectively. $Q(a_{1}, a_{2}, \dots, b_{1}, b_{2}, \dots)$.

The resulting relation state has one tuple for each combination of tuples - One from $R$ and one from $S$. Hence if $R$ has $n_{R}$ tuples (denoted as $|R| = n_{R}$) and $S$ has $n_{S}$ tuples, then $R\times S$ will have $n_{R} \times n_{S}$ tuples. The two operands do not have to be type compatible. 

Generally on its own a cross product is meaningless, however it is useful followed by a selection $\sigma$ that matches values of attributes coming from the component relations 

### Binary Relational Operations
#### JOIN
The join operation $\bowtie$ is like a cross product followed by a select. 
$$\large\text{EMPNAME} \bowtie_{\text{Essn} = \text{Ssn}}\text{DEPENDENT}$$

This operation is very important for any relational database with more than a single relation, because it allows us combine related tuples from various relations.

The general form of join operation is $R \bowtie_{\text{<Join Condition>}}S$ where $R$ and $S$ can be any relations that result from general relational algebra expressions.
![[Pasted image 20240414232029.png | center]]

The result from the join will have less tuples than the equivalent cross product (without a following select)

##### Theta Joins 
$\theta$
The general case of JOIN operation is called a THETA JOIN: $R \bowtie_{Ai \theta Bj} S$ 

The join condition is of the form $Ai \,\,\theta\,\, Bj$,  where:
- $Ai$ is an attribute of $R$ and $Bj$ is an attribute of $S$, 
- $Ai$ and $Bj$ have the same domain  
- ⍬ (theta) is one of the comparison operators 

Most join conditions involve one or more equality conditions “AND”ed together


##### Equijoin Operation
The most common use of the join operation involves join conditions with equality comparison only. Such a join where the only comparison operator used is $=$ is called an EQUIJOIN. 

In the result of an EQUIJOIN we always have one or more pairs of attributes (whose names need not be identical) that have identical values in every tuple. The JOIN seen in the previous examples were all EQUIJOINs.

##### Natural Join operations
Another variation of JOIN called NATURAL JOIN,  denoted by $*$ was created to get rid of the second (superfluous) attribute in an EQUIJOIN condition. 

Because one of each pair of attributes with identical values is superfluous.

The standard definition of natural join requires that the two join attributes, or each pair of corresponding join attributes, have the same name in both relationsm, If this is not the case, a renaming operation is applied first.

To apply a natural join on the `Dnumber` attributes of `DEPARTMENT` and `DEPT_LOCATIONS`, it is sufficient to write: `DEPT_LOCS <- DEPARTMENT * DEPT_LOCATIONS`. 

The only attribute with the same name is `Dnumber`, An implicit join condition is created based on this attribute: `DEPARTMENT.Dnumber=DEPT_LOCATIONS.Dnumber`


#### Division
the DIVISION operation is useful for a special kind of query, For example, retrieve the names of employees who work on all the projects that ‘John Smith’ works on. 

To the projects teat smith works on you would have to select john smith from the employees relation, then use a join and rename to get the Pnos from the works on relation. 

Get a second relation from works on with only essns and pnos of all projects, then apply the division operation which gives the desired employees' number

`SSNS(Ssn) <- SSN_PNOS ÷ SMITH_PNOS`

![[Pasted image 20240414234310.png | center]]

For a tuple t to appear in the result T of the DIVISION, the values in t must appear in R in combination with every tuple in S.

### Complete set of operations
The set of operations, including `SELECT` $\sigma$, `PROJECT` $\pi$ , `UNION` $\cup$, `DIFFERENCE` $-$ , RENAME $\rho$, and `CARTESIAN PRODUCT` $\times$, is called a complete set because any other relational algebra expression can be expressed by a combination of these five operations.

![[Pasted image 20240414233335.png | center]]

### Query Trees
The Query Tree (or query evaluation tree) is an internal data structure used to represent a query, The Standard technique for estimating the work involved in executing the query, the generation of intermediate results, and the optimisation of execution.

Nodes stand for operations like selection, projection, join, renaming, division, and so on. Leaf nodes represent base relations, A tree gives a good visualisation of the complexity of the query and the operations involved.  Algebraic Query Optimisation consists of rewriting the query or modifying query tree into an equivalent tree.

#### Example of Q2
For every project located in ‘Stafford’, list the project number, the controlling department, and the department manager’s last name, address and birthdate.

![[Pasted image 20240416152924.png | center | 400]]
### Aggregate functions
Aggregate functions cannot be expressed in *basic* relational algebra. Examples of these functions include SUM, AVERAGE, MAXIMUM etc

Use of the Aggregate Functional operation $\mathcal{F}$ (script F)
![[Pasted image 20240416153417.png | center | 400]]
#### Grouping
The previous examples all summarised one or more attributes for a set of tuples, Maximum Salary or Count (number of) SSNs. Grouping can be combined with Aggregate Functions 

Example: For each department, retrieve the Dno, `COUNT` Ssn, and `AVERAGE` Salary, A variation of aggregate operation ℑ allows this: 
- Grouping attribute placed to left of symbol 
- Aggregate functions to right of symbol 
$$
\LARGE_{\text{Dno}}\,\mathcal{F}_{\text{COUNT Ssn, AVERAGE Salary}}(\text{(EMPLOYEE)})
$$
The above operation groups employees by `Dno` (department number) and computes the count of employees and average salary per department.

![[Pasted image 20240416153751.png | center | 500]]

### Additional Relational Operations
#### The OUTER JOIN Operation: 
In `NATURAL JOIN` and `EQUIJOIN`, tuples without a matching (or related) tuple are eliminated from the join result. Tuples with `null` in the join attributes are also eliminated, This amounts to loss of information. 

A set of operations, called `OUTER` joins, can be used when we want to keep all the tuples in R, or all those in S, or all those in both relations in the result of the join, regardless of whether or not they have matching tuples in the other relation.

Outer joins are denoted by a bowtie with extra lines on the "outer" sides

The `LEFT OUTER JOIN` operation keeps every tuple in the first or left relation $R$ in $R$ LOJ $S$; if no matching tuple is found in $S$, then the attributes of $S$ in the join result are filled or “padded” with null values.

There are also RIGHT OUTER and FULL OUTER joins that behave similarly but retain values on different sides. 