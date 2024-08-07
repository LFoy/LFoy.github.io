---
layout: post
title:  Priority Queues & Heaps
date:   2024-07-29 18:51:29 +1000
categories: LeetCode
--- 
## Priority Queue
A priority queue is an _Abstract Data Type(ADT)_ that operates similar to a normal queue except that __each element has a certain priority__. The priority of the elements in the PQ determine the order in which elements are removed from the PQ.

>NOTE: PQ only supports _comparable data_, meaning the data inserted into the PQ must be able to be ordered in some way either from least to greatest or greatest to least. This is so that we are able to assign relative priorities to each element.

### Heap
A heap is a _tree_ based DS that satisfies the heap invariant (also called heap property): If `A` is a parent node of `B` then `A` is ordered with respect to `B` for all nodes `A`, `B` in the heap.
(Max Heap, Min Heap)

- Used in certain implementations of Dijkstra's Shortest Path algorithm.
- Anytime you need the dynamically fetch the 'next best' or 'next worst' element.
- Used in Huffman coding (which is often used for lossless data compression).
- _Best_ First Search algorithms such as A* use PQs to continuously grab the next most promising node.
- Used by Minimum Spanning Tree (MST) algorithms.

### Complexity PQ with binary heap
Binary Heap construction | $O(n)$
---------|---------
Polling(Removing) | $O(log(n))$ Because we have to restore the heap invariant. (Bubble down)
Peeking | $O(1)$
Adding | $O(log(n))$ Because we have to restore the heap invariant. (Bubble up)
Naive Removing | $O(n)$ ex) Remove(12) we have to search it. The tree is not BST, so it takes linear time
Advanced removing with help from a hash table | O(log(n))
Naive contains | O(n)
Contains check with help of a hash table | O(1)

Time complexity of _Building heap_ can be easily thought by $O(nlogn)$ since each call to **Heapify** costs $O(lg(n))$ and **Build-Heap** makes $O(n)$ such calls.
_However_, the tight Big-O is $O(n)$... but really hard to understand.
Reference this. [link](https://www.geeksforgeeks.org/time-complexity-of-building-a-heap/)

### Adding Elements to Binary Heap (Bubble up)
We use _complete binary tree_(in which all nodes have two children except for leaf nod and adding from left to right) for heap.

For adding `1` to existing heap, just add it in leaf node(from left to right). Then compare it to the parent node value. If  heap is Min Heap, We have to keep the invariant(_all parent node value is smaller than children node value for that root node have smallest value_). So if added value is smaller than parent node value, then __SWAP__. Recursively do this until added value is bigger than parent node or it will be root node.

### Polling Elements to Binary Heap (Bubble down)
We use _complete binary tree_ for heap.
For polling, first we __swap__ _root node value with last node_(the rightmost node of leaf nodes). And then remove that node. And Let's start Bubble down! _Compare_ root node value to children, if smaller than root node value then choose smaller one, then _Swap_. Recursively do this until the value is smaller than children or reach to leaf!

Removing a certain value is also same! The only difference is _First find(search) that value on O(n) time_ then __swap the last node__. Same~ 

1. [215. Kth Largest Element in an Array](https://leetcode.com/problems/kth-largest-element-in-an-array/), 30/Jul/24
Topics : #Array , #DividAndConquer , #Sorting , #Heap, #PriorityQueue, #QuickSelect
[[2024-06-14-Arrays#^9770ea| Quick Selection]]
could solve it in 10mi but $O(nlogn)$ time. ^009d2f

### Using heap : $O(nlogn)$
```java
    public int findKthLargest(int[] nums, int k) {
        int n = nums.length;
        // Min heap
        PriorityQueue<Integer> heap = new PriorityQueue<>(n);
        for (int num : nums) heap.add(num);

        while (n - k != 0) {
            heap.poll();
            n--;
        }
        return heap.poll();
    }
```

Revised version : O(nlogk) time + O(k) space
```java
    public int findKthLargest(int[] nums, int k) {
        int n = nums.length;
        // Min heap
        PriorityQueue<Integer> heap = new PriorityQueue<>(n);
        for (int num : nums) {
            heap.offer(num);

            if (heap.size() > k) heap.poll();
        }
        return heap.peek();
    }
```

### Using QuickSelect 
i ) $O(n^2)$ worst case

`k = nums.length - k` is much better
```java
   public int findKthLargest(int[] nums, int k) {
        int start = 0, end = nums.length - 1;

        while (start < end) {
            int pivot = partition(nums, start, end);
            if (pivot == nums.length - k) break;
            
            if (pivot < nums.length - k) start = pivot + 1;
            else end = pivot - 1;
        }
        return nums[nums.length - k];
    }

    private int partition(int[] nums, int start, int end) {
        int pivot = nums[start];
        int p = start + 1;
        int q = end;

        while (true) {
            while(p <= q && pivot >= nums[p]) {
                p++;
            }
            while(p <= q && pivot < nums[q]) {
                q--;
            }
              
            if (p < q) swap(nums, p, q);
            else break;
        }
        swap(nums, start, q);
        return q;     
    }

    private void swap (int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
```

ii) $O(n)$ time
The worst case of quick select is the cast in which _whole array is already sorted_ in acsending or decsedning.
The answer is quite simple, we can __randomize__ the input before QuickSelect!

```java
private void shuffle(int[] arr) {

Random rand = new Random();

for (int i = 0; i < arr.length; i++) swap(arr, i, rand.nextInt(arr.length));

}
```

2. [373. Find K Pairs with Smallest Sums](https://leetcode.com/problems/find-k-pairs-with-smallest-sums/), 31/Jul/24
Topics : #Array , #Heap , #PriorityQueue 
couldn't solve it in 30mi. It was so hard even to come up with idea!
And java sorting... haha

Basic idea: Use min heap to keep track on next minimum pair sum, and we only need to _maintain __K__ possible candidates_ in the data structure.

TC : $O(klogk)$ since O(min(k, nums1.length) +  k(2log(k)))
For reducing time and space, __`Math.min(k, nums1.length)` must be!__
```java
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        List<List<Integer>> ans = new ArrayList<>();
        // store index pair
        PriorityQueue<int[]> heap = new PriorityQueue<int[]>((a, b) -> (nums1[a[0]]+nums2[a[1]]) - (nums1[b[0]]+nums2[b[1]]));

        // insert all candidate for nums1 element`s smallest sum.
        for (int i = 0; i < Math.min(k, nums1.length); i++) heap.offer(new int[] {i, 0});
        

        for (int i = 0; i < k; i++) {
            // poll a smallest sum. then add it to answer list.
            int[] curr = heap.poll();
            List<Integer> pair = new ArrayList<>();
            pair.add(nums1[curr[0]]);
            pair.add(nums2[curr[1]]);
            ans.add(pair);
            // insert a possible candidate.
            if (curr[1] + 1 < nums2.length) heap.offer(new int[] {curr[0], curr[1] + 1});
        }
        return ans;
    }
```

But this problem is the same as __how we merge k sorted list__

(1,2) -> (1,9) -> (1,10) -> (1,15)  
(7,2) -> (7,9) -> (7,10) -> (7,15)  
(11,2) -> (11,9) -> (11,10) -> (11,15)  
(16,2) -> (16,9) -> (16,10) -> (16,15

Remember how we do in "_merge k sorted list_"? We simply _add the head of the list into the heap_ and _when a node is poll(), we just add the node.next_.
[by comment](https://leetcode.com/problems/find-k-pairs-with-smallest-sums/solutions/84551/simple-java-o-klogk-solution-with-explanation/comments/232946)