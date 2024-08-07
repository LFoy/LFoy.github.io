---
layout: post
title: DP vs Greedy vs Divide and Conquer
date:   2024-06-17 13:51:29 +1000
categories: LeetCode
--- 

3 things to solve __optimization__ problems.
Optimal means either I have to find _maximum_ answer or _minimum_ answer.

## Dynamic Programming

Divide the problem into a series of __overlapping sub-problems__. Then combine the solutions of sub-problems.

 ### Two features
 
1) Optimal Substructure : The problem is __divisible__.
2) Overlapping SubProblemss : The subproblems are __repeated__.
	>Can store them so that do not solve all the subproblems again and again.
	>Can use recurrence relation like Fibonacci.

## Greedy

At the stage I am standing, choose minimum or maximum one. If I chose something, other's way is not considered.

## Divide and Conquer like _Merge sort_

divide the array into two parts, then divide it into two parts again and agian, then combine these


## DP vs Greedy

DP will follow all the path(traverse all those sequence of decisions).
So DP _always_ give the optimal answer.
__However__, Greedy _doesn't_ give the optimal answer always.  

## DP vs Divide and Conquer

_Optimal substructure_ is same. But the main difference is _Overlapping subproblems_

1. [Best Time to Buy and Sell Stock II](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/),  17/Jun/2024
	Topic : Array, DP, Greedy
	couldn't solve this within 30mi.
[My solution](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/solutions/5325707/2-approaches-is-possible-with-well-explanation)

 Optimal problem is always about Dynamic Programming or Greedy!
 
### Dynamic Programming Approach

i) dp[i] = ith maximum profit -> fail

ii) Think about state machine(all possible situations) -> succeed  
The action we can do on ith day is **either buy(if share is 1), or sell (if share is 0) or DO NOTHING.**  
Using share flag failed cuz we have to consider prev action is holding or not **both**.  
so we have to use 2 case dp!  
hold[] and notHold[]  
but we always consider right after previous day.  
don't need to keep dp array, just need 2 variables.

```java
// must buy a stock at 1st day.
hold = -prices[0];
// must do nothing at 1st day.
notHold = 0;

for(int price : prices) {
    // buy(after notHold) or do nothing(after hold)
    hold = max(notHold - price, hold);
    // sell(after hold) or do nothing
    notHold = max(hold + price, notHold);
}

//As a result, notHold is always bigger than hold.
return notHold;

    public int maxProfit(int[] prices) {
        // must buy a stock at 1st day.
        int hold = -prices[0];
        // must do nothing at 1st day.
        int notHold = 0;

        for(int price : prices) {
            // buy(after notHold) or do nothing(after hold)
            hold = Math.max(notHold - price, hold);
            // sell(after hold) or do nothing
            notHold = Math.max(hold + price, notHold);
        }

        //As a result, notHold is always bigger than hold.
        return notHold;        
    }
```

### Greedy Approach

Whenever we encounter a price(while loop prices), **make a decision whether buy or sell or nothing. That is Greedy!**  
So we have to think about the _criteria_!

Assuming [7 1 5 3 6 4]  
in stage 7(index 0), we can decide nothing cuz we don't have a share and 1(index 1) is less.  
in stage 1(index 1), decide buy cuz we don't have a share and 5(index 2) is more.  
in stage 5(index 2), decide sell cuz we have a share and 3(index 3) is less!  
in stage 3, can decide buy cuz not holing and 3 is less than 6!  
in stage 6, can decide sell cuz holding and 6 is more than 4!  
in stage 4, can decide do nothing cuz not holding and it is last one!

so we have to check the state "hold or not" and "price of next day is more or less".

But Assuming [1 2 3 4 5]  
if we use above criteria. we get max profit is only 2 not 4.  
so Most important Thing is that **decision to sell is when only find last one of a consecutive increasing prices.**

```java
class Solution {
    public int maxProfit(int[] prices) {
        int maxProfit = 0;
        int day = 0;
        int buy = 0;
        int sell = 0;

        // last day cannot do anything.
        while(day < prices.length - 1) {
            // Do nothing until finding decreasing moment!
            // If found decreasing, then Buy!
            // And always sell occurs after buy cuz the codes order! so don't need to keep track of a state "hold or not".
            while(day < (prices.length - 1) && prices[day] >= prices[day + 1] ) { 
                day++;
            }
            buy = day;
            
            // Decide sell
            while(day < (prices.length - 1) && prices[day] <= prices[day + 1]) {
                day++;
            }
            sell = day;

            maxProfit = maxProfit - prices[buy] + prices[sell]; 
        }

        return maxProfit;
    }
}
```


