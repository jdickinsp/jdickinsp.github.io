---
layout: post
title:  "Project Euler 12"
date:   2018-12-10 00:00:00 -0800
categories: projecteuler
---
https://projecteuler.net/problem=12

What is the value of the first triangle number to have over five hundred divisors?

```rust
pub fn problem_12() -> i64 {
    let n = 500;
    let mut i = 1;
    let mut sum = 0;
    let mut divisor_count;
    loop {
        sum += i;
        divisor_count = 0;
        let mut t = sum;
        for k in 1..sum {
            if k >= t { break }
            if sum % k == 0 {
                if k == sum / k { // perfect square
                    divisor_count += 1;
                } else {
                    divisor_count += 2;
                }
                t = sum / k;
            }
        }
        if divisor_count >= n { break };
        i += 1;
    }
    sum
}
```
