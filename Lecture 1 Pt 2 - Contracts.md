# Reasoning about Correctness
## Review
Recall from [[Lecture 1  Pt 1 - Contracts|Lecture 1  Pt 1 - Contracts]] we ended with the function

```c
int POW(int x, int y)
@requires y >= 0
{
	if (y == 0) return 1;
	return POW(x, y - 1) * x;
}

int f(int x, int y) 
// @requires y >= 0;
// @ensures \result == POW(x, y);
{
	int b = x;
	int e = y;
	int r = 1;
	while (e > 1) {
		if (e % 2 == 1) {
			r = b * r;
		}
		b = b * b;
		e = e / 2;
	}
	return r * b;
}
```

The complex part of this function is the loop. It's unclear how the values of variables change at each iteration, or how many iterations are run (and if it's guaranteed to terminate!). We want to identify loop invariants by testing values:
- - - 
## Testing Loop Invariants
We take the sample inputs of `(2, 8)` and build a table of how the variables change at each loop iteration:

| b | e | r | b^e |
| :--: | :--: | :--: | ---- |
| 2 | 8 | 1 | 256 |
| 4 | 4 | 1 | 256 |
| 16 | 2 | 1 | 256 |
| 256 | 1 | 1 | 256 |

It appears the loop invariant is $b^e$. To confirm this loop invariariant we test it on the sample inputs `(2, 7)`:

| b | e | r | b^e | b^e * r |
| :--: | :--: | :--: | ---- | ---- |
| 2 | 7 | 1 | 256 | 128 |
| 4 | 3 | 2 | 64 | 128 |
| 16 | 1 | 8 | 16 | 128 |

We find $b^e$ isn't a valid loop invariant, but we find a potentially more general loop invariant $b^{e}r$. This can be added to `f` as a C0 loop invariant:

```c
int f(int x, int y) 
// @requires y >= 0;
// @ensures \result == POW(x, y);
{
	int b = x;
	int e = y;
	int r = 1;
	// @loop_invariant POW(b, e) * r == POW(x, y);
	while (e > 1) {
		if (e % 2 == 1) {
			r = b * r;
		}
		b = b * b;
		e = e / 2;
	}
	return r * b;
}
```
- - - 
## Proving Correctness
We now have a potential loop invariant $b^{e}r=x^{y}$, but we need to prove it is guaranteed to be correct.
- - - 
### Safety
The precondition of `f`, `y >= 0` guarantees that the call `POW(x, y)` is safe.

We introduce a new idea - the loop guard cannot be used to guarantee safety because it is false at the termination of the loop. Instead we add the loop invariant `e >= 0` to `f` which guarantees the call `POW(b, e)` is safe. This is correct even after the loop terminates.
- - - 
### Loop Invariants
We now have two loop invariants that must be proved correct:

```
// @loop_invariant e >= 0;
// @loop_invariant POW(b, e) * r == POW(x, y);
```
##### Proving $e\geq 0$
Initial (base case):
1. $y >= 0$ by line 2
2. $e = y$ by line 6
3. $e \geq 0$ by math on (1) and (2)

Preservation (induction step):
1. $e \geq 0$ by assumption
2. $e' = \frac{e}{2}$ by line 16
3. $\frac{e}{2} >= 0$ by math on (1)
5. $e' >= 0$ by math on (2) and (3)
##### Proving $b^{e}r=x^{y}$

Initial (base case):
1. $b = x$ by line 5
2. $e = y$ by line 6
3. $r = 1$ by line 7
5. $b^{e} * r = x^{y}$ by math on (1), (2), and (3)

Preservation (induction step):
1. $b^{e}r=x^{y}$ by assumption
2. $b'=b^{2}$
3. Case 1: $e$ is even ()
	A. Let $n\in\mathbb{Z}$ such that $e=2n$
	B. $\frac{e}{2}=n$ by math
	C. $e'=n$ by (B)
	D. $r'=r$ by line 12
	E. $(b^{'})^{e^{'}}\cdot r^{'}=(b^{2})^{n}\cdot r$ by (2), (C), and (D)
	F. $(b^{'})^{e^{'}}\cdot r^{'}=(b)^{2n}\cdot r$ by math on (E)
	G. $(b^{'})^{e^{'}}\cdot r^{'}=(b)^{e}\cdot r$ by (A)
	H. $(b^{'})^{e^{'}}\cdot r^{'}=x^{y}$ by (1)
4. Case 2: $e$ is odd
	A. Let $n\in\mathbb{Z}$ such that $e=2n+1$
	B. $\frac{e}{2}=\frac{2n+1}{2}=n+{\frac{1}{2}}=n$ by math
	C. $e'=n$ by (B)
	D. $r'=b\cdot r$ by line 13
	E. $(b^{'})^{e^{'}}\cdot r^{'}=(b^{2})^{n}\cdot b\cdot r$ by (2), (C), and (D)
	F. $(b^{'})^{e^{'}}\cdot r^{'}=(b)^{2n}\cdot b\cdot r$ by math on (E)
	G. $(b^{'})^{e^{'}}\cdot r^{'}=(b)^{2n+1}\cdot r$ by math on (F)
	H. $(b^{'})^{e^{'}}\cdot r^{'}=(b)^{e}\cdot r$ by (A)
	I. $(b^{'})^{e^{'}}\cdot r^{'}=x^{y}$ by (1)
- - - 
### Correctness
From the loop invariants we know $b^{e}r=x^{y}$ is true at the end of the function. After the loop terminates we know $e\geq 0$ from the loop invariant and $e\leq 1$ from the negation of the loop guard. We break this into two cases:
-  $e=1$: it follows $b\cdot r=x^{y}$ which is what we return:

```
return r * b;
```

- $e=0$: it follows $r=x^{y}$ which is NOT what we return. This is a bug in the program! 

The while loop can be modified to guarantee $e=0$ at the termination of the loop

```c
int f(int x, int y) 
// @requires y >= 0;
// @ensures \result == POW(x, y);
{
	int b = x;
	int e = y;
	int r = 1;
	// @loop_invariant e >= 0;
	// @loop_invariant POW(b, e) * r == POW(x, y);
	while (e > 0) {
		if (e % 2 == 1) {
			r = b * r;
		}
		b = b * b;
		e = e / 2;
	}
	return r * b;
}
```

From the loop invariant $e\geq 0$ and the negation of the loop guard $e\leq 0$ it follows $e=0$. We know from our previous case that we should return $r$ so the return statement is modified:

```c
return r;
```
- - - 
### Termination
We have now proved when the function returns a value it is guaranteed to be correct, but is the function always guaranteed to return a value?

We prove termination as follows
1. $e>0$ by line 8 (loop guard)
2. $e^{'}=\frac{e}{2}$ by line 16
3. $e^{'}<e$ by math
4. $e^{'}\geq 0$ by math
This is a valid proof of termination.

