
## Developing Test suites
- how do we describe tests?  
- what’s the best way to run the tests?  
- when is a test a “good” one?  
- how do we come up with tests?  
- when do we have enough tests?

#### Describing Tests
- What is the Implementation Under Test (IUT)?  
- What is the pretest state?  
- What are the inputs? (test case values and other inputs)  
- What is the expected state?

##### Test Documentation
If testing is to be systematic, it must be planned. A test plan might include:  
- How the testing is to be done  
- Who does the testing  
- What the schedule is  
- What level of quality of testing is to be performed  
- What resources are required  

Each individual test case has to be described and how the test results are to be used.

There are standards for this kind of thing.

##### IEEE 829-1998
- IEEE Standard for Software Test Documentation (1998)  
- Test Plan  
- Test Design Specification  
- Test Case Specification  
- Test Procedure Specification  
- Test Item Transmittal Report  
- Test Log  
- Test Incident Report  
- Test Summary Report

##### Specification

- Purpose — To define a test case identified by a test design specification.  
- Outline — A test case specification shall have the following structure:  
1.  Test case specification identifier;  
2.  Test items;  
3.  Input specifications;  
4.  Output specifications;  
5.  Environmental needs;  
6.  Special procedural requirements;  
7.  Intercase dependencies.

#### Developing Tests
##### Procedure Specification
1.  Test procedure specification identifier  
2.  Purpose  
3.  Special requirements  
4.  Procedure steps (Include as many as possible)
	- Log  
	- Set up  
	- Start  
	- Proceed  
	- Measure  
	- Shut down  
	- Restart  
	- Stop  
	- Wrap up  
	- Contingencies

##### Test Scripts
A detailed sequence of steps needed to perform the test

Example: Authenticated Edit of Wiki
1. Navigate to test page url Set up  
2. Check that Edit tab is visible at top of page Start  
3. Check that Create an account or log in link is visible at top right corner confirms that user is not already authenticated  
4. Click Edit tab Proceed  
5. Verify that Login required to edit page shows  
6. Click login link  
7. (etc)

##### Running Tests
Manually running test scripts is tedious, error prone, and expensive so we automate the process as much as possible  

xUnit family testing frameworks (SUnit, JUnit, NUnit, . . . ) provide frameworks for specifying tests, executing tests, reporting the results  

##### Automation Issues
- Some artifact testing cannot easily be automated (e.g., user interfaces)  
- Some potential failure situations cannot be easily exercised (e.g., timing problems in real-time or concurrent software)  
- Some designs cannot be easily automatically tested (e.g., legacy code)  
- Still a lot of work needed to support traceability — relationship between tests executed and client’s requirements  
- An important part of testing is very difficult to automate (what?)

#### Good Tests
- The purpose of testing is to detect the presence of faults  
- We detect faults by executing the code in such a way to cause a failure  
	- The higher the probability of causing a failure (assuming a fault exists), the better the quality of the test  
	- When deciding on what to test, need to look at where faults are likely to be  
- Really need to consider quality of the test suite 
	- Individual tests are of limited value
	- Ask yourself if a test suite is improved by the addition of a given test

Adding a test to a test suite should depend on whether or not it increases the probability of a failure. In testing, we detect the existence of faults by executing code and causing a failure.

- The faults we detect is somewhere in the code that was executed  
- Faults in code we don’t execute cannot have been detected  
- We increase the probability of a test suite detecting a fault by adding tests that execute code that hasn’t already been executed

##### Statement Coverage
What proportion of the statements in the code are executed by the test suite?

Statement coverage is $=\frac{\text{statements executed by all tests}}{\text{total number of statements}}\%$ 

- If statement coverage is not 100%, then there is a possibility a fault existing in the code will not be detected. 
- It is a measure of test suite quality. 

Sometimes however, 100% coverage isn't enough, it also sometimes isn't possible e.g. dead code. 

##### Strategies
To improve test suites, we need to consider both the artifact we are testing (code) and the quality attribute we are trying to establish (correctness)  

