---
layout: post
title: Red Black Tree
subtitle: Implementation of Red-Black Tree using Go
date: 2019-07-01
author: bboyhao
head-img:
catalog: true
tag:
    - Go
    - data structure
---

>终于要把这个坑填了

## Overview 
Red-black trees are a variation of binary search trees to ensure that the tree
is balanced. Its height is __O(lg n), where n is the number of nodes in the tree.
Operations like searching, inserting, deleting a node take O(lg n) time in the
worst case. Its performance is better and more stable than usual binary search
tree when binary search tree is imbalanced.

Each node in red-black tree has one more attribute than bst nodes, color. The
color is either red or black. All empty nodes(leaves) are colored black. A
single sentinel, nil, is used for all the leaves of red-black tree T, with color
= black, other attributes like pre, left, right and value can be anything as
they do not matter. Using a single sentinel is to help save space.

## Five properties about red-black tree
If a binary search tree is a red-black tree, then it must satisfy the following
five properties:
1. Every node is either red or black
2. The root is black
3. Every leaf(empty node) is black
4. If a node is red, then both its children are black
5. For each node, all paths from the nodes to descendant leaves contain the same
   number of black nodes

## Height and black-height
Height of a node x is h(x) = number of edges in the longest path to a leaf
Black-height of a node x is bh(x) = number of black nodes on the path from x to
leaf, not counting x itself.

Black-height of a red-black tree is the black-height of its root.

Consider a node x in an red-black tree: The longest descending path from x to a
leaf has legth h(x), which is at most twice the length of the shortest descending
path from x to a leaf.
Explanation: The shortest path possible is such that all the nodes on this path
are black(Property 5). If we want to lengthen this path, we can add red nodes into it.
However, according to Property 4, we cannot add in consecutive red nodes to the
path. We can only add in red nodes followed by black nodes. Thus, there should
be equal number of red and black nodes in the longest path. In this way, it's
twice the length of the shortest path.

Lemma 13.1: A red-black tree with n internal nodes has height at most 2 lg(n+1)

## Left and right rotation
Rotations are the basic tree-restructuring operation for almost all balanced
search trees. Rotation takes a red-black tree and a node. It changes pointers to
changes the local structure, and won't violate the bianry-search tree property.
Left rotation and right rotation are inverses.
(Here need rotation image)

To Be Continued...


