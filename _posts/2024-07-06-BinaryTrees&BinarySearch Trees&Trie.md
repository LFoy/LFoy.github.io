---
layout: post
title:  Binary Trees & Binary Search Trees & Trie
date:   2024-07-06 20:51:29 +1000
categories: LeetCode
--- 
A _tree_ is an _undirected graph_ which satisfied any of the following definitions:
- An _acyclic_ connected graph
- A connected graph with _N nodes_ and _N-1 edges_.
- An graph in which any two vertices are connected by _exactly_ one path.

A _Binary Tree_ is a tree for which every node has __at most two child nodes__.

A _Binary Search Tree_ is a binary tree that satisfies the BST invariant: __left subtree has smaller elements(value of node) and right subtree has larger elements__.

BSTs used to 
- implementation of some map and set ADTs
- Red Black Trees
- AVL Trees
- Splay Trees
- etc...

BS used to
- Used in the implementation of binary heaps.
- Syntax trees (used by compiler and calculators)
- Treap - a probabilistic DS (uses a randomized BST)


Time Complexity of BSTs

Operation | Average | Worst
------|-----|-------
Insert | $O(log(n))$ | $O(n)$
Delete | $O(log(n))$ | $O(n)$
Remove | $O(log(n))$ | $O(n)$
Search | $O(log(n))$ | $O(n)$
*Note that if the BST is being a line(all node have one child), it is worst case bc we have some linear behavior going on!*

## BST Removal
- If the node that will be removed has both left and right subtree, we can choose successor both in _largest node of left subtree_ or _smallest node of right subtree_! Because They still keep the invariant of BST(left subtree has smaller elements(value of node) and right subtree has larger elements). To do this, __dig as far left as possible__ in the right subtree.

## Tree Traversals

- Preorder, Inorder & PostOrder : _recursively_ using _stack_
	- Preorder prints before the recursive calls(left and right)
	- _Inoder_ prints between the recursive calls
		Notice that with a _BST_ the values printed by the inorder traversal are in _increasing order_!
	- Postorder prints after the recursive calls

- Level order Traversal : _Iteratively_ by Breadth First Search(BFS) using _Queue_, SC is $O(n)$.

- Morris Traversal : Inorder or preorder but _Iteratively_ not recursively and __SC is $O(1)$__
	- _Threaded(linked)_ Binary Tree : we find inorder or preorder _predecessor_(something that came before the current, 전임자) of a node before going on the left subtree. __Connect that inorder predecessor to the node.__ so establish a link between the inorder predecessor and the node. and then go the left subtree that way since that link exists we have a way to come back to the original node!
	> why thread(link) is needed? bc we have to come back root or a certain node, without stack or recursion!
	 
If we want to travel Binary Tree in inorder, inorder predecessor of root is the rightmost node of left subtree. So before going left subtree, we first must find the rightmost node of leftt subtree and then connect that node to root node! We make the magic portal! haha. So now we can go travel the left subtree!
__The right pointer of right most node is originally `null` so we can use that pointer as a portal(link, thread whatever)__ After using that portal, we have to remove it.

```java
public void inorder(Node root) {
	Node curr = root;
	while (curr != null) {
		//left is null then print the node and go to right
		if (curr.left == null) {
			print(curr.val);
			curr = curr.right;
		}
		else {
			//find the predecessor, the initial value is curr.left bc we can have only one node of left subtree.
			Node predecessor = curr.left;
			//To find predecssor, keep going right till right node is not null and right node is not curr(already establish link)
			while (predecessor.right != curr &&) predecessor = predecessor.right;
			//if right node is null, then go left after establishing link from predecessor to curr
			if(predecessor.right == null) {
				predecessor.right = curr;
				curr = curr.left;
			} // all nodes of left subtree is already visit, so remove that link and visit curr(print root). Go right after visiting curr. 
			else {
				predecessor.right = null;
				print(curr.val);
				curr = curr.right; 
			}
		}
	}
}
```

