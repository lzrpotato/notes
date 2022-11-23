---
id: 0vo6l8eu89f3qkjye0rcdoa
title: MergeSort VS QuickSort
desc: ''
updated: 1669227742392
created: 1669225755010
---

[merge sort and quick sort](https://www.geeksforgeeks.org/quick-sort-vs-merge-sort/)

## Quick Sort
Quick sort is an internal algorithm which is based on divide and conquer strategy.
- The array of elements is divided into parts repeatedly until it is not possible to divide it further.
- It is also known as "partition exchange sort"
- It uses a key element (pivot) for partitioning the elements
- One left partition contains all those elements that are smaller than the pivot and one right partition contains all those elements which are greater than the key element. 

## Merge Sort
Merge sort is an external algorithm and based on divide and conquer strategy.
- The elements are split into two sub-arrays (n/2) again and again until only one element is left.
- Merge sort uses additional storage for sorting the auxiliary array.
- Merge sort uses three arrays where two are used for storing each half, and the third external one is used to store the final sorted list by merging other two and each array is then sorted recursively.
- At last, the all sub arrays are merged to make it ‘n’ element size of the array.

## Comparison
| Basis for comparison | Quick Sort | Merge Sort |
| --- | ---  | --- |
| The partition of elements in the array | The splitting of a array of elements is in any ratio, not necessarily divided into half. |	In the merge sort, the array is parted into just 2 halves (i.e. n/2). |
| Worst case complexity | O(n2) | O(nlogn) |
| Works well on | It works well on smaller array | It operates fine on any size of array |
| Speed of execution | It work faster than other sorting algorithms for small data set like Selection sort etc	|  It has a consistent speed on any size of data | 
| Additional storage space requirement | Less(In-place) | More(not In-place) |  
| Efficiency | Inefficient for larger arrays | More efficient |
| Sorting method | Internal	| External | 
| Stability | Not Stable | Stable |
| Preferred for | for Arrays | for Linked Lists |
| Locality of reference | good | poor |