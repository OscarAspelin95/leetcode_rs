# 58. Length of Last Word

## [Problem link](https://leetcode.com/problems/length-of-last-word/)

## Problem description

```json
Given a string s consisting of words and spaces, return the length of the
last word in the string.

A word is a maximal substring consisting of non-space characters only.



Example 1:

Input: s = "Hello World"
Output: 5
Explanation: The last word is "World" with length 5.
Example 2:

Input: s = "   fly me   to   the moon  "
Output: 4
Explanation: The last word is "moon" with length 4.
Example 3:

Input: s = "luffy is still joyboy"
Output: 6
Explanation: The last word is "joyboy" with length 6.


Constraints:

1 <= s.length <= 104
s consists of only English letters and spaces ' '.
There will be at least one word in s.
```

## Comments

The intuitive solution here would be to first trim spaces from the string. This ensures the last character is not a space. Then, we'd split the string by `" "` and get the length of the last string from the resulting `Vec`. We are guaranteed at least one word.

## Solution

The following solution works and is relatively efficient for shorter strings.

```rust
pub fn length_of_last_word(s: String) -> i32 {
    let trimmed = s.trim_end().split(" ").last().unwrap_or("");

    trimmed.len() as i32
}

fn main() {
    assert_eq!(length_of_last_word(" This is a sentence    ".to_string()), 8);
}
```

However, we might run into performance issues for very long strings. The reason is `.last()`, which consumes the entire iterator.

## Even Better Solution

An even better approach would be to start from the end of the string. We'll have a pointer that decrements until we find a non-space character. At that point, we know we have the end of the last word. Then, we'll keep decrementing until we find a space again, whilst simultaneously counting the number of decrements (which in the end is out length). The benefit here is that we use very little memory (only keep track of the pointer).

We have an edge case here, which is if the first character is a non-space. If we define our right pointer `r` to be of type `usize`, we cannot decrement below 0. To solve this, we'll use `while r > 0` and finally check if `r == 0`. If so, we also check if the character is a space or not.

```rust
pub fn length_of_last_word(s: String) -> i32 {
    let mut r = s.len() - 1;

    let bytes = s.as_bytes();

    while r > 0 && bytes[r] == b' ' {
        r -= 1;
    }

    let mut c: i32 = 0;

    while r > 0 && bytes[r] != b' ' {
        c += 1;
        r -= 1;
    }

    if r == 0 && bytes[r] != b' ' {
        c += 1
    }

    c
}

fn main(){
	assert_eq!(length_of_last_word("A".to_string()), 1);
	assert_eq!(length_of_last_word(" A ".to_string()), 1);
	assert_eq!(length_of_last_word(" A".to_string()), 1);
	assert_eq!(length_of_last_word("A".to_string()), 1);
	assert_eq!(length_of_last_word(" Word ".to_string()), 4);
    assert_eq!(length_of_last_word(" This is a sentence    ".to_string()), 8);
}
```
