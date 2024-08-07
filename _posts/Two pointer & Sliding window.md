
1. [167. Two Sum II - Input Array Is Sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/), 24/Jun/24
Topics : Array, Two Pointer, Binary Search
could solve it in 17mi.
It was typical type.

```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int left = 0;
        int right = numbers.length - 1;
        int[] indices = new int[2];

        while (left < right) {
            // To reduce two sum, decrease right index. 
            if (numbers[left] + numbers[right] > target) right--;
            // To increase two sum, increase left index
            else if (numbers[left] + numbers[right] < target) left++;
            else {
                indices[0] = left + 1;
                indices[1] = right + 1;
                break;
            }
        }

        return indices;      
    }  
}
```
But simplest code is `while (numbers[left]) + numbers[right] != targer)`. 
We don't need `while (left < right)` because they ensure only one solution always.

Its constraint is _SC must $O(1)$_
So Binary Search also works. But its TC is $O(nlongn)$. While for loop(from 0 to len - 1), set a valuable `another = target - numbers[i]`. then check there is another on the range(from i+1 to len - 1)!
So Binary Search occurs _n times_. It means TC is $O(nlongn)$.


2. [11. Container With Most Water](https://leetcode.com/problems/container-with-most-water/), 24/Jun/24
Topics : Array, Two Pointer, Greedy
could solve it in 31mi using hints.
[My solution](https://leetcode.com/problems/container-with-most-water/solutions/5360668/thinking-process-with-well-explanation)

### Intuition

At first, I thought a wider x-axis is best, so I started with left(0) and right(len-1) indices. Then, I considered _how to move these indices_.

Initially, I thought about comparing height[left] with height[right] and _moving the lower one towards the center_. My reasoning was **that moving the larger one would always result in a smaller area compared to the original configuration**. However, I got stuck when considering the case where both heights were the same.

### Approach

The hint helped me rethink the scenario of equal heights. I realized it doesn't matter which index (left or right) gets fixed as long as one stays put. **This is because neither (i+1, j) nor (i, j-1) can be potential solutions when h[i] == h[j]**. The resulting area would always be smaller due to a smaller width and a maximum height that's either equal to or lower than (h[i], h[j]). This concept applies similarly to the cases of (h[l] > h[r]) and (h[l] < h[r]). The key is to **focus on identifying which configuration can be a potential solution or cannot be a potential solutuin!**. Finally, I recognized that (i+1, j-1) also works when h[i] == h[j].

### Complexity

- Time complexity: $O(n)$
- Space complexity: $O(1)$
    
### Code

```java
class Solution {
    public int maxArea(int[] height) {
        int left = 0;
        int right = height.length - 1;
        int water = 0;
        int maxWater = -1;

        while (left < right) {
            water = (right - left) * Math.min(height[left], height[right]); 
            maxWater = Math.max(maxWater, water);

            if (height[left] < height[right]) left++;
            else right--;
        }

        return maxWater;
    }
}
```

3. [15. 3Sum](https://leetcode.com/problems/3sum/), 25/Jun/24
Topics: Array, Two Pointers, Sorting
couldn't solve it within 30mi.
[My solution](https://leetcode.com/problems/3sum/solutions/5370978/two-approaches-sorting-set-sorting-only-with-well-explanation)

# Intuition

This problem needs three pointers. So if we **set one index**, then left 2 indices will be two pointer problem!  
And two pointer _need sorted array_. This probelm we only focus it is all different. but _don't care what is index literally_.  
So we can sort `nums`!

# Approach

## i) Using Sorting and Set

then TC will be O(n2)O(n^2)O(n2). 10^6 is not to exceed time limit :)  
But if we use ArrayList and check duplicate like this if(ans.contains(b) == false) ans.add(b).  
TC of `contains` function is O(n). So overall TC will be O(n3)O(n^3)O(n3). so Time limit exceeded!  
So we can use **Set to avoid duplicate!**

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> triplets = new ArrayList<>();
        Set<List<Integer>>  set = new HashSet<>();
        int twoSum = 0;

        Arrays.sort(nums);
        
        for(int i = 0; i < nums.length; i++) {
            twoSum = nums[i] * -1;
            // To avoid same combination of indices.
            int left = i + 1;
            int right = nums.length - 1;

            while (left < right) {
                if (nums[left] + nums[right] > twoSum) right--;
                else if (nums[left] + nums[right] < twoSum) left++;
                else {
                    // To avoid duplicate triplet!
                    set.add(Arrays.asList(nums[i], nums[left], nums[right]));    
                    right--;
                    left++;
                }
            }
        }
        
        triplets.addAll(set);
        return triplets;
    }
}
```

## ii) Using Sorting only.

If _don't use the Set_. We can just skip duplicate triplet using While and comparing. All 3 nums[i], nums[left], nums[right], _if same value, just skip!_  
Much faster than using set.

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> triplets = new ArrayList<>();
        int twoSum = 0;

        Arrays.sort(nums);
        
        for(int i = 0; i < nums.length; i++) {
            twoSum = nums[i] * -1;
            // To avoid same combination of indices.
            int left = i + 1;
            int right = nums.length - 1;

            while (left < right) {    
                if (nums[left] + nums[right] > twoSum) right--;
                else if (nums[left] + nums[right] < twoSum) left++;
                else {
                    triplets.add(Arrays.asList(nums[i], nums[left], nums[right]));    
                    // To avoid duplicate triplets for nums[left, right]).
                    while (left < right && nums[left] == nums[left + 1]) left++;
                    while (left < right && nums[right] == nums[right - 1]) right--;
                    right--;
                    left++;
                }
            }
            // To avoid duplicate triplets for nums[i]
            while (i < nums.length - 1 && nums[i] == nums[i + 1]) i++;
        }

        return triplets;
    }
}
```

4. [209. Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/), 26/Jun/24
Topics : Array, Binary Search, Sliding Window, Prefix Sum
couldn't solve it within 30mi. It was not that difficult but I was stuck at handling `end` index, and the timing when `nums[end]` is added. But it can be dealt with `while (sum >= target)` not `if (sum > target)`!!

i ) TC : $O(n)$
```java
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int minLen = Integer.MAX_VALUE;
        int sum = 0;
        int start = 0;
        int end = 0;

        while (end < nums.length) {
            sum += nums[end];
            end++;

            while (sum >= target) {
                minLen = Math.min(minLen, end - start);
                sum -= nums[start];
                start++;
            }
        }

        if (minLen == Integer.MAX_VALUE)
            return 0;
        else
            return minLen;
    }
}
```