White (clear, glass) box testing — performing tests based on how the component is implemented 
- Artifact focus  
- Decide on tests based on the code itself  
- E.g., statement coverage  
- Not good at detecting faults relating to requirements  

Black box testing performing tests based on what the component is supposed to do 
- Quality attribute focus  
- Decide on tests based on the requirements independent of the code  
- Not good at detecting faults in implementation decisions  

Black box versus White box now something of an old-fashioned view of testing.  

The process for developing a test suite is essentially the same for both—it is the  
inputs that are different (requirements versus code).

## Control Flow Coverage

### Control Flow Graphs
You can model code as a control flow graph. 

A directed graph can model the flow of code where statements are vertices and edges dictate the paths between lines of code.
![[Pasted image 20231018104406.png | center | 400]]
#### Basic Blocks
CFGs can be simplified by having a vertex represent a basic block.

A basic block is a sequence of statements (or instructions) with the  
following properties:
- The first statement is always the first statement executed (one entry point)  
- Only the last statement can cause the program to begin executing code in a different basic block (one exit point)  
- Consequently, if the first statement is executed then all statements in the basic block are executed
- `AB` in the previous example. 

#### Entry/Exit
Need some way to represent entry point to code being modelled  
- e.g. to represent assignment of actual parameters to formal parameters  
Need some way to represent exit point of code being modelled  
- e.g. to represent multiple exists from code
![[Pasted image 20231018104713.png | center | 400]]

#### Statement CFG Coverage
![[Pasted image 20231018104800.png | center | 400]]
### Coverage Criteria
A test requirement is a specific element of a software artifact that a test case must satisfy (or “cover”)  
- E.g. “statement A must be executed”   

A test requirement set is a set of test requirements and a coverage criterion is a rule or set of rules that impose test requirements on a test suite.  
- E.g. The “statement executed” criterion imposes, for every statement x, the test requirement that “statement x must be executed”  

Given a test requirement set determined by a coverage criterion and a test suite, the Coverage Level is the ratio of the number of test requirements satisfied by the test suite to the total number of test requirements.
- E.g. 15 statements means 15 different test requirements. If there are 3 statements that are not executed by any of the tests in the test suite, then the coverage level is 12/15.
#### Example
**Coverage criterion:** Call Constructor: If X() is a constructor then it must  
be called  
**Test requirement:** set For all classes, for all constructors of those classes,  
the constructor must be called  
**Coverage Level:** For a given test suite, the proportion of constructors that  
get called

#### CFG-Based Test Requirements
Test requirements can come from anywhere, for example, model the code with a control flow graph, develop test requirements based on the CFG  

A *test path* in a CFG is a sequence of vertices in the CFG such that the first is the entry vertex, the last is the exit vertex, and every pair of adjacent vertices is connected by an edge in the CFG
- A test path describes a potential path through the code that the CFG models  
- Each test case has a corresponding test path  
- A test path visits a vertex $v$ if $v$ is one of the vertices in the test path.  
- A test path visits an edge $(v, w)$ if $v$ and $w$ are adjacent in the test path (and in that order)  

**Vertex coverage criterion:** for every vertex $v$ in the CFG, the test requirement is “v is visited” statement coverage  

**Edge coverage criterion:** for every edge $(v,w)$, the test requirement is “$(v, w)$ is visited  branch or decision coverage  

### CFG Construction
If we change how the CFG is constructed (change how we model the code) we get different test requirements  

	Some language features complicate CFG construction. For example:  
- For loops — one statement or 3 (or 4)?  
- Try/catch blocks  
- Conditional expressions — e.g. `(a > b) ? missile.launch() : missile.destruct()`
- Compound expressions — e.g. `a && b || (c > 5)`

### Condition coverage
![[Pasted image 20231018110507.png | center | 400]]

##### Modelling Compound Conditions
One approach to the compound expression problem is to change how CFGs are created for such cases

Modelling statements such as:
```java
if(a > 0 && b > 0) {} else {}
```

as 
```java
if(a > 0) {
	if(b > 0) {}else {}
} else {}
```

By creating CFG from restructured version, the restructuring is done only as  
part of the modelling, the actual code does not change  

