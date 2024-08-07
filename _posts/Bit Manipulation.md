---
layout: post
title:  Bit Manipulation
date:   2024-31-07 13:51:29 +1000
categories: Data structures
--- 

### Swap 2 numbers
>Use _XOR_ instead of `temp` variable

5^5 = 0, meaning that __XOR operation to same numbers is 0__

1. `a = a ^ b`
2. `b = a ^ b` -> `b = (a ^ b) ^ b` -> `a ^ 0` -> `a`
3. `a = a ^ b` -> `a = (a ^ b) ^ a` -> `0 ^ b` -> `b`

### Check if the `i`th bit is set or not
> if (N & (1 << i)) != 0, then `i`th bit is set!
> Using _left shift_ operator and _AND bitwise_

N : 13, i = 1 
(1 << 1) -> 0..010
```
       1101(13)
&(AND) 0010(1<<1)
------------------
	   0000(0)
```
So if `N` is `13` and `i` is `1`, the answer is _unset!_

> if ( (N >> i) & 1 ) is 1, then  `i`th bit is set!
> Using _right shift_ and _AND bitwise_

N : 13, i = 2
(13 >> 2) -> 1101 -> 0011 
```
       0011(13>>2)
&(AND) 0001(1)
------------------
	   0001(1)
```

### Set the `i`th bit
> N | ( 1 << i )
> Use _OR_ bitwise and _left shift_ of `1`.

N = 9, i = 2
```
3210(i)
1001(9)
->
1101(13) How?

(1<<2) |(OR) N
```

### Clear the `i`bit
> N & ~( 1 << i )
> Use _NOT(~) and AND_ bitwise and _left shift_ of `1`.

N = 13, i = 2
```
3210(i)
1101(13)
1011
->
1001(9) How?
0100

~(1<<2) & N
```

### Toggle the `i`th bit
> N ^(XOR) ( 1 << i )
> Use _XOR_ bitwise and _left shift_ of `1`.

N = 13, i = 2
```
3210(i)
1101(13)
->
1001(9) How?

1101(13)
0100

(1<<2) XOR N
```

### Remove the last set bit(rightmost)
> N & (N-1)

N = 40(101000)
```
101000(40)
100111(39)
```
40 = $2^5$ + $2^3$
39 = $2^5$ + $2^2$ + $2^1$ + $2^0$
So (the number - 1) can be divided into _two part_ by the laast set bit!
```
10 1 000 (40) 
10 0 111 (39)

AND operator! 
10 0 000 (removed the last set bit!)
```
_still same leftside_ + removed 0 + _all set rightside!_
So using _AND_ bitwise, then we can get _still same leftside + 0(was last set bit) + 0...0(rightside bc rightside of original is 0 so still 0)_

### Check if the number is a power of 2 or not
> Every power of 2 will have _only 1 set bit_.
> So we can use the previous trait of _the last set bit_. If the number is power of 2 like `16`, then `10000` and 15 is `01111`! If we use _AND_ operator, then we can get `00000` meaning `0`.
> if not, we can't get `0`!
> __Therefore if (N & (N-1)) = 0), then the N is power of 2!__

### Count the number of set bits 
i ) Using trait of Odd number.
> _Odd_ number has _set bit_ on the _2^0_ position.
> So if `Odd & 1`, then outcome is always `1`.
```
int countSetBits(int n){
	cnt = 0
	while (n > 1){
		// (if n is odd count by 1)
		cnt += n & 1
		n = n >> 1
	}
	if (n == 1) cnt += 1
	return cnt;
}
```

ii) __Using "Remove the last set bit"__
Wow freaking brilliant!!!!!!!!!!!!!!!!!!!!!
> Remove the last set bit iteratively until the number will be zero then count the loop times!
```
cnt = 0
while (n != 0){
	n = n & (n-1)
	cnt++;
}
return cnt;
```
Time complexity is O(number of max set bits), meaning $O(31)$ wow...

1. [137. Single Number II](https://leetcode.com/problems/single-number-ii/), 1/Aug/24
Topics : #Array , #BitManipulation 
couldn't solve it in 1h...
I read the hint using the buckets. After getting that Idea, I thought I can solve it. But failed...

>My idea
31 buckets?
counts the 1 set bit in buckets!?!
and divide all counts of bucket 3.
find remain is not 0 then... ohohohohoh.
cnt % 3 == 1 ?
then num += Math.pow(2, position)

The reason why I failed is I __didn't use bit position itself__. I added it. so My code didn't work when _number is negative!_.

My failed and dirty code
```java
    public int singleNumber(int[] nums) {
        int[] cnt = new int[32];
        // Count position of the set bit in cnt array.
        // O(n)
        for(long num : nums) {
            if (num < 0) {
                cnt[31] += 1;
                num = -num;
            }
            while (num > 1) {
                // Clear last set bit (rightmost set bit)
                long removed = num & (num-1);
                long find = num - removed;
                for (int i = 0; i < 31; i++) {
                    if (Math.pow(2, i) == find) {
                        cnt[i] += 1;
                        break;
                    } 
                }
                num = removed;
            }
            if (num == 1) cnt[0] += 1;
        }
        
        int ans = 0;
        for (int i = 0; i < cnt.length - 1; i++) {
            if (cnt[i] % 3 == 1) ans += Math.pow(2, i);
        }
        // if the ans is -ve
        if (cnt[31] % 3 == 1) {
            ans = ~ans;
            ans++;
        }
        return ans;
    }
```

i ) Using Bit Mask(Manipulation)
The idea is to count `i`th postion set bits. Then if that can't be divisible by 3. Then _mask_ that `i`th postion to `ans` variable. Very Simple!
```java
    public int singleNumber(int[] nums) {
        int ans = 0;

        for (int i = 0; i < 32; i++) {
            // Check the position set bit can be divisible 3.
            // If not, masking that postion to ans variable!
            int cnt = 0;
            for (int num : nums) {
                // Check the `i`th position is set or not.
                if ( (num & (1 << i)) != 0) cnt++;
            }
            // if cnt can not be divisible by 3 then making `i`th position set.
            if (cnt % 3 != 0) ans = ans | (1 << i);
        }

        return ans;
    }
```