ii) TC : $O(nlogn)$
As to NLogN solution, logN immediately reminds you of binary search. As you know, Binary search need ==sorted array==. But In this case, we cannot sort it. 
How does one get an ordered array then? _Since all elements are positive, the cumulative sum must be strictly increasing._ Then, __a subarray sum can expressed as the difference between two cumulative sum.__ Hence, given a start index for the cumulative sum array, the other end index can be searched using binary search.
```java
 private int solveNLogN(int target, int[] nums) {
        int[] sums = new int[nums.length + 1];
        // make prefix sum to get increasing order array.
        for (int i = 1; i < sums.length; i++) sums[i] = sums[i - 1] + nums[i - 1];
        int minLen = Integer.MAX_VALUE;
    
        for (int i = 0; i < sums.length; i++) {
	        // sum of subarray : if sum[end] - sum[i] > target
	        // so we have to find end index when sum[end] > target + sum[i]!
            int end = binarySearch(i + 1, sums.length - 1, sums[i] + target, sums);
            if (end == sums.length) break;
            if (end - i < minLen) minLen = end - i;
        }
        return minLen == Integer.MAX_VALUE ? 0 : minLen;
```

5. [3. Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/), 26/Jun/24
Topics : Hash Table, String, Sliding Window
couldn't solve it within 50mi, because I couldn't think that ==we have to update the map with the character's index always! not only map.containsKey(a) is false==

Idea : using hashmap, check a character is contained or not.
```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        HashMap<String, Integer> map = new HashMap<String, Integer>();
        char[] str = s.toCharArray();
        int ans = 0;
        int max = 0;
        int start = 0;
        int end = 0;
        int i = 0;

        while (end < str.length) {
             if (map.containsKey(String.valueOf(str[end])) == false) {
                map.put(String.valueOf(str[end]), end);
                max++;
                ans = Math.max(ans, max);
            }
            else {
                i = map.get(String.valueOf(str[end]));
                // Check str[i] is including in subarray or not!
                if (i < start) {
                    // Update str[end] index within subarray.
                    map.put(String.valueOf(str[end]), end);
                    max++;
                    ans = Math.max(ans, max);
                }
                else {                
                    start = i + 1;
                    max = end - start + 1;
                    // This is that line I couldn't come up with...
                    // must update index always!
                    map.put(String.valueOf(str[end]), end);
                }
            }

            end++;
        }
        
        return ans;
    }
}
```

And this is not good cuz `ans` is not needed!  `maxLen = Math.max(maxLen, end - right + 1)`
And also `HashMap<Character, Integer>` is valid. so `char[] str is not necessary`! `s.charAt(i) is good`