Other forms of coverage can be used to achieve the same result

### Path coverage
- Coverage criterion: any test path  
- Intuition: any execution flow is represented by a path, so any missed paths may correspond to incorrect execution
#### Example paths
• There are many different paths through a CFG. Any path can potentially  
identify a fault.  
• There are potentially a lot of paths!


- A CFG is a model of the code, therefore there can (and usually will) be some differences between the CFG and the code  
- Some of those differences can lead to test requirements that can never be met in reality 
	- Dead code
	- Infeasable


## Testing Practicalities

### Failures, Errors and Failed Tests
JUnit 3.8 distinguishes between errors and failures  
- Both represent failed tests  
- Both apply to what the IEEE standard refers to as a “failure”  

The JUnit terms represent different kinds of problems detected by JUnit  
- Failure The expected result specified by the (human) tester was not what JUnit observed. Typically a failed assertion.  
- Error JUnit was unable to complete the test. Typically some kind of exception being thrown and not caught  

JUnit tests are only useful if, when they do not pass, it is due to a failure in the implementation under test

### Testing Error Checking
Many methods only work correctly if their parameters have certain values, e.g. List must be non-empty, numbers must be non-negative, parameter must be non-null  
- Such methods will often check that their expectations are met, and signal an error if they aren’t  
- how to test this behaviour?

### Exceptions review
- Printing a message isn’t always appropriate  
- Returning a value indicating a problem isn’t always safe  
	- exceptions — a general purpose mechanism for signalling “unexpected situations”  
- In Java, an exception is an object  
- When an unexpected situation occurs, an exception is thrown  
- A thrown exception may be caught, or otherwise the program terminates
 
### Summary
- “number of failed (JUnit) tests” does not necessarily relate to “number of faults” or “how much work has to be done”  
- “number of tests” is difficult to define  
- Test all code, including exception handlers  
- The more specific the test, the more information its failure will provide about the fault that caused the failure (e.g. its location)

## Dataflow Testing
### Test Requirements based on paths
*All paths* coverage criterion often results in too many test requirements while *statement coverage* and *branch coverage* criteria often do not produce enough test requirements, but are subsets of “all paths”  

Are there other subsets of “all paths” that can do better?  
- Annotate CFGs with characteristics of interest, determine path subsets based on characteristics

**Example Criteria:**
A path in a CFG is a sequence of vertices in the CFG $(v_{0}, v_{1}, v_{2}, \dots,v_{k})$ such that $\forall (1 \leq i\leq k)(v_{i}-1,v_{i})$  is an edge in the CFG  
- A test path is a path where $v_{0}$ is the entry vertex and $v_{k}$ is the exit vertex  
- A sub path $q = (w_{0}, w_{1}, \dots, w_{m})$ of a path $q = (v_{0}, v_{1}, v_{2}, \dots,v_{k})$when  $\exists I, \forall 0 \leq i\leq m, w_{i} = v_{I} + i$
- A path is simple if either every vertex only appears once in the path, or the only vertex that appears twice appears as the first and last vertex in the path.  
	- A sub path of a simple path must also be simple (so there can be a lot of simple paths in a CFG)  
- A prime path is a maximal length simple path, that is, path $p$ is prime if it is simple and $∀$ paths $q$, such that $p$ is a sub path of $q$, $q$ is not simple.
![[Pasted image 20231018175808.png | center | 450]]
#### Example Coverage Criteria?
- Criterion: Paths of length 2  
- Criterion: Simple paths  
- Criterion: Prime paths
### Data Flow
Computation consists of manipulating values, moving them among different memory locations  

Almost every failure can be traced to a wrong value being in the wrong place at the wrong time  
-  Look at where the values come from and where they go — “data flow”  

- Choose a subset of “all paths” based on data flow  
- Annotate vertices in CFGs with indications as to what variables are “defined” and what variables are “used” at those vertices

#### Data flow actions
Control flow graphs, except vertices are labelled with what happens to data objects at the corresponding statements.

