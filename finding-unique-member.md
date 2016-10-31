### Finding Unique Member

Given a list `nums` of integers.  Every integer appears twice except for one.  Find the single integer.

#### Naive solution

To start as naively as possible. For each element in the list, I loop through the entire list to see if it is unique:

```python
def FindUniqueMember(nums):
    for n in nums:
        found = False
        for m in nums:
            found = (m == n)
        if not found:
            return n
```

This algoirthm has time-complexity $$O(N^2)$$ and space complexity $$O(1)$$. Where $$N$$ is the size of `nums`.

#### Hash table

I could use a hash table to search the list in constant time:

```python
def FindUniqueMember(nums):
    S = set()
    for n in nums:
        if n in S:
            del S[n]
        else:
            S.add(n)
    return S.pop()
```

This algoirthm has time-complexity $$O(N)$$ but increased space complexity $$O(N)$$.

#### Bit Manipulation

I do not know how to find the intuition to follow this route.  It simply takes a certain familiarity with the power of bit manipulation. Consider all the possible pairwise bit operations on integer x:

```python
x & x = x
x | x = x
x ^ x = 0
```

The $$XOR$$ operater, `^` looks like it might be useful since we are trying to eliminate duplicates from a list.  If we could just rearange `nums` so that duplicate integers are next to each other with the unique member at the end, then chaining all the members of `nums` with $$XOR$$,
 
```result = nums[0]^nums[1]^...^nums[N-1]```

would give us the unique integer. Fortunately, $$XOR$$ is both commutative and _associative_.  To see it is associative, note that all bitwise operators act on each bit independently and build the truth table:

| a | b | c | \(a^b\)^c | a^\(b^c\) |
| --- | --- | --- | --- | --- |
| 0 | 0 | 0 | 0 | 0 |
| 0 | 0 | 1 | 1 | 1 |
| 0 | 1 | 0 | 1 | 1 |
| 0 | 1 | 1 | 0 | 0 |
| 1 | 0 | 0 | 1 | 1 |
| 1 | 0 | 1 | 0 | 0 |
| 1 | 1 | 0 | 0 | 0 |
| 1 | 1 | 1 | 1 | 1 |

So the result of chaining $$XOR$$ operations is _independent_ of order.  

```python
def FindUniqueMember(nums):
    result = 0
    for n in nums:
        result ^= n
    return result
```

This algoirthm has time-complexity $$O(N)$$ and space complexity $$O(1)$$!

#### Follow up

Let's generalize the problem to a list where all integers appear $$k$$ times except for one which appears $$m$$ times.

The same code using $$XOR$$ above still works when $$k$$ is even and $$m$$ is odd (or viceversa!).  What about $$k=3$$ and $$m=1$$? In this case we need to keep track of more variables as we iterate through the list.  Since bit operations act on each bit independently, lets consider the lists with only $$1's$$ and $$0's$$. Every iteration we'd like to count the number of 1's mod 3. To do this we track,

* $$a = 1$$ if $$1$$ has appearred once
* $$b = 1$$ if $$1$$ has appearred twice
* if $$a = 1$$ and $$b = 1$$ reset $a
  if a 1 has appeared once, and if  number that track if a 1 has appeared $$l$$ times such that an odd number of times and a number that tracks if a 1 has appeared an odd multiple of 2 `[1,1,0,1,0,0,1].`  

| nums | a | b |
|---|:-:|:-:|
| 1 | 0 | 0 |
| 1 | 0 | 1 |
| 0 | 1 | 0 |
| 1 | 1 | 1 |
| 0 | 0 | 0 |
| 0 | 0 | 1 |
| 1 | 1 | 0 |

