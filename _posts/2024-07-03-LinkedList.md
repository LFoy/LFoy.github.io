---
layout: post
title:  Linked List
date:   2024-07-03 19:51:29 +1000
categories: LeetCode
--- 

A sequential list of _nodes_ that hold data, _which point to other nodes_ also containing data.

- Used in many List, Queue & Stack implementations.
- Great for creating circular lists.
- Can easily model real world objects such as trains.
- Used in separate chaining, which is present certain HashTable implementations to deal with hashing collisions.
- Often used in the implementation of adjacency lists for graphs.

Terminology
- Head : The first node in a linked list
- Tail : The last node in a linked list
- Pointer : Reference to another node
- Node : An object containing data and pointer(s)

Time Complexity

 Action | Singly Linked | Doubly Linked |
 ------ | ------------- | ------------- |
 Search | $O(n)$ | $O(n)$ 
 Insert at head | $O(1)$ | $O(1)$
 Insert at tail | $O(1)$ | $O(1)$   
 Remove at head | $O(1)$ | $O(1)$
 __Remove at tail__ | __$O(n)$__  | $O(1)$ 
 Remove in middle | $O(n)$ | $O(n)$ 

>
Note that Singly Linked List also can have Head and Tail Node, But only one pointer to next node. When remove at tail in Singly, We can remove last Node using Tail Node. But Tail Node doesn't know what is previous Node only know next is NULL.  So we have to throw away Tail node after removing that. So TC is $O(n)$, not $O(1)$