3 major and 2 minor things can happen to data objects  
- **Defined (d)** data value comes into existence, assigned to variable (or similar)  
- **Killed or undefined (k)** data value no longer available  
- **Usage (u)/Computation(c)** used for computation  
- **Usage (u)/Predicate(p)** used in a predicate  

##### Def:
A location where a value is stored into memory  
- Variable appears on the left side of assignment operator  
- Variable is a formal parameter of a method (occurs in entry vertex)  
- Variable is an input to a program (some languages)  
- Variable is an actual parameter in a method invocation and the method changes its value (some languages)

##### Use
A location where a variable’s value is accessed  
- The variable appears on the right hand side of an assignment  
- The variable appears in a predicate (in a conditional test)  
- The variable is an actual parameter in a method invocation  
- The variable is in a return statement for a method  
- The variable is an output of a program (some languages)

Look at paths on which things happen to the same data object

#### Dataflow Anomalies
Looking at data actions on a test path can provide useful candidates for test cases  
Given a test path and a variable, what data actions take place in what order?

- **dd** (mostly) harmless, but worth checking  
- **dk** possible fault  
- **du** normal case  
- **kd** normal case  
- **kk** it’s dead already (suspicious)  
- **ku** fault (not possible in some languages)  
- **ud** probably not a fault  
- **uk** normal  
- **uu** normal

- **–k**  variable not defined but then killed  
- **–d**  first definition in path  
- **–u**  used before defined  
- **k–**  killed is the last thing that happens  
- **d–**  defined but never used  
- **u–**  used but never killed, probably ok

### Du-Paths
We care about whether a value that is defined is correct for a given use — pair defs with uses  
- we care about which defs “reach” which uses  

- The set $\text{defs}(v)$ is the set of variables that are defined at vertex $v$  
- The set $\text{uses}(v)$ is the set of variables that are used at vertex $v$  

It could be that $x\in \text{defs}(v)$ and $x\in \text{uses}(v)$ 
- `i++` - use followed by def  
- `int i = 0; System.out.println(i);` — basic blocks with multiple statements, def followed by use  

A path $p = (v_{0}, v_{1}, v_{2}, \dots,v_{k})$ is *def-clear* with respect to variable $x$ if for every vertex $i$ in the path between $1\leq i\leq k-1$, $x$ is not in the set $defs(v_{i})$ 

A du-path with respect to variable x is a simple path that is *def-clear* with respect to x such that $x \in \text{defs}(v_{0})$ and $x\in \text{uses} (v_{k})$
- Need special cases for $k = 0$ (def must come before use)  
- There can be other uses of `x` on `p`

![[Pasted image 20231018181604.png | center | 500]]

### Key Points
- Need to find “interesting” subsets of paths — coverage criterion = definition of “interesting”  
- A failure is often due to the wrong value being used somewhere.  
- Finding the fault that caused the failure (“debugging”) involves finding out where that wrong value came from  

- A good place to look for possible faults is along the paths between the source of the value (definition) and its use

Procedure:  
- Identify definitions of values  
- Identify uses of those definitions  
- Identify (control flow) paths from definitions of values to their uses


## Dataflow Continued
### Du  Pairs
![[Pasted image 20231018183536.png | center  | 400]]

- Reachable uses - there is a def-clear path from the definition to this use  
- A du pair with respect to a variable $x$ is a pair of vertices $(v_{d}, v_{u})$ such that there is a du path with respect to $x$ from $v_{d}$ to $v_{u}$.

Example Some du-paths:
![[Pasted image 20231018183745.png | center | 500]]
A path $q = (w_{0}, w_{1}, \dots , w_{m})$ is a prefix of a path $p=(v0, v1, v2, \dots , v_{k})$ when $\forall (0 \leq i\leq m), w_{i} = v$

- A test path that visits ever vertex and edge in $p$ must also visit every vertex and edge in a prefix $q$
- Usually can remove prefix du-paths -- depending on the feasibility of $p$

There can be many du paths, and there can be multiple du-paths between a given du pair.

### Dataflow Coverage Criteria
**All du-paths** visit every du path for every variable  
- Strongest data flow strategy  
- Not as strong as all paths  

**All uses** for every use, visit at least one du path from every definition to that use  

