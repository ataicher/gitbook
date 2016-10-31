###Finding Unique Member###

Given a list ```nums``` of integers.  Every integer appears twice except for one.  Find the single integer.

####Naive solution####

To start as naively as possible. For each element in the list, I loop through the entire list to see if it is unique

```python
def FindUniqueMember(nums):
    for n in nums:
        found = False
        for m in nums:
            found = (m == n)
        if not found:
            return n
```

This algoirthm has time-complexity $$O(N^2)$$ and space complexity $$O(1)$$. Where $$N$$ is the size of ```nums```.

####Hash table####

I could use a hash table to search the list in constant time

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

####Bit Manipulation####

I do not know how to find the intuition to follow this route.  It simply takes a certain familiarity with the power of bit manipulation. Consider all the possible pairwise bit operations ```&, ^, |```.  For an integer x:

```python
x & x = x
x | x = x
x ^ x = 0
```

The $$XOR$$ operater, ```^``` looks like it might be useful since we are trying to eliminate duplicates from a list.  If we could just rearange ```nums``` so that duplicate integers are next to each other with the unique member at the end, then chaining all the members of ```nums``` with $$XOR$$ would give us the unique integer. Fortunately, $$XOR$$ is both commutative and _associative_.  To see this, note that all bitwise operators act on each bit independently and build the truth table:

| a | b | c | (a^b)^c | a^(b^c) | 
|---|---|---|:-------:|:-------:| 
| 0 | 0 | 0 | 0       | 0       | 
| 0 | 0 | 1 | 1       | 1       | 
| 0 | 1 | 0 | 1       | 1       | 
| 0 | 1 | 1 | 0       | 0       | 
| 1 | 0 | 0 | 1       | 1       | 
| 1 | 0 | 1 | 0       | 0       | 
| 1 | 1 | 0 | 0       | 0       |  
| 1 | 1 | 1 | 1       | 1       |

So the result of chaining $$XOR$$ operations is _independent_ of order.

```python
def FindUniqueMember(nums):
    result = 0
    for n in nums:
        result ^= n
    return result
```

This algoirthm has time-complexity $$O(N)$$ and space complexity $$O(1)$$!

