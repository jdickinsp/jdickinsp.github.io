---
layout: post
title:  "Project Euler 17"
date:   2018-12-29 00:00:00 -0800
categories: projecteuler
---

https://projecteuler.net/problem=17


```rust

/*
one, two, three, four, five, six, seven, eight, nine,
ten, eleven, twelve, thirteen, fourteen, fifteen, sixteen, seventeen, eighteen. nineteen,
ten, twenty, thirty, forty, fifty, sixty, seventy, eighty, ninety,
one hundred and one,
two hundred,
one thousand
*/
pub fn problem_17() -> i128 {
    let t_1_9 = [3, 3, 5, 4, 4, 3, 5, 5, 4];
    let t_10_19 = [3, 6, 6, 8, 8, 7, 7, 9, 8, 8];
    let sum_t_1_9 = t_1_9.iter().fold(0, |a, &b| a + b);
    let sum_t_10_19 = t_10_19.iter().fold(0, |a, &b| a + b);

    // tens, twenty, ..., ninety
    let tens = [0, 6, 6, 5, 5, 5, 7, 6, 6];
    // t_1_20 = sum(1-20)
    // t_20_100 = [6, 6, 5, 5, 5, 7, 6, 6] * 10 + sum(1-9) * 8
    let mut t_1_99 = 0;
    for i in tens.iter() {
        t_1_99 += i * 10 as i128;
    }
    t_1_99 += sum_t_1_9 * 9 + sum_t_10_19;
    /*
    100-199
    t_100_199 = [3+7] * 100 + t_1_99
    */
    let mut t_100_999 = 0;
    for i in t_1_9.iter() {
        t_100_999 += (*i as i128 + 7 + 3) * 100 + t_1_99 - 3;
    }
    let t = t_1_99 + t_100_999 + 11;
    t
}
```
