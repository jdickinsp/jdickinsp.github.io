---
layout: post
title:  "Project Euler 18"
date:   2019-01-05 00:00:00 -0800
categories: projecteuler
---

https://projecteuler.net/problem=18

To simplify finding the maximum path in the triangle, the first step is to translate the problem into a directed acyclic graph structure.

So
```
1
2 3
4 5 6
```

is represented as a graph.
```
graph[4] = [2]
graph[5] = [2, 3]
graph[6] = [3]
graph[2] = [1]
graph[3] = [1]
graph[1] = []
```

However, since each number in the triangle could be non unique, the position is used instead to identity the node in the graph.
So the graph becomes an adjacency list of tuple positions x and y.
```
graph[(2, 0)] = [(1, 0), (1, 1)]
```

Since we want to find the previous max value from the node's neighbors, we reverse the edges in the graph, so the inner edges become outer edges.

Each node has at most only two possible edges, so traversing from the bottom to the top only requires checking O(# of edges) plus the cost of traversing the adjacency list to determine the maximum path.

The max sum of all neighbors and the current node value is stored in a maximum-values table, which is initialized with the original positional value. The table is populated by traversing from bottom to top, along each row of the triangle. From there, the maximum path sum is found at the origin (0, 0).




```rust
/*
maximum path
*/
type AdjListPos = HashMap<(usize, usize), Vec<(usize, usize)>>;

pub fn reverse_edges_tuples(edges: &mut AdjListPos) -> AdjListPos {
    let mut in_edges: AdjListPos = HashMap::new();
    for (key, value) in edges.iter() {
        for i in value.iter() {
            in_edges.entry(*i).or_insert(Vec::new()).push(*key);
        }
    }
    in_edges
}

pub fn find_max_path(graph: &str) -> i128 {
    let line_strings: Vec<&str> = graph.split("\n").map(|s| s.trim()).collect();
    let mut list = vec!();
    // parse into vector
    for line in line_strings.iter() {
        let arrayline: Vec<i128> = line.split_whitespace().filter_map(|s| s.parse::<i128>().ok()).collect();
        if arrayline.len() > 0 {
            list.push(arrayline);
        }
    }
    // generate adjacency list of graph
    let mut adjlist: AdjListPos = HashMap::new();
    let mut maxvalues: HashMap<(usize, usize), i128> = HashMap::new();
    let mut maxpaths: HashMap<(usize, usize), (usize, usize)> = HashMap::new();
    for (i, row) in list.iter().enumerate().rev() {
        for (j, value) in row.iter().enumerate() {
            let pos = (i, j);
            if i == 0 {
                adjlist.insert(pos, Vec::new());
            }
            else {
                if j == 0 {
                    adjlist.entry(pos).or_insert(Vec::new()).push((i-1, j));
                } else if j < i {
                    adjlist.entry(pos).or_insert(Vec::new()).push((i-1, j-1));
                    adjlist.entry(pos).or_insert(Vec::new()).push((i-1, j));
                } else {
                    adjlist.entry(pos).or_insert(Vec::new()).push((i-1, j-1));
                }
            }
             maxvalues.entry(pos).or_insert(*value);
        }
    }
    let reversed_edges = reverse_edges_tuples(&mut adjlist);
    // find max path bottom up to top
    for (i, row) in list.iter().enumerate().rev() {
        for (j, item) in row.iter().enumerate() {
            let node = (i, j);
            match reversed_edges.get(&node) {
                Some(positions) => {
                    let mut max_value = 0;
                    for pos in positions {
                        let val = *maxvalues.get(pos).unwrap();
                        if val > max_value {
                            max_value = val;
                        }
                    }
                    let node_val = list[i][j];
                    maxvalues.insert(node, max_value + node_val);
                },
                None => ()
            }
        }
    }
    // maximum path sum is accumulated at the origin
    *maxvalues.get(&(0, 0)).unwrap()
}


pub fn problem_18() -> i128 {
    let triangle_str = "
    75
    95 64
    17 47 82
    18 35 87 10
    20 04 82 47 65
    19 01 23 75 03 34
    88 02 77 73 07 63 67
    99 65 04 28 06 16 70 92
    41 41 26 56 83 40 80 70 33
    41 48 72 33 47 32 37 16 94 29
    53 71 44 65 25 43 91 52 97 51 14
    70 11 33 28 77 73 17 78 39 68 17 57
    91 71 52 38 17 14 91 43 58 50 27 29 48
    63 66 04 68 89 53 67 30 73 16 69 87 40 31
    04 62 98 27 23 09 70 98 73 93 38 53 60 04 23
    ";
    find_max_path(triangle_str)
}
```
