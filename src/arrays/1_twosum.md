# 1. Two Sum

## [Problem link](https://leetcode.com/problems/two-sum/)

## Problem description

```
Given an array of integers nums and an integer target,
return indices of the two numbers such that they add up to target.

You may assume that each input would have exactly one solution,
and you may not use the same element twice.

You can return the answer in any order.


Example 1:
Input: nums = [2,7,11,15], target = 9
Output: [0,1]
Explanation: Because nums[0] + nums[1] == 9, we return [0, 1].
Example 2:

Input: nums = [3,2,4], target = 6
Output: [1,2]
Example 3:

Input: nums = [3,3], target = 6
Output: [0,1]


Constraints:

2 <= nums.length <= 104
-109 <= nums[i] <= 109
-109 <= target <= 109
Only one valid answer exists.


Follow-up: Can you come up with an algorithm that is less than O(n2) time complexity?
```

## Comments

Intuitively, one thinks of a double for loop because this is one way to check all numbers against each other.

We loop over all elements and their indices with .enumerate() and check that indices are not equal, but that the sum of the two numbers are equal to the target. Sure enough, something like this works:

```rust
pub fn two_sum(nums: Vec<i32>, target: i32) -> Vec<i32> {
    for (i1, n1) in nums.iter().enumerate() {
        for (i2, n2) in nums.iter().enumerate() {
            if i1 != i2 && n1 + n2 == target {
                return vec![i1 as i32, i2 as i32];
            }
        }
    }

    unreachable!("Solution is expected to exist")
}


fn main(){
	assert_eq!(two_sum(vec![2, 7, 11, 15], 9), vec![0, 1]);
}
```

However, this is not a very efficient solution because it is `O(n^2)` due to the double for loop.

## Solution

We can make this `O(n)` because when we have done a single pass, we have seen all numbers once which actually is enough. The approach we'll use is NOT to look ahead of our current number, but rather backwards to what we have already seen. This requires us to store numbers somewhere when we iterate. This <q>somewhere</q> needs to have a lookup time of `O(1)` for it to be efficient so we'll use a HashMap.

Let's think about this with the example of `nums = [2, 7, 11, 15]` and `target = 9`. We start iterating over nums and get the first number `2`. We realize that if a `7` (`9` - `2`) would have been in our HashMap, we'd be done. Since our HashMap is empty, we have no match but instead add `2` along with its index `0` to the HashMap.

We then reach `7`. The target number we'd like is `9` - `7` = `2`. We check our HashMap and see that `2` actually exists. We can extract the index of `2` from the HashMap and we also have the index of `7` since we use `.enumerate()`.

```rust
use std::collections::HashMap;

pub fn two_sum(nums: Vec<i32>, target: i32) -> Vec<i32> {
    let mut hmap: HashMap<i32, i32> = HashMap::new();

    for (i, n) in nums.iter().enumerate() {
        let diff = target - n;

        match hmap.get(&diff) {
            Some(j) => return vec![*j, i as i32],
            None => hmap.insert(*n, i as i32),
        };
    }

    unreachable!("Solution is expected to exist")
}

fn main() {
    let sol = two_sum(vec![2, 7, 11, 15], 9);

    println!("{:?}", sol);
}
```

## Even Better Solution
We can do better. We see that the length of `nums` can be `<=10^4`, which is rather large. Our HashMap does not infinite capacity, but needs to re-allocate when it reaches its capacity. If we pre-allocate the capacity to be the length of `nums`, we can avoid repeated re-allocations. Essentially, we only have to change to:

```rust,noplayground
let mut hmap: HashMap<i32, i32> = HashMap::with_capacity(nums.len());
```
