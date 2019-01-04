---
layout: post
title:  "Project Euler 16"
date:   2018-12-19 00:00:00 -0800
categories: projecteuler
---
https://projecteuler.net/problem=16

How do you factor a large exponential number when rust only supports integers up to 128 bits?

The solution was to write a big integer struct, to support multiplication and addition. The input integer could be arbitrarily large, so I used a string argument to initialize the bigint struct.
- After which its split into chunks of 8 digits, stored in reverse order in a vector.
- First I handled addition, checking the overflow of chunk size limit and carrying it over to the n+1 place, and storing the reminder modulus of the chunk limit at n.
- The multiplication method, loops over each chunk and creates a vector of BigInts for intermediate products, and then sums the total.
- Finally, the exponential function was composed of a loop of the multiply method accumulated into a sum in a BigInt struct.


```rust
use std::cmp;
/*
big integer class
- stores large ints in chunk sizes in a stack
- supports add and multiply
*/

// split string into chunk size integers
fn split_chunks(val: &str) -> Vec<i64> {
    let c_size = 8;
    let mut parts = vec!();
    let mut j = val.len();
    let mut i = if j > c_size { j - c_size } else { 0 };
    while j > 0 {
        let part = &val[i..j];
        parts.push(part.parse::<i64>().unwrap());
        j = i;
        i = if i > c_size { i - c_size } else { 0 };
    }
    parts
}

pub struct BigInt {
    // stored in reverse order
    chunks: Vec<i64>
}

impl BigInt {
    fn new(val: &str) -> BigInt {
        // split into chunks
        let chunks = split_chunks(val);
        // println!("BitInteger::new {:?}", chunks);
        BigInt { chunks: chunks }
    }
    fn add(&self, b: &BigInt) -> BigInt {
        let mut sum = vec!();
        let max_len = cmp::max(self.chunks.len(), b.chunks.len());
        let mut carry = 0;
        for i in 0..max_len {
            let x = if i < self.chunks.len() { self.chunks[i] } else { 0 };
            let y = if i < b.chunks.len() { b.chunks[i] } else { 0 };
            let s = x + y + carry;
            // check overflow
            carry = s / (100_000_000);
            let rem = s % 100_000_000;
            sum.push(rem);
        }
        if carry > 0 { sum.push(carry) };
        BigInt{ chunks: sum }
    }
    fn multiply(&self, b: &BigInt) -> BigInt {
        let max_len = cmp::max(self.chunks.len(), b.chunks.len());
        let mut middle_sums = vec!();
        for j in 0..b.chunks.len() {
            let mut sum = vec!();
            // add zeros place
            for k in 0..j {
                sum.push(0);
            }
            let mut carry = 0;
            for i in 0..max_len {
                let x = if i < self.chunks.len() { self.chunks[i] } else { 1 };
                let y = b.chunks[j];
                let s = x * y + carry;
                carry = s / (100_000_000);
                let rem = s % 100_000_000;
                sum.push(rem);
            }
            if carry > 0 { sum.push(carry) };
            middle_sums.push(BigInt{ chunks: sum });
        }
        let mut total = BigInt::new("0");
        for i in middle_sums.iter() {
            total = total.add(i);
        }
        total
    }
    pub fn value(&self) -> String {
        let mut digits = vec!();
        for (i, chunk) in self.chunks.iter().enumerate().rev() {
            let valstr = if i == self.chunks.len() - 1 { chunk.to_string() } else { format!("{:08}", chunk) };
            digits.push(valstr);
        }
        digits.join("")
    }
}


pub fn big_exponential(a: i64, x: i64) -> BigInt {
    let base = BigInt::new(&a.to_string());
    let mut total = BigInt::new("1");
    for _ in 0..x {
        total = total.multiply(&base);
    }
    total
}
```

I also wrote unit tests for the bigint struct.
You run tests with this command
```bash
cargo test
```

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_bigint_add() {
        let a = BigInt::new("99999999923456789");
        let b = BigInt::new("99950999");
        let x = a.add(&b);
        let y: i128 = 99999999923456789 + 99950999;
        assert_eq!(x.value(), y.to_string());
    }

    #[test]
    fn test_bigint_multiply() {
        let a = BigInt::new("99999999923456789");
        let b = BigInt::new("1299950999");
        let x = a.multiply(&b);
        let y: i128 = 99999999923456789 * 1299950999;
        assert_eq!(x.value(), y.to_string());
    }

    #[test]
    fn test_bigint_exponential() {
        let x = big_exponential(2, 10);
        assert_eq!(x.value(), "1024");
    }
}
```

The solution was easy once the bigint struct support was written.
```rust
use euler::bigint;

pub fn problem_16() -> i128 {
    let x = bigint::big_exponential(2, 1000);
    let y = x.value();
    let mut accum = 0;
    for i in 0..y.len() {
        let ch = y.chars().nth(i).unwrap();
        accum += ch.to_digit(10).unwrap();
    }
    println!("total: {}", accum);
    println!("y: {:?}", y);
    accum as i128
}
```
