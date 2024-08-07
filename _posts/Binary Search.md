---
layout: post
title: Binary Search
date:   2024-25-07 19:51:29 +1000
categories: Data structures
--- 
The most important thing on BS is to think about __which we must choose btwn left and right subarray__.

1. [74. Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/), 25/Jul/24
Topics : #Array , #BinarySearch, #Matrix 
could solve it in 1h using hint(__convert 1D index to 2D index__)
Wow... Why I couldn't come up with that idea?! idiot....
After getting idea, I took 40mi to create `row` and `col`.
I used to solve matrix problem with `N*N` matrix. So I think I just memorize that `row = 1D idx / m` and `col = 1D idx / m` _without full understanding_.
We must divide _1D index_ by _column size_ not row size! 

```java
    public boolean searchMatrix(int[][] matrix, int target) {
        int m = matrix.length;
        int n = matrix[0].length;
        // convert 1D index to 2D index.
        int start = 0, end = m*n - 1;
        while (start <= end) {
            int mid = (start + end) / 2;
            int row = mid / n;
            int col = mid % n;

            if (matrix[row][col] == target) return true;
            if (matrix[row][col] < target) start = mid + 1;
            else end = mid - 1;
        } 
        return false;
    }
```

2. [162. Find Peak Element](https://leetcode.com/problems/find-peak-element/), 26/Jul/24
Topics : #Array , #BinarySearch 
could solve it in 37mi using hint...
Wow so hard!!
[My solution](https://leetcode.com/problems/find-peak-element/solutions/5537061/thinking-process-with-well-explanation)

Binary search has two aspects.
- A sorted array.
- Splitting an array into two halves.

In this problem, we don't have the first one, So only Think about the second point.
We can easily come up with _binary search_ bc problem suggest _'solve it in O(logn) time'_.
So we then just think about _mid_ index.

The possible ways is _only three_ because of the constaint that `nums[i] != nums[i + 1]` for all valid `i`

i ) `[mid-1] < [mid] > [mid+1]`
ii ) `[mid-1] < [mid] < [mid+1]`
iii ) `[mid-1] > [mid] > [mid+1]`

if _i)_ is case, then just return `mid`. The problem cases is _ii)_ and _iii)_. __What We Have To Choose? left? or right?__

We should focus on the constraints _that `nums[-1] = nums[n] = -∞`. In other words, an element is always considered to be strictly greater than a neighbor that is outside the array._

So if _ii)_ is the case, then we must choose  `[mid+1]` __to keep that constraint!__
`[mid] < [mid+1] ....... > [n]`. So we have same properties of initial array. That means we can find any peak element in right subarray!

```java
public int findPeakElement(int[] nums) {
        int start = 0, end = nums.length - 1;
        while (start <= end) {
            int mid = (start + end) / 2;
            if (((mid - 1) < 0 || nums[mid] > nums[mid - 1]) 
            && ((mid + 1) > (nums.length - 1) || nums[mid] > nums[mid + 1])) return mid;
            if ((mid - 1) < 0 || nums[mid] > nums[mid - 1]) start = mid + 1;
            else end = mid - 1;
        }
        return -1;
    }
```

3. [33. Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/), 26/Jul/24
Topics : #Array , #BinarySearch 
could solve it in 54mi.
My first idea is _phase 1) Find decreasing point(idx)_ ^7dd397

Phase 1) Find decreasing point(idx)
_Note that this pivot meaning is different from problem's._
if (nums[mid] < nums[mid - 1]) pivot = mid
if (nums[mid] < nums[0]) end = mid - 1;
else start = mid + 1;

Phase 2) Choose valid subarray based on pivot.
nums[start] ~ nums[pivot-1]
nums[pivot] ~ nums[end];

Phase 3) then binary search~
```java
    public int search(int[] nums, int target) {
        int n = nums.length;
        int start = 0, end = n - 1; 
        // phase 1) Find decreasing point(idx)
        // 6 7 0 1 2 4 5 -> pivot 2(decrasing idx)
        // Note that this pivot meaning is different from problem's.
        int pivot = 0;
        while (start <= end) {
            int mid = (start + end) / 2;
            if (mid > 0 && nums[mid] < nums[mid - 1]) pivot = mid;
            if (nums[mid] < nums[0]) end = mid - 1;
            else start = mid + 1;
        }

        // phase 2) Choose valid subarray based on pivot.
        // (nums[0] ~ nums[pivot-1]) OR (nums[pivot] ~ nums[n - 1])
        if (nums[pivot] <= target && nums[n - 1] >= target) {
            start = pivot;
            end = n - 1;
        }
        else {
            start = 0;
            end = pivot - 1;
        }
        // phase 3) Start binary search
        while (start <= end) {
            int mid = (start + end) / 2;
            if (nums[mid] == target) return mid;
            if (nums[mid] < target) start = mid + 1;
            else end = mid - 1;
        }
        return - 1;
    }
```

