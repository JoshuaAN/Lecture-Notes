# Stacks
## The Stack Interface
**Stacks** are data structures that allow us to insert and remove items. They operate on a LIFO (Last in first out) principle. The last element inserted is the first one that comes out.

The interface for a stack may look like:

```c
//typedef _____* stack_t

bool stack_empty(stack_t S)    // O(1), check if stack is empty
/*@requires S != NULL @*/

stack_t new_stack()            // O(1), create a new empty stack
/*@ensures \result != NULL @*/
/*@requires stack_empty(\result) @*/

void push(stack_t S, string x) // O(1), add new item on top of stack
/*@requires S != NULL @*/
/*@ensures !stack_empty(\result) @*/

string pop(stack_t S)          // O(1), remove item from top
/*@requires S != NULL @*/
/*@requires !stack_empty(\result) @*/
```

Like `ssa_t`, the abstract type `stack_t` is representing a *mutable* data structure, where pushing and popping modifies the contents of the stack.
- - -
## Using the Stack Interface
We look at some simple examples to understand how a stack works. We write a stack as:
$$x_{1},x_2,\ldots,x_n$$
where $x_{1}$ is the *bottom* of the stack, and $x_{n}$ is the *top* of the stack. We *push* elements on the top, and also *pop* them from the top.

An example of several operations on a stack:

![[Screenshot 2024-02-16 at 1.15.41 PM.png]]
- - - 
## Abstraction
An important point about designing an interface to a data structure like a stack is to achieve abstraction. This means that as a client of the data structure we can only use the functions in the interface. In particular, we are not permitted to use or even know about details of the implementation of stacks.

Let’s consider an example of a client-side program. We would like to examine the element at the top of the stack without removing it from the stack. Such a function would have the declaration

```c
string peek(stack_t S) 
/*@requires S != NULL && !stack_empty(S); @*/ ;
```

If we knew how stacks were implemented, we might be able to implement, as clients of the stack data structure, something like this

```c
 string peek(stack_t S) 
 //@requires is_stack(S) && !stack_empty(S); 
 { 
	 return S->data[S->top]; 
 }
```

This violates the abstraction though by relying on the internal implementation of `stack_t`. A implementation that respects the interface would look like:

```c
string peek(stack_t S) 
//@requires S != NULL && !stack_empty(S); 
{ 
	string x = pop(S); 
	push(S, x);
	return x;
}
```
- - -
## Computing the Size of a Stack
We can now write an interface-respecting function that computes the size of a stack like so:

```c
int stack_size(stack_t S)
//@requires S != NULL;
//@ensures \result >= 0;
{
	stack_t T = new_stack();
	int count = 0;
	while (!stack_empty(S)) {
		push(T, pop(S));
		count++;
	}
	while (!stack_empty(T)) {
		push(S, pop(T));
	}
	return count;
}
```

The complexity of this function is $O(n)$ where $n$ is the number of elements in the input.
- - - 
# Queues
## The Queue Interface
**Queues** are data structures that allow us to insert and remove items. They operate on a FIFO (First in first out) principle. The first element inserted is the first one that comes out. 

For example picture a line of people waiting to be checkout out by a cashier at a grocery store, the last person in the line is the last person to get checked out.

```c
```c
//typedef _____* stack_t

bool queue_empty(queue_t Q)    // O(1)
/*@requires Q != NULL @*/

queue_t new_queue()            // O(1)
/*@ensures \result != NULL @*/
/*@requires queue_empty(\result) @*/

void enq(queue_t Q, string x)  // O(1)
/*@requires Q != NULL @*/
/*@ensures !queue_empty(\result) @*/

string deq(queue_t Q)          // O(1)
/*@requires Q != NULL @*/
/*@requires !queue_empty(\result) @*/
```
- - - 
## Using the Queue Interface
We look at some simple examples to understand how a queue works. We write a queue as:
$$x_{1},x_2,\ldots,x_n$$
where $x_{1}$ is the *front* of the stack, and $x_{n}$ is the *back* of the queue. We *enqueue* elements in the back, and *dequeue* them from the front.

An example of several operations on a stack:

![[Screenshot 2024-02-16 at 1.36.55 PM.png]]
- - -
## Copying a Queue Using Its Interface
We can copy a queue with an interface-respecting function like so:

```c
stack_t copy_queue(queue_t Q)
//@requires Q != NULL
//@ensures \result != NULL
{
	queue_t T = new_queue();
	while (!queue_empty(Q)) {
		enq(T, deq(Q));
	}
	//@assert queue_empty(Q);
	queue_t C = new_queue();
	while (!queue_empty(T)) {
		string item = deq(T);
		enq(Q, item);
		enq(C, item);
	}
	return C;
}
```

This function works by creating a temporary queue `T` which all the elements from `Q` are emptied into. It then empties all the items from `T` into both `Q` and `C` and returns `C`.

```c
string stack_bottom(stack_t S) 
//@requires S != NULL; 
//@requires !stack_empty(S); 
{
	stack_t T = stack_new();
	push(T, pop(S));
	while (!stack_empty(S)) 
	//@loop_invariant !stack_empty(T);
	{
		push(T, pop(S));
	}
	//@assert stack_empty(S);
	string result = pop(T);
	push(S, result);
	while (!stack_empty(T)) {
		push(S, pop(T));
	}
	return result;
}
```