If we want to travel BT in preorder, just find preorder predecessor, actually same with inorder predecessor. The only difference is the print(visit) timing. Before going to left subtree, just print current node(root). And then go left subtree. easy~ 

# Trie

^2383dd

is a _Tree_ data structure used for storing collections of _Strings_. If two strings have a common _prefix_ then they will have a same ancestor in this tree.
```java
TrieNode {
	Map<Character, TrieNode> children;
	boolean endOfWord;
}
```
If the possible character is just lower or upper, then we exchange the `map` to `array sized n`

The main idea of _Trie_ is that _recursion(or iteratively)_ every character of input(`String word`).
And every word's __endpoint(end node)__ has only _empty map and endOfword is true_
[[2024-07-06-BinaryTrees&BinarySearch Trees&Trie#^36a171| insert, search, prefix search code in Java]]

The main idea of `Delete` is to change endOfWord to _false_! If end of word has no other character in map, then remove that node itself then remove that character in parent map. If end node of word has other char, then just keep that.

1. [105. Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/), 6/Jul/24
Topics : #Array, #HashTable , #DividAndConquer, #Tree, #BinaryTree
could solve it in 2h... My idea is similar with solution: Using `inorder`array for judging next one is left or right and divide the left and right array of `inorder` and `preorder`.
But I wanted to use `subarray` function to divide them. But the solution is just to use `start` and `end` indices!

My first Idea: 
we know what is `root`. it is def `preorder[0]`...
and how we know `9` is left or right of root?
we can see `inorder`. 9 is left by 3... so 9 is left...

### i) Using HashMap (SC : $O(n)$)
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    private HashMap<Integer, Integer> pos; 

    public TreeNode buildTree(int[] preorder, int[] inorder) {
        pos = new HashMap<Integer, Integer>();
        int len = preorder.length;     
        
        for(int i = 0; i < len; i++) pos.put(inorder[i], i);

        return buildSubtree(preorder, inorder, 0, len - 1, 0, len - 1);
    }

    private TreeNode buildSubtree(int[] preorder, int[] inorder, int startPre, int endPre, int startIn, int endIn) {
        if (startPre > endPre || startIn > endIn) return null;
        TreeNode root = new TreeNode(preorder[startPre]);
        int rootIdx = pos.get(preorder[startPre]);
        int preLeftLen = rootIdx - startIn;

        root.left = buildSubtree(preorder, inorder, startPre + 1, startPre + preLeftLen, startIn, rootIdx - 1);
        root.right = buildSubtree(preorder, inorder, startPre + preLeftLen + 1, endPre, rootIdx + 1, endIn); 
    
        return root;
    }
}
```

### ii) Without HashTable (SC : $O(1)$)
```java
int inIndex = 0; // Index of current root in inorder, it means same with preLeftLen(rootIdx - startIn)
    for (int i = inStart; i <= inEnd; i++) {
        if (inorder[i] == root.val) {
            inIndex = i;
        }
    }
```

2. [106. Construct Binary Tree from Inorder and Postorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/), 7/Jul/24
Topics : #Array, #HashTable , #DividAndConquer, #Tree, #BinaryTree 
could solve it in 44mi (felt shame... took 10mi to correct `startIn` of `root.right`)

It is almost same with above problem. So I could solve it easily.
def travle in reverse order of postorder bc we have to start root node first!
left subtree [9]
right subtree [15, 7, 20]
 */
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public TreeNode buildTree(int[] inorder, int[] postorder) {
        int len = inorder.length;
        return buildSubtree(0, len - 1, 0, len - 1, inorder, postorder);
    }

    private TreeNode buildSubtree(int startIn, int endIn, int startPost, int endPost, int[] inorder, int[] postorder) {
        if (startIn > endIn || startPost > endPost) return null;

        TreeNode root = new TreeNode(postorder[endPost]);
        int postLeftLen = 0;
        for(int i = startIn; i <= endIn; i++) {
            if (inorder[i] == root.val) break;
            postLeftLen++;
        } 

        root.left = buildSubtree(startIn, startIn + postLeftLen - 1, startPost, startPost + postLeftLen - 1, inorder, postorder);
        root.right = buildSubtree(startIn + postLeftLen + 1, endIn, startPost + postLeftLen, endPost - 1, inorder, postorder);

        return root;
    }
}
```

3. [117. Populating Next Right Pointers in Each Node II](https://leetcode.com/problems/populating-next-right-pointers-in-each-node-ii/), 8/Jul,24
Topics : #LinkedList , #Tree , #DFS, #BFS, #BinaryTree 
couldn't solve it in 1h... It was so difficult because of the constraint(Using only constant space). This problem is about _level order traversal_. So df BFS is easiest way. But Using BFS means Using queue that is not constant space.
So we have to solve it DFS!

### i) Using BFS (SC : $O(n)$), But  I made`LevelNode`. It is bad... 
```java
/*
// Definition for a Node.
class Node {
    public int val;
    public Node left;
    public Node right;
    public Node next;

    public Node() {}
    
    public Node(int _val) {
        val = _val;
    }

    public Node(int _val, Node _left, Node _right, Node _next) {
        val = _val;
        left = _left;
        right = _right;
        next = _next;
    }
};
*/

class Solution {
    public Node connect(Node root) {
        ArrayDeque<LevelNode> q = new ArrayDeque<LevelNode>();
        if (root != null) q.add(new LevelNode(root, 0));
        
        while(!q.isEmpty()) {
            LevelNode curr = q.removeFirst();
            if (q.size() != 0 && q.peekFirst().level == curr.level) {
                curr.node.next = q.peekFirst().node;
            }

            if(curr.node.left != null) q.add(new LevelNode(curr.node.left, curr.level + 1));
            if(curr.node.right != null) q.add(new LevelNode(curr.node.right, curr.level + 1));
        }

        return root;
    }

    private class LevelNode {
        Node node;
        int level;

        LevelNode (Node node, int level) {
            this.node = node;
            this.level = level;
        }
    }
}
```

### ii) Using `for` loop inside `while(~q.isEmpty())`. So good~ 
The key point is to store _size of current level_
```java
    public Node connect(Node root) {
        ArrayDeque<Node> q = new ArrayDeque<Node>();
        if (root != null) q.add(root);
        
        while(!q.isEmpty()) {
            for (int size = q.size(); size > 0; size--) {
                Node curr = q.removeFirst();

                if (size != 1) {
                    curr.next = q.peekFirst();
                }
                if(curr.left != null) q.add(curr.left);
                if(curr.right != null) q.add(curr.right);
            }
        }

        return root;
```

But still space complexity is $O(n)$.

### iii) Using DFS, SC is $O(1)$.
The important thing is to update __right node first__ not left.
This code will dig the right direction first __bc if we dig left node first, we can't know what is the next node of parent.__  
```java
class Solution {
    public Node connect(Node root) {
        if (root != null) {
            // update right node first.
            if(root.right != null) {
                root.right.next = findNext(root.next);
                connect(root.right);
            }

	            if(root.left != null) {
                if(root.right != null) root.left.next = root.right;
                else root.left.next = findNext(root.next);
                connect(root.left);
            }
        }

        return root;
    }
    
    // Scan all next node of parent Node, until we find the first left or right child.
    private Node findNext(Node parent) {
        if (parent == null) return null;
        if (parent.left != null) return parent.left;
        if (parent.right != null) return parent.right;
        if (parent.left == null && parent.right == null && parent.next != null) {
            return findNext(parent.next);
        }
        return null;
        
    }
}
```

### iv) Using iterative, but constant space.
Using `dummy`, `parent` node, and store __what is previous node__ 

```java
    public Node connect(Node root) {
        Node curr = root;

        // first node of parent level nodes
        while (curr != null) {
            // we have to remember what is first parent node of next level.
            Node dummy = new Node(0);
            // we have to remember what is previous node of current node.
            Node prev = dummy;
            

            // all current level nodes using parent nodes.
            while (curr != null) {
                if (curr.left != null) {
                    prev.next = curr.left;
                    prev = curr.left;
                }
                if (curr.right != null) {
                    prev.next = curr.right;
                    prev = curr.right;
                }

                curr = curr.next;
            }
            // first node of next level.
            curr = dummy.next;
        }

        return root;
    }
```

4. [114. Flatten Binary Tree to Linked List](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/), 9/Jul/24
Topics : #LinkedList , #Stack, #Tree , #DFS , #BinaryTree 
couldn't solve it, But at first, I came up with _using prev node_  But I can't solve it without global variable. but global variable is okay haha.

### i) Using global(class) variable, `prev` Node.
We want to write `node.right = prev` not `node.right = next` bc we want to solve it using _recursion_. For that, we have to travel in order of_ reversed preoder!_
For right of `4` node To point `5` node, We have to visit `5` node right before `4` node visit. For 3's right to point 4 node, we have to visit first 4 and then 3! So the order of visit is __Right -> Left -> Root__ 
So the code must have 
```java
flatten(root.right)
flatten(root.left)
``` 

```java
    // the order of visit is reverse of preorder, `Right -> Left -> Root`
    // So until right most node, we don't visit anything, so null.    
    private TreeNode prev = null;
 
    public void flatten(TreeNode root) {
        
        if (root != null) {
            flatten(root.right);
            flatten(root.left);

            root.right = prev;
            prev = root;
            root.left = null;
        }
    }
```

The SC is $O(1)$, But the class variable doesn't seem to be good.

### ii) Using Stack and iterative method, but SC is O(n)
If we just want to use `preoder` itself, we can use _Stack_ instead of recursion.

```java
public void flatten(TreeNode root) {
        Stack<TreeNode> stack = new Stack<>();
        if (root == null) return;
        
        stack.push(root);

        while (!stack.isEmpty()) {
            TreeNode node = stack.pop();
            // push right node first for the left node to have high priority.
            if (node.right != null) stack.push(node.right);
            if (node.left != null) stack.push(node.left);

            if (!stack.isEmpty()) node.right = stack.peek();
            // we already push `node.left` to `stack`. so can change it.
            node.left = null;
        }
    }
```


### iii) Using iterative method, but SC is O(1)
For right of `4` node To point `5` node, we can think that __rightmost node of left subtree _point to_ first node of right subtree.__

```java
    public void flatten(TreeNode root) {
        while (root != null) {
            TreeNode curr = root;

            while (curr != null) {
                // next can be null
                // store first node of right subtree.
                TreeNode next = curr.right;
                TreeNode left = curr.left;

                // find rightmost node of left subtree
                if (left != null) {
                    curr.right = left;
                    curr.left = null;
                    curr = left;
                    while (curr.right != null) curr = curr.right;
                }

                curr.right = next;
                curr = left;
            }
            root = root.right;
        }        
    }
```

More compact code.
```java
    public void flatten(TreeNode root) {
        while (root != null) {
            // if root.left is already null, we don't care that condtion at all.
            if (root.left != null) {
                // store the root of left subtree.
                TreeNode curr = root.left;
                
                // find right most node of left subtree
                while (curr.right != null) curr = curr.right;
                // rightmost node of left subtree point to first node of right subtree.
                curr.right = root.right;
                root.right = root.left;
                root.left = null;
            }
            root = root.right;
        }        
    }
```

5. [129. Sum Root to Leaf Numbers](https://leetcode.com/problems/sum-root-to-leaf-numbers/), 9/Jul/24
Topics : #Tree , #DFS, #BinaryTree 
could solve it in 28mi. It is basic problem of Recursion.
The main idea is only `depth * 10`

```java
class Solution {
    public int sumNumbers(TreeNode root) {
        return sum(root, 0);
    }

    private int sum(TreeNode root, int sum) {
        sum = sum*10 + root.val;
        int totalSum = sum;
        if (root.left != null) totalSum = sum(root.left, sum);
        if (root.left == null && root.right != null) totalSum = sum(root.right, sum);
        if (root.left != null && root.right != null) totalSum += sum(root.right, sum);
        
        return totalSum;
    }
}
```

Same idea but more good __recursion__ code. (__base code__ is more good)
```java
private int sum(TreeNode root, int sum) {
        if (root == null) return 0;
        sum = sum*10 + root.val;

        if (root.left == null && root.right == null) return sum;
        
        return sum(root.left, sum) + sum(root.right, sum);
        
    }
```

> When writing the recursion code, 
Focus on the what is best base code!

Space Complexity : __$O(H)$__, where `H` is the max depth of tree. This space is required for _implicit recursive stack space_. In the worst case, the tree maybe _skewed_ and `H = N` in which case, space required is equal to __$O(N)$__

Other ways are __Using Stack iteratively not recursion__ Or Using __Morris traversal__ for the _constant space_

6. [173. Binary Search Tree Iterator](https://leetcode.com/problems/binary-search-tree-iterator/), 10/Jul/24
Topics : #Stack, #Tree , #Design , #BinarySearchTree, #BinaryTree , #Iterator
could solve it in 1h after seeing topics(stack).

The constraint is `next()` and `hasNext()` to run in _average_ $O(1)$ time and use $O(h)$ memory.

When we see the $O(h)$, we can come up with Using _recursion(DFS)_ or _Stack iteratively_. We can choose Stack for inorder traversal.

The important thing is a word, _average_ not worst. So the time of worst case is can be distributed(amortized) by all node! So the worst TC of this code is $O(h)$ but average Time is $O(1)$. Because We visit a node already, the while loop doesn't execute!

```java
class BSTIterator {
    //private TreeNode ptr;
    Stack<TreeNode> stack;

    public BSTIterator(TreeNode root) {
        //ptr = new TreeNode(-1);
        stack = new Stack<>();
        // locate left most node to top of stack for initial next().
        while (root != null) {
            stack.push(root);
            root = root.left;
        }
        
    }
    
    public int next() {
        ptr = stack.pop();

        // locate  next smallest node to top os stack
        TreeNode smallest = ptr;
        if(smallest.right != null) {
            TreeNode next = smallest.right;
            while (next != null) {
                stack.push(next);
                next = next.left;
            }
        }

        return ptr.val;
    }
    
    public boolean hasNext() {
        return !stack.isEmpty();
    }
}
```

this `while` loop is redundant. so we can make a function for compact code.
```java
 while (next != null) {
                stack.push(next);
                next = next.left;
            }
```

7. [236. Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/), 11/Jul/24
Topics : #Tree , #DFS, #BinaryTree 

This problem is really typical. But I couldn't solve it... It was soooooo difficult. wow...... After I read the pseudocode, I couldn't understand! I think I cannot come up with this algorithm even if given eternal time.
TC : $O(n)$
SC : $O(h)$ // implicit stack space

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || root == p || root == q) return root;
        
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);

        // it means root is lowest common ancestor bc p and q exist in left and right subtree of root respectively.
        if (left != null && right != null) return root;
        // if we can't find p or q, just return null to signal this node is useless.
        if (left == null && right == null) return null;
		// else, return not null node, because we have to keep track of this node. we can't ensure this node is LCA at this moment. So just keep.
		return (left == null) ? right : left;
    }
}
```

8. [199. Binary Tree Right Side View](https://leetcode.com/problems/binary-tree-right-side-view/), 11/Jul/24
Topics : #Tree , #DFS, #BFS, #BinaryTree 
could solve it in 14mi. Bc I already solve similar problem using _size to divide the depth_ in BFS .
The idea is __How to know right most node in each depth__

### i) Using BFS
```java
class Solution {
    public List<Integer> rightSideView(TreeNode root) {
        List<Integer> rightView = new ArrayList<>();
        ArrayDeque<TreeNode> q = new ArrayDeque<>();

        if (root != null) q.add(root);

        while(!q.isEmpty()) {
            for (int size = q.size(); size > 0; size--){
                TreeNode node = q.removeFirst();
                if (node.left != null) q.addLast(node.left);
                if (node.right != null) q.addLast(node.right);
                
                if (size == 1) rightView.add(node.val);    
            }
        }
        
        return rightView;
    }
}
```

### ii) Using DFS
The return type of `rightmost function` can be `void` not `List<Integer>`. Are you dumb? We send the `rightView` itself! So don't care about it. 

The main idea is DFS _right to left_. and also we have to care about left child! It is about _First come First served_. Using size and depth is important bc __each one depth we have to choose one node!__ so `rightView` size must be same with height of tree.

```java
    public List<Integer> rightSideView(TreeNode root) {
        List<Integer> rightView = new ArrayList<>();
        rightmost(root, rightView, 0);
        return rightView;
    }

    private void rightmost(TreeNode root, List<Integer> rightView, int depth) {
        if(root == null) return;
        if (rightView.size() == depth) rightView.add(root.val);

        rightmost(root.right, rightView, depth + 1);
        rightmost(root.left, rightView, depth + 1);
    }
