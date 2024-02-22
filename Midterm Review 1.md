# Pointers and Structs
## Pointers
Local memory is used for *literals*, such as:
- ints
- bools
- strings
- any direct values
Allocated memory is used 

Allocated memory can be used like so:

```c
type* name = alloc(type);
```

where `type` is the type of the variable we want to create and `name` is the name of the variable.

The default values for objects in allocated memory is:
- `int` : `0`
- `char` : `\0`
- `string` : `""`
- `bool` : `false`
- `pointer` : `NULL`
- `array` : empty array

We can deference a pointer by using the `*` symbol before the variable name like so:

```c
bool* honkVeryCool = alloc(bool);
*honkVeryCool = true;
```
- - - 
### Aliasing
Aliasing is when you have multiple values that point to the same block of memory in allocated memory. For example:

```c
int* honkAge = alloc(int);
*honkAge = 122;
int* chonkAge = alloc(int);
chonkAge = honkAge;
*chongAge = 15122;
```

After this code the value of `*honkAge` will be `15122` since it is aliased to the same block of memory as `chonkAge`.

Garbage collection can be done by crossing out any objects in allocated memory that don't have a pointer to them stored somewhere.
- - -
## Structs
**Structs** are a data structure which can store multiple *fields* in one datatype.

To create a struct we would use the following syntax:

```c
struct goose_header {
	string name;
	string bowtie_color;
	image* img;
}

struct goose_header* silly = alloc(struct goose_header);
```

Typing out the full `struct goose_header` might become annoying, so we can use `typdef` to create an alternate syntax:

```c
typedef struct goose_header goose;
```

To access a field in a struct we would first deference the pointer to the struct, and then access the parameter with `.`. It's also possible to use the arrow operator `->` on the pointer directly. For example:

```c
goose* silly = alloc(goose);

(*silly).name = "Hi";
silly->bowtie_color = "Red";
```

These are both valid syntaxes!

We should always use contracts to ensure we aren't accessing null pointers.

Tips:
- Make sure pointer arrows typecheck
- Label all variables and structs
- Keep track of what is garbage collected (i.e. cross out pointer arrows as you go through and then erase what you don't need)
- - - 
# Stacks and Queues
## Stacks
**Stacks** work on a last in first out (LIFO) principle. Imagine a stack of papers, we can add more papers to the top and then also remove them from the top. 

This is essentially how a stack operates, the last value we add to a stack will be the first value we get when popping from the stack.

We have two main operations on a stack *push* and *pop*:
- Pushing to the stack will add an element to the top of our stack of papers.
- Popping from the stack will return the top of the stack of papers and remove it from the stack.
- - - 
## Queues
**Queues** work on a first in first out (FIFO) principle. Imagine standing in a line at the grocery store, the first person to enter a line will be the first person to get checked out (and in contrast the last person to enter a queue will be the last person checked out).

This is essentially how a queue operates, the last value we add to a queue will be the last value we get when popping from the stack.

We have two main operations on a queue *enq* and *deq*:
- Enq-ing an element to the stack will add an element to the back of the queue.
- Deq-ing an element from the stack will return the first item in the queue and remove it from the queue.

Approaching a problem with stacks and queues:
- Preserve your queue/stack - make sure that when you are pushing/popping/enq-ing/deq-ing that you always return your stack of queue to the state it was in before you changed it (keep aliases in mind).
- Draw pictures
- Sometimes, you may have to decide which struct you want to use. Figure out whether you want a LIFO or FIFO. For example, when reversing a queue you may want to use a stack to temporarily store elements in.

Example problem, we want to find the oldest goose in the line given a non-empty queue of geese with unique ages (remember to preserve the queue)

```c
goose_t oldest_goose_in_line(queue_t geese)
//@requires geese != NULL;
//@requires all_goose_different_ages(geese);
{
	queue_t temp = queue_new();
	goose_t oldest = deq(geese);
	enq(temp, oldest);
	while (!queue_empty(geese)) {
		goose_t g = deq(geese);
		if (g->age > oldest->age) {
			oldest = g
		}
		enq(temp, g);
	}
	while (!queue_empty(temp)) {
		enq(geese, deq(temp));
	}
	return oldest;
}
```
- - - 
# Interface vs Implementation
## Interface
The client uses the interface.

Never use specification functions as a client.
- - -
## Implementation
The developer uses the implementation.
- - -
# Contracts and Loop Invariant Proofs
## Contracts
We use contracts to ensure *safety* and *correctness*
- Safety is when we meet all the preconditions needed to use the function/cycle through the loop
- The function spits out what we expect it to

We have several types of contracts
- Preconditions - checked just before execution for safety `//@requires`
- Postconditions - checked just after execution for correctness `//@ensures`
- Asserts - like a statement, checked every time when encountered for correctness/safety checks - `//@assert`
- Loop invariants - Checked before the first iteration, after each iteration - `//@loop_invariant`

Things we can use in point-to-reasoning
- "X" is unchanged
- Recent boolean expressions
- Statements based on contracts
- Assignments within the current block of code
- Math

Things we cannot use in point-to-reasoning
- Reasoning over multiple iterations of a loop
- Bodies of earlier loops
- Bodies of functions that you are calling in
- - -
## Loop Invariant Proofs
### Initialization
Loop invariants are true before the first iteration of the loop
- - -
### Preservation
Loop invariants hold through an arbitrary iteration of the loop.

This is the approach:
- Use loop invariant tog et assumption before this iteration
- Use lines within the loop to get new prime values of changing and unchanging variables
- Show these prime values satisfy the loop invariants
- - - 
### Exit
The loop invariants and negation of the loop guard imply the postconditions
### Termination
The loop eventually terminates

"The expression ___ strictly increases/decreases at each iteration of the loop and can never become smaller than the constant ___ where the loop guard is false."
- - - 
