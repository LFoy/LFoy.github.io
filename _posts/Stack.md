t---
layout: post
title:  Stack
date:   2024-2-07 12:51:29 +1000
categories: Data structures
--- 

A one-ended linear data structure which models a real world stack by having two primary operations, namely __Push__ and __Pop__.

- Used by undo mechanisms in text editors.
- Used in compiler syntax checking for matching brackets and braces.
- Can be used to model a pile of books or plates.
- Used behind the scenes to support recursion by keeping track of previous function calls.
- Can be used to do a Depth First Search (DFS) on a graph.

Time Complexity

Pushing | $O(1)$
------------|------
Popping | $O(1)$
Peeking(without removing) | $O(1)$ 
Searching | __$O(n)$__
Size | $O(1)$

1. [71. Simplify Path](https://leetcode.com/problems/simplify-path/), 2/Jul/24
Topics : String, Stack
could solve it in 27mi.
But if I didn't know this problem is about Stack, Can I solve it? I dunno...

```java
class Solution {
    public String simplifyPath(String path) {
        Stack<String> simplePath = new Stack<String>();
        String[] paths = path.split("/");

        for (String file : paths) {
            switch (file) {
                case "..":
                    if(simplePath.isEmpty() == false) simplePath.pop();
                    break;
                case "":
                case ".":
                    break;
                default:
                    simplePath.push("/"+file);
            }
        }
        
        String ans = "";
        for (String file : simplePath) {
            ans += file;
        }     
        if (ans == "") return "/";
        else return ans;
        
    }
}
```
`Switch` can be switched to `if else`.

And `return "/" + String.Join("/", simplePath)` is more sexy.

2. [155. Min Stack](https://leetcode.com/problems/min-stack/), 2/Jul/24
Topics : Stack, Design
could solve it in 19mi using Hint, _Consider each node in the stack having a minimum value._

For returning minimum value of Stack, we have to keep track of `minNum`. but If the `minNum` popped? then __How we know 2nd minimum value?__ 

We can come up with _If previous node has second minimum value?_ Actually previous node can't know what is next node and next node value will be minimum or not. So when previous node was pushed, We can push value of node _with the currently minimum value_.

### i) Implemented by Stack itself
```java

    Stack<int[]> stack;
    public MinStack() {
        stack = new Stack<int[]>();
    }
    
    public void push(int val) {
        if (!stack.isEmpty() && stack.peek()[1] <= val) stack.push(new int[] {val, stack.peek()[1]});
        else stack.push(new int[] {val, val}); 
    }
    
    public void pop() {
        stack.pop();
    }
    
    public int top() {
        return stack.peek()[0];
    }
    
    public int getMin() {
        return stack.peek()[1];
    }
```

### ii) Implemented by Linked List
```java
class MinStack {
    Node head;

    //public MinStack() {
    //}
    
    public void push(int val) {
        if (head == null) head = new Node(val, val, head);
        else head = new Node(val, Math.min(head.min, val), head);
    }
    
    public void pop() {
        head = head.prev;
    }
    
    public int top() {
        return head.val;
    }
    
    public int getMin() {
        return head.min;
    }

    private class Node {
        int val;
        int min;
        Node prev;

        private Node(int val, int min, Node prev) {
            this.val = val;
            this.min = min;
            this.prev = prev;
        }
    }
}

```

3. [150. Evaluate Reverse Polish Notation](https://leetcode.com/problems/evaluate-reverse-polish-notation/), 2/Jul/24
Topics : Array, Math, Stack
could solve it in 18mi. I dunno why it is not easy?...

Pseudocode
```pseudocode
if (val == expression)
    a = pop
    b = pop
    stack.push(a expression b)
else stack.push(val); 

return stack.pop();
```


```java
class Solution {
    public int evalRPN(String[] tokens) {
        Stack<Integer> stack = new Stack<Integer>();
        HashSet<String> exp = new HashSet<String>(Arrays.asList("+", "-", "*", "/"));

        for (String token : tokens) {
            if (exp.contains(token)) {
                int b = stack.pop();
                int a = stack.pop();
                
                switch (token) {
                    case "+":
                        stack.push(a + b);
                        break;
                    case "-":
                        stack.push(a - b);
                        break;
                    case "*":
                        stack.push(a * b);
                        break;
                    default:
                        stack.push(a / b);
                        break;
                }
            }
            else stack.push(Integer.parseInt(token));
            
        }

        return stack.pop();
    }
}
```