**All-p-uses/some-c-uses** for every definition, visit at least one du path to every predicate use, then for any definition not covered add a du path to a computation use.

**All-c-uses/some-p-uses** for every definition, visit at least one du path to every computation use, then for any definition not covered add a du path to a predicate use  

**All definitions** for every definition, visit at least one du path (and others)

#### Examples
![[Pasted image 20231018184726.png | center | 500]]

##### All DU-Paths
![[Pasted image 20231018184408.png | center | 500]]
![[Pasted image 20231018184431.png | center | 500]]
##### All-P-Uses/Some C Uses
![[Pasted image 20231018185039.png | center | 600]]
###### Complications
There are infeasible paths  
- relax requirements of coverage criteria  
Use before def in the same vertex (e.g. i=i+1 )  
- improve model

### Practicality of All Du-Paths
![[Pasted image 20231018185252.png | center | 600]]
### Key Points
- A failure is often due to the wrong value being used somewhere.  
- Finding the fault that caused the failure (“debugging”) involves finding out where that wrong value came from  
	- A good place to look for possible faults is along the paths between the source of the value (definition) and its use  =
Procedure  
- Identify definitions of values  
- Identify uses of those definitions  
- Identify (control flow) paths from definitions of values to their uses (du-paths)  
- Choose a set of du-paths based on the chosen testing strategy  
- Create a test suite that causes all of the chosen du-paths to be followed.  

> Doing any of this without tool support will be difficult!!!

## Equivalence Partitioning ?
### Input space partitioning
All test suite creation is about choosing inputs, for any given test requirement there will often be a number of inputs that can satisfy it - all of those inputs are essentially equivalent.

Why not choose the test requirements according to inputs that are “essentially equivalent”?  

Principles:  
- minimise the number of tests  
- maximise the usefulness of each test  

Modelling the input space
#### Triangle problem example
Are these two test cases different?  
- Test Case 1: (3, 3, 4)  
- Test Case 2: (4, 4, 5)  

If we have test case 1, will adding test case 2 increase the probability of causing the IUT to fail (assuming the presence of a fault in it)?  

Observations:  
- The expected output in both cases is the same  
- It is quite likely that the IUT will behave exactly the same in both cases

### Equivalence partitioning
Partition possible input values into equivalence classes  
- Every possible input value is in at most one equivalence class (classes are disjoint)  
- Every possible input value is in at least one equivalence class (union of all classes is all possible input values)  

Expect all input values in one class to have the “same behaviour” by the IUT, so:  
- If one member of the class produces correct behaviour, then expect all other members to do so  
- If one member of the class produces incorrect behaviour, then same 

**Coverage criterion**: there is at least one input value tested from each equivalence class if we find set of largest possible equivalence classes, then:
- Each test case will effectively test the whole class (maximise effect of test)  
- Need no more tests than the number of classes (in theory) (minimise number of tests)

### Boundary value analysis
Faults often occur “at the edges”, Boundary value analysis chooses test cases from the boundaries of  
the equivalence classes.

**Coverage criterion:** inputs from both sides of every boundary, E.g., if equivalence classes are: 
- `[−2^31, 0], [1..12], [13, 2^31 − 1]`  
Then test cases are: `−10` (from first equivalence class), `9` (second), `397` (third), `−2^31` & `0` (either end), `1` & `12` (ditto), `13` & `2^31 − 1`

### Complex input spaces
“Boundary” and “side” (in “both sides”) can be difficult to identify, when the class is an area, there can be many values “on the boundary”  

- Make each boundary a separate equivalence class, apply BVA to them  
- (repeat as necessary for multi-dimensional data)  

### Fun facts?
Faults that may not have been detected by test suites developed using coverage criteria from CFGs are very likely to be detected with test suites developed using coverage criteria from BVA  

Faults due to missing requirements more likely to be caught using equivalence partitioning  
- e.g. having a partition `exam < 0 || practical < 0`  catches problems dealing with invalid scores
### Input domain modelling
- Equivalence classes can also be determined by more general means than just groups of input values
- An input domain model (IDM) represents the inputs to an IUT in an abstract way
- The inputs are described in terms of characteristics
- Characteristics can then be used to partition inputs  
- Characteristics can be developed directly from:
	- Parameters to the IUT - interface-based 
	 - From a functional view of the IUT - functionality-based  
