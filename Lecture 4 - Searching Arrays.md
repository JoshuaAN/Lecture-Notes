# Linear Search
We can implement a linear search function with the code:

```c
int search(int x, int[] A, int n)
//@requires n == \length(A);
{
	for (int i = 0; i < n; i++)
	//@loop_invariant 0 <= i && i <= n;
	{
		if (A[i] == x) return i;
	}
	return -1;
}
```

To ensure safety when calling the function and using it to index an array, we can add postconditions:

```c
int search(int x, int[] A, int n)
//@requires n == \length(A);
//@ensures \result == -1 || (0 <= \result && \result < n && A[\result] == x);
{
	for (int i = 0; i < n; i++)
	//@loop_invariant 0 <= i && i <= n;
	{
		if (A[i] == x) return i;
	}
	return -1;
}
```
- - - 
## Array Segments
Mathematical definition $x\in A[lo,hi)$
$$a\in A[lo,hi)=\begin{cases}\text{false}&& \text{if }lo=hi \\
\text{true}&& \text{if }lo\neq 0\text{ and } A[lo]=x \\
x\in A[lo+1,hi)&&\text{if }lo\neq hi\text{ and} A[lo]\neq x\end{cases}$$
- - - 
## Contract Exploit
This code should return the correct result by operational reasoning, but what if we changed the code to:

```c
int search(int x, int[] A, int n)
//@requires n == \length(A);
//@ensures \result == -1 || (0 <= \result && \result < n && A[\result] == x);
{
	return -1;
}
```

This code is still technically correct as it satisfies the preconditions and postconditions! This is known as a **contract exploit**.

To patch the contract exploit we want to add an additional clause on the postcondition such that if the result is -1, $x\not\in A[0,n)$:

```c
int search(int x, int[] A, int n)
//@requires n == \length(A);
//@ensures (\result == -1 && !is_in(x, A, 0, n)) || (0 <= \result && \result < n && A[\result] == x);
{
	for (int i = 0; i < n; i++)
	//@loop_invariant 0 <= i;
	{
		if (A[i] == x) return i;
	}
	return -1;
}
```

