# Time Complexity
How can we measure the length of a program? 
- - - 
## Wall-Clock Time
A possible way is to time the number of seconds the program takes to execute.

This can be done with the command `time`. To run a file that generates arrays we would use:

```
time - ./a.out -n 5000000 -r 200
```

which outputs the result

```
real 1.28
user 1.27
sys 0.01
```

This isn't a very satisfying number though, as the execution time could change depending on the hardware.

Increasing the size of the arrays to `10000000` would produce the result

```
real 2.72
user 2.70
sys 0.02
```

Notice we doubled the size of the input, and the execution size of the program also doubled!
- - - 
## Operations
An alternative way to count the length of a program would be the number of operations a program takes. For the program:

```c
int search(int x, int[] A, int n) {
	for (int i = 0; i < n; i++) {
		if (A[i] == x) return i;
	}
	return -1;
}
```

This function takes a worst case of $n$ loop iterations before finding the element $i$, for a total of $3n + 2$ operations.
- - - 
## Big O
Given two C0 functions that solve the same problem:

- Function $F$ has cost $f(n)$
- Function $G$ has cost 

We say $f$ is better than $g$ if for all $n$, $f(n)\leq g(n)$. This could be incorrect though if $g(n)\leq f(n)$ for small values of $n$, but $f(n)\leq g(n)$ is true for the general case.

The formal definition of big O is $f\in O(g)$ if:
	There exists a natural number $n_{0}$ and real $c$ such that for all $n\geq n_{0},f(n)\leq c\cdot g(n)$.