1. [45. Jump Game II](https://leetcode.com/problems/jump-game-ii/),  22/Jun/2024
	Topic : Array, DP, Greedy
	could solve this within 30mi using DP method.
[My solution](https://leetcode.com/problems/jump-game-ii/solutions/5352257/two-method-with-thinking-intuition-process-and-well-explanation)

# Intuition

Jump Game I is about true or not if from nums[0] to nums[last index].  
Jump Game II is about the minimum number of jump to reach mums[last index]!  
and always can reach to last!

We can come up with _DP method and Greedy_ when we see **"maximum" or "minimum" word from problem**.

But we can also come up with _BFS_ when we see **"shortest path(route)" words from problem or problem implying that meaning without direct words.**

# Approach

1. DP for mimimum number of jump!
    
    ```cpp
    public int jump(int[] nums) {
        // dp[i] = The minimum num of jumps to reach nums[i];
        int n = nums.length;
        int[] dp = new int[n];
        Arrays.fill(dp, Integer.MAX_VALUE);
        dp[0] = 0;
    
        for (int i = 0; i < n; i++) {
            for(int jump = 0; jump <= nums[i] && (i + jump) < n; jump++) {
                dp[i + jump] = Math.min(dp[i + jump], dp[i] + 1);
            }
        }
    
        return dp[n-1];
    }
    ```
    

This way can solve it cuz nums[i] < 10310^3103. so 10710^7107 don't exceed the time limit.  
==But if the nums[i] would be bigger or nums length longer?...==

Then!

2. BFS for finding shortest route!

We know a template of BFS like below.

```python
visited = []

q = deque()
q.append(root)

while q:
	cur_node = q.popleft()
	visited.append(cur_node.value)

	if cur_node.left:
		q.append(cur_node.left)
	if cur_node.right:
		q.append(cur_node.right)

return visited
```

But this problem is quite different with typical BFS problem.  
we don't need to route(visited) but focusing on the jump count in that case.  
So we can swich _visited array_ to _a valuable containing minimum jump count_ until certain index!  
And we cannot search from current node to left and right node. But we can search(go) from current index to Possible Jump Position(index).

In BFS, If we cannot reach destination in a certain level (of graph).  
Then we change cur_node to next level node and repeat to travel. and we add the cur_node to visited array! ==We don't care whether cur_node is left or right. We only focus the fact one more level search is needed!==

Like that, if we cannot reach last index(target) from current index(of array).  
We have to jump from curr index to the next level index. and we update possble way(reachable indice is now current level range which will be searched!)

_Then how can we know what is the next level index?_ it means how can we know the range current search indice range.

First, start the `MaxReach = nums[0]`.  
Then curr search range(curr level) is now [1, MaxReach] (with 1 jump from 0 index).  
Then we have to upgrade maxReach while travel on [1, MaxReach].  
But while traveling, MaxReach can be changed. so we have to hold one temp valuable for MaxReach.  
if current level travel ended, MaxReach will be next level index! with increasing jump by 1!  
if we can reach last index then break! return jumps!

**Be careful that MaxReach index doesn't mean best next jump index. it means just for same level(jump) range end point!**

So that way like BFS, we can find shortest path(minimum num of jumps)!

Okay. Let's write the code~

```java
class Solution {
    public int jump(int[] nums) {
        // Minimum num of jumps
        int jumps = 0;
        // To set next level range
        int maxReach = 0; 
        // Store maxReach temparily.
        int next = 0;
        int n = nums.length;

        for (int i = 0; i < n; i++) {
            // for check end, We have to use `next` not `maxReach` because the jump timing is the time when the `next` update.  
            if (next < n - 1) {
                // set the search range.
                maxReach = Math.max(maxReach, i + nums[i]);
                
                // if current level(on same jump) travel ended, MaxReach will be next level index! with increasing jump by 1!
                if (i == next) {
                    next = maxReach;
                    jumps++;
                }
            }
            else break;
        }

        return jumps;
    }
}
```

### Intervals (but I think this is about Greedy and Array)
> Oh... There is `Interval` Datatype in Java!! it means `[start, end]`

3. [56. Merge Intervals](https://leetcode.com/problems/merge-intervals/), 30/Jun/24
Topics : Array, Sorting
couldn't solve it in 30mi, but I didn't know _How to sort and nested array and How to convert List to Array. So I searched it..._
전에 백준에서 풀었던 미팅룸 최대로 잡기? 뭐 그런 문제랑 비슷함. 그때도 그리디였음. 포문 돌고 요소 마주할 때마다 최선의 선택을 하는 것, 그 조건을 생각해내는게 어렵긴해.

```java
class Solution {
    public int[][] merge(int[][] intervals) {
        ArrayList<int[]> ans = new ArrayList<int[]>();
        //intervals = Arrays.stream(intervals).sorted(Comparator.comparingInt(arr -> arr[0])).toArray(int[][]::new);
        //Arrays.sort(intervals, (i1, i2) -> Integer.compare(i1[0], i2[0]));
        Arrays.sort(intervals, (a, b) -> a[0] - b[0]);

        ans.add(new int[] {intervals[0][0], intervals[0][1]});

        for (int i = 1; i < intervals.length; i++) {
            int len = ans.size();

            // Overlapping occurs
            if (ans.get(len - 1)[1] >= intervals[i][0]) {
                //if (ans.get(len - 1)[1] < intervals[i][1]) ans.get(len - 1)[1] = intervals[i][1];
                ans.get(len - 1)[1] = Math.max(ans.get(len - 1)[1], intervals[i][1]);
            }
            else ans.add(new int[] {intervals[i][0], intervals[i][1]});
        }

        //return ans.stream().toArray(int[][]::new);
        return ans.toArray(new int[ans.size()][]);
    }
}
```

4. [57. Insert Interval](https://leetcode.com/problems/insert-interval/), 1/Jul/24
Topics : Array
couldn't solve it 1h... Why... I don't know...
I just wanted to use only one loop. But __using `else if` so many times is so confusing.__ I can't think about all possible errors...

So I have to think very straight-forward way like this.

```java
class Solution {
    public int[][] insert(int[][] intervals, int[] newInterval) {
        List<int[]> ans = new ArrayList<>();
        int len = intervals.length;
        int i = 0;
        
        // Add all intervals before newInterval starts. 
        while (i < len && intervals[i][1] < newInterval[0]) {
            ans.add(intervals[i]);
            i++;
        }  

        // Merge all overlapping intervals using newInterval
        while (i < len && intervals[i][0] <= newInterval[1]) {
            newInterval[0] = Math.min(intervals[i][0], newInterval[0]);
            newInterval[1] = Math.max(intervals[i][1], newInterval[1]);
            i++;
        }

        // Insert(add) the newInterval on itself or merged state
        ans.add(newInterval);

        // Add all the rest
        while (i < len) {            
            ans.add(intervals[i]);
            i++;
        }
       
        return ans.toArray(new int[ans.size()][]);
    }
}
```

I tried to solve it using one `for` loop like below.
```java
public List<Interval> insert(List<Interval> intervals, Interval newInterval) {
    List<Interval> result = new ArrayList<Interval>();
    
    for (Interval i : intervals) {
        if (newInterval == null || i.end < newInterval.start)
            result.add(i);
        else if (i.start > newInterval.end) {
            result.add(newInterval);
            result.add(i);
            // Use newInterval as a Flag.
            newInterval = null;
        } else {
            newInterval.start = Math.min(newInterval.start, i.start);
            newInterval.end = Math.max(newInterval.end, i.end);
        }
    }
    
    if (newInterval != null)
        result.add(newInterval);
    
    return result;
}
```

5. [452. Minimum Number of Arrows to Burst Balloons](https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/), 1/Jul/24
Topics : #Array, #Greedy, #Sorting
could solve it using Hint... (__Keep track intersect range, and Narrow it__)
Actually That hint is essence of this problem.

_Be careful that when you sort, i1- i2 can be overflow So use `Interger.compare(i1, i2) more_

```java
class Solution {
    public int findMinArrowShots(int[][] points) {
        Arrays.sort(points, (i1, i2) -> Integer.compare(i1[0], i2[0]));
        int minShot = 1;
        // keep track of intersect range;
        int start = points[0][0];
        int end = points[0][1];
        
        for (int i = 1; i < points.length; i++) {
            // shot is not included in next balloon range.
            if (end < points[i][0]) {
                minShot++;
                start = points[i][0];
                end = points[i][1];
            }
            // narrow possible shot range.
            else {
                start = Math.max(start, points[i][0]); 
                end = Math.min(end, points[i][1]);            
            }      
        }

        return minShot;
    }
}
```

Actually, we don't need to keep track of `start` variable bc we sort `points` _in increasing order of `start` postion_.

This is the solution based on my _first_ idea.
I thought We have to choose `points[i][1]` and keep that as possible as that arrow knock down next balloon. It is based on __Greedy__. Do the best choice on current index(stage).
We should shoot as to the _right_ as possible, because since balloons are sorted, this gives you the best chance to take down more balloons.
plus) sort first `points` in order of  _the increasing `end` position_.

```java
public int findMinArrowShots(int[][] points) {
        if (points.length == 0) {
            return 0;
        }
        Arrays.sort(points, (a, b) -> Integer.compare(a[1], b[1]));
        int arrowPos = points[0][1];
        int arrowCnt = 1;
        for (int i = 1; i < points.length; i++) {
            if (arrowPos >= points[i][0]) {
                continue;
            }
            arrowCnt++;
            arrowPos = points[i][1];
        }
        return arrowCnt;
    }
```

6. [148. Sort List](https://leetcode.com/problems/sort-list/), 22/Jul/24
Topics : #LinkedList , #TwoPointers , #DividAndConquer , #Sorting , #MergeSort
couldn't solve it within 2h. It was so difficult to me.
I forgot how to sort by Merge or Quick. So I have to learn them again.
And The main difficulty if _to do merge sort using constant space $O(1)$_
At the first, I used `left.val variable` not `left` itself. This problem is about _LinkedList_. So I should've used to use traits of that!
And I started to `mergeSort` not using `sortList` at all. That is also failure point. ^0c80f8

```java
    public ListNode sortList(ListNode head) {
        // base code
        if (head == null || head.next == null) return head;
        
        // divide two sublist using Two pointers.
        ListNode slow = head; // To find mid
        ListNode fast = head.next.next;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        // For dividing disconnect two sublist.
        ListNode rightStart = slow.next;
        slow.next = null;

        ListNode left = sortList(head);
        ListNode right = sortList(rightStart);

        return merge(left, right);    
    }

    private ListNode merge(ListNode left, ListNode right) {
        ListNode dummy = new ListNode();
        ListNode curr = dummy;
		// using traits of LiknedList
        while (left != null && right != null) {
            if (left.val < right.val) {
                curr.next = left;
                left = left.next;
            }
            else {
                curr.next = right;
                right = right.next;
            }
            curr = curr.next;
        }
        // if left sublist is left to be unprocessed.
        if (left != null) curr.next = left;
        // if left sublist is left, just connect two
        else curr.next = right;

        return dummy.next;
    }
```

7. [427. Construct Quad Tree](https://leetcode.com/problems/construct-quad-tree/), 22/Jul/24
Topics : #Array , #DividAndConquer , #Tree , #Matrix 
could solve it in 34mi bc it is typical DAC and I've solve a lot this type problem!

### i ) It is easy, just Follow the step described by problem. (my code)

Time Complexity : $O(N^2 * logN)$
If we have a grid, size 4. then we have to visit 4^2 cell to check has same `val`.

size | visit cnt
-------|------
4 | 16
2 | 16
1 | 16

So we have to visit every cell(N^2) logN times!
And my code doesn't use _base code_ that is essential in recursion. 
```java
    public Node construct(int[][] grid) {
        return quadTree(new Node(), 0, 0, grid.length, grid);
    }

    private Node quadTree(Node curr, int row, int col, int size, int[][] grid) {
        // Check if grid has the same value
        int res = hasSame(row, col, size, grid);
        if (res != -1) {
            curr.isLeaf = true;
            curr.val = (res == 1) ? true : false ;
        }// if grid has different value then divide it into 4 sub-grids
        else {
            curr.isLeaf = false;
            curr.val = true; // any value
            curr.topLeft = quadTree(new Node(), row, col, size/2, grid);
            curr.topRight = quadTree(new Node(), row, col+size/2, size/2, grid);
            curr.bottomLeft = quadTree(new Node(), row+size/2, col, size/2, grid);
            curr.bottomRight = quadTree(new Node(), row+size/2, col+size/2, size/2, grid);
        }

        return curr;
    }

    private int hasSame(int row, int col, int size, int[][] grid) {
        int check = grid[row][col];
        for (int i = row; i < row + size; i++) {
            for (int j = col; j < col + size; j++) {
                if (check != grid[i][j]) return -1; 
            }
        }
        return (check == 0) ? 0 : 1; 
    }
```

### ii ) Best solution using base code
Time complexcity : $O(N^2)$, bc we don't need to visit all cell every recursion! (without `hasSame` function). We visit only each cell just once.
It works actually post order(meaning reverse).
_It divide whole grid into by 1 sized grid._
_And then check_ if return node is `leaf` and `val` is same with other 3 part.
Wow so genious.

```java
    public Node construct(int[][] grid) {
        return helper(grid, 0, 0, grid.length);
    }
    private Node helper(int[][] grid, int x, int y, int len) {
        if (len == 1) {
            return new Node(grid[x][y] != 0, true, null, null, null, null);
        }
        Node result = new Node();
        Node topLeft = helper(grid, x, y, len / 2);
        Node topRight = helper(grid, x, y + len / 2, len / 2);
        Node bottomLeft = helper(grid, x + len / 2, y, len / 2);
        Node bottomRight = helper(grid, x + len / 2, y + len / 2, len / 2);
        if (topLeft.isLeaf && topRight.isLeaf && bottomLeft.isLeaf && bottomRight.isLeaf
           && topLeft.val == topRight.val && topRight.val == bottomLeft.val && bottomLeft.val == bottomRight.val) {
            result.isLeaf = true;
            result.val = topLeft.val;
        } else {
            result.topLeft = topLeft;
            result.topRight = topRight;
            result.bottomLeft = bottomLeft;
            result.bottomRight = bottomRight;
        }
        return result;
    }
```

8. [198. House Robber](https://leetcode.com/problems/house-robber/), 2/Aug/24
Topics : #Array , #DynamicProgramming 
could solve it in 19mi bc it is typical DP.

The main Idea is to start with _Greedy_. _Choose the best(max) at that house_. _Should I steal this house or not?_ For that answer, I __have to store previous best__(max amount) So I decided that it is _DP not greedy_!
```java
    public int rob(int[] nums) {
        /**
        dp[i] = max amount until `i`th house
        Math.max(including `i`th houst, not including `i`th house)
        Math.max(dp[i-2] + nums[i], dp[i-1]);
        */
        int n = nums.length;
        int[] dp = new int[n];        
        dp[0] = nums[0];
        if (n > 1) dp[1] = Math.max(nums[0], nums[1]);
        if (n > 2) {
            for (int i = 2; i < n; i++) {
                dp[i] = Math.max(dp[i-2] + nums[i], dp[i-1]);
            }
        }
        return dp[n-1];
    }
```

### constant space
Just switch
> dp[i-2] -> prepreMax
> dp[i-1] -> preMax
```java
    public int rob(int[] nums) {
        int prepreMax = nums[0];
        if (nums.length == 1) return nums[0];
        if (nums.length == 2) return Math.max(nums[0], nums[1]); 
        int preMax = Math.max(nums[0], nums[1]);
        
        for (int i = 2; i < nums.length; i++) {
            int temp = preMax;
            preMax = Math.max(preMax, prepreMax + nums[i]);
            prepreMax = temp;
        }
    
        return preMax;
    }
```
more fantastic code
```java
public int rob(int[] num) {
    int prevNo = 0;
    int prevYes = 0;
    for (int n : num) {
        int temp = prevNo;
        prevNo = Math.max(prevNo, prevYes);
        prevYes = n + temp;
    }
    return Math.max(prevNo, prevYes);
}
```

9. [139. Word Break](https://leetcode.com/problems/word-break/), 2/Aug/24
Topics : #Array , #HashTable , #String , #DynamicProgramming , #Trie , #Memoization
couldn't solve it bc of the edge case... but my idea is right. 

Two properties of Dynamic Programming
1) overlapping subproblem
2) divisible subproblem

>if (cat, sand) is failed already, 
>we don't have to care about (cats, and) case. 
>It will be failed also.
So I want to store(memoization) _every result_ __to prevent redundant same process__.

i) Using recursion
```java
    public boolean wordBreak(String s, List<String> wordDict) {
        HashSet<String> dictSet = new HashSet<>();
        for (String word : wordDict) dictSet.add(word);
        // dp[i] = s.substring(0, i) can make segemenerk or not.
        Boolean[] dp = new Boolean[s.length()];
        Arrays.fill(dp, null);
        return helper(s, 0, dp, dictSet);
    }

    private boolean helper(String s, int start, Boolean[] dp, HashSet<String> dictSet) {
        if (start == s.length()) return true;
        if (dp[start] != null) return dp[start];

        for (int i = start; i < s.length(); i++) {
            if (dictSet.contains(s.substring(start, i+1)) == true) {
                boolean res = helper(s, i+1, dp, dictSet);
                if (res == true) {
                    dp[start] = true;
                    return true;
                }
            }
        }
        dp[start] = false;
        return false;
    }
```

ii) Using iteratively
```java
    public boolean wordBreak(String s, List<String> wordDict) {
        // dp[i] = s.substring(0, i) can make segemenerk or not.
        boolean[] dp = new boolean[s.length() + 1];
        Arrays.fill(dp, false);
        // default base
        dp[0] = true;

        for (int i = 0; i < s.length(); i++) {
            // prevent to useless case
            if (!dp[i]) continue;
            // already true then break!
            if (dp[s.length()] == true) return true;
            for(String word : wordDict) {
                int end = i + word.length();
                if (end <= s.length() && s.substring(i, end).equals(word)) {
                    dp[end] = dp[i]; 
                }
            }
        }
        return dp[s.length()];
    }
```

10. [322. Coin Change](https://leetcode.com/problems/coin-change/), 3/Aug/24
Topics : #Array , #DynamicProgramming , #BFS 
couldn't solve it in 1h...
I solved this before, but forgot...
[[2023-12-10-day44#^6c6981 | Same problem]]
dp[i] = fewest number of coins to make up `i` amount money.

>The main idea is for make up _`i` amount money_, we have to choose _a coin_ __at the last__. So we can express that _dp[i] = 1 + dp[i - coin]_
```java
    public int coinChange(int[] coins, int amount) {
        int[] dp = new int[amount + 1];
        Arrays.fill(dp, -1);
        dp[0] = 0; // the cast in which Choose nothing.

        for (int i = 1; i <= amount; i++) {
            for (int coin : coins) {
                if (coin <= i && dp[i - coin] != - 1) {
                    if (dp[i] == - 1) dp[i] = 1 + dp[i - coin];
                    else dp[i] = Math.min(dp[i], 1 + dp[i - coin]);
                }
            } 
        }
        return dp[amount];
    }   
```

ii) Using BFS
> _Fewest(minimum) coins_ can be interpreted that _find shortest path_.

```java
    public int coinChange(int[] coins, int amount) {
        ArrayDeque<Integer> q = new ArrayDeque<>();
        int minCoin = 0;
        boolean[] visited = new boolean[amount+1];
        q.add(0);
        visited[0] = true;

        while (!q.isEmpty()) {
            int size = q.size();
            while (size-- > 0) {
                int total = q.pop();
                if (total == amount) return minCoin;
                for (int coin : coins) {
                    if (coin <= amount && total + coin <= amount && visited[total+coin] == false) {
                        q.add(total + coin);
                        visited[total+coin] = true;
                    }
                }
                
            }
            minCoin++;
        }

        return -1;
    }
```

11. [300. Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/), 4/Aug/24
Topics : #Array , #DynamicProgramming , #BinarySearch 
could solve it in 38mi using hint.
> dp[i] : LIS including `i` index.

I know I already solved it... but also forgot ha....
```java
    public int lengthOfLIS(int[] nums) {
        int[] dp = new int[nums.length];
        dp[0] = 1;
        int longest = 1;

        for (int i = 1; i < nums.length; i++) {
            dp[i] = 1;
            for (int j = 0; j < i; j++) {
                if (nums[i] > nums[j]) dp[i] = Math.max(dp[i], dp[j] + 1);
                
            }
            if (longest < dp[i]) longest = dp[i];
        }

        return longest;
    }
```
This code TC is $O(n^2)$.
So we have to reduce it until $O(nlogn)$.

ii) Using binary search
```java
    public int lengthOfLIS(int[] nums) {
        ArrayList<Integer> ans = new ArrayList<>();
        ans.add(nums[0]);

        for (int num : nums) {
            int last = ans.get(ans.size() - 1);
            if (last < num) ans.add(num);
            else {
                // find `num` position in ans.
                replace(ans, num);
            }
        }

        return ans.size();
    }

    private void replace(ArrayList<Integer> ans, int num) {
        // start binarysearch and replace.
        int start = 0, end = ans.size() - 1;

        while (start < end) {
            int mid = (start + end) / 2;
            if (ans.get(mid) == num) return;
            else if (ans.get(mid) < num) start = mid + 1;
            else end = mid;
        }

        ans.set(start, num);
    }
```

```java
else if (ans.get(mid) < num) start = mid + 1;
else end = mid;
```

This part is important!
>
imagine
ans = `[3, 4, 6]`
and you want put `5` to ans.
the right position is 2(value 6).
if `ans[mid] > num` then def mid can't be the right position. so `start = mid + 1`.
But if `ans[mid] < num`, then mid can be the right position. so `end = mid`

Using `size`, don't need to use `ArrayList`.
```java
public int lengthOfLIS(int[] nums) {
    int[] tails = new int[nums.length];
    int size = 0;
    for (int x : nums) {
        int i = 0, j = size;
        while (i != j) {
            int m = (i + j) / 2;
            if (tails[m] < x)
                i = m + 1;
            else
                j = m;
        }
        tails[i] = x;
        if (i == size) ++size;
    }
    return size;
}
```

12. [120. Triangle](https://leetcode.com/problems/triangle/), 5/Aug/24
Topics : #Array , #DynamicProgramming 
I know, I got this problem before.
could solve it in 45mi bc _it was hard to handle the `ArrayList array`._
```java
    public int minimumTotal(List<List<Integer>> triangle) {
        // dp[i][j] = min path sum until `j` index on `i` row and including `j` index.
        int n = triangle.size();
        ArrayList<Integer>[] dp = new ArrayList[n];
        dp[0] = new ArrayList<Integer>();
        dp[0].add(triangle.get(0).get(0));
        
        for (int i = 1; i < n; i++) {
            dp[i] = new ArrayList<>();
            for (int j = 0; j < triangle.get(i).size(); j++) {
                int curr = triangle.get(i).get(j);
                if (j == 0) dp[i].add(dp[i-1].get(j) + curr);
                else if (j == triangle.get(i).size() - 1) dp[i].add(dp[i-1].get(j-1) + curr);
                else dp[i].add(Math.min(dp[i-1].get(j-1) + curr, dp[i-1].get(j) + curr));
            }
        }
        int min = Integer.MAX_VALUE;
        for (int i = 0; i < dp[n - 1].size(); i++) {
            min = Math.min(min, dp[n - 1].get(i));
        }         
        return min;
    }
```
This code has $O(row * col)$ space.
But follow up question ask __using only $O(row)$__.

### Bottom up method
top-down method can't solve it on `int[n]` because when we _overwrite_ `dp[j]` we can't use that for `dp[j+1]`. Even if using `temp` we can't use `dp[j+1]` to `next loop`. So we have to use `bottom up` bc `dp[i][j]` is __only used__ for `dp[i-1][j]`. So we can overwrite it!
```java
    public int minimumTotal(List<List<Integer>> triangle) {
        // dp[i] : min path sum until `i` index on row and including `i` index.
        int n = triangle.size();
        int[] dp = new int[n];
        for (int i = 0; i < n; i++) dp[i] = triangle.get(n-1).get(i);
        // bottom-up
        for (int i = n - 2; i >= 0; i--) {
            for (int j = 0; j <= i; j++) {
                dp[j] = Math.min(dp[j], dp[j+1]) + triangle.get(i).get(j);
            }
        }
        return dp[0];
    }
```
So it can run on $O(1)$ space if using `triangle` itself.

13. [64. Minimum Path Sum](https://leetcode.com/problems/minimum-path-sum/), 5/Aug/24
Topics : #Array , #DynamicProgramming , #Matrix 

```java
    public int minPathSum(int[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        int[][] dp = new int[m][n];
        dp[0][0] = grid[0][0];
        // set all topmost and leftmost cell.
        for (int col = 1; col < n; col++) dp[0][col] = dp[0][col-1] + grid[0][col];
        for (int row = 1; row < m; row++) dp[row][0] = dp[row-1][0] + grid[row][0];

        for (int row = 1; row < m; row++) {
            for (int col = 1; col < n; col++) {
                dp[row][col] = Math.min(dp[row-1][col], dp[row][col-1]) + grid[row][col];
            }
        }

        return dp[m-1][n-1];
    }
```
We can reduce _space complexity_ to $O(1)$ overwriting `graph` itself . 

14. [63. Unique Paths II](https://leetcode.com/problems/unique-paths-ii/), 5/Aug/24
Topics : #Array , #DynamicProgramming , #Matrix 
could solve it in 25mi.
The important thing is when setting the `row and col = 0`, we must  `if (obstacleGrid[row][0] == 1) break;` __to prevent `dp[row][0] = 1` when there is a obstacle before that row.__

```java
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int m = obstacleGrid.length;
        int n = obstacleGrid[0].length;
        int[][] dp = new int[m][n];
        // Set [row][0] 
        for (int row = 0; row < m; row++) {
            Arrays.fill(dp[row], 0);
            if (obstacleGrid[row][0] == 1) break;
            dp[row][0] = 1;
        }
        // Set [0][col] 
        for (int col = 0; col < n; col++) {
            if (obstacleGrid[0][col] == 1) break;
            dp[0][col] = 1;
        }

        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (obstacleGrid[i][j] == 1) continue;
                dp[i][j] = dp[i-1][j] + dp[i][j-1];
            }
        }
        return dp[m-1][n-1];
    }
```

using `obstacleGrid` itself
```java
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int m = obstacleGrid.length;
        int n = obstacleGrid[0].length;
        if (obstacleGrid[0][0] == 1) obstacleGrid[0][0] = 0;
        else obstacleGrid[0][0] = 1;

        for (int col = 1; col < n; col++) {
            if (obstacleGrid[0][col] == 1) obstacleGrid[0][col] = 0;
            else obstacleGrid[0][col] = obstacleGrid[0][col-1];
        }
        for (int row = 1; row < m; row++) {
            if (obstacleGrid[row][0] == 1) obstacleGrid[row][0] = 0;
            else obstacleGrid[row][0] = obstacleGrid[row-1][0];
        }

        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (obstacleGrid[i][j] == 1) obstacleGrid[i][j] = 0;
                else obstacleGrid[i][j] = obstacleGrid[i-1][j] + obstacleGrid[i][j-1];
            }
        }
        return obstacleGrid[m-1][n-1];
    }