```

9. [102. Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/), 11/Jul/24
Topics : #Tree , #BFS , #BinaryTree 
could solve it in 7mi.
It is exactly same with any other _levelorder traversal dividing by depth_ Using size!

```java
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> ans = new ArrayList<>();
        ArrayDeque<TreeNode> q = new ArrayDeque<>();
        
        if (root != null) q.add(root);

        while(!q.isEmpty()) {
            List<Integer> li = new ArrayList<>();

            for (int size = q.size(); size > 0; size--) {
                TreeNode node = q.removeFirst();
                li.add(node.val);

                if (node.left != null) q.add(node.left);
                if (node.right != null) q.add(node.right);
            }
            ans.add(li);
        }

        return ans;
    }
```

It can be solved by _DFS_ also. Using __depth__!
Just remember If you want convert BFS code to DFS. Just add __depth__ in parameters!

This guy used `depth` as _a array index_.
```java
public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<List<Integer>>();
        levelHelper(res, root, 0);
        return res;
    }
    
    public void levelHelper(List<List<Integer>> res, TreeNode root, int height) {
        if (root == null) return;
        if (height == res.size()) {
            res.add(new LinkedList<Integer>());
        }
        res.get(height).add(root.val);
        levelHelper(res, root.left, height+1);
        levelHelper(res, root.right, height+1);
    }
