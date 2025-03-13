# State of the Art: Sorting Networks

## Table of Contents
- [Introduction](#introduction)
- [Problem Definition](#problem-definition)
- [Key Concepts](#key-concepts)
  - [Comparator Networks](#comparator-networks)
  - [Sorting Networks](#sorting-networks)
  - [Zero-One Principle](#zero-one-principle)
- [Metrics of Interest](#metrics-of-interest)
  - [Size (Number of Comparators)](#size-number-of-comparators)
  - [Depth (Parallel Steps)](#depth-parallel-steps)
- [Optimal Sorting Networks](#optimal-sorting-networks)
  - [Known Optimal Values](#known-optimal-values)
  - [Bounds for Larger Networks](#bounds-for-larger-networks)
- [Construction Algorithms](#construction-algorithms)
  - [Insertion and Bubble Networks](#insertion-and-bubble-networks)
  - [Batcher's Odd-Even Mergesort](#batchers-odd-even-mergesort)
  - [Bitonic Sort](#bitonic-sort)
  - [AKS Network](#aks-network)
  - [Zig-Zag Sorting Network](#zig-zag-sorting-network)
- [Recent Advances](#recent-advances)
- [Applications](#applications)
- [References](#references)

## Introduction

Sorting networks are a mathematical model of comparison-based sorting algorithms that operate on fixed-length lists. Unlike traditional sorting algorithms that make decisions based on the values being sorted, sorting networks follow a fixed sequence of comparisons regardless of the input data. This makes them particularly suitable for hardware implementation and parallel processing.

The study of sorting networks focuses on finding optimal networks for a given number of inputs, where optimality can be measured in terms of the number of comparators used (size) or the number of parallel steps required (depth).

## Problem Definition

The specific problem we are addressing is:

**Determine the smallest number of comparators (vertical wire pairs) required to build a sorting network that sorts all input sequences on n wires.**

A comparator network consists of k parallel horizontal lines (wires or channels) and a sequence of n vertical segments (comparators), each connecting two wires. A comparator network is called a sorting network if its output is sorted in ascending order for every possible input.

## Key Concepts

### Comparator Networks

A comparator network with n channels and size k is a sequence of comparators C = (i₁, j₁); …; (iₖ, jₖ) where each comparator (iₗ, jₗ) is a pair of channels 1 ≤ iₗ < jₗ ≤ n. The size of a comparator network is the number of its comparators.

When a pair of values traveling through a pair of wires encounters a comparator, the comparator swaps the values if and only if the top wire's value is greater than the bottom wire's value. This ensures that the smaller value is on the top wire and the larger value is on the bottom wire after the comparator.

### Sorting Networks

A sorting network is a comparator network that correctly sorts all possible input sequences. The network must produce a sorted output (in ascending or descending order) regardless of the input values.

### Zero-One Principle

The zero-one principle is a fundamental theorem in the study of sorting networks that significantly simplifies the verification of sorting networks. It states that:

*If a sorting network can correctly sort all 2ⁿ sequences of zeros and ones, then it is also valid for arbitrary ordered inputs.*

This principle reduces the number of test cases from n! (all possible permutations) to 2ⁿ (all possible binary sequences), which is still exponential but much smaller for n ≥ 4.

## Metrics of Interest

### Size (Number of Comparators)

The size of a sorting network refers to the total number of comparators used. Finding the minimum number of comparators needed for a sorting network with n inputs is a fundamental problem in the field.

### Depth (Parallel Steps)

The depth of a sorting network is defined as the largest number of comparators that any input value can encounter on its way through the network. Assuming all comparisons take unit time and can be performed in parallel (when they lie on the same vertical line), the depth represents the number of time steps required to execute the network.

## Optimal Sorting Networks

### Known Optimal Values

For small values of n, optimal sorting networks (both in terms of size and depth) have been determined:

| n (inputs) | Optimal Size (comparators) | Optimal Depth (parallel steps) |
|------------|----------------------------|--------------------------------|
| 1          | 0                          | 0                              |
| 2          | 1                          | 1                              |
| 3          | 3                          | 3                              |
| 4          | 5                          | 3                              |
| 5          | 9                          | 5                              |
| 6          | 12                         | 5                              |
| 7          | 16                         | 6                              |
| 8          | 19                         | 6                              |
| 9          | 25                         | 7                              |
| 10         | 29                         | 7                              |
| 11         | 35                         | 8                              |
| 12         | 39                         | 8                              |
| 13         | 45                         | 9                              |
| 14         | 51                         | 9                              |
| 15         | 56                         | 9                              |
| 16         | 60                         | 9                              |

For n = 9, it was proven in 2014 by Codish et al. that the optimal number of comparators is 25, while the optimal depth is 7. Similarly, for n = 10, the optimal size is 29 comparators with a depth of 7.

The optimality of the smallest known sorting networks for n = 11 and n = 12 was resolved in 2020, confirming that 35 and 39 comparators are optimal, respectively.

### Bounds for Larger Networks

For larger values of n, exact optimal values are not known, but lower and upper bounds have been established. A lower bound on the size S(n) can be derived inductively using Van Voorhis' lemma: S(n) ≥ S(n − 1) + ⌈log₂n⌉.

## Construction Algorithms

Several algorithms exist for constructing sorting networks, each with different characteristics in terms of size and depth.

### Insertion and Bubble Networks

The insertion network (or equivalently, bubble network) can be constructed recursively using the principles of insertion sort or bubble sort. For a network of size n, the depth is 2n - 3, which is better than the O(n log n) time needed by random-access machines but not optimal.

### Batcher's Odd-Even Mergesort

Batcher's odd-even mergesort is a generic construction devised by Ken Batcher for sorting networks of size O(n (log n)²) and depth O((log n)²). Although it is not asymptotically optimal, it is practical and widely used.

The algorithm works by recursively sorting two halves of the input and then merging them using a special merging network. The pseudocode for generating the indices for sorting n elements is:

```
# note: the input sequence is indexed from 0 to (n-1)
for p = 1, 2, 4, 8, ... # as long as p < n
  for k = p, p/2, p/4, p/8, ... # as long as k >= 1
    for j = mod(k,p) to (n-1-k) with a step size of 2k
      for i = 0 to min(k-1, n-j-k-1) with a step size of 1
        if floor((i+j) / (p*2)) == floor((i+j+k) / (p*2))
          compare and sort elements (i+j) and (i+j+k)
```

### Bitonic Sort

Bitonic sort is another parallel algorithm for sorting, also devised by Ken Batcher. It works by first creating a bitonic sequence (a sequence that first increases, then decreases, or vice versa) and then recursively sorting it.

The resulting sorting networks consist of O(n log²(n)) comparators and have a delay of O(log²(n)). This makes it a popular choice for sorting large numbers of elements on architectures with many parallel execution units, such as GPUs.

Although the absolute number of comparisons is typically higher than Batcher's odd-even sort, many of the consecutive operations in a bitonic sort retain a locality of reference, making implementations more cache-friendly and typically more efficient in practice.

### AKS Network

The AKS network, named after its discoverers Ajtai, Komlós, and Szemerédi, is a construction that achieves O(log n) depth (hence size O(n log n)). While this is an important theoretical discovery, it has limited practical application due to the large constant factors hidden by the Big-O notation.

### Zig-Zag Sorting Network

A more recent construction called the zig-zag sorting network of size O(n log n) was discovered by Goodrich in 2014. While its size is much smaller than that of AKS networks, its depth O(n log n) makes it unsuitable for a parallel implementation.

## Recent Advances

Recent research has focused on finding optimal sorting networks for specific small values of n, using techniques from constraint programming, SAT solving, and genetic algorithms.

For example, the smallest known sorting network for n = 13 was found by Hugues Juillé in 1995 "by simulating an evolutionary process of genetic breeding," and the minimum depth sorting networks for n = 9 and n = 11 were found by Loren Schwiebert in 2001 "using genetic methods."

In 2014, Codish et al. proved that 25 comparators is optimal when sorting nine inputs, and 29 comparators is optimal for ten inputs. These results were achieved using sophisticated SAT solving techniques.

## Applications

Sorting networks have several practical applications:

1. **Hardware Implementation**: Due to their fixed structure, sorting networks are well-suited for hardware implementation in circuits.

2. **Parallel Processing**: Sorting networks naturally support parallel execution, making them ideal for multi-core processors and GPUs.

3. **Graphics Processing**: They are used in graphics processing for operations like depth sorting and order-independent transparency.

4. **Switching Networks**: The principles of sorting networks are applied in the design of switching networks for telecommunications.

## References

1. Knuth, D. E. (1998). The Art of Computer Programming, Volume 3: Sorting and Searching (2nd ed.). Addison-Wesley.

2. Batcher, K. E. (1968). Sorting networks and their applications. In Proceedings of the April 30--May 2, 1968, spring joint computer conference (pp. 307-314).

3. Codish, M., Cruz-Filipe, L., Frank, M., & Schneider-Kamp, P. (2014). Twenty-five comparators is optimal when sorting nine inputs (and twenty-nine for ten). In Proceedings 26th IEEE International Conference on Tools with Artificial Intelligence (pp. 186-193).

4. Bundala, D., Codish, M., Cruz-Filipe, L., Schneider-Kamp, P., & Závodný, J. (2017). Optimal-Depth Sorting Networks. Journal of Computer and System Sciences, 84, 185-204.

5. Ajtai, M., Komlós, J., & Szemerédi, E. (1983). An O(n log n) sorting network. In Proceedings of the fifteenth annual ACM symposium on Theory of computing (pp. 1-9).

6. Goodrich, M. T. (2014). Zig-zag sort: A simple deterministic data-oblivious sorting algorithm running in O(n log n) time. In Proceedings of the forty-sixth annual ACM symposium on Theory of computing (pp. 684-693).

7. Parberry, I. (1989). A computer assisted optimal depth lower bound for sorting networks with nine inputs. In Proceedings of the 1989 ACM/IEEE Conference on Supercomputing (pp. 152-161).

8. Dobbelaere, B. List of sorting networks. Retrieved from https://bertdobbelaere.github.io/sorting_networks.html
