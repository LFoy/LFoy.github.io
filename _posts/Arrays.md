---
layout: post
title:  Arrays
date:   2024-13-06 17:51:29 +1000
categories: Data structures
--- 

## Static Array
> is a fixed length container containing n elements **indexable** from the range [0, n-1].

- When and Where is a static Array used?
	1. Storing and accessing sequential data
	2. Temporarily storing objects
	3. Used by IO routines as buffers
	4. Lookup tables and inverse lookup tables
	5. used to return multiple values from a function
	6. used in dynamic programming to cache answers to subproblems

## Dynamic Array
> can be **grow** and **shrink** in size.


## Kadane's Algorithm to Maximum Sum Subarray Problem.

^d02bcb

> The Key point is only focus on positive sum(+)!

1. Brute Force : ==Every subarray generation==
	```
	maximum = INT_MINIMUM
	
	for(i=0; i<n; i++)
	{
		for(j=i; j<n; j++)
		{
			sum = 0
			// every subarray generation
			for(k = i until j)
			{
				sum += array[k]
			}
			maximum = max(sum, maximum)
		}
	}
	```
	TC : $O(n^3)$
	SC : $O(1)$

2. Better solution
	```
	maximum = INT_MINIMUM
	
	for(i=0; i<n; i++)
	{
		sum = 0
		
		for(j=i; j<n; j++)
		{
			sum += array[j]
			maximum = max(sum, maximum)
		}
	}
	```
	TC : $O(n^2)$
	SC : $O(1)$

3. Optimal solution (Kandane's algorithm)
	==The Key point is only focus on positive sum(+)!==
[[Arrays#^a99fe3 | codes]]
	Assuming `[-2, -3, 4, -1, -2, 1, 5, -3]`
	if `sum < 0`, just ignore! it is not helpful to get maximum subarray sum.
	max : 4 -> 7
	`[4, - 1]` -> still positive sum! but sum is less than previous max. so don't change it.
	`[4, -1, -2]` -> positive
	`[4, -1, -2, 1]` -> +
	`[4, -1, -2, 1, 5]` -> positive and sum is greater than previous max(4)! so change it!  

	TC : $O(n)$
	SC : $O(1)$

## $O(nlogn)$ Sorting
### Merge sort
> Divide and Conquer strategy
최대한 쪼갠 뒤, 정렬하고 합치고 정렬하고 합치고

[[DP vs Greedy vs Divide and Conquer#^0c80f8 | merge sort with constant space]]

### Quick Sort & Quick Selection
> also Divide and Conquer strategy
> Constant space! 

pivot 자리 찾아주고 피봇 위치 기준으로 쪼개고, 또 pivot 위치 찾아주고 또 쪼개고. 그러니까 merge와 반대로 다 쪼갠다음 정렬하는게 아니라 정렬하고 쪼개고 정렬하고 쪼개고.

We need _Three pointers_.
pivot : first element of array.
p = second element
q = last element

p pointer move one by one to the righthand side. If _p value_ is _greater_ than the _pivot_ element, then we move _q pointer_ to the _lefthand_ side until q value is less or equal than pivot element.
if _p and q pointer stoped_, then _swap_ two value.
While processing, if _p and q cross each other_, then _Swap_ the _pivot_ element with _q_ element.
So we can decide _what is correct position of pivot_. And we can divide the left and right! So we can recursion left and right _repectively_!

Time complexity
- Worst case : $T(n) = T(n-1) + n$ meaning $O(n^2)$ if array already sorted(ascending or descending).
- Average case : $O(nlogn)$

To prevent worst case, we can choose the middle index as a pivot instead of first index.

#### Quick selection
^9770ea
Instead of recursing into _both sides_ as in Quicksort, quickselect only recurs into _one side_.
The main idea is from that **our pivot at any given partition will always end up at the correct index**.

- If our pivot is at our "kth largest" index, return the number at that index.
- If our pivot comes before the "kth largest" index, perform quickselect on the right partition.
- If our pivot comes after the "kth largest" index, perform quickselect on the left partition.


[[Priority Queues & Heaps#^009d2f | leetcode problem]]
- Find the maximum element
- Find the minimum element
- Find the median element
- Find the `k`th smallest element

A possible approach is to sort first and then take the correct one.

If you sorting using quick sort, and the pivot is
- less than the element you are looking for, then ignore the partition of smaller elements
- larget than the element you are looking for, then ignore the partition of bigger elements
- the element you are looking for, then Stop looking!


```pseudocode
Partition (start, end):
	pivot = A[start];
	i = start, j = end;

	while(i < j):
		Do{
			i++;
		}while(A[i] <= pivot);
	
		DO{
			j--;
		}while(A[j] > pivot);
	
		if(i < j) swap(A[i], A[j]);

	swap(A[start], A[j]);
	return j;
```
i ) Using Recursion
```pseudocode 
QUICKSELECT(A[], start, end, rank):
	if(start == end) return A[start]

	pivotIndex = PARTITION(A, start, end)
	
	if (pivotIndex == rank) return A[rank]
	if(pivotIndex < rank) return QUICKSELECT(A, pivotIndex+1, end, rank)
	else return QUICKSELECT(A, start, pivotIndex-1, rank) 
```

ii ) Using Iteratively
```pseudocode
QUICKSELECT(A[], start, end, rank):
	while (start <= end):
		if(start == end) return A[start]
		pivotIndex = PARTITION(A, start, end)
		if(pivotIndex == rank) return A[rank]
		if(pivotIndex < rank) start = pivotIndex+1
		else end = pivotIndex-1
```


Pivot plays as a divider of left and right

## Linear Time Sorting
- Counting Sort, Radix Sort, and Bucket Sort
- __Stable sort__
 >if for two object with the same value, they don't swap order. (keeps the _same order_ between any element with _equal values_!)
 >Stable sort is not required for all sorting algorithms, but it is preferred
 
### Counting Sort
- Input must be a _small range of k, `0 to k-1`_
- Value must be _Integer(or Object with Integer)_ and _cannot be minus(-)_ cuz have to use index as an element.
- Time Complexity = $O(n)$
- Space Complexity = $O(n + k)$ cuz need _new sorted array and counter array_
- 

Steps 
1) _New_ Array needed to _count_ how many of each elements.
2) Loop through original array, incrementing appropriate counter for each
3) Loop through counter array, adding previous value, to get summation of counts.
4) Loop through original in _reverse order_.
Loop in reverse order keeps this sort is ==stable==!
For each value(element) :
	- using a value as a index, decrement count array value
	- using decremented value as index to copy an element in new sorted array.
>last value of original is 0. 0 index have 3 value in counter array. so decrement by 1. and using 2 as an index, copy that value(0) into new sorted array!
>[ \ \ 0 \ \ \ \ \ ]
>Then Counter array become [2 4 4 7 9 10]
5) if want to loop fornt to back and keep stable sort, then, change counter array(holding first position of objects(values) not last) and incrementing counters after use them. 

