---
layout: post
title:  Hash tables
date:   2024-06-18 18:51:29 +1000
categories: LeetCode
--- 

> a data structure that provides a mapping from keys to values using a technique called _hashing_.

Key(name) | Value(fav color)
--------|---------
"Jihye" | "blue"
"William" | "green"
"Moong" | "blue"

- key-value pairs.
- ==H(key) -> returns an index, and HashTable(index) = value==
not H(key) -> returns value!
HT(HF(key)) => returns value.
- Keys must be unique, but values can be repeated.
- often used to track item frequencies.
- key-value pairs can be any type like string, numbers and _object_!
- ==Insertion, lookup and removal time is $O(1)$ using a hash function as a way to _index into a hash table(fancy word of array)_ given having a good _uniform(not collided)_ hash function!==

### Hash function, H(x)
> a function that _maps_ a key 'x' to a whole number _in a fixed range_. so always with operation (Modulo range+1)

- ex) H(x) = $(X^2 - 6x + 9)$ mod $10$
	all integer keys to the range $[0,9]$ 

Properties
- If H(x) = H(y), then objects x and y _might be equal_, but if H(x) != H(y), then x and y are _certainly not equal_.
>We can use this as an advantage to _speedup object comparisons_.
>Instead of comparing x and y directly, first compare their hash values, and only if the hash values match, do compare x and y
- H(x) must be __deterministic__.
>If H(x) = y, then H(x) must always produce y and never another value.
- try very hard to make uniform hash functions to minimize the number of hash collisions.

### Hash collision
> when two objects x, y hash to the same value (i.e H(x) = H(y))

### Hashable
> To make a _key_ of type T _hashable_, the keys used in hash table are immutable(unchanged) data types for the deterministic property.

## Hash collision resolution

### Separate chaining
By maintaining a data structure(usually a linked list) to hold all the different values which hashed to a particular value.

> When collision occurs, just add that bucket(hash value) using linked list, array whatever.


### Open addressing
By finding another place within the hash table for the object to go by offsetting it from the position to which it hashed to.

> When collision occurs, try another position in the hash table by offsetting the current position subject to a _probing sequence P(x)_. _Keep doing_ this until an unoccupied slot is found!

#### Probing sequences, P(x)
- Linear probing
	P(x) = $ax + b$ where a, b are constants
- Quadratic probing
	P(x) = $ax^2 + bx + c$
- Double hashing
	P(x) = $x * H2(k)$, where H2(k) is a secondary hash function
- Pseudo random number generator
	P(k, x) = $x * RNG(H(k), x)$, where RNG is seeded with H(K)

Most P(x) can produce a cycle. so when trying to insert a key-value pair and all the buckets(slots) on the cycle are occupied, then get stuck in an _infinite loop._
The consensus is that don't handle this cycle issue, instead avoiding, just ==restrict the domain of P(x) to produce a cycle of exactly length $N$!==

How to generate full cycle, length N?
> in P(x) = ax (linear probing), when a and N are relatively prime. Hence when Greatest Common Denominator GCD(a,N) = 1! so if GCD(a,n) != 1, then generate bad cycle!
> So common choice is P(x) = 1x, since GCD(N,1) = 1 no matter the choice of N(table size) 

Therefore open addressing is very sensitive. so if using separate chaining, don't need to worry a cycle issue. 

#### Load factor = items in table / size of table

Open addressing is good($O(1)$) only the load factor is kept below a certain _threshold_. it means once load factor > threshold, need to grow the table size! 


