# Structs
So far in C0 we've used five different datatypes:
- `int`
- `bool`
- `char`
- `string`
- And array types of all the previous datatypes
There is one more datatype to consider - structs. A struct is a collection of various named datatypes. For example:

```c
struct img_header {
	pixel_t[] data;
	int width;
	int height;
};
```

Here `data`, `width`, and `height` are *fields* of the struct.

C0 values such as integers, characters, and the address of arrays are all *small*. This means they range from either 32 to 64 bits of stored memory in the computer.

Structs may use more memory as they have a various amount of *small types* collected together into one type. They can grow too large for the computer to easily copy around, so C0 doesn't let us use them as locals. 

Therefore, we can only create structs in allocated memory, similar to arrays. Instead of `alloc_array`we use `alloc` which returns a pointer to the struct that has been allocated in memory:

```c
struct img_header* IMG = alloc(struct img_header);
```

All of the elements in an array are initialized to their default values (zero for ints, etc). We can access the fields of a struct using arrow notation:

```c
IMG->data = alloc_array(pixel_t, 5);
IMG->width = 5;
IMG->height = 5;
```

It's also possible to deference the pointer to a struct with `*` before the name, and then use `.` after the name to select the field like so:

```c
(*IMG).data = alloc_array(pixel_t, 5);
(*IMG).width = 5;
(*IMG).height = 5;
```

The arrow notation is usually preferred.
# Pointers
As seen previously, a pointer is needed to refer to a struct that has been created in allocated memory. They can also be used to refer to arbitrary types created in allocated memory. For example:

```c
int* ptr1 = alloc(int)
```

In this case we refer to the value of `ptr1` with `*ptr1` similarly to how we did for structs, this is known as **deferencing** a pointer. This is similar to arrays with one key difference, pointers also have the special value `NULL`.

Pointers are essentially memory addresses, with two different kinds. A valid address is one that has been explicitly allocated with `alloc`, while `NULL` is an invalid address.

Attempting to deference a null pointer in C0 will throw an exception, while it will cause undefined behavior in C.
# Creating an Interface
We will now build a data structure for a *self-sorting array* of strings. The interface will look like:

```c
ssa_t  ssa_new(int size);  // alloc_array(string, size);
string ssa_get(ssa_t A, int i);            // A[i]
void   ssa_set(ssa_t A, int i, string x);  // A[i] = x
```

This isn't completely safe though. What if a user tries to access an array index with negative length?

We can add preconditions:

```c
ssa_t ssa_new(int size);  // alloc_array(string, size);
/*@requires 0 <= size; @*/ ;

string ssa_get(ssa_t A, int i)             // A[i]
/*@requires 0 <= i; @*/ ;

void ssa_set(ssa_t A, int i, string x)     // A[i] = x
/*@requires 0 <= i; @*/ ;
```

This still isn't fully safe though. What if user tries to access an array index past the length of the array?

More preconditions! Since we don't have the `\length` function normally available for C0 arrays, we need to add our own:

```c
ssa_t ssa_new(int size);  // alloc_array(string, size);
/*@requires 0 <= size; @*/ ;

int ssa_len(ssa_t A);
/*@ensures 0 <= \result; @*/ ;

string ssa_get(ssa_t A, int i)             // A[i]
/*@requires 0 <= i && i < ssa_len(A); @*/ ;

void ssa_set(ssa_t A, int i, string x)     // A[i] = x
/*@requires 0 <= i && i < ssa_len(A); @*/ ;
```

To add even more safety, we will say `ssa_t` is a *pointer type*. This is important because we can add preconditions and postconditions to ensure we're not operating on an invalid memory address like so:

```c
// typedef ______* ssa_t; 

int ssa_len(ssa_t A) 
/*@requires A != NULL; @*/ 
/*@ensures \result >= 0; @*/ ; 

ssa_t ssa_new(int size) 
/*@requires 0 <= size; @*/ 
/*@ensures \result != NULL; @*/ 
/*@ensures ssa_len(\result) == size; @*/ ; 

string ssa_get(ssa_t A, int i) 
/*@requires A != NULL; @*/ 
/*@requires 0 <= i && i < ssa_len(A); @*/ ; 

void ssa_set(ssa_t A, int i, string x) 
/*@requires A != NULL; @*/ 
/*@requires 0 <= i && i < ssa_len(A); @*/ ;
```
# The Library Perspective
When we implement the library for `ssa_t`, we will declare a type `ssa` as a synonym for `struct ssa_header` to avoid having to rewrite the struct keyword every time we want to use the struct.