This code TC is __$O(n * 32)$___ always. It is __not same with $O(n)!$__ Think about $O(nlogn)$, when `nums.length` = $2^32$, TC can be $O(n * 32)$ So $O(n * 32)$ is _so waste!_...  We have to optimize this more!
So it is __worse__ than just _sorting_ the `nums` and find single number!.

ii )  Using Bit manipulation
Wow freaking hard ever I've met...
> Use trait of XOR (n ^ n = 0) and
>  trait of AND (n & ~n = 0)
```java
    public int singleNumber(int[] nums) {
        // to keep track of once number and twice number while loop 
        int once = 0;
        int twice = 0;
        // using trait of XOR (n ^ n = 0)
        // using trait of AND (n & ~n = 0)
        for (int num : nums) {
            // add num to once if num is not in twice.
            /** i ) if num is twice then ((num ^ num) & whatever) = 0
                meaning that erect num from once.
                ii) if num is third, then (num ^ 0) & ~num = num & ~num = 0
                bc twice has num already. So num cannot be added at once! 
            */
            once = (num ^ once) & ~twice;
            // add num to twice if num is in once.
            /** we count once and twice same number.
                i ) If we add num to "once" already then we must do not add to twice.
                    That's why we use "~once".
                ii) if nums is third, then (num ^ num) & whatever = 0,
                meaning erect num from twice.
            */
            twice = (num ^ twice) & ~once;
        }
        return once;
    }
```

After reading the my code including the comments, read this. much better to undersatnd the idea.

For each number `i` in the array:
a. `ones = (ones ^ i) & (~twos);`:        
    - `ones ^ i` XORs the current number `i` with the previous value of `ones`. This operation toggles the bits that have appeared an odd number of times, keeping the bits that have appeared twice unchanged.
    - `(~twos)` negates the bits in `twos`, effectively removing the bits that have appeared twice from consideration.
    - The `&` operation ensures that only the bits that have appeared once (after XOR) and not twice (after negating `twos`) are retained.
            
b. `twos = (twos ^ i) & (~ones);`:
    - `twos ^ i` XORs the current number `i` with the previous value of `twos`. This operation toggles the bits that have appeared an even number of times, effectively removing the bits that have appeared twice.
    - `(~ones)` negates the bits in `ones`, effectively removing the bits that have appeared once from consideration.
    - The `&` operation ensures that only the bits that have appeared twice (after XOR) and not once (after negating `ones`) are retained.

2. [201. Bitwise AND of Numbers Range](https://leetcode.com/problems/bitwise-and-of-numbers-range/), 1/Aug/24
Topics : #BitManipulation 
could solve it in 36mi using hints (__Think about common prefix__

When we read the _Bit Manipulation_ problem, we must start getting idea from the __traits__ of _bitwise operations_.

>Think about traits of AND opertator
i) num & 0 = 0
-> if 0 is including once, then after that all is 0!.
ii) num & ~num = 0
iii) num & num = num

If a number in range has `0` as `i`th bit, then after that number, `i`th bit will be always `0`. That is traits of AND operator, **(N & 0 = 0)**. So we can think for `i`th bit to keep `1` bit is so difficult. Then How can we keep `1`(set) bit on `i`th bit?.

Think about this two number. we can use trait of AND, **(N & N = N)**.  
011010....100 left  
011010....111 right
```java
    public int rangeBitwiseAnd(int left, int right) {
        int common = 0;
        for (int i = 30; i >= 0; i--) {
            // if `i`th bit of left is different from right's then break
            // else masking that bit to common.
            int bits = left & (1 << i);
            if (bits != (right & (1 << i))) break;
            common |= bits;
        }

        return common;
    }
```

Same idea looking for the common prefix.
```java
    public int rangeBitwiseAnd(int left, int right) {
        // clear the last set bit.
        // to find longest common prefix    
        while (right > left) right = right & (right - 1);
        return right;
    }
```

other
```java
public int rangeBitwiseAnd(int m, int n) {
      int i = 0; // i means we have how many bits are 0 on the right
      while(m != n){
        m >>= 1;
        n >>= 1;
        i++;  
      }
	// the m became 0011
	// then restore only same part(common prefix) like if i is 4, 00110000
      return m << i;  
    }
```