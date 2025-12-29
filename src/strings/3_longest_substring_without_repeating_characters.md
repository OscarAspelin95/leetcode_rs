# 3. Longest Substring Without Repeating Characters

## [Problem link](https://leetcode.com/problems/longest-substring-without-repeating-characters/)

## Problem description

```json
Given a string s, find the length of the longest substring
without duplicate characters.

Example 1:

Input: s = "abcabcbb"
Output: 3
Explanation: The answer is "abc", with the length of 3.
Note that "bca" and "cab" are also correct answers.
Example 2:

Input: s = "bbbbb"
Output: 1
Explanation: The answer is "b", with the length of 1.
Example 3:

Input: s = "pwwkew"
Output: 3
Explanation: The answer is "wke", with the length of 3.
Notice that the answer must be a substring, "pwke"
is a subsequence and not a substring.


Constraints:

0 <= s.length <= 5 * 104
s consists of English letters, digits, symbols and spaces.
```

## Comments

We first need to think about the constraints and edge cases here:

- If `length == 0`, we have no substring.
- If `length == 1`, we have a substring of length 1.

Otherwise, we need to do something smart. The brute force approach would be to check every possible substring. However, this is not feasible, even for relatively short strings.

We are concerned with uniqueness in a range. E.g., in a given range, we cannot have duplicates. This tells me we should use a HashSet to keep track of duplicates. As for the algorithm itself, We could start a dynamic window at each position and increment a pointer as long as we have unique values. For each window, we'll use a HashSet to keep track of duplicates.

E.g., we start at the beginning and add the first character to our HashSet.

```
let mut longest: usize = 0

"abcabcbb"
 |

seen = {a}
```

We then increment by one. `b` is next, and is not in our HashSet so we'll add it.

```
let mut longest: usize = 0

"abcabcbb"
 ||

seen = {a, b}
```

We increment again by one. `c` is next, and is not in our HashSet so we'll add it.

```
longest = 0

"abcabcbb"
 |||

seen = {a, b, c}
```

We increment a fourth time. `a` is next, and is in our HashSet. We have found one substring. We'll calculate its length and update our longest so far seen substring. We also reset seen.

```
longest = 0

"abcabcbb"
 ||||

seen = {a, b, c}


longest = max(longest, len(seen)) = 3
seen = {}

```

We bump our pointer to start at index 1 (`b`). Then we continue with this logic until we have reached the end of the string.

```
longest = 3

"abcabcbb"
  |

seen = {b}
```

## Solution

```rust
use std::collections::HashSet;

pub fn length_of_longest_substring(s: String) -> i32 {
    if s.len() <= 1 {
        return s.len() as i32;
    }

    let mut longest: usize = 0;
    let s_vec = s.as_bytes();
    let mut i: usize = 0;

    while i <= s.len() - 1 {
		let mut hset: HashSet<u8> = HashSet::new();
        hset.insert(s_vec[i]);
    
        let mut j: usize = i + 1;

        while j <= s.len() - 1 {
            match hset.contains(&s_vec[j]) {
                true => {
                    longest = longest.max(hset.len());
                    break;
                }
                false => {
                    hset.insert(s_vec[j]);
                    j += 1;
                }
            }
        }

        longest = longest.max(hset.len());

        i += 1;
    }

    longest as i32
}

fn main(){
	assert_eq!(length_of_longest_substring("abcabcbb".to_string()), 3);
}
```

## Even Better Solution
We can make this solution rather memory efficient and a bit more runtime efficient. If we define our HashSet once, and reuse it across iterations (but make sure to clean it first) we can save some memory. We can also improve the runtime by skipping the last iterations based on the longest substring seen so far. E.g., if our longest substring is 4 and we only have 3 characters to start from and iterate over, we cannot possibly find a longer substring.

```rust
use std::collections::HashSet;

pub fn length_of_longest_substring(s: String) -> i32 {
    if s.len() <= 1 {
        return s.len() as i32;
    }

    let mut longest: usize = 0;
    let s_vec = s.as_bytes();
    // Reuse the same hashset.
    let mut hset: HashSet<u8> = HashSet::new();

    let mut i: usize = 0;

    while i <= s.len() - 1 {
    	// Clear before each iteration.
        hset.clear();
        
        // E.g., if our longest substring is 4 and we only have 3 characters left
        // to check, we cannot possibly find a longer substring.
        if i >= s.len() - longest {
            break;
        }

        hset.insert(s_vec[i]);
        let mut j: usize = i + 1;

        while j <= s.len() - 1 {
            match hset.contains(&s_vec[j]) {
                true => {
                    longest = longest.max(hset.len());
                    break;
                }
                false => {
                    hset.insert(s_vec[j]);
                    j += 1;
                }
            }
        }

        longest = longest.max(hset.len());

        i += 1;
    }

    longest as i32
}

fn main(){
	assert_eq!(length_of_longest_substring("abcabcbb".to_string()), 3);
}
```
