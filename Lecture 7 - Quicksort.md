# Quicksort
## Algorithm
The quicksort algorithm works as follows
- If the array has one or less elements we are done.
- Pick an arbitrary element as the 'pivot'.
- Divide array into two segments with one such that all elements are less than the pivot, and the other such that all elements are larger than the pivot.
- Repeat the process recursively on both segments.
---
### Example
Take the example array:

```
[3, 1, 4, 4, 7, 2, 8]
```

We can choose $3$ to be the pivot element. A partition would look like:

```
[1, 2], 3, [4, 4, 7, 8]
```

Where we would repeat the process on the array subsegments:

```
[1, 2], [4, 4, 7, 8]
```
---
### Complexity
The complexity of this algorithm is based on the number of recursive steps. In the worst case we would always picking the largest or smallest element in the array like so:

```
[3, 1, 4, 4, 8, 2, 7]
1, [3, 4, 4, 8, 2, 7]
1, 2, [3, 4, 4, 8, 7]
1, 2, 3, [4, 4, 8, 7]
1, 2, 3, 4, [4, 8, 7]
1, 2, 3, 4, 4, [8, 7]
1, 2, 3, 4, 4, 8, [8]
```

Note we had $7$ elements and $6$ recursive steps. 

In the worst case $n-1$ recursive calls for an array of size $n$. The $k$-th recursive call has to sort a subarray of size $n-k$ which requires $O(n-k)$ comparisons. The total number of comparisons in the worst case is defined by the summation:
$$\begin{align*}
c\sum\limits^{n-1}_{k=0}k=c\frac{n(n-1)}{2}\in O(n^2)
\end{align*}$$
Quicksort has $O(n^2)$ time complexity in the worst case, how can we do better?

If we pick the median element when partitioning the array both segments would have half the elements as the original array, yielding $\log n$ recursive calls and an asymptotic complexity of $O(n \log n)$. 

It can be expensive to compute the median element though, so we instead choose a random element in the array. This produces the same asymptotic complexity of $O(n\log n)$. It's possible we could randomly always choose the worst pivots, but this is highly unlikely so we will still get $O(n\log n)$ complexity on average.
- - - 
## Implementation
### Partition
To partition the array we can use the following implementation:

```c
int partition(int[] A, int lo, int pi, int hi)
//@requires 0 <= lo && lo <= pi; 
//@requires pi < hi && hi <= \length(A); 
//@ensures lo <= \result && \result < hi; 
//@ensures ge_seg(A[\result], A, lo, \result); 
//@ensures le_seg(A[\result], A, \result+1, hi);
{
	// Store the pivot element in the left.
	int pivot = A[pi];
	swap(A, lo, pi);

	int left = lo + 1;
	int right = hi;

	while (left < right)
	//@loop_invariant lo+1 <= left && left <= right && right <= hi;
	//@loop_invariant ge_seg(pivot, A, lo + 1, left);
	//@loop_invariant le_seg(pivot, A, right, hi);
	{
		if (A[left] <= pivot) {
			left++;
		} else {
			right--;
			swap(A, left, right);
		}
	}
	//@assert left == right;

	// Finally swap pivot back into place
	swap(A, lo, left - 1);
	return left - 1
}
```
---
### Sort
To implement quicksort we can use the following implementation

```c
void sort(A, lo, hi)
//@requires 0 <= lo && lo <= hi && hi <= \length(A);
//@ensures is_sorted(A, lo, hi);
{
	if (hi - lo <= 1) return;
	int pi = lo + (hi - lo) / 2; // Pivot selection should be random
	
	int mid = partition(A, lo, pi, hi);
	sort(A, lo, mid);
	sort(A, mid + 1, hi);
}
```
---
# Mergesort
## Algorithm
The mergesort algorithm works as follows
- If the array has one or less elements we are done.
- Divide array in half with two segments. 
- Recursively sort both segments with mergesort.
- Merge the two sorted segments together.
---
### Example
Take the example array:

```
[3, 1, 4, 4, 7, 2, 8]
```

We split it in half with two segments:

```
[3, 1, 4, 4], [7, 2, 8]
```