- original : `[1 3 4 5 3 0 3 4 0 0]`
- new counter array : `[3 1 0 3 2 1]` index represents each element. value means the count of each element.
- new sorted array : 

after Step 3.

- counter array : `[3 4 4 7 9 10]`
it means that ==index 3(value 3) will be located in indices 4, 5, 6== 


### Radix(기수, 진법체계에 기준이 되는 수) sort
- Run __Counting sort__ on __each digit(자릿수)__ of the input separately!
- can ==extend the input range bigger== than Counting sort practically!
> if input is 0~999. each digit just goes from 0 to 9. so counting sort works great. just counter array have 9 index!
- Two version : Most Significant Digit or Least Significant Digit
- ==Can use any radix(base) : 10, 2, k, characters, etc.==
- TC  :  $O(nd)$, For d(the number of counting sort = the number of digits) passes
- Can even use base n(large enough!)
- But still has limited integers or strings, so it's still not as general a sort as quick sort!
- still uses extra space compare to quick sort cuz using counting sort, so still bad for large enough inputs !

#### Most Significant Digit Steps
- using __sub-problem and recursion__ 
1. Sort by most significant digit(100의 자리수) with counting sort
2. Pad front with 0's if it has fewer digits.
3. Then we have _blocks_ sorted by their digit(100의 자리수).
4. And Sort by next digit(10의 자리수) with counting sort those blocks ==independently==. 
5. Like this, Recursively sort each value's __block__ by next most sigificant digit
>Optimizaiton : once recursive blocks get small enough, use insertion sort instead of counting sort!

Works best for _lexicographical(generalization of alphabeticall or dictionary orders for String) ordering_
- Can sort by characters instead of digits
- No padding needed to make strings equal lengths
- Related to radix trees and tries

#### Least Significant Digit steps
- _don't worry about separating blocks for subproblems_

