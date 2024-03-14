

1. $n^3 = \Theta(100n^3)$




3. n2 · n3 ̸= O(n4)  
4. n2 · 2n = O(22n)

5. 22n ̸= O(3n)

6. nlog(n5)=O(nlogn).



$$T_{n} = n^3,\, g = 100n^3,\,c = \frac{1}{1000}, \, C = 2$$
$$
c \cdot g(n) \leq T_{n} \leq C\cdot g(n)
$$
$$
\frac{1}{10}n^3 \leq n^3\leq 200n^3
$$


$$
g=n^4, \, T_{n} = n^2\times n^3 = n^5
$$
$$
T_{n} < C\cdot g(n)$$
$$
n^5 \leq C\cdot n^4
$$
This is a contradiction, whatever constant you choose will eventually be overpowered by the higher power of $n^5$

$$
T_{n} = n\log(n^5) = 5n\log n, \, g = n\log n, c = 6
$$
$$
T_{n} \leq c\cdot g(n)
$$$$
5n\log n \leq  6\cdot n\log n
$$

$$
f(n) = n² \cdot 2^n, \,g=2^{2n}
$$
$$
\begin{align}
f(n) & \leq C\times g(n) \\
n^{2} \cdot 2^n & \leq C\times2^{2n} \\
n^{2} \cdot 2^n & \leq C\times(2^n)^2 \\
n^{2} & \leq C\times2^n
\end{align}
$$
This is true for $n_{0} > 0$ and $C = 4$

### Exercise two notes
We want to find this function 