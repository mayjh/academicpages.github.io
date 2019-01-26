---
title: "Leetcode Algorithm Questions in Python"
excerpt: "In this blog piece, I am going to discuss my learnings from the Leetcode algorithm questions using Python as the tool."
collection: projects
---

One of my 2019 goals is to become a better programmer, which means to get a deeper understanding of algorithms. So hey, let's learn through the Leetcode algorithm questions.

## 1. Two Sums.

**Generic Approach:**
1. We need a dictionary *ref* with the nums as the keys and the indices of the nums as the values;
2. We can loop through the nums, getting the index *ind_i* and the current number *num_i* in each iteration;
3. The other number *other* can be calculated using the target minus the current number, we need to ensure that it is indeed the other number by checking if its index *ref[other]* equals the index of the current number *ind_i*.
4. We can check if the other number exists in the dictionary *ref*, if yes, return the index of the current number *ind_i* and that of the other number *ref[other]*.

```Python
def two_sums(nums, target):
  ref = dict(zip(nums, range(len(nums))))
  for ind_i, num_i in enumerate(nums):
    other = target - num_i
    if other in ref and ref[other] != ind_i:
      return [ind_i, ref[other]]
```
**Edge Cases:**

In cases like below, where the target is the sum of two elements with the same value, say 3 + 3 = 6 here. The index of the second 3 would over-write the first 3, so the other number's index would be given as 2, which is correct.

```Python
nums = [3,2,3] 6
```

## 
