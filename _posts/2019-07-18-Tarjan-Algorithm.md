---
layout: post
title: Tarjan Algorithm
subtitle: Find Strongly Connected Components
date: 2019-07-18
author: bboyhao
head-img:
catalog: true
tag:
    - Go
    - SCC
    - Algorithm
---

## Overview
Tarjan algorithm is used to find SCCs in a directed graph. Compared to
Korasaju's algorithm, it only DFS once and no need to transpose the graph. It
has linear time complexity of O(|V|+|E|).

## Implementation using Go
```Go
package main

import (
	"fmt"
	"tarjan/graph"
)

var timeStamp int = 1
var stack = make([]*Node, 0)

type Node = graph.Node

func main() {
	n1 := Node{Id: 1, Neighbours: make([]*Node, 0)}
	n2 := Node{Id: 2, Neighbours: make([]*Node, 0)}
	n3 := Node{Id: 3, Neighbours: make([]*Node, 0)}
	n4 := Node{Id: 4, Neighbours: make([]*Node, 0)}
	n5 := Node{Id: 5, Neighbours: make([]*Node, 0)}
	n6 := Node{Id: 6, Neighbours: make([]*Node, 0)}
	n1.AddNeighbour(&n2)
	n2.AddNeighbour(&n3)
	n3.AddNeighbour(&n4)
	n4.AddNeighbour(&n5)
	n5.AddNeighbour(&n6)
	n6.AddNeighbour(&n1)

	tarjan(&n1)
}

func tarjan(u *Node) {
	u.Visited = true
	u.Low = timeStamp
	u.Dfn = timeStamp
	timeStamp += 1
	stack = append(stack, u)
	for _, v := range u.Neighbours {
		if v.Dfn == 0 {
			tarjan(v)
			u.Low = min(v.Low, u.Low)
		} else {
			u.Low = min(v.Dfn, u.Low)
		}
	}
	if u.Low == u.Dfn {
		for stack[len(stack)-1].Id != u.Id {
			last := stack[len(stack)-1]
			stack = stack[:len(stack)-1]
			last.Visited = false
			fmt.Println(last.Id)
		}
		last := stack[len(stack)-1]
		stack = stack[:len(stack)-1]
		last.Visited = false
		fmt.Println(last.Id)
		fmt.Println()
	}
}

func min(a int, b int) int {
	if a >= b {
		return b
	}
	return a
}
```

```Go
package graph

type Node struct {
	Id         int
	Visited    bool
	Low        int
	Dfn        int
	Neighbours []*Node
}

func (u *Node) AddNeighbour(v *Node) {
	u.Neighbours = append(u.Neighbours, v)
}
```

## Details about this algorithm
More details can be found at this [link](https://blog.csdn.net/hurmishine/article/details/75248876)