```

10. [103. Binary Tree Zigzag Level Order Traversal](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/), 12/Jul/24
Topics : #Tree , #BFS , #BinaryTree 
could solve it 36mi. When I read the problem, I immediately came up with the using `flag` and `reverse`. But It is not easy, but medium. So I wanted to solve it another way. But failed haha.

```java
class Solution {
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        boolean flag = false;
        ArrayDeque<TreeNode> q = new ArrayDeque<TreeNode>();
        List<List<Integer>> zigzag = new ArrayList<>();

        if (root != null) q.add(root);

        while(!q.isEmpty()) {
            List<Integer> li = new ArrayList<>();

            for (int size = q.size(); size > 0; size--) {
                TreeNode node = q.removeFirst();
                if (node.left != null) q.add(node.left);
                if (node.right != null) q.add(node.right);
                li.add(node.val);
                }
                if (flag == true) reverse(li, 0, li.size() - 1);
                    zigzag.add(li);
                    flag = !flag;
            }
        }
        return zigzag;
    }

    private void reverse(List<Integer> li, int left, int right) {
        while (left < right) {
            int temp = li.get(left);
            li.set(left, li.get(right));
            li.set(right, temp);
            left++;
            right--;
        }
    }

}
```

Or can use `addFirst()` of `ArrayList`, Or another `Stack` for not using `reverse` function.
Or can use `DFS`. when depth grow, create new `List`. use `depth` as a `index`. and `depth % 2 == 0` not using flag.

11. [230. Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/), 13/Jul/24
Topics : #Tree, #DFS, #BFS, #BinaryTree 
could solve it in 32mi. I just considered _using stack_ or _using DFS with class variable_.

### i) Using DFS with class variable
```java
class Solution {
    private int cnt = 0;
    private int kth = 0;