```

__Using 1D dp__
wow so difficult to understand. I wonder _what happen to row[0]_. if `0` index of previous row has obstacle, then `row[0] = 0`, __after that `row[0]` is always `0`!!__ So brilliant! We don't touch `row[0]` except for that cell has obstacle. 
```java
class Solution {
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int n = obstacleGrid[0].length;
        int[] dp = new int[n];
        dp[0] = 1;

        for (int[] row : obstacleGrid) {
            for (int i = 0; i < n; i++) {
                if (row[i] == 1) dp[i] = 0;
                else {
                    if (i > 0) dp[i] = dp[i] + dp[i-1];
                }
            }
        }

        return dp[n-1];
    }
}
```

15. [5. Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/), 6/Aug/24
Topics : #TwoPointers , #String , #DynamicProgramming 

The key point here is that Think about 2 cases, __Odd__ length and __Even__ length!

### i) Using DP
TC : $O(n^2)$
SC : $O(n^2)$
>
The important thing is that using `dp[l+1][r-1]`. _We iterate `r` from `0` to `n-1`, so when we check `dp[l][r]`, we can use `dp[0~(r-1)][r-1]` bc `r-1` is previous step, already calculated(checked)!!_
```java
    public String longestPalindrome(String s) {
        int n = s.length();
        // dp[i][j] : index from `i` to `j` on String s has pelindrome or not
        boolean[][] dp = new boolean[n][n];
        String ans = "";

        for (int r = 0; r < n; r++) {
            // alone word is pelindrome.
            if (ans.length() < 1) ans = s.substring(r,r+1);
            dp[r][r] = true;
            for (int l = 0; l < r; l++) {
                // Odd length case
                if (s.charAt(l) == s.charAt(r) && (r-l == 2 || dp[l+1][r-1])) {
                    dp[l][r] = true; 
                    if (r-l+1 > ans.length()) ans = s.substring(l, r+1);        
                }
                // Even length case
                if (s.charAt(l) == s.charAt(r) && (r-l == 1 || dp[l+1][r-1])) {
                    dp[l][r] = true; 
                    if (r-l+1 > ans.length()) ans = s.substring(l, r+1);        
                }
            }
        }
        return ans;
    }
