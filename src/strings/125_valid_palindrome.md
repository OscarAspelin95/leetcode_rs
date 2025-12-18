# 125. Valid Palindrome

## [Problem link](https://leetcode.com/problems/valid-palindrome/)

## Problem description

```
A phrase is a palindrome if, after converting all uppercase letters into lowercase
letters and removing all non-alphanumeric characters, it reads the same forward
and backward. Alphanumeric characters includeletters and numbers.

Given a string s, return true if it is a palindrome, or false otherwise.



Example 1:

Input: s = "A man, a plan, a canal: Panama"
Output: true
Explanation: "amanaplanacanalpanama" is a palindrome.
Example 2:

Input: s = "race a car"
Output: false
Explanation: "raceacar" is not a palindrome.
Example 3:

Input: s = " "
Output: true
Explanation: s is an empty string "" after removing non-alphanumeric characters.
Since an empty string reads the same forward and backward, it is a palindrome.


Constraints:

1 <= s.length <= 2 * 105
s consists only of printable ASCII characters.
```

## Comments

There are multiple ways of solving this problem. One intuitive approach is to filter out non-alphanumeric characters and map the remaining characters to lowercase. Then, we could compare the filtered string with its reverse. There are a few performance issues with this:

- We first to a pass through the string for filtering and mapping.
- We then have to reverse the resulting string for comparison.

However, lets try to do this anyways because this let's us use `filter_map` which is awesome.

```rust
pub fn is_palindrome(s: String) -> bool {
    if s.len() <= 1 {
        return true;
    }

    let pruned: String = s
        .chars()
        .filter_map(|c| match c.is_alphanumeric() {
            true => Some(c.to_ascii_lowercase()),
            false => None,
        })
        .collect();

    let pruned_reverse: String = pruned.chars().rev().collect();

    pruned == pruned_reverse
}


fn main() {
    println!("{}", is_palindrome("A man, a plan, a canal: Panama".to_string()));
}
```

With `filter_map` we can filter out non-alphanumeric characters and map the remaining characters to lowercase at the same time. The secret is that returning `None` does the filtering, whilst returning `Some` with an optinal mapping keeps the character.

## Solution

Let's skip reversing the string. A better solution is a two pointer approach. If we start at indices `0` and `s.len() - 1` with our left and right pointers, we can compare the characters at these indices. If they match, we move both pointers one step inwards. If at any point the characters don't match, we return `false`.

Consider the case of a pruned string `abba`. Our layout would look something like this in the beginning.

```
a b b a
^     ^
l     r

```

where `l = 0` and `r = s.len() - 1 = 3`. We see that `s[l] = a` and `s[r] = a` match so we move both pointers inwards. We now have `l = 1` and `r = 2`.

```
a b b a
  ^ ^
  l r
```

These also match so we move `l` and `r` inwards again. However, now they pass each other at which point we can return `true` because all characters matched.

How do we get the nth character of a string? One way is `s.chars().nth(n).unwrap()`, but we can do better. The problem with `.chars().nth(n)` is that we need to iterate to `n` each time to find the char of interest. We'd like `O(1)` lookup, which we have in a `Vec`. Hence, we first convert our string to `Vec<char>` and then access chars with `[n]`.

```rust
pub fn is_palindrome(s: String) -> bool {
    if s.len() <= 1 {
        return true;
    }

    let pruned: String = s
        .chars()
        .filter_map(|c| match c.is_alphanumeric() {
            true => Some(c.to_ascii_lowercase()),
            false => None,
        })
        .collect();

    if pruned.len() <= 1 {
        return true;
    }

    let s_vec: Vec<char> = pruned.chars().collect();

    let mut i = 0;
    let mut j = s_vec.len() - 1;

    while i < j {
        if s_vec[i].to_ascii_lowercase() != s_vec[j].to_ascii_lowercase() {
            return false;
        }
        i += 1;
        j -= 1;
    }

    true
}

fn main() {
    let s = ".,".to_string();
    println!("{}", is_palindrome(s));
}
```


## Even Better Solution
For an even more efficient solution, we skip pruning the string first. Instead, we implement a <q>fast forward</q> approach for our pointers. While the ith character is not alphanumeric, we increment it. Similarly, while the jth character is not alphanumeric, we decrement it. After fast forwarding, we compare. If equal, we continue. If not, we return false.

```rust
pub fn is_palindrome(s: String) -> bool {
    if s.len() <= 1 {
        return true;
    }

    let s_vec: Vec<char> = s.chars().collect();

    let mut i = 0;
    let mut j = s_vec.len() - 1;

    loop {
        // Fast forward to alphanumeric character.
        while !s_vec[i].is_alphanumeric() && i < j {
            i += 1;
        }

        while !s_vec[j].is_alphanumeric() && j > i {
            j -= 1;
        }

        if i >= j {
            break;
        }

        let left_char = s_vec[i].to_ascii_lowercase();
        let right_char = s_vec[j].to_ascii_lowercase();

        if left_char != right_char {
            return false;
        }
        i += 1;
        j -= 1;

        if i > j {
            break;
        }
    }
    true
}

fn main() {
    let s = "    A    b     B a              ".to_string();
    println!("{}", is_palindrome(s));
}
```
