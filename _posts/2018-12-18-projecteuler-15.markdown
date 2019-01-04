---
layout: post
title:  "Project Euler 15"
date:   2018-12-18 00:00:00 -0800
categories: projecteuler
---
https://projecteuler.net/problem=15

- Total number of possible paths in a directed acyclic graph
- Traversing the graph and counting the total paths in a depth first search is only feasible on a small n*n lattice graph.
- The solution is to backtrack the possible paths from incoming edge nodes. The base case starts with one possible path,
    - so when i=0; f(i) = 1
    - from there f(i+1) = sum of (map of incoming_edges[i+1] applied to f)
- First, a lattice graph based on size n*n is generated in a adjacency list, then the edges are reversed to produce an incoming edge list.
- Finally, the total paths at the destination node is counted by traversing the graph in topological order.

```rust
type AdjList = HashMap<i128, Vec<i128>>;
/*
Only able to move right and down to the bottom right corner.
How many such routes are there through a 20×20 grid?
- its a dag
1 -> 2 -> 3
|    |    |
v    v    v
4 -> 5 -> 6
|    |    |
v    v    v
7 -> 8 -> 9

the solution is to backtrack the count,
you know
f(1) = 1
f(2) = f(1)
f(5) = f(4) + f(5)
f(9) = f(6) + f(8)
*/
pub fn create_graph_p15(n: i128) -> AdjList {
    let total = n*n;
    let mut outgoing: AdjList = HashMap::new();
    for i in 1..(total+1) {
        outgoing.insert(i, Vec::new());
        if i < 1 { continue }
        if i % n != 0 {
            let m = i + 1;
            outgoing.entry(i).or_insert(Vec::new()).push(m);
        }
        // not on last row
        if i < (n * (n-1) + 1) {
            let m = i + n;
            outgoing.entry(i).or_insert(Vec::new()).push(m);
        }
    }
    outgoing
}

pub fn reverse_edges(edges: &mut AdjList) -> AdjList {
    let mut in_edges: AdjList = HashMap::new();
    for (key, value) in edges.iter() {
        for i in value.iter() {
            in_edges.entry(*i).or_insert(Vec::new()).push(*key);
        }
    }
    in_edges
}

pub fn count_paths(edges: &AdjList, n: i128) -> i128 {
    let mut npaths = HashMap::new();
    // base case
    npaths.insert(1, 1);
    for i in 2..(n*n+1) {
        let count_iter = edges.get(&i).unwrap().iter();
        let mut count = 0;
        for k in count_iter {
            count += npaths.get(k).unwrap();
        }
        npaths.insert(i, count);
    }
    let index = n*n;
    *npaths.get(&index).unwrap()
}

pub fn problem_15() -> i128 {
    let n = 20+1;
    let mut outgoing = create_graph_p15(n);
    // reverse as incoming edges
    let incoming = reverse_edges(&mut outgoing);
    let npaths = count_paths(&incoming, n);
    npaths
}
```
