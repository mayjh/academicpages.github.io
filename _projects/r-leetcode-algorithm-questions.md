---
title: "Leetcode Algorithm Questions in Python"
excerpt: "In this blog piece, I am going to discuss my learnings from the Leetcode algorithm questions using Python as the tool."
collection: projects
---

One of my 2019 goals is to become a better programmer, which means to get a deeper understanding of algorithms. So hey, let's get rolling and learn through the Leetcode algorithm questions.

## 1. Two Sums.

**Approach:**
1. We need a dictionary *ref* with the nums as the keys and the indices of the nums as the values;
2. We can loop through the nums, getting the index *ind_i* and the current number *num_i* in each iteration;
3. The other number *other* can be calculated using the target minus the current number, we need to ensure that it is indeed the other number by checking if its index *ref[other]* equals the index of the current number *ind_i*.
4. We can check if the other number exists in the dictionary *ref*, if yes, return the index of the current number *ind_i* and that of the other number *ref[other]*.

```
def two_sums(nums, target):
  ref = dict(zip(nums, range(len(nums))))
  for ind_i, num_i in enumerate(nums):
    other = target - num_i
    if other in ref and ref[other] != ind_i:
      return [ind_i, ref[other]]
```
**Edge Cases:**

```
nums = [3,2,3]
target = 6
two_sums( nums, target)
```

In cases like the above, where the target is the sum of two elements with the same value, say 3 + 3 = 6 here. The index of the second 3 would over-write the first 3, so the other number's index would be given as the second 3's index, 2, which is correct. So we are good, the dictionary's over-write took care of it!

## 26. Remove Duplicates from Sorted Array.

**Approach:**
1. Since the array is already sorted, we can use a *while* loop to scan through the list and delete the next value when it equals the current value. The loop stops when the next value is the last value of the list *(i+1) < len(nums)*.
2. Let's start with a pointer *i* at 0 and *n_pop = 0* which counts the number of elements poped. In each iteration, the while loop would evaluate if the current value *nums[i]* equals the next value *nums[i+1]*. If yes, we pop the next value, the pointer stays where it is, and we update the length of the list to be *len(nums) - n_pop*.
3. The while statement stops when we reach the second to the last value.

```
def remove_duplicates(nums):
  n_pop = 0
  i = 0
  l = len(nums) # the length of the list
  while i < (l-1):
    if nums[i] == nums[i+1]:
        nums.pop(i+1)
        l = l - 1
    else:
        i = i+1
  return l      
```
**Edge Cases:**
```
nums = []
```
In case the list is empty, the while loop won't even begin because *l-1* would be 0.

## 27. Remove Elements.

**Approach:**
1. This one is very similar to question *26. Remove Duplicates from Sorted Array.* An intuitive approach would be to loop through the list using a while statement and pop each value that matches the target.
2. Let's start with a pointer *i* at 0 and a length variable *l = len(nums)*. In each iteration, we compare the current value *nums[i]* to the target *val*. If they equal, we pop *nums[i]*, the pointer stays where it is, and we update l by *l-1*. If they don't equal, we move to the next element by *i + 1*.
3. The while statement stops when it reaches the final element *nums[l-1]*.
```
def remove_val(nums, val):
  l = len(nums)
  i = 0
  while i < l:
    if nums[i] == val:
      nums.pop(i)
      l = l-1
    else:
      i = i+1
  return l
```

**Edge Cases:**
```
nums = []
```
In case the list is empty, the while loop would give an error when trying to read *nums[0]*. Let's take care of that by adding an if statement before the while loop.
```
def remove_val(nums, val):
  l = len(nums)
  if l == 0:
    return l
  i = 0
  while i < l:
    if nums[i] == val:
      nums.pop(i)
      l = l-1
    else:
      i = i+1
  return l
```

## 35. Search Insert Position

**Approach:**
1. If we choose brutal force, we can loop through the list, move forward if the current value is greater than the target, stop if the current value is smaller or equal to the target. Let's actually try a fancier trick -- divide and conquer.
2. Let's look at the entire list, with *l = 0* as the left boundary, *r = len(nums) - 1* as the right boundary, then we can compare the *target* to the number in the middle *nums[m]*, where *m = (l+r) // 2*.
3. If the *target* is greater than *nums[m]*, then we zoom in to the right half of the list. Else, we zoom in to the left half of the list.
4. Step 3 would be done recursively, and it'd stop when there's at least one gap between *l* and *r*, i.e., *(r - l) > 1*, because if we ever get to adjacent indices, i.e., *(r - l) == 1*, *m* would always evaluate to *l*, leading to a while loop that runs forever.
5. Once we are out of the while loop, we can now judge the situation and return an index. If the *target* is smaller than or equal to *nums[l]*, then we need to return *l*. Else, if the *target* is smaller than or equal to *nums[r]*, we need to return *r*. Else, we need to return *r+1*.

```
def search_insert(nums, target):
  l = 0
  r = len(nums) - 1
  while (r - l) > 1:
    m = (l+r) // 2
    if target > nums[m]:
      l = m + 1
    else:
      r = m
  if target <= nums[l]:
    return l
  elif target <= nums[r]:
    return r
  else:
    return r+1
```  

## 1002. Find Common Characters

**Approach:**  

1. find shared elements

2. count shared elements and save in a dictionary

3. construct the list of elements using the dictionary

```
class Solution:
    def commonChars(self, A: List[str]) -> List[str]:
        
        # find shared elements
        l = set(A[0]).intersection(set(A[1]))
        for i in range(2,len(A)):
            l = l.intersection(A[i])
        
        # use a dictionary to save counts of each shared element
        d = dict()
        for l_i in l:
            d[l_i] = 100
            for str_i in A:
                d[l_i] = min(str_i.count(l_i), d[l_i])
        
        # construct shared list from the dictionary
        shared = []
        for key, value in d.items():
            while value > 0:
                shared.append(key)
                value = value - 1
        return(shared)   
```

Below is a really elegant solution from [Leetcode](https://leetcode.com/problems/find-common-characters/discuss/247560/Python-1-line). User *lee215* used the Counter data type.

```
from collections import Counter

class Solution:
    def commonChars(self, A: List[str]) -> List[str]:
        
        # build counter
        c = Counter(A[0])
        for i in range(1,len(A)):
            c &= Counter(A[i])
        
        # unpack and return elements
        return ( list( c.elements() ) )
```