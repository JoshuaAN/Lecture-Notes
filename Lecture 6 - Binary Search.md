# Binary Search
Binary search works by repeatedly dividing in half the portion of the list that could contain the item and comparing the middle value to the value you're looking for, until you've narrowed down the possible locations to just one. This runs in O(log$\;n$)
## Process
1. Look in the middle!
2. Compare the midpoint element with x 
3. If found, great, return the index 
4. If x is smaller, look for x in the lower half
5. If x is bigger, look for x in the upper half 
![[Screenshot 2024-02-06 at 9.51.38 AM.jpg || 350]]
## Why is it better?
We are throwing out half of the array each time whereas with linear search we only throw out one element each time.

## Final Code for Binary Search
```C
int search(int x, int[] A, int n)
//@requires n == \length(A);
//@requires is_sorted(A, 0, n);
/*@ensures (\result == -1 && !is_in(x, A, 0, n))
	|| (0 <= \result && \result < n && A[\result] == x); @*/
{
	int lo = 0;
	int hi = n;
	while (lo < hi)
	//@loop_invariant 0 <= lo && lo <= hi && hi <= n;
	//@loop_invariant gt_seg(x, A, 0, lo); // implies !is_in(x, A,0,lo)
	//@loop_invariant lt_seg(x, A, hi, n); // implies !is_in(x, A,hi,n)
	{	
		int mid = lo + (hi - lo)/2;	
		//@assert lo <= mid && mid < hi;
		if (A[mid] == x) return mid;
		if (A[mid] < x) {
		//@assert mid + 1 <= hi;
		//@assert gt_seg(x, A, 0, mid+1); // implies !is_in(x, A,0,mid+1)
		lo = mid+1;
		} else { //@assert A[mid] > x;
		//@assert lt_seg(x, A, mid, n); // implies !is_in(x, A,mid,n)
		hi = mid;
		}
	}
	//@assert lo == hi;
	//@assert !is_in(x,A,0,n);
	return -1;
}
```

## Complexity of Binary Search
Given an array of size n, we halve the segment considered at each iteration and we can do this at most $log(n)$ times, each iteration also has constant cost so the complexity of binary search is $O(log(n))$