    public int kthSmallest(TreeNode root, int k) {
        inorder(root, k);
        return kth;
    }

    private void inorder(TreeNode root, int k) {
        if (root == null) return;
        
        inorder(root.left, k);
        if (++cnt == k) kth = root.val;
        inorder(root.right, k);
    }
}
```

### ii) Using Stack iteratively
```java
    public int kthSmallest(TreeNode root, int k) {
        Stack<TreeNode> stack = new Stack<>();
        int cnt = 0;
        int kth = 0;

        update(stack, root);

        while (!stack.isEmpty()) {
            TreeNode leftmost = stack.pop();
            if (++cnt == k) {
                kth = leftmost.val;
                break;
            }

            if (leftmost.right != null) update(stack, leftmost.right);
        }

        return kth;
    }

    // locate leftmost node of right subtree to the top of stack;
    private void update (Stack<TreeNode> stack, TreeNode leftmost) {        
        while (leftmost != null) {
            stack.push(leftmost);
            leftmost = leftmost.left;
        }
    }
```

>**Follow up:** If the BST is modified often (i.e., we can do insert and delete operations) and you need to find the kth smallest frequently, how would you optimize?

>If we have ordered array with value of each node, Space Complexity will increase by $O(n)$. But Time complexity will be better!  We can make `ArrayList` and then return `li.get(k-1)!`

12. [98. Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/), 13/Jul/24
Topics : #Tree, #DFS, #BFS, #BinaryTree 
could solve it in 44mi... _Use Long not Integer.MAX_VALUE or MIN_VALUE_
For that, I have to change `min > curr.val` to `min >= curr.val` and `curr.val - 1` to `curr.val` bc `curr.val` is `int` so `curr.val - 1` can be overflow if `curr.val` is `Integer.MIN_VALUE` 

```java
    public boolean isValidBST(TreeNode root) {
        return minMax(root, Long.MIN_VALUE, Long.MAX_VALUE);
    }

    private boolean minMax(TreeNode curr, long min, long max) {
        if (min >= curr.val || max <= curr.val) return false;

        if (curr.left != null) {
            if (!minMax(curr.left, min, curr.val)) return false;
        }
        if (curr.right != null) {
            if (!minMax(curr.right, curr.val, max)) return false;
        }
        return true;
    }
