---
layout: post
title: Backtracking
date:   2024-18-07 20:51:29 +1000
categories: Data structures
--- 

1. [17. Letter Combinations of a Phone Number](https://leetcode.com/problems/letter-combinations-of-a-phone-number/), 18/Jul/24
Topics : #HashTable , #String , #Backtracking
could solve it in 30mi bc I've solved a lot backtracking problems.
The important idea of _Backtracking_ is just to think about _base line(condition)_. Backtracking is actually just _recursion and brute force(but controlled by some condition)_.
And _before backtracking call_ just add element, and _after backtracking_, remove that element.
But this code doesn't need to remove it.

```java
class Solution {
    public List<String> letterCombinations(String digits) {
        ArrayList<String> comb = new ArrayList<>();
        if (digits.length() == 0) return comb;
        HashMap<Character, String> tel = new HashMap<>();
        tel.put('2', "abc");
        tel.put('3', "def");
        tel.put('4', "ghi");
        tel.put('5', "jkl");
        tel.put('6', "mno");
        tel.put('7', "pqrs");
        tel.put('8', "tuv");
        tel.put('9', "wxyz");
        backtracking(tel, digits, 0, comb, new char[digits.length()]);
        return comb;
           
    }

    private void backtracking(HashMap<Character, String> tel, String digits, int idx, ArrayList<String> comb, char[] letter) {
        if (digits.length() == idx) {
            comb.add(new String(letter));
            return;
        }

        String letters = tel.get(digits.charAt(idx));
        for (int i = 0; i < letters.length(); i++) {
            char ch = letters.charAt(i);
            letter[idx] = ch;
            backtracking(tel, digits, idx + 1, comb, letter);
        }
    }
}
```

2. [77. Combinations](https://leetcode.com/problems/combinations/), 19/Jul/24
Topics : #Backtracking 
could solve it 36mi. but using chatGPT... because I couldn't figure out what is fault in my code!!!
It is really typical backtracking. So I could solve it 15mi if without error....
`for (int i = start + 1; i <= end; i++) {`
_I wrote `k` instead of `end(n)`. and waste time almost 20mi._
And _`ArrayList.remove(last index)` is also $O(1)$_
And I just did `combs.add(comb)`, we have to create `new List`.
```java
class Solution {
    public List<List<Integer>> combine(int n, int k) {
        List<List<Integer>> combs = new LinkedList<>();
        backtracking(combs, 0, n, k, new LinkedList<>());
        return combs;
    }

    private void backtracking(List<List<Integer>> combs, int start, int end, int k, List<Integer> comb) {
        if (comb.size() == k) {
            combs.add(new LinkedList<Integer>(comb));
            return;
        }

        for (int i = start + 1; i <= end; i++) {
            comb.add(i);
            backtracking(combs, i, end, k, comb);
            comb.removeLast();
        }
    }
}
```

Genius solution, So difficult to understand...
```java
public List<List<Integer>> combine(int n, int k) {
        List<List<Integer>> ans = new LinkedList();
        if (n < k || k == 0) return ans;

        // Case I: Add number n to answer
        ans = combine(n-1, k-1);
        if (ans.isEmpty()) ans.add(new LinkedList<Integer>()); // base case, initialize empty list
        for (List<Integer> list:ans) list.add(n);              // add n to final ans
        
        // Case II: Do not add number n to answer
        ans.addAll(combine(n-1, k));
        
        return ans;
    }
```

3. [46. Permutations](https://leetcode.com/problems/permutations/), 19/Jul/24
Topics : #Array , #Backtracking 
could solve it in 9mi. So typical.

```java
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> perms = new ArrayList<>();
        Set<Integer> visited = new HashSet<>();
        backtracking(perms, nums, visited, new ArrayList<>());
        return perms;
    }

    private void backtracking(List<List<Integer>> perms, int[] nums, Set<Integer> visited, List<Integer> perm) {
        if (perm.size() == nums.length) {
            perms.add(new ArrayList<>(perm));
            return;
        }

        for (int num : nums) {
            if (visited.contains(num)) continue;
            perm.add(num);
            visited.add(num);
            backtracking(perms, nums,visited, perm);
            perm.remove(perm.size() - 1);
            visited.remove(num);
        }
    }
```

can be solved by iteratively.
Main idea is we can _add the nth number_ into the resulting `List<List<Integer>>` _from the n-1 numbers_, in every possible position.
```java
public List<List<Integer>> permute(int[] num) {
    List<List<Integer>> ans = new ArrayList<List<Integer>>();
    if (num.length ==0) return ans;
    List<Integer> l0 = new ArrayList<Integer>();
    l0.add(num[0]);
    ans.add(l0);
    for (int i = 1; i< num.length; ++i){
        List<List<Integer>> new_ans = new ArrayList<List<Integer>>(); 
        for (int j = 0; j<=i; ++j){            
           for (List<Integer> l : ans){
        	   List<Integer> new_l = new ArrayList<Integer>(l);
        	   new_l.add(j,num[i]);
        	   new_ans.add(new_l);
           }
        }
        ans = new_ans;
    }
    return ans;
}
```

4. [39. Combination Sum](https://leetcode.com/problems/combination-sum/), 19/Jul/24
Topics : #Array , #Backtracking 
could solve it in 20mi. bc it is typical.
But I didn't optimize it. So my code is so slow...
I should've used `sort` __at the first__ and `in the backtracking for loop` I should've use `start variable` but i didn't... So I have to check every possible _duplicates_. And I have to get `sum`. _If I just use that as a parameter_, i don't need to every `for` loop for `sum`.

```java
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        Set<List<Integer>> combs = new HashSet<>();
        backtracking(candidates, target, combs, new ArrayList<>());
        return new ArrayList<List<Integer>>(combs);
    }
    
    private void backtracking(int[] candidates, int target, Set<List<Integer>> combs, List<Integer> comb) {
        int sum = 0;
        for (int num : comb) sum += num;
        if (sum == target) {
            List<Integer> newComb = new ArrayList<>(comb); 
            Collections.sort(newComb);
            if (combs.contains(newComb)) return;
            combs.add(new ArrayList<>(newComb));
            return;
        }
        if (sum > target) return;

        for (int num : candidates) {
            comb.add(num);
            backtracking(candidates, target, combs, comb);
            comb.remove(comb.size() - 1);
        }
    }
```

>using _sort candidates_ and didn't use `Set`. and use `sum` as a parameter. and Add the `int i = start` to prevent dup.
```java
class Solution {
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<List<Integer>> combs = new ArrayList<>();
        Arrays.sort(candidates);
        backtracking(candidates, target, combs, new ArrayList<>(), 0, 0);
        return combs;
    }
    
    private void backtracking(int[] candidates, int target, List<List<Integer>> combs, List<Integer> comb, int sum, int start) {
        if (sum == target) { 
            combs.add(new ArrayList<>(comb));
            return;
        }
        if (sum > target) return;

        for (int i = start; i < candidates.length; i++) {
            comb.add(candidates[i]);
            backtracking(candidates, target, combs, comb, sum + candidates[i], i);
            comb.remove(comb.size() - 1);
        }
    }
}
```

5. [22. Generate Parentheses](https://leetcode.com/problems/generate-parentheses/), 19/Jul/24
Topics : #String , #DynamicProgramming, #Backtracking 
could solve it 40mi using hints.
The main idea is to choose `(` or `)` every place, but choose `)` if possible.
It is like _binary tree_ __because the options is only two. choose this or that__
```java
class Solution {
    public List<String> generateParenthesis(int n) {
        Stack<Character> stack = new Stack<>();
        List<String> combs = new ArrayList<>();
        backtracking(combs, new char[2*n], n, 0, stack);
        return combs;
    }
    
    private void backtracking(List<String> combs, char[] comb, int left, int times, Stack<Character> stack) {
        if (times == comb.length) {
            combs.add(new String(comb));
            return;
        }

        if (left > 0) {
            // choose "("
            stack.push('(');
            comb[times] = '(';
            backtracking(combs, comb, left - 1, times + 1, stack);
            stack.pop();
        }
        // choose ")" if possible
        if(!stack.isEmpty()) {
            char paren = stack.peek();
            if (paren == '(') {
                stack.pop();
                comb[times] = ')';
                backtracking(combs, comb, left, times + 1, stack);
                stack.push('(');
            }
        }
    }
}
```

My first idea was `int left` and `int right`. But i couldn't think that __If `right < left` then we can add `')'` to the current string__ without `Stack` and use `String comb`  instead of `char[] comb`.
It is still _backtracking_. Bc after recursion call, we use `comb` again. It implies _undo behavior_.

```java
class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> combs = new ArrayList<>();
        backtracking(combs, "", 0, 0, n);
        return combs;
    }
    
    private void backtracking(List<String> combs, String comb, int left, int right, int n) {
        if (comb.length() == 2*n) {
            combs.add(comb);
            return;
        }

        if (left < n) {
            // choose "("
            backtracking(combs, comb + "(", left + 1, right, n);
        }
        // choose ")" if possible
        if(right < left) {
            backtracking(combs, comb + ")", left, right + 1, n);
        }
    }
}
```

6. [79. Word Search](https://leetcode.com/problems/word-search/), 20/Jul/24
Topics : #Array , #String , #Backtracking , #Matrix 
could solve it in 33mi without using hints.
It is quite typical.
And my code is so _DFSful with undo_(backtracking) bc I used a lot _base conditons_!!! without __BFSful code(to check before call recursion or before pushing a data to queue.)__
```java
    public boolean exist(char[][] board, String word) {
        int m = board.length;
        int n = board[0].length;
        

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (backtracking(new boolean[m][n] ,board, word, i, j, 0)) return true;
            }
        }

        return false;
    }

    private boolean backtracking(boolean[][] visited, char[][] board, String word, int row, int col, int idx) {
        if (idx == word.length()) return true;
        if (row < 0 || row >= board.length || col < 0 || col >= board[0].length) return false;
        if (word.charAt(idx) != board[row][col]) return false;
        if (visited[row][col] == true) return false;
        
        visited[row][col] = true;
        boolean res = backtracking(visited, board, word, row - 1, col , idx + 1)
                || backtracking(visited, board, word, row, col + 1, idx + 1)
                || backtracking(visited, board, word, row + 1, col, idx + 1)
                || backtracking(visited, board, word, row, col - 1, idx + 1);
        visited[row][col] = false;
        return res;   
    }
```


**Follow up:** Could you use search pruning to make your solution faster with a larger `board`?
> a smart pruning technique is to check the frequency of first and last char of the word in the board, and if the freq of first char is greater than last one, simple reverse the string and find the result.