1. [2. Add Two Numbers](https://leetcode.com/problems/add-two-numbers/), 3/Jul/24
Topics : Linked List, Math, Recursion
couldn't solve it bc when I allocated the `l1` and `l2` for next loop, I checked whether `l1.next` and `l2.next` is `null` or not. So if `l1` itself is `null`, `null.next` can be pointed. It is error.
And it can be optimized by using just `currSum` and `/` and `%` expressions not using `carry`.  Before `return` just `if (currSum / 10 == 1) tail.next = new ListNode(1)` 


```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode sum = new ListNode();
        ListNode tail = sum;
        int carry = 0;

        while (l1 != null || l2 != null || carry == 1) {
            int currSum = carry;
            if (l1 != null) {
                currSum += l1.val;
                l1 = l1.next;
            }
            if (l2 != null) {
                currSum += l2.val;
                l2 = l2.next;
            } 

            if (currSum >= 10) {
                currSum -= 10;
                carry = 1;
            }
            else carry = 0;

            tail.next = new ListNode(currSum);
            tail = tail.next;
        }

        return sum.next;
    }
}
```

2. [138. Copy List with Random Pointer](https://leetcode.com/problems/copy-list-with-random-pointer/), 3/Jul/24
Topics : Hash Table, Linked List
could solve it in 31mi!

First I thought _How to know `X` is `X`\` after making deepCopy for handling random pointers._ And then I came up with Using __Mapping!__

The tricky point is What I use as a _Key_, original? or copied?

### i) Using HashMap 
```java
/*
// Definition for a Node.
class Node {
    int val;
    Node next;
    Node random;

    public Node(int val) {
        this.val = val;
        this.next = null;
        this.random = null;
    }
}
*/

class Solution {
    public Node copyRandomList(Node head) {
        HashMap<Node, Node> map = new HashMap<Node, Node>();
        
        Node deepCopy = new Node(0);
        Node tail = deepCopy;
		// DeepCopy value and next of all nodes. (create Linked List)
        while (head != null) {
            tail.next = new Node(head.val);
            tail = tail.next;
            tail.random = head.random;

            map.put(head, tail);
            
            head = head.next;
        }

        tail = deepCopy.next;
		// Assign random pointers
        while (tail != null) {
            tail.random = map.get(tail.random);

            tail = tail.next;
        }
        
        return deepCopy.next;
    }
}
```

But My solution is not that good if it is doubly Linked List bc It returns _next pointer_ of copied List not itself.
`return map.get(head)` is so amazing!!
And also clever that first `while` loop use `node` itself not `head`.

```java
public RandomListNode copyRandomList(RandomListNode head) {
  if (head == null) return null;
  
  Map<RandomListNode, RandomListNode> map = new HashMap<RandomListNode, RandomListNode>();
  
  // loop 1. copy all the nodes
  RandomListNode node = head;
  while (node != null) {
    map.put(node, new RandomListNode(node.label));
    node = node.next;
  }
  
  // loop 2. assign next and random pointers
  node = head;
  while (node != null) {
    map.get(node).next = map.get(node.next);
    map.get(node).random = map.get(node.random);
    node = node.next;
  }
  
  return map.get(head);
}
```


### ii ) Wow... Using DFS method is so clever!!

```java
public class Solution {
    public RandomListNode copyRandomList(RandomListNode head) {
        Map<RandomListNode, RandomListNode> map = new HashMap<RandomListNode, RandomListNode>();
        return copyRandomList(head, map);
    }
    private RandomListNode copyRandomList(RandomListNode head, Map<RandomListNode, RandomListNode> map) {
        if (head == null) {
            return null;
        }
        if (map.containsKey(head)) {
            return map.get(head);
        }
        RandomListNode newHead = new RandomListNode(head.label);
        map.put(head, newHead);
        newHead.next = copyRandomList(head.next, map);
        newHead.random = copyRandomList(head.random, map);
        
        return newHead;
        
    }
}
```

### iii) Without HashMap, SC is $O(1)$

The main idea is _using original list point to corresponding of copied list._
> a -> a\` -> b -> b\` -> c -> c\`

So we can know what is corresponding without HashMap! And we can keep track of `next` order also!

1. Insert each node's copy right after it.
2. Set each copy's random pointer.
3. Separate the copied list from the original, and set the every next pointer correctly.

```java
public RandomListNode copyRandomList(RandomListNode head) {
  RandomListNode iter = head, next;

  // First round: make copy of each node,
  // and link them together side-by-side in a single list.
  while (iter != null) {
    next = iter.next;

    RandomListNode copy = new RandomListNode(iter.label);
    iter.next = copy;
    copy.next = next;

    iter = next;
  }

  // Second round: assign random pointers for the copy nodes.
  iter = head;
  while (iter != null) {
    if (iter.random != null) {
      iter.next.random = iter.random.next;
    }
    iter = iter.next.next;
  }

  // Third round: restore the original list, and extract the copy list.
  iter = head;
  RandomListNode pseudoHead = new RandomListNode(0);
  RandomListNode copy, copyIter = pseudoHead;

  while (iter != null) {
    next = iter.next.next;

    // extract the copy
    copy = iter.next;
    copyIter.next = copy;
    copyIter = copy;

    // restore the original list
    iter.next = next;

    iter = next;
  }

  return pseudoHead.next;
}
```

my code
```java
class Solution {
    public Node copyRandomList(Node head) {
        Node ori = head;
        Node deepCopy;
        
        if (head == null) return null;

        // 1. Insert dup node on right of original node.
        while (ori != null) {
            Node dup = new Node(ori.val);
            dup.next = ori.next;
            ori.next = dup;

            ori = dup.next;
        }

        ori = head;
        // 2. Set random pointers for dup nodes.
        while (ori != null) {
            if (ori.random != null) ori.next.random = ori.random.next;
            else ori.next.random = null;
            
            ori = ori.next.next;
        }

        ori = head;
        deepCopy = head.next;
        // 3. (Re)Set next pointers.
        while (ori != null) {
            Node dup = ori.next;
            ori.next = dup.next;
            if (dup.next != null) dup.next = dup.next.next;
            else dup.next = null;

            ori = ori.next;
        }
        
        return deepCopy;
    }
}
```

3. [92. Reverse Linked List II](https://leetcode.com/problems/reverse-linked-list-ii/), 4/Jul/24
Topics : Linked List
couldn't solve it __2h 33mi__... I was struggled to handle __Error (Found a cycle)__

The Key points are
- Using _dummy Node_ for `pre` pointer to prevent `left = 1`.
- Clarify confusing name like `head.next` to `ListNode reversed = head.next`. 
- This problem is about locating node using `next` pointer of each node _not really copy all value of node_.
```java
	if (idx < left) {
        head = head.next;
        // 여기가 pre = head; 로 돼있었음...
	    pre = pre.next;
        continue;
    }
//그리고 for문 돌때 마지막에 head = temp(reversed)로 갱신하면 안됨. 그럼 다음 포문 돌때 head가 prev.next즉 전 포문에서 위치 바뀐 노드를 바라보고 있어서 다시 처음부터 도는 거랑 같음 ㄷㄷ 이미 head.next로 interator 돌고 있으니까.
```


```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode reverseBetween(ListNode head, int left, int right) {
        // To prevent `left = 1` 
        ListNode dummy = new ListNode(0);
        ListNode pre = dummy;
        pre.next = head;
    
        for (int idx = 1; idx < right; idx++) {
            // Adjusting until locate `pre` on  `left - 1`th node.
            // Adjusting until locate `head` on  `left`th node.
            if (idx < left) {
                head = head.next;
                pre = pre.next;
                continue;
            }
            
            // reversed pointer will be reversed.
            ListNode reversed = head.next;
            
            head.next = reversed.next;
            reversed.next = pre.next;
            pre.next = reversed;
        }

        return dummy.next;
    }
}
```

4. [19. Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/), 4/Jul/24
Topics : #LinkedList, #TwoPointers 
could solve it 30mi, Using Hint(__fast and slow__ Pointers)

First, I came up with using `HashMap` or `ListNode[]` just to store the pointer of `nth` node. But It used space $O(sz)$, `sz` is the number of nodes is the list.
Using fast and slow pointer, we can solve it with constant space.

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode fast = head;
        ListNode dummy = new ListNode(0);
        ListNode slow = dummy;
        slow.next = head;

		// Move fast pointer until gap between slow and fast becomes `n`
        for (int i = 0; i < n; i++) fast = fast.next;

		// Move until fast touch last node!
		// then slow.next points the node to remove.
        while (fast != null) {
            slow = slow.next;
            fast = fast.next;
        }
		// remove slow.next!
        slow.next = slow.next.next;
        return dummy.next;
    }
}
```

5. [82. Remove Duplicates from Sorted List II](https://leetcode.com/problems/remove-duplicates-from-sorted-list-ii/), 5/Jul/24
Topics : #LinkedList , #TwoPointers 
couldn't solve it even though using Hint... So difficult!
But I got it after 2h! 

- Using Dummy node
- Using Two pointers
- When comparing, using `curr` and `curr.next` not `prev.next` and `curr`! That was so tricky.
- Know the timing when `prev.next` and `prev` is changed.
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null) return null;
        if (head.next == null) return head;

        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode prev = dummy;
        ListNode curr = head; 
    
        while (curr != null) {
            if (curr.next != null && curr.val == curr.next.val) {
                while (curr.next != null && curr.val == curr.next.val) curr = curr.next;
                prev.next = curr.next;
            }
            else {
                prev = prev.next;
            }

            curr = curr.next;
        }

        return dummy.next;
    }
}
```

6. [61. Rotate List](https://leetcode.com/problems/rotate-list/), 5/Jul/24
Topics : #LinkedList , #TwoPointers 
could solve it in 50mi. but I think it is easy! I just was stuck at some mistake(when I found `size`, I move head itself not using `iter`;; and using `if` not `while` wow...)

Just use Circular trait of Linked list! for Rotate!
1. just change head node to `kth` node __from end of list.__
2. connect last and head node.
3. disconnect k+1th node and kth node from end of list.
and using modular  `k % list.size`


```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode rotateRight(ListNode head, int k) {
        int size = 0;
        ListNode iter = head;
        if (head == null || head.next == null) return head;
        // find the number of nodes in the list.
        while (iter != null) {
            size++;
            iter = iter.next;
        }
    
        k = k % size;

        ListNode slow = head;
        ListNode fast = head;
        
        // make gap k between slow and fast. 
        for (int i = 0; i < k && fast != null; i++) fast = fast.next;
        
        // locate slow pointer at the (k + 1)th node from end of list.
        // for the disconnect first.
        while (fast.next != null) {
            fast = fast.next;
            slow = slow.next;
        }

        // The order of connect and disconnect is so important!
        // 1. connect last and head node.
        fast.next = head;
        // 2. change head node to `kth` node from end of list.
        head = slow.next;
        // 3. disconnect k+1th node and kth node from end of list. 
        slow.next = null;

        return head;
    }
}
```

7. [86. Partition List](https://leetcode.com/problems/partition-list/), 5/Jul/24
Topics : #LinkedList , #TwoPointers 
could solve it in 18mi!! AM I GENIOUS?

As soon as read problem, I came up with below!
just use two Linked List during travel!
one is less than x, the other is greater than or equal to x!
at last just connect them! god damm!

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode partition(ListNode head, int x) {
        ListNode less = new ListNode(0);
        ListNode lessFirst = less;
        ListNode great = new ListNode(0);
        ListNode greatFirst = great; 
        ListNode curr = head;
        
        //one is less than x, the other is greater than or equal to x!
        while (curr != null) {
            if (curr.val < x) {
                less.next = curr;
                less = less.next;     
            }
            else {
                great.next = curr;
                great = great.next;
            }
            curr = curr.next;
        }
        // Make end of list
        if (great.next != null) great.next = null;

        // just connect them!
        if (greatFirst.next != null) {
            less.next = greatFirst.next;    
        }
        
        return lessFirst.next;
    }
}
```