```

More compact code
```java
public boolean isValidBST(TreeNode root, long minVal, long maxVal) {
        if (root == null) return true;
        if (root.val >= maxVal || root.val <= minVal) return false;
        return isValidBST(root.left, minVal, root.val) && isValidBST(root.right, root.val, maxVal);
    }
```

13. [208. Implement Trie (Prefix Tree)](https://leetcode.com/problems/implement-trie-prefix-tree/), 18/Jul/24
Topics : #HashTable , #Stack , #Design , #Trie
could solve it 1h, but after studying _Trie_.
[[2024-07-06-BinaryTrees&BinarySearch Trees&Trie#^2383dd| Trie]] ^36a171

_NOTE THAT_
> DO NOT USE `++idx` as a parameter in a recursion call! we have to come back previous call, and the `idx` can be used again! So we have to use `idx + 1` without changing `idx`!

```java
class Trie {
    TrieNode root;

    public Trie() {
        root = new TrieNode();
    }
    
    public void insert(String word) {
        root.insert(word, 0);
    }
    
    public boolean search(String word) {
        return root.search(word, 0);
    }
    
    public boolean startsWith(String prefix) {
        return root.startsWith(prefix, 0);
    }

    private class TrieNode {
        HashMap<Character, TrieNode> map;
        boolean endOfWord;

