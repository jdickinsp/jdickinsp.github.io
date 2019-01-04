---
layout: post
title:  "Project Euler 14"
date:   2018-12-10 00:00:00 -0800
categories: projecteuler
---
https://projecteuler.net/problem=14

Collatz sequence
- n -> n/2 (when n is even)
- n -> 3n + 1 (when n is odd)
- Which starting number, under one million, produces the longest chain?
- 1 -> 2 -> 4 -> 8 -> 16 -> 5 -> 10 -> 20

```rust
pub fn collatz_rec(i: i64, cache: &mut HashMap<i64, i128>) -> i128 {
    if i < 2 { return 1 }
    match cache.get(&i) {
        Some(&value) => return value,
        _ => (),
    }
    let mut result;
    if i % 2 == 0 {
        result = collatz_rec(i/2, cache) + 1;
    } else {
        result = collatz_rec(3*i + 1, cache) + 1;
    }
    cache.insert(i, result);
    result
}


pub fn problem_14() -> i64 {
    let n = 1_000_000;
    let mut cache = HashMap::new();
    for i in 0..n {
        let f = collatz_rec(i, &mut cache);
    }
    let mut max_value = 0;
    let mut max_key = 0;
    for (&k, &v) in cache.iter() {
        if v > max_value {
            max_value = v;
            max_key = k;
        }
    }
    max_key
}
```
