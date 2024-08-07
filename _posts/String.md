---
layout: post
title:  String
date:   2024-22-06 14:51:29 +1000
categories: Data structures
--- 

1. [6. Zigzag Conversion](https://leetcode.com/problems/zigzag-conversion/), 22/Jun/24
couldn't solve it within 30 mi.
Topic : String
[My solution](https://leetcode.com/problems/zigzag-conversion/solutions/5350015/solution-with-thinking-process-and-well-explanation)

### Intuition

1. make 2 dimentional array and then try find a rule or pattern of indice -> fail
2. try to make recursion function? -> fail

### Approach

It doesn't need **2 dimentional array**.

```
P   A   H   N
A P L S I I G
Y   I   R
```

_Is same with_

```
sb[0] PAHN
sb[1] APLSIIG
sb[2] YIR
```

So we don't care about column actually!  
Just focus on row only and just append next char!

### Complexity

- Time complexity: O(n)O(n)O(n)
- Space complexity: O(n)O(n)O(n)

### Code

```java
class Solution {
    public String convert(String s, int numRows) {
        // To access using Index.
        char[] str = s.toCharArray();

        // Declare array which can hold StringBuilder as a element.
        StringBuilder[] sb = new StringBuilder[numRows];
        int row = 0;
        int curr =0;

        // Construct StringBuilder for Each element.
        for (int i = 0; i < numRows; i++) sb[i] = new StringBuilder();

        if(numRows == 1) return s;

        while (curr < str.length) {
            while (row < numRows && curr < str.length) {
                sb[row++].append(str[curr]);
                curr++;
            }

            row -= 2;

            while (row >= 0 && curr < str.length) {
                sb[row--].append(str[curr]);
                curr++;
            }

            row += 2;
        }

        for (int i = 1; i < numRows; i++) {
            sb[0].append(sb[i]);
        }

        return new String(sb[0]);
        
    }
}
```

But this code can be improved without exceptional return.  
We Know already vertical down writing range `[0, numRows)`  
and vertical up writing range `(numRows, 0]`

So we improve like below.

```java
class Solution {
    public String convert(String s, int numRows) {
        // To access using Index.
        char[] str = s.toCharArray();
        int curr = 0;

        // Declare array which can hold StringBuilder as a element.
        StringBuilder[] sb = new StringBuilder[numRows];
        
        // Construct StringBuilder for Each element.
        for (int i = 0; i < numRows; i++) sb[i] = new StringBuilder();

        while (curr < str.length) {
            for (int row = 0; row < numRows && curr < str.length; row++) {
                sb[row].append(str[curr]);
                curr++;
            }

            for (int row = numRows - 2; row >= 1 && curr < str.length; row--) {
                sb[row].append(str[curr]);
                curr++;
            }
        }

        for (int i = 1; i < numRows; i++) {
            sb[0].append(sb[i]);
        }

        return new String(sb[0]);
        
    }
}
```

2. [12. Integer to Roman](https://leetcode.com/problems/integer-to-roman/), 23/Jun/24
couldn't solve it within 30 mi.
Topic : String, Hash Table, math
[My solution](https://leetcode.com/problems/integer-to-roman/solutions/5357442/thinking-process-with-well-explanation)

### Intuition

It's important to implement the function based on the _literal_ logic of the problem. However, the most important thing is to **make the logic easy to understand and implement the code**.

### Approach

We have _two rules_ based on whether the value is 4 or 9, but the constraints are only 1 <= num <= 3999! So, we **don't need to** divide the logic. We don't care if the number is 4 or 9.

The problem already gives us examples of 4, 9, 40, 90, 400, and 900 in Roman numerals! We don't care about 4000 or 9000 because the maximum Roman numeral here is 1000!

Therefore, we can use a HashMap.

Now, we have only one logic: Select the symbol of the **maximum value** that can be subtracted from the input. Append that symbol to the result, and **subtract** its value. Repeat this until the value reaches 0!

For the maximum value of the input `num`, we need to start subtracting from the biggest (1000) to the smallest (1). However, **the HashMap cannot give us an iterator based on the order of inputs**. So, we can create two arrays: one for containing integer values and another for containing corresponding Roman symbols **in the order of the integer values**.

### Complexity

- Time complexity: O(1)
    
- Space complexity: O(1) // Assuming constant size for the arrays
    

### Code

```java
class Solution {
    public String intToRoman(int num) { 
        int[] values = {1, 4, 5, 9, 10, 40, 50, 90, 100, 400, 500, 900, 1000};
        String[] symbols = {"I", "IV", "V", "IX", "X", "XL", "L", "XC", "C", "CD", "D", "CM", "M"};
        String romanNum = "";
        int i = values.length - 1;

        while (num > 0) {            
            if ( num >= values[i] ) {
                num -= values[i];
                romanNum += symbols[i];
            }
            else i--;
        }

        return romanNum;    
    }
}
```