        private TrieNode() {
            this.map = new HashMap<>();
            this.endOfWord = false;
        }

        private void insert(String word, int idx) {
            char ch = word.charAt(idx);
            if (!map.containsKey(ch)) map.put(ch, new TrieNode()); 
            TrieNode node = map.get(ch);
            
            if (word.length() - 1 == idx) node.endOfWord = true;
            // ++idx -> idx + 1 
            else node.insert(word, ++idx);
        }

        private boolean search(String word, int idx) {
            char ch = word.charAt(idx);
            if (!map.containsKey(ch)) return false;
            TrieNode node = map.get(ch);
            
            if (word.length() - 1 == idx && node.endOfWord == true) return true;
            if (word.length() - 1 == idx && node.endOfWord == false) return false; 
            // ++idx -> idx + 1
            return node.search(word, ++idx);
        }

        private boolean startsWith(String word, int idx) {
            char ch = word.charAt(idx);
            if (!map.containsKey(ch)) return false;
            if (word.length() - 1 == idx) return true;
            
            TrieNode node = map.get(ch);
            // ++idx -> idx + 1
            return node.startsWith(word, ++idx);
        }
    }
}
```

My code use recursion, but it can be solved as _iteratively_ and using _array_ not map. 
```java
class TrieNode {
    public char val;
    public boolean isWord; 
    public TrieNode[] children = new TrieNode[26];
    public TrieNode() {}
    TrieNode(char c){
        TrieNode node = new TrieNode();
        node.val = c;
    }
}