We recursively sort both segments (the full process won't be shown for the sake of brevity):

```
[1, 3, 4, 4], [2, 7, 8]
```

The two arrays are merged like so:

```
	First           Second                 Result 
[1, 3, 4, 4],     [2, 7, 8],      []
[3, 4, 4],        [2, 7, 8],      [1]
[3, 4, 4],        [7, 8],         [1, 2]
[4, 4],           [7, 8],         [1, 2, 3]
[4],              [7, 8],         [1, 2, 3, 4]
[],               [7, 8],         [1, 2, 3, 4, 4]
[],               [],             [1, 2, 3, 4, 4, 7, 8]

```
---
### Complexity
The complexity of this algorithm will depend on the number of recursive steps and the implementation of the merging function.

The asymptotic complexity of merging two arrays is $O(n+m)$ where $n,m$ are the number of elements in each array respectively. The $k$-th recursive step has to merge two arrays of size $\frac{n}{2^{k+1}}$ a total of $2^{k}$ times. The full asymptotic complexity for any recursive step is then:
$$c\cdot\left(\frac{n}{2^{k+1}}+\frac{n}{2^{k+1}}\right)\cdot 2^{k}=c\cdot n\in O(n)$$
The size of each segment decreases by $\frac{1}{2}$ at each recursive step, so there will be $\log n$ recursive steps.

This yields a total asymptotic time complexity of $O(n\log n)$.
- - -
## Implementation
### Merge
To merge two sorted arrays we can use the following implementation:

```c
void merge(int[] A, int lo, int mid, int hi)
//@requires 0 <= lo && lo <= mid && mid <= hi && hi <= \length(A);
//@requires is_sorted(A, lo, mid) && is_sorted(A, mid, hi);
//@ensures is_sorted(A, lo, hi);
{
	int[] B = alloc_array(int, hi-lo);
	int i = lo;
	int j = mid;
	int k = 0;

	while (i < mid && j < hi)
	//@loop_invariant lo <= i && i <= mid;
	//@loop_invariant mid <= j && j <= hi;
	//@loop_invariant k == (i - lo) + (j - mid);
	{
	    if (A[i] <= A[j]) {
		    B[k] = A[i];
		    i++;
	    } else { //@assert A[i] > A[j];
			B[k] = A[j];
			j++;
	    }
	    k++;
    }

	//@assert i == mid || j == hi;
	while (i < mid) 
	//@loop_invariant lo <= i && i <= mid;
	{ 
		B[k] = A[i]; 
		i++; 
		k++; 
	}
	while (j < hi)  
	//@loop_invariant mid <= j && j <= hi;
	{
		B[k] = A[j]; 
		j++; 
		k++; 
	}
	
	// Copy sorted array back into A
	for (k = 0; k < hi-lo; k++)
	A[lo+k] = B[k];
}
```
### Sort
To implement mergesort we can use the following implementation:

```c
void sort (int[] A, int lo, int hi) 
//@requires 0 <= lo && lo <= hi && hi <= \length(A); 
//@ensures is_sorted(A, lo, hi); 
{ 
	if (hi-lo <= 1) return; 
	int mid = lo + (hi-lo)/2; 
	
	sort(A, lo, mid); //@assert is_sorted(A, lo, mid);
	sort(A, mid, hi); //@assert is_sorted(A, mid, hi); 
	merge(A, lo, mid, hi); 
	return; 
}
```
# Stability
A sort is said to be **stable** if the sorting algorithm doesn't change the relative position of elements which are seen as equivalent by the sorting algorithm. For example given the following table

| x | y |
| :--: | :--: |
| 2 | 1 |
| 5 | 2 |
| 2 | 4 |
| 1 | 5 |
| 4 | 6 |
we want to sort by values of $x$:

| x | y |
| :--: | :--: |
| 1 | 5 |
| 2 | 1 |
| 2 | 4 |
| 4 | 6 |
| 5 | 2 |
Note how for the $2$ element in the $x$ column, the tiebreaker was the original relative position of them (aka the $y$ column assuming it was originally sorted). An example of an *unstable* sort would be:

| x | y |
| :--: | :--: |
| 1 | 5 |
| 2 | 4 |
| 2 | 1 |
| 4 | 6 |
| 5 | 2 |
which swapped the relative position of the two $2$ elements.

How do our various sorting algorithms stack up?
- Selection sort is in-place, slow, and not stable
- Quicksort is in-place, fast, and not stable
- Merge sort is not in-place, fast, and stable
