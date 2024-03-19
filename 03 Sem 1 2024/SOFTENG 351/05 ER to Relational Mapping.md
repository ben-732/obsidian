
When mapping ER to Relational, the order is important. 

1. Mapping of Regular Entity Types  
2. Mapping of Weak Entity Types  
3. Mapping of Binary 1:1 Relation Types  
4. Mapping of Binary 1:N Relationship Types. 
5. Mapping of Binary M:N Relationship Types. 
6. Mapping of Multivalued attributes.
7. Mapping of N-ary Relationship Types.

#### Goals of mapping
- Preserve all information (that includes all attributes)
- Maintain the constraints to the extent possible (Relational Model cannot preserve all constraints – e.g., max cardinality ratio such as 1:10 in an ER relationship type).
- Minimise null values  

The mapping procedure described has been implemented in many commercial tools.


### Mapping
#### 1. Regular entity types
For each regular (strong) entity type $E$ in the ER diagram, create a relation $R$ that includes all the simple attributes of $E$.
- Composite attributes are represented by their ungrouped components.
- Choose one of the key attributes of $E$ as the primary key for $R$.
- If the chosen key of $E$ is composite, the set of simple attributes that form it will together form the primary key of $R$.


#### 2. Weak entities
For each weak entity type $W$ in the ER diagram with owner entity type $E$, create a relation $R$ & include all simple attributes (or simple components of composite attributes) of $W$ as attributes of $R$.

- Also, include as foreign key attributes of $R$ the primary key attribute(s) of the relation(s) that correspond to the owner entity type(s).

- The primary key of $R$ is the combination of the primary key(s) of the owner(s) and the partial key of the weak entity type $W$, if any.

#### 3. Binary 1:1 Relationships
For each binary 1:1 relationship type $R$ in the ER diagram, identify the relations $S$ and $T$ that correspond to the entity types participating in $R$.

There are three possible approaches for this.

**Foreign Key ( 2 relations) approach:** Choose one of the relations (e.g., $S$) and include a foreign key in $S$ as the primary key of $T$. It is better to choose an entity type with total participation in $R$ for the role $of$ $S$.

**Merged relation (1 relation) option:** An alternate mapping of a 1:1 relationship type is possible by merging the two entity types and the relationship into a single relation. This may be appropriate when both participations are total.

- I think this means combine two tables into one?

**Cross-reference or relationship relation (3 relations) option:** The third alternative is to set up a third relation R for the purpose of cross-referencing the primary keys of the two relations S and T representing the entity types

#### 4. Binary 1:N Relationships
**The foreign key approach:**
For each regular binary 1:N relationship type $R$, identify the relation $S$ that represents the participating entity type at the N-side of the relationship type.

Then include as foreign key in S the primary key of the relation $T$ that represents the other entity type participating in $R$.
  
Include any simple attributes of the 1:N relation type as attributes of $S$.

**The relationship relation approach:**
An alternative approach uses a relationship relation (cross referencing relation) option as in the third option for binary 1:1 relationships. 


#### 5. Binary M:N Relationship
**The relationship relation (cross-reference) option:**
For each regular binary M:N relationship type $R$, create a new relation $S$ to represent $R$. This is a relationship relation.

In $S$ include the primary keys of participating entity types as foreign keys. The combination of foreign keys will form the primary key of $S$
    
Include any simple attributes of the M:N relationship type (or simple components of composite attributes) as attributes of $S$.

#### 6. Multivalued attributes
This is for when you have multiple values assigned to one attribute in a table. 

For each multivalued attribute $A$, create a new relation $R$.

This relation R will include an attribute corresponding to $A$, plus the primary key attribute $K$ as a foreign key in $R$ of the relation that represents the entity type of relationship type that has $A$ as an attribute.

The primary key of $R$ is the combination of $A$ and $K$. If the multivalued attribute is composite, we include its simple components.

#### 7. N-ary Relationships
For each n-ary relationship type $R$, where n>2, create a new relationship $S$ to represent $R$.
    
Include as foreign key attributes in $S$ the primary keys of the relations that represent the participating entity types.
    
Also include any simple attributes of the n-ary relationship type (or simple components of composite attributes) as attributes of $S$.
![[Pasted image 20240314152220.png | center | 200]]

### Summary
![[Pasted image 20240317143925.png | center | 600]]
