# Arithmetic vs Bitwise Operations
Never mix and match the arithmetic and bitwise operators (e.g you can't multiply two pixels)
- - - 
# Memory Model
## Active Frame
We begin with the following code

```c
int POW(int x, int y) {
	if (y == 0) {
		return 1;
	}
	return x * POW(x, y - 1);
}

int square(int n) {
	return n * n;
}

int main() {
	int x = 10;
	int y = square(x - 1);
	//@assert y == POW(x - 1, 2);
	return y;
}
```

In C) the *active frame* is the function is currently being run
-  Line 13
	- Active Frame: `main`
		- `x = 10`
		- `y = `
- Line 14
	- Frames: `main`
		- `x = 10`
		- `y = `
	- Active Frame: `square`
		- `n = x - 1`
- - - 
## Arrays
Programs have two different types of memory, local memory and allocated memory.
- - - 
### Allocated Memory
Allocated memory forms in blocks where each byte of memory has an *address* written in hex (e.g. `0xF782BC12`).

To allocate memory for an array we use `alloc_array`:

```c
int[] A = alloc_array(int, 5);
```

which returns `A is 0x6C1B74E0`. Running this program multiple times will allocate the memory in a different address! When we allocate an array the address will be stored in local memory, but not the allocated memory itself.

If we run

```c
int[] A = alloc_array(int, 5)
int[] B = A;
B[0] = 7
```

It would follow that `A[0]` would also return `7` because `A` and `B` both *point* to the same location in memory.
- - - 
### Copying Arrays
If we want to copy arrays we could define a function like so:

```c
int[] array_copy(int[] A, int n) 
//@requires n == \length(A);
{
	int[] B = alloc_array(int, n);
	for (int i = 0; i < n; i++) {
		B[i] = A[i];
	}
	return B;
}

int main() {
	int[] = alloc_array(int, 3);
	for (int i = 0; i < 3; i++) {
		I[i] = i+ 
	}
	int[] J = array_copy(I, 3);
	return 0;
}
```

