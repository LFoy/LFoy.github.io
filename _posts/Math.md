---
layout: post
title:  Math
date:   2024-01-08 18:51:29 +1000
categories: Data structures
--- 

1. [172. Factorial Trailing Zeroes](https://leetcode.com/problems/factorial-trailing-zeroes/), 2/Aug/24
Topics : #Math
[[23일차 백준#^13cf84 | Same problem and Explanation]]

Because all trailing 0 is from __factors 5 * 2__.
But sometimes _one number may have several 5 factors_, for example, 25 have two 5 factors, 125 have three 5 factors. In the n! operation, _factors 2 is always ample_. So we just _count how many 5 factors in all number from 1 to n_.

TC : $O(log_5(n))$
```java
    public int trailingZeroes(int n) {
        int fivePow = 5;
        int ans = 0;
        while (n >= fivePow) {
            ans += n / fivePow;
            fivePow *= 5;
        }
        return ans;
    }
```
more simpler version
```java
    public int trailingZeroes(int n) {
        int ans = 0;
        while (n > 0) {
            n /= 5;
            ans += n;
        }
        return ans;
    }
```

2. [50. Pow(x, n)](https://leetcode.com/problems/powx-n/), 2/Aug/24
Topics : #Math , #Recursion
couldn't solve it in 1h bc there are so many edge(corner) cases!!!!
And this problem also I solved it before but forgot... haha
[[2023-11-01-day30#^45de9a | Same problem]]

i ) $(a^b) = (a^2)^(b/2)$ 
The main Idea is using _exponential laws_
>$(a^b) = (a^2)^(b/2)$
We can reduce time complexity $O(N)$ -> $O(logN)$

To handing _negative base_ is key point here. 
```java
    public double myPow(double x, int n) {        
        if (n == 0) return 1;
        // useless code
        if (n == 1) return x;
		// to prevent stack overflow, when n is INT_MIN
        if (n < 0) return 1/x * myPow(1/x, -(n+1));
        if (n % 2 == 0) return myPow(x*x, n/2);
        else return myPow(x*x, n/2) * x;
    }
```

ii ) $(a^(c+b)) = (a^c) * (a^b)$;
**Basic Idea is to divide the work using binary representation of exponents**

x = 7, n = 11 and pow = 1
Here, we have to calculate $7^11$
Binary of n : 1   0   1   1
OR we can also write this as
1 0 1 1
8 4 2 1 <-- Corresponding place values of each bit

Now, $7^8 × 7^2 × 7^1$ == $7^11$ as $7^(8 + 2 + 1) == 7^11$
NOTE: We have not considered $7^4$ in this case as the 4th place bit is OFF
```java
class Solution {
    public double myPow(double x, int n) {
        
        if(n < 0){
            n = -n;
            x = 1 / x;
        }
        
        double pow = 1;

        while(n != 0){
	        // check the a certain postion is set for not. if set, multiply x^(2^position)
            if((n & 1) != 0){
                pow *= x;
            } 
                
            x *= x;
            // prevent INT_MIN overflow
            // unsigned right shift
            n >>>= 1;
            
        }
        
        return pow;
    }
}
```