1. [380. Insert Delete GetRandom O(1)](https://leetcode.com/problems/insert-delete-getrandom-o1/), 19/Jun/24
Topic : Array, Hash Table, Math, Design, Randomized
couldn't solve this within 30mi.
[My solution](https://leetcode.com/problems/insert-delete-getrandom-o1/solutions/5336073/my-understanding-process-i-hope-this-is-helpful-to-you-like-me)

# Intuition

First, we have to come up with data structures(Abstract data type) where insert, delete time is O(1)O(1)O(1).  
In java, I'm gonna take HashMap

- Insert  
    HashMap has Key-Value pairs.  
    so Key must be val(inserted item).  
    put(val, ?)  
    and value can be whatever.

```kotlin
if (containsKey(val) == true ) return false;
put(val, 0);
return true;
```

- Remove

```kotlin
if (containsKey(val) == false ) return false;
remove(val);
return true;
```

- Get random  
    we know rand() which generate random integer.  
    ==_How to associate this rand() to HashMap?_==

# Approach

Consider this.

- get(rand() % size()) -> Fail. cuz we have to use key(−232-2^32−232<= val <= 231−12^31 -1231−1 ) Not index!

Then... **we want to use rand() as an index.**  
How about using array combined with HashMap?  
we can use array as a Hash Table!  
Remember? we yet don't use the value of pair.

|Hash Table(index, key(val))|key|value|
|---|---|---|
|0, 102|102|0|
|1, 674|674|1|
|2, 5|5|2|
|3, 6700|6700|3|

And we don't know how much call insert.  
so We take ArrayList not Linked list cuz we have to also get val with O(1).  
**But we know already only ArrayList.add(val) add it at the last with O(1).  
and only ArrayList.remove(last index) is O(1).**

So we can improve the previous functions.

- Insert

```kotlin
if (containsKey(val) == true ) return false;
put(val, 0);
return true;
```

improved version(main diffenrence is using value of pair as an index from ArrayList for combining each other.)

```kotlin
if (containsKey(val) == true) return false;
ArrayList.add(val);
put(val, ArrayList.size -1);
return true;
```

- Remove

```kotlin
if (containsKey(val) == false ) return false;
remove(val);
return true;
```

->

```kotlin
if (containsKey(val) == false ) return false;
int i = get(val); // return ArrayList index.
//Because we don't care about insert orders!
//Overwrite ArraList[i].
ArrayList[i] = ArrayList[lastIndex]
//change also HashMap for index concurrency.
replace(ArrayList[i](was ArrayList[lastIndex]), i)
ArrayList.remove(lastIndex);
remove(val);
return true;
```

- Get random  
    `get(rand() % size())` -> Fail. cuz we have to use key(−231-2^31−231 <= val <= 231−12^31 -1231−1 ) Not index!

But Now, we can use rand() for index of ArrayList!!

```kotlin
return ArrayList[rand() % ArrayList.size]
```

# Complexity

- Time complexity: O(1)
    
- Space complexity: O(n), cuz ArrayList is used
    

# Code

```csharp
class RandomizedSet {

    ArrayList<Integer> hashTable;
    HashMap<Integer, Integer> map;
    java.util.Random rand = new java.util.Random();

    public RandomizedSet() {
        hashTable = new ArrayList<Integer>();
        map = new HashMap<Integer, Integer>();
    }
    
    public boolean insert(int val) {
        if (map.containsKey(val) == true) return false;
        // Insert val to hashTable in last Index.
		// And Store that index to value of key(val) in the map.
        hashTable.add(val);
        map.put(val, hashTable.size() - 1);
        return true;
    }
    
    public boolean remove(int val) {
        if (map.containsKey(val) == false ) return false;
        // get ArrayList index.
        int i = map.get(val);
        //Because we don't care about insert orders!
        //Overwrite ArraList[i] with ArrayList[lastIndex].
        int lastNum = hashTable.get(hashTable.size() - 1);
        hashTable.set(i, lastNum); 

        //change also HashMap for index concurrency.
        map.replace(lastNum, i);
        
        hashTable.remove(hashTable.size() - 1);
        map.remove(val);
        return true;
    }
    
    public int getRandom() {
        return hashTable.get(rand.nextInt(hashTable.size()));
    }
}

```

2. [49. Group Anagrams](https://leetcode.com/problems/group-anagrams/), 29/Jun/24
Topics : Array, Hash Table, String, Sorting
couldn't think about the main idea...

The intuition is to group words that are anagrams of each other together. Anagrams are words that have the __same characters but in a different order__.
So we can think _"How about making same order using Sorting?"_

pseudo code
for (String str : strs)
    1. make str to char array temporally.
    2. Sort it.
    3. Find it to HashMap,
        if (not in hashMap)
            Add it to Map with value is index of group(answer) Array.
            Add groupd Array
            cnt++;(increase next new index of group Array)
        else 
            Add it Group Array uisng hashMap value.

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        List<List<String>> group = new ArrayList<>();
        HashMap<String, Integer> map = new HashMap<String, Integer>();
        int next = 0;
        
        for (int i = 0; i < strs.length; i++) {
            char[] temp = strs[i].toCharArray();
            Arrays.sort(temp);
            String str = new String(temp);

            if (map.containsKey(str)) {
                group.get(map.get(str)).add(strs[i]);
            }
            else {
                map.put(str, next);
                group.add(new ArrayList<>());
                group.get(next).add(strs[i]);
                next++;
            }
        }

        return group;
    }
}
```

Other smarter people write the code like this.
```java
public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> map = new HashMap<>();
        
        for (String word : strs) {
            char[] chars = word.toCharArray();
            Arrays.sort(chars);
            String sortedWord = new String(chars);
            
            if (!map.containsKey(sortedWord)) {
                map.put(sortedWord, new ArrayList<>());
            }
            
            map.get(sortedWord).add(word);
        }
        
        return new ArrayList<>(map.values());
    }