- Adding more (or better) characteristics and partitioning potentially improves test suite

**Example:**
![[Pasted image 20231018190759.png | center | 600]]

### Assessment:
- Can be applied to different levels: e.g. unit, module, system  
- Can be applied to different artifacts: e.g. code, requirements, design  
	- Don’t need implementation to lead the development of tests  
	- Don’t need as much technical knowledge (e.g. programming)  
- The better the model of the inputs, the better the resulting test suite is likely to be  
- There is no reason why the model cannot include information from an implementation  
- Many (more sophisticated) variations on the basic idea  
- As with paths (including du-paths), considering every possibility (every possible combination) can lead to impractical number of test requirements, so need coverage criteria that produce subsets
### Key points
[Lecture 21: Testing Tutorial 1 and Input Space Partitioning (panopto.com)](https://auckland.au.panopto.com/Panopto/Pages/Viewer.aspx?id=c352983f-7803-4f39-b2bf-b09100e4059a)

![[Pasted image 20231016194956.png]]


## Logic coverage
### Predicates
  
Logic (or boolean) expressions appear in many aspects of software development  
- Test conditions (e.g. loops, if)  
- Descriptions of what code is supposed to do (e.g., pre and post conditions of methods)  
- Database queries  
- Specifications (formal requirements)  
- Decision tables  

A logic expression, or predicate, is an expression that evaluates to a Boolean value. A predicate can contain:  
- Boolean variables  
- Relational expressions (using <,<=,>,>=, =\=, ! = etc)  
- Methods that return Booleans  
- Expressions involving logical operators (!, &&, ||, etc)  

A clause is a logic expression with no logical operators while a predicate is a set of 1 or more clauses

#### Testing Predicates
Model the IUT we want to test with a predicate (if possible), Test requirements are based on clauses in the predicate.

**Predicate Coverage**: Given a predicate p, there are two test requirements - p evaluates to true, and p evaluates to false. This is also known as *branch coverage*.

**Clause Coverage:** For every clause c in a predicate p, there are two test requirements - c evaluates to true, and c evaluates to false.  

**Combinatorial Coverage:** For a predicate p, there is a test requirement for every combination of truth values for every clause c in p. Also known as *condition coverage*.

### Active Clauses
Consider `p = a > 0 && b< 10`, if a = −1 then a > 0 is false, and so p is false no matter what b is  

For a given predicate `p` consisting of clauses `c1, c2, . . . ck`, choose one clause (any one) as the major clause, then all the others are minor clauses  

For p with clauses c1, c2, . . . ck, if ci is the major clause, then ci determines p if the values of the minor clauses are such that changing the value of ci changes the value of p. A major clause that determines a predicate is called an *active clause*
![[Pasted image 20231031165614.png | center ]]
#### Testing Active Clauses
Find tests for each clause when that clause determines the value of the predicate  

**Active Clause Coverage (ACC):** For each predicate `p` consisting of $k_{i}$ clauses `c1, c2, . . . c` , for each major clause $c_{i} , 0 \leq i \leq k$, choose minor clauses $c_{j}, i \neq j$ so that $c_{i}$ determines $p$: there are two test requirements - ci evaluates to true and ci evaluates to false.

Other variations to deal with issues that arise with ACC, in particular should the minor clauses have the same values when changing the major clause?  

Also *Inactive Clause Coverage* — choose values of minor clauses so that major clause does not determine the predicate
![[Pasted image 20231031170126.png | center | 600]]

### Infeasible Test Requirements
For other forms of coverage, logic coverage criteria may lead to infeasible test requirements, especially predicates found in code. Things like short-circuit Boolean evaluation

```java
while(i < a.length && a[i] > 0){}
```
In this snippet, if `a.length < i` then the second, `a[i] > 0` section will not get run, so would be impractical to test. 

### Decision Tables
In some cases can use decision tables to model the IUT (or rather, the requirements). Decision tables use predicates in the models that dictate actions that need to be taken.

Once a model is created, test cases can be generated from the model.
A decision table needs:  
- One of several distinct responses is to be selected according to distinct cases of input variables  
- Cases can be modelled by Boolean expressions on the input variables  
- Actions do not depend on order of inputs  
- Actions do not depend on state, i.e, depends only on inputs

There are two main parts: condition section, action section  

**Condition section:** lists input variables, Boolean expression consisting of input variables  
**Action section:** lists actions when conditions are true  

• Each combination of conditions and actions is a variant  
• Each combination must be unique  
• The same actions may apply to multiple combinations
#### Process
1. Model the implementation with a decision table  
	1. Identify decision variables and conditions  
	2. Identify resultant actions  
	3. Identify actions for each combination of conditions  
	4. Verify completeness and consistency  
2. Derive logic function (optional)  
3. Choose test suite generation strategy  
4. Generate test suite

#### Example: Car insurance renewal
- Determine what happens to car insurance on annual renewal of policy  
- What happens depends on person’s age, number of claims  
- What happens is how much the premium increases or whether or not the policy is cancelled

  
If the person is 25 or younger and they have zero claims they will receive a $50 increase in their insurance premium  
- If the person is 26 or older and they have zero claims they will receive a $25 increase in their insurance premium  
- If the person has one claim and they are:  
	- 25 or younger ⇒ $100 increase, warning letter  
	- 26 or older ⇒ $50 increase  
- If the person has 2-4 claims, they get a warning letter, and if they are:  
	- 25 or younger ⇒ $400 increase  
	- 26 or older ⇒ $200 increase  
- If the person has more than 5 claims, their policy is cancelled (and no increase to premium)

Here there are three actions; Warning letter, premium increase and policy cancel. 
There are Several conditions too:  Age $\leq 25$ or $\geq 26$, and Claims $0, 1, 2-4, \geq 5$
These conditions are combined with the and operator

![[Pasted image 20231031173224.png | center | 600]]

#### Different representations
There are a number of different ways decision tables could be represented:  
- Row or column  
- Tree  
- Truth table  

Some representations are easier for humans to deal with, some are easier for machines (or for converting to machine use).

Here is the last example but represented as a decision tree,.
![[Pasted image 20231031173351.png | center | 400]]

And as a truth table
![[Pasted image 20231031173433.png | center |400]]


#### Number of Variants
Insurance example has 6 conditions: 4 different values for number of claims, 2 different values for age  
- $2^6 = 64$ different variants are possible, but only 7 are shown in the decision table/tree

#### Implicit Variants
**Explicit Variants:** listed explicitly in the model  
**Implicit Variants:** not listed for various reasons - ==Outside the scope of this course==

Some reasons for them not to be listed include
Don’t need to be as they are implied by explicit variants  
- Don’t cares  
- Type-safe exclusions  
Missing due to incorrect modelling  
- Can’t happen  
- Don’t know

### Key Points
- For predicates, combinatorial coverage gives the best test suite, but the number of test grows exponentially
	- Need a way to find a subset that still gives a good quality test suite  
- An active clause maximises the variation of the value of the predicate while restricting the number of variation of clauses needed  
- Decision tables can be used to model functionality that can be expressed as predicates
## Testing the Whole System

Testing changes across different stages as there are different criteria and expectations for some piece of software. 
Developers, Does it all work together? 

#### Different Testing Perspectives
Developers — does it all work together?  
- Not “live”, nothing bad can happen (that anyone will notice)  

Independent Validation and verification organisations (IV&V) - is there something to test?  
- External visibility of product, but finding problems is their job  

Clients (System) — does it do what we want?  
- Some kinds of faults will reflect poorly  

Clients (Acceptance) — are we prepared to pay for it?  
- Some kinds of faults will reflect no payment!  

Deployment — does it actually work for real?  
- Live, real data — some kinds of faults will be a disaster

#### Where do the tests come from
Developers  
- We make the tests (based on our understanding of what’s supposed to  happen)  
- We know testing  
- We tend to care about full correctness, including all the weird (but possible) cases  
IV&V  
- They make the tests (based on their understanding of what’s supposed  to happen)  
- They know testing  
- Also care on weird cases  
Deployment  
- We make the tests
- Can be a combination of both the clients and the development team. 
Clients (System Testing)  
- We and they make the tests  
- They don’t know testing  
- Clients tend to care about the typical cases  
Clients (Acceptance Testing)  
- They make the tests  
- They don’t know testing  
- Clients tend to care about the typical cases

#### Client tests
Clients know what they need (or what they want, at least)  
- They need to convey this to developers somehow  

Developers and Clients speak different languages  
- Client: “We need the system to provide stochastic arbitrage for the equity trading”  
- Developer: “Is that .NET or J2EE?”  
- Client: “What?”  

The Client needs to be able to present the tests they need in a form they can understand  
- Developer: “Oh just write the tests in the standard RUP-style use-case format”  
- Client: “What’s the number of your competitor again?”

### Fit Testing
Simple means for customer to specify tests for automated testing  
- Tests are specified in table form in HTML, both inputs and expected outputs  
- Tables can come from Word, Excel, etc  
- Programmers provide “fixtures” to turn the table into tests  
- Load page in (suitably configured) web browser to execute tests  
- Helps make the system “fit”  
- Framework for *Integrated* Test (FIT)  
- Another product (CRC cards, Design Patterns, XP, Wiki) of Ward Cunningham

#### Test Specification
![[Pasted image 20231101103240.png | center | 400]]
“Non method” Column heading means input  
- Corresponds to field of same name in fixture class  
“Method” Column heading means expected output  
- Corresponds to method of same name in fixture class  
- “Method-ness” Specification depends on how Fit is invoked
![[Pasted image 20231101103405.png | center | 400]]
The passing and failing of a test is represented by colours as in `JUnit`
![[Pasted image 20231101103551.png | center | 400]]

Now days we use `?` instead of `()` to mark a method.
#### Fixtures

The glue between test specification and test execution, there are three standard fixtures:  
*ColumnFixture* each row of a table is a test, comparing inputs with outputs. useful for testing responses to individual inputs  
*RowFixture* the whole table is the test, with the rows specifying the expected objects and their properties. useful for testing lists of things  
*ActionFixture* rows are sequences of things to do in order. useful for testing that things happen in a particular orde


##### Example
![[Pasted image 20231101103900.png | center  | 200]]
![[Pasted image 20231101103846.png | center | 500]]

#### Behind the scenes
How the column fixture process works
1. For each row . ..  
2. Construct a ColumnFixture object (example.TestArithmetic1 in this case)  
3. Assign the values from the “field” columns to the fields of the object  
4. Call the methods on the object specified by the “method” column headings  
5. Compare the results with what’s given in the method column cells  
6. Colour the cell with the expected value green if it matches, otherwise red and give the actual value
#### Good practice
use string datatypes when writing fixtures as you don't know what the user is going to input. 

Depends on what datatypes are in the table but use string if there is a mix, and use double if there is a mix of int and double only.


##### Supporting testing
Sometimes we need some intermediate infrastructure to connect the System Under Test and the Fixture
![[Pasted image 20231101104608.png | center  | 500]]

![[Pasted image 20231101104708.png | center | 500]]

#### Practical Considerations
Running Fit tests requires producing web pages, putting them in the right place, and getting CGI scripts (or similar) to work. or we could use FitNesse - “The fully integrated standalone wiki, and acceptance testing framework. `fitness.org`:
- Stand-alone server that does all the Fit actions  
- Use Wiki-type actions to specify tables  
- Use Wiki-type naming to identify tests and test suites  
- Robert C. Martin (“Clean Code”) is a significant developer for it

![[Pasted image 20231101105119.png | center | 500]]

- Need to specify `classpath`. `!path` full path will add specified path to the classpath. Use as many as necessary.  
- `MyTestCases` is a WikiWord but we want it to be a class name — put ! (exclamation mark) at the beginning of the line  
- Begin the name of the page with Test and it will automatically get a Test button (otherwise set in properties)

