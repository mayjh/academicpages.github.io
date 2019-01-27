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