public class Trie {
    private TrieNode root;
    public Trie() {
        root = new TrieNode();
        root.val = ' ';
    }

    public void insert(String word) {
        TrieNode ws = root;
        for(int i = 0; i < word.length(); i++){
            char c = word.charAt(i);
            if(ws.children[c - 'a'] == null){
                ws.children[c - 'a'] = new TrieNode(c);
            }
            ws = ws.children[c - 'a'];
        }
        ws.isWord = true;
    }

    public boolean search(String word) {
        TrieNode ws = root; 
        for(int i = 0; i < word.length(); i++){
            char c = word.charAt(i);
            if(ws.children[c - 'a'] == null) return false;
            ws = ws.children[c - 'a'];
        }
        return ws.isWord;
    }

    public boolean startsWith(String prefix) {
        TrieNode ws = root; 
        for(int i = 0; i < prefix.length(); i++){
            char c = prefix.charAt(i);
            if(ws.children[c - 'a'] == null) return false;
            ws = ws.children[c - 'a'];
        }
        return true;
    }
}

```

14. [211. Design Add and Search Words Data Structure](https://leetcode.com/problems/design-add-and-search-words-data-structure/), 18/Jul/24
Topics : #String , #DFS , #Design , #Trie 
could solve it 1h 40mi _because of `++idx`!!!!!!!!!_ OMG... WTH!!

```java
class WordDictionary {
    TrieNode root;

    public WordDictionary() {
        root = new TrieNode();
    }
    
    public void addWord(String word) {
        TrieNode curr = root;
        for (int i = 0; i < word.length(); i++) {
            char ch = word.charAt(i);
                        
            if (curr.letters[ch - 'a'] == null) curr.letters[ch - 'a'] = new TrieNode();
            curr = curr.letters[ch - 'a'];
        }
        curr.isEnd = true;
    }
    
    public boolean search(String word) {
        return root.search(word, 0);
    }

    private class TrieNode {
        TrieNode[] letters;
        boolean isEnd;

        private TrieNode() {
            this.letters = new TrieNode[26];
            isEnd = false;
        }

        private boolean search(String word, int idx) {
            if (word.length() == idx) return isEnd;

            char ch = word.charAt(idx);

            if (ch == '.') {
                for (TrieNode next : letters) {
                    if (next != null) {
                    // we must use idx + 1 not ++idx!!! it can affect after recursion!!
                        if (next.search(word, idx + 1)) return true;
                    } 
                }
            }
            else {
                TrieNode next = letters[ch - 'a'];
                if (next == null) return false;
                return next.search(word, idx + 1);
            }
            return false;
        }     
    }
}
```
This DFS is more DFS. It means we use a _base line_. But in my 13. problem code. I didn't use a base line. I checked the exceptional case before calling recursion call. It is less DFSful.
