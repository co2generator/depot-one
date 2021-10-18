# Polynomial Time Approximation Scheme



In computer science, a polynomial time approximation scheme (PTAS) is a type of approximation algorithm for optimization problems. 

A PTAS is an algorithm which takes an instance of an optimization problem and a parameter $\epsilon >0$ and, in polynomial time, produces a solution that is within a factor $1 + \epsilon$ of being optimal. For example, for the Euclidean traveling salesman problem, a PTAS would produce a tour with length at most $(1+\epsilon)L$, with $L$ being the length of the shortest tour. 

The running time of a PTAS is required to be polynomial in $n$ for every fixed $\epsilon$ but can be different for different $\epsilon$. Thus an algorithm running in time $O(n^{1/\epsilon})$ or even $O(n^{exp(1/\epsilon)})$ counts as PTAS. 

>**Definition 1.** A polynomial time approximation scheme (PTAS) for an optimization problem is a set of algorithms such that, given $\epsilon > 0$, it contains a $(1±\epsilon)$ approximation algorithm with running time polynomial in n when $\epsilon$ is fixed.