```c
struct ssa_header {
	int length;
	string[] data;
};
typedef struct ssa_header ssa;

typedef ssa* ssa_t;
```

Note we use `ssa_t` as a typedef for `ssa*` which is a pointer as we previously stated.

Inside the library we will use `ssa*` instead of `ssa_t` to make it clear we're working with a pointer instead of a value.

```c
int ssa_len(ssa* A)
//@requires A != NULL;
//@ensures 0 <= \result;
{
	return A->length;
}

string ssa_get(ssa* A, int i) 
//@requires A != NULL; 
//@requires 0 <= i && i < ssa_len(A); 
{ 
	return A->data[i]; 
}
```

There are already safety issues with this implementation - how do we know the length of the stored array in `A` is equal to the result of `ssa_len(A)`? To ensure length safety we will add a **specification function**:

```c
bool is_array_expected_length(string[] A, int length) {
	//@assert \length(A) == length;
	return true;
}

bool is_ssa(ssa* A) {
	return A != NULL
		&& is_array_expected_length(A->data, A->length)
		&& ssa_sorted(A);
}
```

The specification function can now be used in our library to ensure safety:

```c
```c
int ssa_len(ssa* A)
//@requires is_ssa(A);
//@ensures 0 <= \result;
{
	return A->length;
}

string ssa_get(ssa* A, int i) 
//@requires is_ssa(A); 
//@requires 0 <= i && i < ssa_len(A); 
{ 
	return A->data[i]; 
}
```

Functions that create new instances of the data structure should also add the specification function as a postcondition:

```c
ssa* ssa_new(int size) 
//@requires 0 <= size; 
//@ensures is_ssa(\result); 
{ 
	ssa* A = alloc(ssa); 
	A->length = size; 
	A->data = alloc_array(string, size); 
	return A; 
}

void ssa_set(ssa* A, int i, string x) 
//@requires is_ssa(A); 
//@requires 0 <= i && i < ssa_len(A); 
//@ensures is_ssa(A); 
{ 
	A->data[i] = x; 
	// Move x up the array if needed 
	for (int j = i; j < A->length-1 && 
				    string_compare(A->data[j],A->data[j+1]) > 0; 
		 j++)
	//@loop_invariant i <= j && j <= A->length - 1; 
	{
		swap(A->data, j, j+1); 
	}
	// Move x down the array if needed 
	for (int j = i; 
		 j > 0 && string_compare(A->data[j],A->data[j-1]) < 0; 
		 j--) 
	//@loop_invariant 0 <= j && j <= i;
	{
		swap(A->data, j, j-1); 
	}
}
```
### Debugging a Library Implementation
It's not unlikely there may be a bug in our first implementation of the `ssa_t` interface. We can debug it by writing print functions to examine what is happening inside the interface. 

If the contracts are being violated, we can use an unsafe print function to see which pre/postcondition is failing:

```c
void ssa_print_unsafe(ssa* A) {
	int len = A->length;
	printf("SSA length: %d; SSA data: [", len);
	for (int i = 0; i < len; i++) 
	//@loop_invariant 0 <= i && i <= len; 
	{ 
		printf(A->data[i]); 
		if (i < len-1) printf(", "); 
	} 
	printf("]"); }
}
```

If the contracts are not being violated we can use a safe print function

```c
void ssa_print_internal(ssa* A) 
//@requires is_ssa(A);
{
	ssa_print_unsafe(A);
}
```
# The Client Perspective
The client of our self-sorting array library can use any function exported by the library *and nothing else*. As an example, we write a client-side printing function:

```c
void display_this_ssa(ssa_t A) 
//@requires A != NULL; 
{ 
	printf("\n"); 
	for (int j = 0; j < ssa_len(A); j++) 
	//@loop_invariant 0 <= j; 
	{ 
		printf("%d => %s\n", j, ssa_get(A, j)); 
	}
}
```

Note this code uses `ssa_get` and `ssa_len`. Using the fields `data` and `length` would violate the interface, and mostly likely result in bugs when compiled with another implementation of the library.