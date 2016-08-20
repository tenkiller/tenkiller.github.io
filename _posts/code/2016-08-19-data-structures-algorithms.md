---
layout: article
image:
  teaser: algo_logo.png
title: Data Structures and Algorithms
---

> I don't know about you, but data structures and algorithms are cool! 

Breaking complex problems down into simple, logical pieces is a fascinating process. It's one you get good at with 
practice - just like any physical skill. Physical skills get better with muscle memory, so too does problem analysis. 
Here we're strengthening our brain. Repetition is key. I spent a lot of time watching the lecture and recitation videos 
offered (for free) by the Massachusetts Institute of Technology (MIT) on their 
[OpenCourseWare](http://ocw.mit.edu/index.htm) website. Specifically, I reviewed videos from their 6.006 Introduction to 
Algorithms (Fall 2011) class. If you have the time, I highly recommend watching it and taking notes. If you're too lazy 
to take notes, well, you're in luck. Since I'm such a nerd, I took notes for you! Much of the information is gleaned 
from WikiPedia, but collating all that into one place is better than clicking through multiple pages. Here it's all in 
one place.

__Disclaimer__: I do not attest or guarantee that all the information listed here is 100% accurate. Use this as a spring 
board and go learn more about algorithms and data structures. Try to implement them yourself. It really is fun!

__Merge Sort__

Time | Space
--- | ---
O(_n log n_) | O(_n_)

Divide the unsorted list into n sub-lists, each containing one element. A list of one element is considered sorted. 
Repeatedly merge sub-lists to produce new sorted sub-lists until there is only one sub-list remaining. Most 
implementations preserve the input order of equal elements in the sorted output.

__Heap Sort__

Time | Space
--- | ---
O(_n log n_) | O(_1_)

A max-heap is built out of the input data. A sorted array is created by repeatedly taking the largest element from the 
heap (the root) and swapping it with the last element in the array. The range of elements being sorted decreases by one 
and it is heapified before repeating.

__Quick Sort__

Time | Space
--- | ---
O(_n log n_) | O(_1_)

Pick an element, called the pivot, from the input list. Reorder the list so that all elements with values less than the 
pivot come before it, while all elements with values greater than the pivot come after it (equal values can go either 
way). Recursively apply the same steps to the sub-list of smaller values and the sub-list of greater values.

A popular partition scheme uses two indices that start at the ends of the array being partitioned, and then move towards 
each other until they detect an inversion (a pair of elements, one greater than the pivot, one smaller, that are in the 
wrong order relative to each other). The inverted elements are then swapped. The scanning continues, and when the 
indices cross over each other, the algorithm stops and swaps in the pivot at the rightmost index of the left sub-array.

__Min/Max Heap__

A min/max heap is a specialized tree-based data structure that satisfies the heap property: if A is a parent node of B, 
then the value of node A is ordered with respect to the value of node B, with the same ordering applying across the 
heap. It is usually placed in an array with the layout of a complete binary tree. Each array index represents a node; 
the indices of the node’s parent, left child branch, or right child branch are simple expressions

Operation | Time
--- | ---
Heapify | O(_n_)
Sift-up | O(_n log n_)
Sift-down | O(_n log n_)
Peek | O(_1_)
Push | O(_n log n_)
Pop | O(_n log n_)

 
__Linked List__

A linked list is a linear collection of data elements, called nodes, pointing to the next node by means of a pointer 
(link). The principle benefit of a linked list over a conventional array is that the list elements can easily be 
inserted or removed without reallocation or reorganization of the entire structure. Linked lists allow insertion and 
removal of nodes at any point in the list. However, they do not allow random access or indexing.

Operation | Time
--- | ---
Add-first | O(_1_)
Add-after | O(_1_)
Add-before | O(_1_)
Add-last | O(_1_)
Find | O(_n_)
Remove | O(_n_)
Remove-first | O(_1_)
Remove-last | O(_1_)

__Stack__

A stack is a data structure that resembles of a set of physical items stacked on top of each other. The order in which 
an element is added to and removed from the stack gives rise to its alternative name, last-in, first-out (LIFO). It is 
usually implemented as a singly linked list, with a pointer at the head.

Operation | Time
--- | ---
Peek | O(_1_)
Push | O(_1_)
Pop | O(_1_)

__Queue__

A queue is a collection of elements that are kept in the order they are added. The first element added to the data 
structure is the first element removed; a.k.a. first-in, first-out (FIFO). It is usually implemented as a singly linked 
list, with a pointer at the head and tail.

Operation | Time
--- | ---
Peek | O(_1_)
Enqueue | O(_1_)
Dequeue | O(_1_)
 
__Binary Search Tree (BST)__

A binary search tree is a rooted binary tree whose internal nodes each store a key and links to two distinguished 
subtrees, commonly denoted as left and right. It also satisfies the property that the key in each node must be greater 
than all keys stored in the left subtree and smaller than all keys in the right subtree.

They allow fast lookup, addition, and removal of items. They keep their keys in sorted order. When looking for a key (or 
a place to insert a new key), they traverse the tree from root to leaf, making comparisons to keys stored in the nodes 
and deciding to continue searching in the left of right subtrees. On average, this means that each comparison allows the 
operations to skip about half of the tree.

Operation | Time
--- | ---
Insert | O(_h_)
Remove | O(_h_)
Search | O(_h_)
Min/Max | O(_h_)
In-order | O(_n_)
Pre-order | O(_n_)
Post-order | O(_n_)

__AVL Tree__

An AVL tree is a self-balancing BST. The heights of the two child subtrees of any node differ by at most one. If at any 
time they differ by more than one, rebalancing is done to restore this property. A tree rotation restores balance 
without interfering with the order of the elements. It is used to decrease the height of a tree by moving smaller 
subtrees down and larger subtrees up. Balancing the tree involves rotating nodes left or right, or a combination of 
both. Walking backward from the node of insertion, check the balance of each ancestor. If the current ancestor node is 
imbalanced, it must be rotated. 

-	If the key of the inserted node is less than the current ancestor’s left child, do a right-rotation of the current 
ancestor. If it’s greater, do a left-rotate of the ancestor’s left child and a right-rotate of the current ancestor.
-	If the key of the inserted node is greater than the current ancestor’s right child, do a left-rotation of the current 
ancestor. If it’s less than, do a right-rotate of the ancestor’s right child and a left-rotate of the current ancestor.

Operation | Time
--- | ---
Insert | O(_log n_)
Remove | O(_log n_)
Search | O(_log n_)
Min/Max | O(_log n_)
In-order | O(_n_)
Pre-order | O(_n_)
Post-order | O(_n_)
 
__Rolling Hash__

A rolling hash is a hash function where the input is hashed in a window that moves through the input. As the window 
moves, the new hash is rapidly calculated given the old hash value.

Operation | Time
--- | ---
Append | O(_1_)
Skip | O(_1_)
Hash | O(_1_)

__Hash Table__

A hash table is a data structure used to implement an associative array which maps keys to values. It uses a hash 
function to compute an index into an array of slots from which the desired value is found. Ideally, the hash function 
will assign each key to a unique slot, but it is possible that two keys will generate an identical hash (collision). One 
solution to collisions is to implement slot chaining using singly linked lists or dynamic arrays. The hash table itself 
is usually implemented as a fixed size array that grows or shrinks as necessary.

Operation | Time
--- | ---
Add | O(_1_)
Get | O(_1+n/k_)
Contains | O(_1_)
Remove | O(_1+n/k_)

__Adjacency List__

An adjacency list is a collection of unordered lists used to represent a finite graph. Each list describes the set of 
neighbors of a vertex in the graph. Two popular representations of vertices and edges exist. One suggests using a hash 
table to associate each vertex with an array of adjacent vertices (there is no explicit representation of edges as 
objects). The other more object-oriented way suggests vertex objects contain an instance variable pointing to a 
collection object that lists the neighboring edge objects. Each edge object points to two vertex objects at its 
endpoints. This version uses more memory, but the existence of edge objects allows it extra flexibility in storing 
additional information about edges.

Operation | Time
--- | ---
Get-neighbors | O(_v+e_)
Breadth-first-search |
Depth-first-search |
