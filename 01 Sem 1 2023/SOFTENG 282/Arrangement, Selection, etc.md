## Summary
![[Pasted image 20230528221851.png]]

## Counting Problems
Counting the number of ways that we can choose a number of objects. These basically fall int two types:
1. Arrangement problems - The order in which we make the choices matter
2. Selection Problems - The order is not significant. 

### The union rule (Sum Rule)
Let $A_{1}, A_{2}, \dots, A_{k}$ be finite, disjoint sets, then $|\cup^K_{i=1}A_{i}| = \sum^k_{i=1}|A_{i}|$

>[!example]
>![[Exclusion Inclusion Example.png]]

>[!example]
>The mathematics department must choose either a student or faculty member as a representative for a university committee.
>
>How many choices are there for this representative if there are:
>- 37 members of staff
>- 83 Mathematics majors
>
Solution:
By the union rule, there are $37 + 83 = 120$ possible ways to pick a representative


### The product Rule
A procedure can be broken down into a sequence of two tasks There are $n_1$ ways to do the first task and $n_2$ ways to do the second task, therefore there are $n_{1}\times n_{2}$ ways to do the procedure. 

>[!example] Example - The product rule
>![[Pasted image 20230517220041.png]]


### Combining rules
>[!example] Union + Product Rules
>A password consists of six to eight characters. Each character is an uppercase letter OR a digit. Each password must contain at least one digit. How many possible passwords are there?
>![[Pasted image 20230517220832.png]]


### The counting lemma
Let $A$ and $B$ be finite sets, and let $\Psi: A \to B$ be a function such that for every $b \in B, |\psi^{-1} (a)| = r$

>[!Example]
![[Counting Lemma Example.png| center | 500]]

### Tree Diagrams
A branch represents a possible choice and leaves represent possible outcomes.
>[!example]
>![[Pasted image 20230517222246.png]]

## Arrangement
### With Replacement
We have $n$ different types of objects and we want to put $r$ of them in a row. The ordering matters. We have an unlimited supply of each type of object. How many ways can we do this?

>[!Example] 
>Consider 5 consecturive throws of a 6 sided dice. 
>The first throw gives us one out of siz numbers, as does the second, third and fourth. 
>
>Solution: The product rule gives (6, 6, 6, 6). Therefore there are $6^4$ possible results

If we wanted to count the number of arrangements of r things with n arrangements, the answer is $n^r$

Number of car licence plates XXX000
This gives (26, 26, 26, 10, 10, 10) so $26³ \times_{}10^3 = 17,576,000$

### Without Replacement
If an element is chosen, it cannot be chosen again in the next step

If there were originally 6 choices, the second round could only have 5 and the third could only have 4, etc. 
$$\large p(n, r) = n(n-1)(n-2)\dots(n-r-1) = \frac{n!}{(n-r)!}$$

if $r = n$ then there are $n!$ choices?

>[!example]
>![[Pasted image 20230517224107.png]]

### With repetitions
In many cases, we do not want to consider all the permutations of $X$ as being distinct.

For example, there are only three ways of arranging the letters in the word EGG if we consider the G's as identical. 

By the counting lemma, there are $\Large\frac{3!}{2!}$ 


>[!example] Example: Arrangement with Repetitions
>If we regard the two Us and Es in QUEUE as being identical, then how many ways are there to arrange the letters of the word QUEUE
>![[Pasted image 20230518213403.png]]

>[!example]
`WHANGAMOMONA`
$$\frac{12!}{3!\times2!\times2!\times2!}$$

## Selections
An *r-combination* of elements of a set is an unordered selection of $r$ elements form the set. 
An $r$-combination is simply a subset of the set with $r$ elements
The number of $r$-combinations of a set with $n$ distinct elements is denoted by $c(n, r)$ or $\begin{pmatrix}n  \\ r \end{pmatrix}$ 

For example if you have 10 letters, how many sets of 3 letters can you form (unordered)
$$
\frac{10 \times 9 \times 8}{3!} \text{ options}
$$
#### Binomial (R) Combinations
the number of $r$-combinations of a set with $n$ elements equals 
$$\Large C(n, r) = |B| = \frac{|A|}{r!} = \frac{P(n, r)}{r!} = \frac{n!}{(n-r)!r!}$$

$$
\binom{n} {k} = \frac{n!} {k! (n-k)!}
$$

The binomial coefficients are 
$$
\begin{pmatrix}
n \\ r
\end{pmatrix}
$$
>[!example]
>![[Pasted image 20230518223249.png]]
>![[Pasted image 20230518223726.png|center|400]]


#### With repetitions ==allowed==
In general, the number of ways of selecting $n$ objects where the order is unimportant but repetition is allowed then
$$
\binom{n+k-1}{k-1} = \frac{(n+k-1)!}{(k-1)!(n + k - 1 - ( k - 1))!} = \frac{(n+k-1)!}{(k-1)!n!} = C(n+ k-1, n)
$$

Number of ways of selecting $n$ objects of $k$ different types
Number of ways of placing $n$ indistinguishable objects into $k$ bins
Number of ways of arranging $n$ objects and $k-1$ dividers

>[!example] Marbles from a bag
>We do not care about order, just the number of each type. 
>![[Pasted image 20230525224514.png]]


#### Corollary
Let $n$ and $r$ be nonnegative integers with $r \leq n$. Then $\underbrace{ C(n, r) }_{ LHS } = \underbrace{ C(n, n-r) }_{ RHS }$
![[Pasted image 20230528222316.png]]

#### Pascals Identity
If $n$ and $k$ are integers with $n \geq k \geq 0$ then
$$
\begin{pmatrix}
n+1 \\k
\end{pmatrix} = \begin{pmatrix}
n \\k-1
\end{pmatrix} + \begin{pmatrix} n \\ k \end{pmatrix}
$$
![[Pasted image 20230528223506.png]]

#### Binomial Theory
![[Pasted image 20230528224038.png]]



>[!example]-
>![[Pasted image 20230528223949.png]]

>[!Example]- Example 2,,, Kinda important?
>![[Pasted image 20230528224956.png]]

## Inclusion / Exclusion

If A and B are disjoin sets, $|A\cup B| = |A| + |B|$ 
If A and B are not disjoint sets: $|A \cup B| = |A| + |B| - |A \cap B|$

>[!example]-
>Mississippi example - You don't need to use it for PIMS because you can only form PIMS once. 
>![[Pasted image 20230528230750.png]]
>![[Pasted image 20230528231016.png]]

![[Pasted image 20230531185150.png]]

## Pigeonhole Principal

![[Pasted image 20230601121117.png]]
![[Pasted image 20230601121137.png]]