```
They make Map's value to the `List<string>` without using `next index to keep track of count of group`! So brilliant!

TC : $O(m*nlogn)$ 
SC : $O(n)$

3. [128. Longest Consecutive Sequence](https://leetcode.com/problems/longest-consecutive-sequence/), 29/Jun/24
Topics : Array, Hash Table, Union Find
couldn't solve it with 60mi even though using pseudo code in discussion...

My first thought was whenever iterate the num of nums, check `num - 1` and `num + 1` for finding consecutive sequence. But my thought was stuck at _what if there is 99, 101, and I encounter 100, which I have to choose? 99? 101?_ I had to connect them both! like ii) method... but I couldn't think about it... OR I have to choose only one `num - 1` or `num + 1` always! __Only one direction!__ that method is i) method (choose `num - 1`!).
And the key point to get rid of redundant `for loop` is only starting to _first number_. How to know if that is first or not?
Check whether hashTable contains `num - 1` or `not`!

### i) Using HashMap only

```java
class Solution {
    public int longestConsecutive(int[] nums) {
        HashMap<Integer, Integer> map = new HashMap<Integer, Integer>();
        int maxLen = 0;
                
        for(int num : nums) map.put(num, num);
        
        for(int num : map.keySet()) {
            // Check a element can be first number of consecutive or not.
            // If there is no (num-1), just keep their values itself.
            if (map.containsKey(num - 1)) {
                // Connect num to (num - 1)
                map.put(num, num - 1);            
            }
        }

        for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
            int key = entry.getKey();
            int value = entry.getValue();
            int cnt = 1;
            // if it is a first element of sequence, start to count the sequence.   
            if(key == value) {
                while (map.containsKey(++key)) cnt++;                    
            }
            maxLen = Math.max(maxLen, cnt);
        }
        
        return maxLen;
    }
}
```

But my code is not good bc we can integrity two for loop in one loop like below.
I thought it will create the redundant checks. but it is not! bc only first element can make while loop! it is not dup! only O(n) time!

```java
class Solution {
    public int longestConsecutive(int[] nums) {
        HashMap<Integer, Integer> map = new HashMap<Integer, Integer>();
        int maxLen = 0;
                
        for(int num : nums) map.put(num, num);
        
        for(int num : map.keySet()) {
            // Check a element can be first number of consecutive or not.
            if (!map.containsKey(num - 1)) {
                int cnt = 1;

                while (map.containsKey(++num)) cnt++;
                maxLen = Math.max(maxLen, cnt);            
            }
        }

        return maxLen;
    }
}
```

### Using HashMap and UnionFind
```java
public int longestConsecutive(int[] nums) {
        Map<Integer,Integer> ranges = new HashMap<>();
        int max = 0;
        for (int num : nums) {
            if (ranges.containsKey(num)) continue;
            
            // 1.Find left and right num
            // If there is left or right num in hashMap, its value has length of consecutive sequence for now(until now? before? anywawy).
            int left = ranges.getOrDefault(num - 1, 0);
            int right = ranges.getOrDefault(num + 1, 0);
            // Connect their hands with itself(num) :)  
            int sum = left + right + 1;
            // Get maxLen
            max = Math.max(max, sum);
            
            // 2.Union by only updating boundary
            // Leave middle k-v dirty to avoid cascading update
            // We can focus on boundary values only. 
            ranges.put(num - left, sum);
            ranges.put(num + right, sum);
            ranges.put(num, sum); // Keep each number in Map to de-duplicate
        }
        return max;
    }
```