1. Sort list by Least Significant digit with (stable) counting sort
2. Sort _entire_ list agian by next least significant digit
3. Repeat on increasingly significant digits until you run out of digits!
>Stability : equal keys will be sorted by less significant digit(previously sorted).

### Bucket sort
- Useful when data is randomly chosen from a uniform distribution like _real numbers_ not integers.
-  Using _Linked Lists_ to store the elements in each bucket.
- TC : $O(n)$
- SC : $O(n)$ cuz making linked lists

Steps
1. To sort n values, break range into n different equal sized parts, "Buckets"
2. Use value to compute index of its bucket in constant time.
3. Append that value onto end of that(a bucket) linked list in constant time.
> As n grows large, we expect approximately.
> ==Over 99.5% of items in buckets with 5 or fewer items!==
4. Sort each bucket with insertion sort
> When finishing 3.(putting stuff in buckets), they are unsorted but everything in one bucket goes before next bucket.
> so can sort the buckets _independently_ with _insertion sort_(quadratic but efficient for sorting small sets).

Another option : To combine the lists of buckets without sorting each one individually, and then run insertion sort on the whole list! Because it is almost sorted, so insertion sort will finish the job quickly. 
	

### Bucket Sort on Counting Sort Input
- ==One bucket per Value!==
- Items might be distributed unevenly (한 버킷에 쏠려 있을 수도 있음.) cuz For counting sort, the values are in some fixed range of integers, but doesn't make any assumptions about distribution like bucket sort does! 
- ==No need to sort each bucket, all values for a bucket have the same Value!==
- $O(n)$ even for the worst case for $O(n)range$

# Leetcode Problems

1. Remove Duplicates from Sorted Array II, 14/Jun/2024
	Topic : Array, Two Pointers
	couldn't solve this within 30mi.
	[My solution](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/solutions/5315727/i-hope-this-can-help-you-like-me)
	Other way : First, I came up with using the index, but it couldn't cuz nums[i] can be minus. So I should've come up with using the dictionary in python like this.
	```python
	class Solution:
	    def removeDuplicates(self, nums: List[int]) -> int:
	        index = 0
	        dict_a = {}
	
	        for i in range(len(nums)):
	            dict_a[nums[i]] = dict_a.get(nums[i], 0) + 1
	            if dict_a[nums[i]] <= 2:
	                nums[index] = nums[i]
	                index += 1
	        return index
	```