ii) Binary search considering two case
Just Think about which you choose left or right subarray.
```java
    public int search(int[] nums, int target) {
        int n = nums.length;
        int start = 0, end = n - 1; 
    
        while (start <= end) {
            int mid = (start + end) / 2;
            
            if (nums[mid] == target) return mid;

            if (nums[start] <= nums[mid]) {
                if (target < nums[mid] && nums[start] <= target) end = mid - 1;
                else start = mid + 1;
            }
            else {
                if (target > nums[mid] && target <= nums[end]) start = mid + 1;
                else end = mid - 1;
            }
        }
        return - 1;
    }
```

iii ) freaking hard solution
```java
public int search(int[] nums, int target) {
    int lo = 0, hi = nums.length - 1;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        
        int num = nums[mid];
        // If nums[mid] and target are "on the same side" of nums[0], we just take nums[mid].
        if ((nums[mid] < nums[0]) == (target < nums[0])) {
            num = nums[mid];
        } else {
            num = target < nums[0] ? Integer.MIN_VALUE : Integer.MAX_VALUE;
        }

        if (num < target)
            lo = mid + 1;
        else if (num > target)
            hi = mid - 1;
        else
            return mid;
    }
    return -1;
}
```
my comments about side meaning
[@0alexzhong0](https://leetcode.com/u/0alexzhong0) I think your understanding is wrong. here _side_ means side divided by turning point of array. If `nums` is [16, 17, 0, 1, 2, 3, 4, 5, 6, 7], only two sides exist, [16,17] and [0~7] in whole `while loop`. That's why he compare the `nums[mid]` to `nums[0]` not to `nums[start]`. if mid is 4 and target is 6, `2 < 16` and `6 < 16`. so they(mid and target) are on the same side(0,1,2,3,4,5,6,7). If they are both `false` Then it means they are on same side[16,17].

[@Ice-bear](https://leetcode.com/u/Ice-bear) Your change `(nums[mid] > nums[0]) == (target > nums[0])` can't ensure the same side when two conditions are false (if two condition are true then ensure the same side). If `nums` is [16, 0, 1], and mid idx is 1(value 0), and target value 16. your two conditions both are `false` but, target is on the side[16] and mid is on the side[0,1]. they are on the different side!

AM I GENIOUS? haha

4. [34. Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/), 27/Jul/24
Topics : #Array , #BinarySearch 
could solve it 36mi but not $O(logn)$ but $O(n)$.
So that is not good solution.

phase 1) find target.
if it is -1 then return [-1, -1]
else int pivot = mid

phase 2) based on pivot divide two subarray left and right.
and find target on left, find target on right.
I think we need recursion.
But this code runs $O(n)$ bc every recursion, they take left and right both.
```java
    public int[] searchRange(int[] nums, int target) {
        return binarySearch(nums, target, 0, nums.length - 1); 
    }

    private int[] binarySearch(int[] nums, int target, int start, int end) {
        while(start <= end) {
            int mid = (start + end) / 2;
            if (nums[mid] == target) {
                int[] left = binarySearch(nums, target, start, mid - 1);
                int[] right = binarySearch(nums, target, mid + 1, end);
                int startPos = 0, endPos = 0;
                if (left[0] == -1) startPos = mid;
                else startPos = left[0];

                if (right[0] == -1) endPos = mid;
                else endPos = right[1]; 
                return new int[] {startPos, endPos};
            }
            if (nums[mid] < target) start = mid + 1;
            else end = mid -1;
        }
        return new int[] {-1, -1};
    }
```

- Revised version for $O(logn)$ runtime
The main differences are 
> i ) To prevent worst case
> ii ) To prevent useless case
```java
    public int[] searchRange(int[] nums, int target) {
        if (nums.length == 0) return new int[] {-1, -1};
        return binarySearch(nums, target, 0, nums.length - 1); 
    }

    private int[] binarySearch(int[] nums, int target, int start, int end) {       // To prevent useless search.
        while(start <= end && nums[start] <= target && nums[end] >= target) {   // To prevent worst case.
            if (nums[start] == target && nums[end] == target) return new int[] {start, end}; 
            
            int mid = (start + end) / 2;
            if (nums[mid] == target) {
                int[] left = binarySearch(nums, target, start, mid - 1);
                int[] right = binarySearch(nums, target, mid + 1, end);
                int startPos = 0, endPos = 0;
                if (left[0] == -1) startPos = mid;
                else startPos = left[0];

                if (right[0] == -1) endPos = mid;
                else endPos = right[1]; 
                return new int[] {startPos, endPos};
            }
            if (nums[mid] < target) start = mid + 1;
            else end = mid -1;
        }
        return new int[] {-1, -1};
    }
```

