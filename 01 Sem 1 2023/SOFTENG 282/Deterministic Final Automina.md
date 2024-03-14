Defining languages

### An example of Grammar
1. A *sentence* is made up of a valid *noun phrase* followed by a *verb phrase*
2. A noun phrase is made up of an *article* followed by an *adjective* followed by a noun
3. Or by a article followed by a noun.
4. A verb phrase is made up of a verb followed by an adverb
5. or just a verb on its own
6. an article is a, 
7. an article is the
8. an adjective is large
9. an adjective is hungry
10. a noun is rabbit
11. a noun is mathematician
12. a verb is eats
13. a verb is hops
14. an adverb is quicly
15. an adverb is wildly

Using these rules, we can come up with a valid *Sentence* though a sequence of replacement
- Noun phrase | verb phrase
- article noun adjective | verb phrase
- article noun adjective | verb adverb
- etc etc
- The large rabbit hops quickly



## Alphabets and strings
Let $\Sigma$ be a finite alphabet. Our typical binary alphabet is $\{ a, b \}$. A finite sequence of symbols from $\Sigma$ is called a string or a word

- abbbba
- aaaaa

#### Strings
- Each string $v$ is of the form $\sigma_{1}, \sigma_{2}, \dots, \sigma_{n}$ where $\sigma_{i} \in \Sigma$
- The length of $v$ is denoted by $|v|$, is the number of symbols it has:
	- $|\text{abba}| = 4$
- The empty string $\lambda$ is a string of length 0 
- There are $k^n$ strings of length $n$ over a $k$ letter alphabet. 
	- This is arrangement with repetition. 

#### Concatenation Operation
- The set of all strings over the alphabet $\Sigma$ is $\Sigma^* = \{ \sigma_{1}, \sigma_{2}, \dots,  \sigma_{m} | \sigma_{1}, \sigma_{2}, \dots, \sigma_{m} \in \Sigma, m \in \mathbb{N} \}$
- We denote strings by the letters $u, v, w$. The concatenation of $u$ and $v$ is obtained by writing $u \cdot v$ or $uv$
	- $(abb) \cdot (aba) = (abbaba)$
- Operation is associative: $u \cdot(v\cdot w) = (u\cdot v)\cdot v$
	-  In general - $uv \neq vu$
- For any string, we have $u\cdot \lambda = \lambda \cdot u = u$ provided $\lambda$ is the empty string.

#### Substrings
- For a string $u$ we denote $u^n = \underbrace{ u\cdot u\cdot u }_{ \text{n times} }$
	- $(aba)^3 = abaabaaba$
-  If a string $w$ occurs inside a string $u$ then we say that $w$ is a substring of $u$

#### Prefix/Suffix
- A string $w$ is a prefix of $u$ if we can represent it in the form $u = wu_1$
	- Prefixes of $aba$ are $\lambda, a, ab, aba$
- A suffix is the same thing but for at the end of the string $u = u_{1} w$
	- Suffixes of $abb$ are $\lambda, b, bb, bba$

#### Language
A language over an alphabet $\Sigma$ is a subset of $\Sigma^*$

Examples:
- $\varnothing$: Empty language
- $\Sigma^*$
- $\{ a^n | n\in \mathbb{N} \} = \{ a, aa, aaa, \dots \}$
- $\{ aba, bab \}$
- $\{w \in \{ a, b \}^* | bab \text{ is a substring of } w\} = \{ bab, aaababa, \cancel{ bbaaaba } \}$
- $\{ w\in \Sigma^* | w \text{ has even length} \} = \{ \lambda, ab, aa, \dots \}$

## Operations on languages
#### Boolean Operations
Let $U$ and $V$ be languages over an alphabet $\Sigma$
1. The union of $U$ and $V$ is $U \cup V$
2. The Intersection of $U$ and $V$ is $U \cap V$
3. The complement of $U$  is $\Sigma^* \backslash U$

#### Concatenation
let $A = \{ 0, 11 \}$ and $B = \{ 1, 10, 110 \}$
Then: $AB = \{ 01, 010, 0110, 111, 1110, 11110 \}$
and $BA = \{ 10, 111, 100, 1011, 1100, 11011 \}$

#### Equivalence
If $U$ and $V$ are languages on alphabet $\Sigma$. 
- Prove $V\cdot U = V$ if
- $V = \{ w | \text{ aba is a substring} \}$
- $U = \{ w |\,\,\,  |w| \text{ is even} \}$
![[Pasted image 20230602110333.png | 350]]

## Deterministic Finite Automata
Let $U$ by a language over an alphabet $\Sigma$ Suppose we are given a string $v$, and we would like to check whether $v$ belongs to $U$.

### DFA Requirements
Is defined by a 5-tuple $(S, q_{0}, T, F, \Sigma)$
- $S$ is the set of states
- $T$ is the transition function, $T: S \times \Sigma \to S$
- $F$ is a subset of $S$ called the set of accepting states
- $\Sigma$ is an alphabet
- $q_{0}$ is the initial state, $q_{0} \in S$


>[!example]+ 
>aba subset example
![[Pasted image 20230602110624.png]]
>- $S = \{ 0, 1, 2,3 \}$ 
>- $F = \{ 3 \}$ 
>- $\Sigma = \{ a, b \}$ 
>- $q_{0} = 0$ 
>
>|$T$ |  -- $a$ --  | -- $b$ -- |
>|--|-|-|
>|0|1|0|
>|1|1|2|
>|2|3|0|
>|3|3|3|


#### Converting to human readable
![[FSM Example.png]]


#### Proof (2 ways?)

![[FMS Example.png]]







## eeee
#### Runs and Acceptance
Let $M = (S, q_{0}, T, F, \Sigma)$ be a DFA and $u = \sigma_{1} \dots \sigma_{n}$

##### $Run(M, u)$
1. Initialise $s = q_{0}, i=1$ and prints $s$
2. While $i \leq n$ do
	1. Set $\sigma = \sigma_{i}$
	2. Set $s = T(s, \sigma)$
	3. Print $s$

We say that $M$ accepts $u$ if the run $s_{1}, s_{2}\dots s_{n}, s_{n+1}$ of $M$ on $u$ with the last state $s_{n+1} \in F$.
Such a run is called an accepting run. 


#### DFA Recognisable Languages
Let $M = (S, q_{0}, T, F, \Sigma)$ be a DFA. The language accepted by $M$ is the language  $L(M) = \{ w | M \text{ accepts } w \}$. 
- A language $L\in \Sigma^*$ is a DFA recognisable...