2. Rotate Array, 16/Jun/24
	Topic : Array, Math, Two Pointers
	couldn't solve this within 40mi.
	I came up with using modular, stack, queue like pop and addFirst. But it is impossible cuz in java int[] is fixed length list. 
	And the space complexity have to be $O(1)$.
	[My solution](https://leetcode.com/problems/rotate-array/solutions/5322741/2-approach-is-possble-with-well-explanation)
	
	__2 approach is possible.__
	
	1. Using cyclic Replacements.
		The method I first try inspired by Hint 4...
		_When confusing with so many valuables, just write in_
		_have to keep track of_
		- start index
		- overwritten index(right position after k rotate).
		- overwritten number.
		- rotate count
		
		```java
		class Solution {
		
			public void rotate(int[] nums, int k) {
				// starting Index of a cycle
				int start = 0;
				// count to move a element until nums.length
				int cnt = 0;
				// become beforeIndex, after a loop
				int afterIndex = 0;
				// become overwriting Number, after a loop
				int overwrittenNum = 0;
				// To store overwittenNum temporaly
				int temp = 0;
			
				while (cnt < nums.length) {
					afterIndex = start;
					overwrittenNum = nums[start];
			
					// start cyclic replacements.
					while (true) {
						// find right position after k rotate.
						afterIndex = (afterIndex + k) % nums.length;
						temp = nums[afterIndex];
			
						// rotate a element
						nums[afterIndex] = overwrittenNum;
						overwrittenNum = temp;
						cnt++;
			
						// To keep a cycle
						if( start == afterIndex) {
							start++;
							break;
						}
					}
				}
			}
		}
		```

	2. Using Reverse. 
		This method is really good to rotate! I have to remember it.
		
		Before: `[1, 2, 3, 4, 5, 6, 7]`, k = 3
		After: `[5, 6, 7, 1, 2, 3, 4]`

		This is equivalent to picking the last 3 elements and moving them to the front of the array. The remaining elements are shifted backwards.
		
		**Handling k greater than nums.length:**
		
		We can use the modulo operator (%)! If the array has a length of 7 and k is 5, the result is the same as when k is 12 (`7 % 5 = 2`, and `12 % 5 = 2`). So, we can calculate `k % nums.length` to ensure k is always within the array bounds.
		
		**Dividing the Array:**
		
		Now that k is within the bounds, we can divide the array into two parts: the first `nums.length - k` elements and the last `k` elements.
		
		**Moving the Last Element to the Front:**
		
		Our goal is to move the last element (in this case, element 4) to the end of the entire array, with element 5 becoming the new first element.
		
		**Solution:**
		
		We achieve this by reversing the two parts separately:
		
		1. Reverse the first part: `[4, 3, 2, 1]`
		2. Reverse the second part: `[7, 6, 5]`
		
		**Final Step:**
		
		Finally, we reverse the entire array to get the desired result: `[5, 6, 7, 1, 2, 3, 4]`.
		
		This approach ensures that element 4 is now at the end of the array, and element 5 is at the beginning.

		```java
		class Solution {
		
		    public void reverse(int[] nums, int start, int end) {
		        while (start < end) {
		            int temp = nums[end];
		            nums[end] = nums[start];
		            nums[start] = temp;
		            start++;
		            end--;
		        } 
		    }
		
		    public void rotate(int[] nums, int k) {
		        // Ensure the k is within array bound.  // Optional period added
		        k = k % nums.length;
		
		        // Divide nums to two parts with nums[:(nums.length - k)] and nums[(nums.length - k):]
		        // Reverse two parts respectively.
		        reverse(nums, 0, (nums.length - k - 1));
		        reverse(nums, (nums.length - k), (nums.length - 1));
		        // Reverse whole nums.
		        reverse(nums, 0, (nums.length - 1));
		    }
		}
		
		```


3. [Jump Game](https://leetcode.com/problems/jump-game/) 17/Jun/24
	Topic : Array, Greedy
	couldn't solve this within 40mi.
	[My solution](https://leetcode.com/problems/jump-game/solutions/5327615/two-approaches-start-from-the-frond-or-back-with-good-explanation)

	**This problem looks so easy... why medium? Any trick here?!**

	Oh, it means maximum jump, not a fixed jump length!
	
	**Not greedy, then DP?**
	
	Okay, let's think about all possible situations.
	
	**Almost everyone thinks this problem can be solved with DP, including me! But if using DP, the time complexity (TC) is very bad.**
	
	If `nums[i]` is 2, then `nums[i + 1]`, `nums[i + 2]`, ... (many possibilities). This could lead to a time complexity of O(n^m), where n is the length of the `nums` array and m is the maximum jump value. This would exceed the time limit for large inputs. Additionally, using visited sets for overlapping subproblems would also negatively impact TC.
	
	**1. Greedy + Kadane's Algorithm**
	
	For every index, check whether the current index can be reached by the previous jump. To determine this, we need to keep track of the maximum possible jump reached so far (`maxReach`). Then, we compare `maxReach` to the current index (`curr`).
	
	- **Key Point:** We only focus on the current index, not the last index. This avoids unnecessary computations.
	- **Return Condition:** If `maxReach` is less than `curr`, it means the current index is unreachable, so we can return `false`.
	
	The goal is to return `true`, indicating that all indices in the `nums` array can be reached (are reachable).
	
	```java
	int maxReach = nums[0];
	
	for (int curr = 1; curr < nums.length; curr++) {
	    // Check if the current index can be reached by the previous jump
	    // Assuming [0, 1, 5], at first index, will return false haha.
	    if (maxReach < curr) return false;
	
	    // Update maxReach
	    maxReach = Math.max(maxReach, curr + nums[curr]);
	}
	
	return true;
	```
	
	**2. Greedy + Recursion + Start from Last**
	
	We can check if it's possible to jump from the current index (`curr`) to the target index (last index) using recursion.
	
	- If `curr + nums[curr] >= target` (can reach the target), return `true`. Additionally, set `curr` as the new target for further recursion.
	
	This approach focuses on reaching the target from the end, greedily checking if each index can be reached from the previous one.
	
	```java
	int target = nums.length - 1;
	
	for (int curr = nums.length - 2; curr >= 0; curr--) {
	    // If the current index cannot reach the target
	    // -> means cannot jump at curr index to target
	    // so target is still target
	    // then check (curr--) + nums[curr--] >= target!
	    if (curr + nums[curr] < target) {
	        // Skip to the previous index (no need to check further)
	        curr--;
	    } else {
	        // Update target to the current index
	        target = curr;
	    }
	}
	
	return target == 0; // True if we can reach the first index (0)
	```


4. [H-Index](https://leetcode.com/problems/h-index/),18/Jun/24
Topic : Array, Sorting, Counting Sort
solved it in 18mi.
but my codes is not good. cuz constraints is small, like 5000 and 1000. so $O(5 * 10^6)$ can pass.

```java
public int hIndex(int[] citations) {
    int totalPaper = 0;    // Keeps track of the number of papers cited at least hIndex times
    int hIndex = 1000;     // Initialize hIndex with a high starting value

    while (hIndex >= 0) {   // Loop until hIndex reaches -1(worst is 0) or a valid value

        totalPaper = 0;      
        for (int citation : citations) {
            if(hIndex <= citation) totalPaper++;  // Count papers cited at least hIndex times
        }

        if(totalPaper >= hIndex) break;  // If totalPaper meets the hIndex criteria, break the loop
        else hIndex--;                   
    }

    return hIndex;
}

```

For TC is $O(n)$ or $O(nlogn)$
> What are the possible maximum value of h-index?
> It's `citations.length`(the number of paper published by a researcher!)  not 1000(max of citation).

1. Sort the array first.
- TC : $O(nlogn)$ if just using like java sort, not counting or radix or bucket. 
- SC : $O(1)$

집가서 코드 내가 짜보기

2. Count with extra space. 
- TC : $O(n)$
- SC : $O(n)$ or $O(Maximum of citations[i])$

집가서 코드 내가 짜보기



5. [238. Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/) 19/Jun/24
Topic : Array, Prefix Sum
solved it in 2 hours... with hints(Topic).
TC : $O(n)$
SC : $O(1)$ // cuz The output array **does not** count as extra space for space complexity analysis.
Most people doesn't start their for loop `i=1 or i = len -2` just start `0 and len - 1` 
And they use just 1 temp valuable for storing left product and right product. just differ in order of pre or suffix product first or not. 

Others
```java
public int[] productExceptSelf(int[] nums) {
    int[] result = new int[nums.length];
    for (int i = 0, tmp = 1; i < nums.length; i++) {
        result[i] = tmp;
        tmp *= nums[i];
    }
    for (int i = nums.length - 1, tmp = 1; i >= 0; i--) {
        result[i] *= tmp;
        tmp *= nums[i];
    }
    return result;
}
```


My solution
```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int len = nums.length;
        int[] answer = new int[len];
        int sufProduct = 1;

        // Initialize answer array with 1s
        Arrays.fill(answer, 1);

        // Store left product except for nums[i] in answer array.
        for (int i = 1; i < len; i++) {
            answer[i] = answer[i - 1] * nums[i - 1];
        }

        // Calculate right product and multiply with left product for each element
        //already answer[last Index] is over cuz left product in answer array.
		//so start (len -2)
        for (int i = len - 2; i >= 0; i--) {
            sufProduct *= nums[i + 1];
            answer[i] *= sufProduct;
        }

        return answer;
    }
}
```

6. [53. Maximum Subarray](https://leetcode.com/problems/maximum-subarray/), 22/Jul/24
Topics : #Array , #DividAndConquer , #DynamicProgramming 
could solve it in 19mi bc I already studied about _Kadane's algorithm_.
It is typical problem of that.
[[Arrays#^d02bcb | Kadane's algorithm]] ^a99fe3

### i ) Using Kadane's algo
```java
    public int maxSubArray(int[] nums) {
        int max = Integer.MIN_VALUE;
        int sum = 0;
        for (int num : nums) {
            sum += num;
            max = Math.max(max, sum);
            if (sum < 0) sum = 0;
        }    
        return max;
    }
```

### ii ) Using Divide and Conquer
wow so elegant... I couldn't come up with that idea within 1h...
TC : $O(nlogn)$ bc we divide that `logn` times, and we have to find max across left(start -> mid) and right(mid+ 1 -> end)
```java
public int maxSubArray(int[] nums) {

return divide(nums, 0, nums.length - 1);

}

private int divide(int nums[], int start, int end){

// base code

if (start == end) return nums[start];

int mid = (start + end) / 2;

int sum = 0, leftMid = -Integer.MAX_VALUE, rightMid = -Integer.MAX_VALUE;

  

// find max including across the left and right subarray.

for (int i = mid; i >= start; i--) {

sum += nums[i];

leftMid = Math.max(leftMid, sum);

}

sum = 0;

for (int i = mid + 1; i <= end; i++) {

sum += nums[i];

rightMid = Math.max(rightMid, sum);

}

// find left max and right max respectively.
// This part is so briliant! wow...
int leftMax = divide(nums, start, mid);

int rightMax = divide(nums, mid + 1, end);

// Compare three values

// leftMax, rightMax and max across btw left and right.

return Math.max(Math.max(leftMax, rightMax), leftMid + rightMid);

}
```

### iii) Using DP
dp[i] : the max subarray ending with nums[i].
```java
    public int maxSubArray(int[] nums) {
        //dp[i] : the max subarray ending with nums[i].
        int[] dp = new int[nums.length + 1];
        dp[0] = 0;
        int max = Integer.MIN_VALUE;

        for(int i = 1; i < dp.length; i++) {
            dp[i] = Math.max(dp[i - 1] + nums[i - 1], nums[i - 1]);
            max = Math.max(dp[i], max);
        }
        return max;
    }
```
The TC is $O(n)$ but SC is $O(n)$
__Actually Kandane's algorithm is base on DP.__
_We don't need to store all dp. So we just use only two variables_, `maxEndingHere`(`sum`, it means same thing with `dp[i] = Math.max(dp[i - 1] + nums[i - 1], nums[i - 1]);` above dp codes)
and maxSoFar(max, max = Math.max(dp[i], max);)
```java
public static int maxSubArray(int[] A) {
    int maxSoFar=A[0], maxEndingHere=A[0];
    for (int i=1;i<A.length;++i){
    	maxEndingHere= Math.max(maxEndingHere+A[i],A[i]);
    	maxSoFar=Math.max(maxSoFar, maxEndingHere);	
    }
    return maxSoFar;
}
```

7. [918. Maximum Sum Circular Subarray](https://leetcode.com/problems/maximum-sum-circular-subarray/), 25/Jul/24
Topics : #Array , #DividAndConquer , #DynamicProgramming , #Queue, #MonotonicQueue
could solve in 1h using a lot hints... So freaking hard... wow...

The main Idea of both methods is _to divide cases into normal case and circular case_

### i ) Finding Min subarray using kadane's algo
```java
    public int maxSubarraySumCircular(int[] nums) {
        int n = nums.length;
        int maxSum = 0, minSum = 0;
        int max = Integer.MIN_VALUE, min = Integer.MAX_VALUE;
        int totalSum = 0;

        for (int num : nums) {
            totalSum += num; 
            maxSum = Math.max(maxSum + num, num);
            minSum = Math.min(minSum + num, num); 
            max = Math.max(max, maxSum);
            min = Math.min(min, minSum);
        }
        // case when all the elements are negative
        if (totalSum == min) return max;
	    // `max` means normal case
	    // (totalSum - min) means circular case
        return Math.max(max, totalSum - min);
    }
```

### ii) Prefix and suffix
For circular case, we can use the prefix and suffix.

- Prefix
>subarray that _starts at the first element_ of the array
- Suffix
>subarray that _ends at the final element_ of array

In circular case, we have to find _maximum of `prefix + suffix` without overlapping_.
Prefix can be calculated _easily while kadane's algorithm_.
Then we calualate `max = max(max, prefix[i] + suffix[i+1])`, but what we need is the maximum of suffix after `i` index to find maximum of sum!, not real `suffix[i + 1]`. Think about if `suffix[i+1] < suffix[i+2]`, we have to choose `suffix[i+2]` for `max` of `prefix[i] + suffix[i+1]`. So we have to find `maxSuffix` until that `i` idx, not real suffix.  

```java
    public int maxSubarraySumCircular(int[] nums) {
        int n = nums.length;
        int suffix = nums[n - 1];
        int[] maxSuffix = new int[n];
        maxSuffix[n - 1] = nums[n - 1];
        

        for (int i = n - 2; i >= 0; i--) {
            suffix += nums[i];
            maxSuffix[i] = Math.max(suffix, maxSuffix[i+1]);
        }

        int sum = 0;
        int max = Integer.MIN_VALUE;
        int prefix = 0;

        for (int i = 0; i < n; i++) {
            sum = Math.max(sum + nums[i], nums[i]);
            max = Math.max(max, sum);

            prefix += nums[i];
            if (i != n - 1) max = Math.max(max, prefix + maxSuffix[i + 1]);
        }
        return max;
    }
```