- Divide leftmost and rightmost recursion
TC : $O(logn)$
my code is shit...
```java
    public int[] searchRange(int[] nums, int target) {
        int start = 0, end = nums.length - 1;
        
        while(start <= end) {
            int mid = (start + end) / 2;
            if (nums[mid] == target) {
                int left = findLeft(nums, target, start, mid - 1);
                int right = findRight(nums, target, mid + 1, end);
                if (left == -1) left = mid;
                if (right == -1) right = mid;

                return new int[] {left, right};
            }
            if (nums[mid] < target) start = mid + 1;
            else end = mid - 1;
        }
        return new int[] {-1, -1};
    }
    private int findLeft(int[] nums, int target, int start, int end) {
        while(start <= end) {
            int mid = (start + end) / 2;
            if (nums[mid] == target) {
                int left = findLeft(nums, target, start, mid - 1);
                if (left != -1 && left < mid) mid = left;
                return mid;
            }
            if (nums[mid] < target) start = mid + 1;
            else end = mid - 1;
        }
        return -1;
    }

    private int findRight(int[] nums, int target, int start, int end) {
        while(start <= end) {
            int mid = (start + end) / 2;
            if (nums[mid] == target) {
                int right = findRight(nums, target, mid + 1, end);
                if (right > mid) mid = right;
                return mid;
            }
            if (nums[mid] < target) start = mid + 1;
            else end = mid - 1;
        }
        return -1;
    }
```

Others code is good...
The main difference is when we find `nums[mid] == target` we didn't return `mid`. Just keep going.
```java
    public int[] searchRange(int[] nums, int target) {
        return new int[] {findLeft(nums, target), findRight(nums, target)};
    }
    private int findLeft(int[] nums, int target) {
        int start = 0, end = nums.length - 1;
        int res = -1;
        while(start <= end) {
            int mid = (start + end) / 2;
            if (nums[mid] == target) {
                res = mid;
                end = mid - 1;
            }
            if (nums[mid] < target) start = mid + 1;
            if (nums[mid] > target) end = mid - 1;
        }
        return res;
    }

    private int findRight(int[] nums, int target) {
        int start = 0, end = nums.length - 1;
        int res = -1;
        while(start <= end) {
            int mid = (start + end) / 2;
            if (nums[mid] == target) {
                res = mid;
                start = mid + 1;
            }
            if (nums[mid] < target) start = mid + 1;
            if (nums[mid] > target) end = mid - 1;
        }
        return res;
    }
```

- Other brilliant code using method that __find location(index) where we insert the target in array.__
```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        if (nums.length == 0) return new int[] {-1, -1};
        
        int left = binarySearch(nums, target - 0.5);
        int right = binarySearch(nums, target + 0.5);
        // if all elements is bigger than target, left and right will be 0.
        // if all ele is less than target then left and righy will be last index.
        // if there is no target in the `nums` array, left and right will has same position. 
        if (left == right) return new int[] {-1,-1};
        // if target is there, right must subracted by 1.
        // I know it is quite confusing...
        return new int[] {left, right - 1};
    }

    private int binarySearch(int[] nums, double target) {
        int start = 0, end = nums.length - 1;
        
        while (start <= end) {
            int mid = (start + end) / 2;
        
            if (nums[mid] < target) start = mid + 1;
            if (nums[mid] > target) end = mid - 1;
            // There is no possibility that `target == nums[mid]`
        }
        return start;
    }
}
```

5. [153. Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/), 29/Jul/24
Topics : #Array , #BinarySearch 
could solve it in 18 bc it is almost same with [[Binary Search#^7dd397 |  Search in rotated sorted array]]
I just considered _"Which subarray I have to choose?"_
```java
    public int findMin(int[] nums) {
        int start = 0, end = nums.length - 1;

        if (nums[start] < nums[end]) return nums[start];
        if (nums.length == 1) return nums[0];

        while (start < end) {
            int mid = (start + end) / 2;
            if (nums[mid] >= nums[0]) start = mid + 1;
            else end = mid;
        }     
        return nums[start];
    }
```

But... all others solve it in different way!
They use `nums[end]` as _criteria_. 
So they illuminate the exceptional case.
If using `nums[start]` as criteria, then we can't know where `mid` is on the side. So we can't decide which we have to choose.
```java
    public int findMin(int[] nums) {
        int start = 0, end = nums.length - 1;

        while (start < end) {
            int mid = (start + end) / 2;
            if (nums[mid] > nums[end]) start = mid + 1;
            else end = mid;
        }     
        return nums[end];
    }
```