8. [146. LRU Cache](https://leetcode.com/problems/lru-cache/), 5/Jul/24
Topics : #HashTable, #LinkedList, #Design, #Doubly-LinkedList
could solve it 1h 42mi... so long...
If I used `last` is also `new Node`, I can avoid `null` error more easily.
```java
  head = new DLinkedNode();
  head.pre = null;

  tail = new DLinkedNode();
  tail.post = null;

  head.post = tail;
  tail.pre = head;
```

```java
class LRUCache {
    private HashMap<Integer, Node> map;
    private int size, capa;
    private Node last, first;

    public LRUCache(int capacity) {
        map = new HashMap<Integer, Node>(capacity);
        this. capa = capacity;
        first = new Node(-1, -1, null, null);
        last = first;
        this.size = 0;
    }
    // HashMap + when using it, reset the order of linked List
    public int get(int key) {
        if (map.containsKey(key) == true) {
            // reset the order of key to most recently.
            resetOrder(map.get(key));
            return map.get(key).val;
        } 
        else return -1;

    }
    
    // HashMap + when using reset the order of likned List
    // + if size > capacity, remove least recntly used key; 
    public void put(int key, int value) {
        if (map.containsKey(key)) {
            // reset the order of key and then switch the value.
            resetOrder(map.get(key));
            map.get(key).val = value;
        }
        else {
            if (size == capa) {
                // remove the least recently key.
                map.remove(first.next.key);
                if (first.next == last) {
                    first.next = null;
                    last = first;
                }
                else {
                    first.next.next.prev = first;
                    first.next = first.next.next;
                }
                
                size--;
            }
            // Put new key
            Node curr = new Node(key, value, last, null);
            last.next = curr;
            last = curr;
            map.put(key, curr);
            size++; 
        }
    }

    private class Node {
        int key;
        int val;
        Node next;
        Node prev;

        private Node (int key, int val, Node prev, Node next) {
            this.key = key;
            this.val = val;
            this.prev = prev;
            this.next = next;
        }
    }
    // Set ther order of curr to most recently used node.
    public void resetOrder(Node curr) {
        if (curr != last) {
            curr.prev.next = curr.next;
            curr.next.prev = curr.prev;
            curr.prev = last;
            last.next = curr;
            last = curr;
            last.next = null;
        }      
    }
}
```

wow... other solution is so good.
The idea is `update` function is combination of __`remove` and `add`__.
And the naming is so good like `before` and `after` Nodes
```java
public int get(int key) {
        Node n = map.get(key);
        if(null==n){
            return -1;
        }
        update(n);
        return n.value;
    }
    
    public void set(int key, int value) {
        Node n = map.get(key);
        if(null==n){
            n = new Node(key, value);
            map.put(key, n);
            add(n);
            ++count;
        }
        else{
            n.value = value;
            update(n);
        }
        if(count>capacity){
            Node toDel = tail.prev;
            remove(toDel);
            map.remove(toDel.key);
            --count;
        }
    }
    // so briliant!
    private void update(Node node){
        remove(node);
        add(node);
    }
    private void add(Node node){
        Node after = head.next;
        head.next = node;
        node.prev = head;
        node.next = after;
        after.prev = node;
    }
    
    private void remove(Node node){
        Node before = node.prev, after = node.next;
        before.next = after;
        after.prev = before;
    }
```