```

### ii) Using intuitive
So beautiful code.
TC : $O(n^2)$
SC : $O(1)$ <---- BEST!!
```java
    public String longestPalindrome(String s) {
        String ans = "";

        for (int center = 0; center < s.length(); center++) {
            String odd = expand(s, center, center);
            String even = expand(s, center, center + 1);
            
            if (odd.length() > ans.length()) ans = odd;
            if (even.length() > ans.length()) ans = even;
        }
        return ans;
    }

    private String expand(String s, int left, int right) {
        while (left >= 0 && right < s.length() && s.charAt(left) == s.charAt(right)) {
            left--;
            right++;
        }
        // return "restored pelindrom"
        return s.substring(left + 1, right);
    }
```

16. [97. Interleaving String](https://leetcode.com/problems/interleaving-string/), 7/Aug/24
Topics : #String , #DynamicProgramming 
couldn't solve it in 1h.

The main idea is 
>dp[i][j] = s3[:i+j] can be formed by interleaving s1[:i] and s2[:j]  or not

### Using 2D DP
```java
    public boolean isInterleave(String s1, String s2, String s3) {
        //if (s1.length() == 0 && s2.length() == 0) return (s3.length() == 0);
        if (s1.length() + s2.length() != s3.length()) return false;
        // dp[i][j] = s3[:i+j] can be formed by interleaving s1[:i] and s2[:j]  
        boolean[][] dp = new boolean[s1.length() + 1][s2.length() + 1];
        dp[0][0] = true;
        for (int i = 1; i <= s1.length(); i++) dp[i][0] = (dp[i-1][0] && s1.charAt(i-1) == s3.charAt(i-1));
        for (int j = 1; j <= s2.length(); j++) dp[0][j] = (dp[0][j-1] && s2.charAt(j-1) == s3.charAt(j-1)); 
        
        for (int i = 1; i <= s1.length(); i++) {
            for (int j = 1; j <= s2.length(); j++) {
                dp[i][j] = (dp[i-1][j] && s3.charAt(i+j-1) == s1.charAt(i-1))
                        || (dp[i][j-1] && s3.charAt(i+j-1) == s2.charAt(j-1));  
            } 
        }
        return dp[s1.length()][s2.length()];
    }
```

### Using 1D DP
