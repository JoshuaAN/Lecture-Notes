# Linked Lists
We define a **linked list** as:

```c
typedef struct list_node list;

struct list_node {
	int data;
	list* next;
}
```

To construct a linked list node we would allocate memory for it:

```c
list* L = alloc(list);
```

Adding more nodes would look like:

```c
list* L = alloc(list);
L-> data = 3;
L-> next = alloc(list);
L->next->data = 7;
L->next->next = alloc(list);
list->next->next->data = 2;
```

The last node in the linked list has it's `next` field be a null pointer.
- - -
# Implementing Queues

# Implementing Stacks

# Sharing
