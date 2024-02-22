# Number Representations
Numbers can be represented in many ways:
- 7
- seven
- sept
- VII
These all represent the same number in different forms or languages. Computers use *binary* to represent numbers.
- - - 
## Decimal
The base of a number can be indicated by the subscript $_{[d]}$ where $d$ is the base.  An example representing a number in base 10 would be:
$$1209_{[10]}=1\times10^{3}+2\times 10^{2}+0\times 10^{1}+9\times10^{0}$$
- - - 
## Binary
An example of representing a number in binary would be:
$$100101_{[2]}=1\times 2^{5}+0\times 2^{4}+0\times 2^{3}+1\times 2^{2}+0\times 2^{1}+1\times2^{0}$$
- - - 
## Hexadecimal
An example of representing a number in hexadecimal would be:
$$B0A_{[16]}=B\times 16^{2}+0\times 16^{1}+A\times 16^{0}$$
Converting a binary number would work as follows
$$\begin{align*}
1100\quad 0000\quad 1111\quad 1111\quad 1110\quad 1110\implies C\quad 0\quad F\quad F\quad E\quad E
\end{align*}$$
As each set of four binary numbers is translated into the corresponding hexadecimal is.
- - - 
## Binary Arithmetic
Adding binary numbers work similarly to base 10
$$
\begin{array}
  0 & 1 & 1 & 0 & 0 \\
+ & 0 & 1 & 1 & 1 \\
\hline
  1 & 0 & 0 & 1 & 1 & \\
\end{array}$$
- - - 
## Machine Words
Computer hardware processes batches of $k$ bits in parallel.
- $O$ is a *machine word*
- $O$ is 32 bits nowadays
An *int* is represented as a word, in C0 an int is always 32 bits. 
- - - 
# Overflow
We will use the example of the machine word having a size of 4 bits. Take the following addition of two four bit numbers:
$$
\begin{array}
  0 & 1 & 1 & 0 & 0 \\
+ & 1 & 0 & 0 & 1 \\
\hline
  1 & 0 & 1 & 0 & 1 & \\
\end{array}$$
The result doesn't fit in the machine word size! What could we do as a way to continue the program?
- - - 
## Eliminate Overflow Bits
Instead of throwing an error, we could simply eliminate the bits that overflowed past the machine word size:
$$1100+1001=\cancel{1}+0+1+0+1=101$$
Notice the answer $101$ is $5$ in base 10 which is congruent to the real answer $21$ modulo $16$!
$$5\equiv 21\text{ mod }16$$
- - - 
## Modular Arithmetic
Modular arithmetic obeys the same laws as normal arithmetic, and is used to handle overflow in C0.

An example:

```c
string foo(int x) {
	int z = 1+x;
	if (x+1 == z) {
		return "Goodshit";
	} else {
		return "Damn that sucks"
	}
}
```

This function is guaranteed to always return `"goodshit"` as:
$$1+x\equiv x+1\text{ mod }O$$
- - - 
## Computing Modulo $n$
Rather than viewing numbers as a *field* we can view them as a *manifold* instead.

![[Screenshot 2024-01-23 at 11.47.25 AM.png | 400]]

For example the position $12$ corresponds to 
$$12,28,44,60,\ldots$$
- - - 
## Negative Numbers
It follows extending this principle to negative numbers $12$ also corresponds to 
$$-4,-20,-36,\ldots$$
- - - 
## Comparing Numbers
We split the number circle in half, it follows the total range of the integers is the set
$$\{-8,-7,-6,-5,-4,-3,-2,-1,0,1,2,3,4,5,6,7\}$$
- - - 
## Two's Complement
For the following code

```c
string bar(int x) {
	if (x+1 > x) {
		return "Goodshit"
	} else {
		return "ayo what is blud yapping about 😂"
	}
}
```

This function does not always return `"Goodshit"`! If `x` has the value of `int_max` it will overflow to `int_min` and the comparison $x+1 > x$ is false.
- - - 
## Division and Modulus
In calculus $\frac{x}{y}$ is $z$ such that $y\times z=x$.
- - - 
# Bit Patterns
## Pixels as Ints
We can describe a pixel as an integer like so:

![[Screenshot 2024-01-23 at 12.09.03 PM.png]]

Where the int is split into four parts corresponding to alpha, red, green, and blue.
- - - 
## Bitwise Operations
Note all operators are performed on a single bit

and operator:

| & | 0 | 1 |
| :--: | :--: | :--: |
| 0 | 0 | 0 |
| 1 | 0 | 1 |
or operator:

| \| | 0 | 1 |
| :--: | :--: | :--: |
| 0 | 0 | 1 |
| 1 | 1 | 1 |
xor operator:

| ^ | 0 | 1 |
| :--: | :--: | :--: |
| 0 | 0 | 1 |
| 1 | 1 | 0 |
not operator:

| ~ | 0 | 1 |
| :--: | :--: | :--: |
| - | 1 | 0 |
