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

`result = nums[0]^nums[1]^...^nums[N-1]`

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

The same code using $$XOR$$ above still works when $$k$$ is even and $$m$$ is odd \(or viceversa!\).  What about $$k=3$$ and $$m=1$$? In this case we need to keep track of more variables as we iterate through the list.  Since bit operations act on each bit independently, lets consider the lists with only $$1's$$ and $$0's$$. Every iteration we'd like to count the number of 1's mod 3. To do this we track,

* $$a = 1$$ if $$1$$ has appearred once
* $$b = 1$$ if $$1$$ has appearred twice
* if $$a = 1$$ and $$b = 1$$ reset $$a$$ and $$b$$

If a 1 has appeared once, and if number that track if a 1 has appeared $$l$$ times such that an odd number of times and a number that tracks if a 1 has appeared an odd multiple of 2 `[1,1,0,1,0,0,1].`

| nums | a | b |
| --- | --- | --- |
| 1 | 0 | 0 |
| 1 | 0 | 1 |
| 0 | 1 | 0 |
| 1 | 1 | 1 |
| 0 | 0 | 0 |
| 0 | 0 | 1 |
| 1 | 1 | 0 |

```python
def FindUniqueMember(nums):
    a = 0, b = 0
    for n in nums:
        # 1 if a is 1 and the next number in the list is 1.
        # otherwise, remain the same
        b |= a & n

        # 1 if a 1 has appeared an odd number of times
        a ^= n

        # when a = 1 and b = 1, we have see three 1's so 
        # we reset a and b
        a &= ~(a&b), b &= ~(a&b)

    # now a stores all the bit positions that have appeared     
    #l times where l mod 3 = 1
    return a
```

#### Follow up Part 2

Now consider we have a list of integers in which every element appears twice except for _two_.  Find the two integers. The order of the result is not important.

We can extend the power of $XOR$. Let the two unique elements in `nums` be $$a$$ and $$b$$. When we $$XOR$$ every element of `nums` we get `a^b.` At every bit position this quantity tells us whether $$a$$ and $$b$$ are the same or different. Since $$a\neq b$$, $$a^b \neq 0$$.  If we just find that bit position where $$a$$ and $$b$$ are different, we can split `nums` into two lists with each list containing every integer twice except for one.

```python
def FindUniqueMembers(nums): 
    # Each bits of XOR of all the elements in nums means:
    # if a bit is 0 then a and b have the same bit
    # if a bit is 1 then a and b are different for that bit
    axorb = 0
    for n in nums:
        axorb ^= n

    # Since a and b are unique axorb != 0.
    # Find the first 1 bit and clear the rest.
    # We can use this bit to split nums into two list. 
    # Each list has all elements appearing twice
    # and one element appearing once
    firstNonZeroBit = (axorb & (axorb - 1)) ^ axorb
    result = [0, 0]
    for n in nums:
        if n & firstNonZeroBit == 0:
            result[0] ^= n
        else:
        result[1] ^= n

    return result
```

