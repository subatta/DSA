# Algorithm Patterns Library

**A comprehensive collection of algorithm patterns for technical interview preparation**

---

## 📚 Quick Navigation

### 🎯 [Pattern Recognition Guide](pattern_recognition_guide.md)
**Start here!** Learn how to identify which pattern applies to any problem based on keywords, constraints, and problem structure.

### ✅ [Checklist](checklist.md)
Quick reference checklist for problem-solving.

---

## 🔍 Algorithm Patterns

### **Linear Data Structure Patterns**

| Pattern | Best For | Key Problems |
|---------|----------|--------------|
| [**Sliding Window**](sliding_window_pattern.md) | Contiguous subarrays/substrings with constraints | Longest substring without repeating, max sum subarray of size K |
| [**Two Pointers**](two_pointers_pattern.md) | Sorted arrays, palindromes, in-place modifications | Two sum (sorted), container with most water, remove duplicates |
| [**Fast & Slow Pointers**](fast_slow_pointers_pattern.md) | Cycle detection, linked list middle, O(1) space | Linked list cycle, happy number, palindrome linked list |
| [**Hash Map / Frequency Counting**](hash_map_frequency_counting_pattern.md) | Counting, grouping, O(1) lookups | Anagrams, two sum, frequency problems |
| [**Prefix Sum**](prefix_sum_running_sum_pattern.md) | Range queries, subarray sums | Subarray sum equals K, range sum queries |
| [**Monotonic Stack**](monotonic_stack_pattern.md) | Next greater/smaller elements | Daily temperatures, largest rectangle in histogram |

---

### **Range & Interval Patterns**

| Pattern | Best For | Key Problems |
|---------|----------|--------------|
| [**Intervals / Merging**](intervals_merging_pattern.md) | Overlapping ranges, scheduling | Merge intervals, meeting rooms, insert interval |

---

### **Heap & Priority-Based Patterns**

| Pattern | Best For | Key Problems |
|---------|----------|--------------|
| [**Top K Elements**](top_k_elements_pattern.md) | K largest/smallest/frequent, Kth element | Kth largest, top K frequent, K closest points |
| [**Heap / Priority Queue**](heaps_priority_queue_pattern.md) | Median, merging streams, scheduling | Median from stream, merge K lists, task scheduler |

---

### **Search & Divide-Conquer Patterns**

| Pattern | Best For | Key Problems |
|---------|----------|--------------|
| [**Binary Search**](binary_search_pattern.md) | Sorted data, monotonic search spaces | Find target, rotated sorted array, search insert position |

---

### **Exploration & Generation Patterns**

| Pattern | Best For | Key Problems |
|---------|----------|--------------|
| [**Graph Traversal (BFS/DFS)**](graph_traversal_pattern.md) | Connected components, shortest paths, tree traversal | Number of islands, shortest path, topological sort |
| [**Backtracking**](backtracking_pattern.md) | Generate all solutions, constraint satisfaction | All subsets, permutations, N-Queens, Sudoku |

---

### **Optimization Patterns**

| Pattern | Best For | Key Problems |
|---------|----------|--------------|
| [**Greedy**](greedy_pattern.md) | Local optimal → global optimal | Activity selection, jump game, scheduling |
| [**Dynamic Programming (1D)**](dynamic_programming_pattern.md) | Overlapping subproblems, counting ways | Climbing stairs, house robber, coin change, max subarray |

---

## 🎓 Learning Path

### **Beginner → Advanced**

1. **Start with fundamentals:**
   - [Two Pointers](two_pointers_pattern.md) - Easiest to grasp
   - [Fast & Slow Pointers](fast_slow_pointers_pattern.md) - Cycle detection technique
   - [Hash Map](hash_map_frequency_counting_pattern.md) - Essential for many problems
   - [Sliding Window](sliding_window_pattern.md) - Builds on two pointers

2. **Master linear structures:**
   - [Prefix Sum](prefix_sum_running_sum_pattern.md) - Clever optimization
   - [Monotonic Stack](monotonic_stack_pattern.md) - Unique thought pattern

3. **Learn search & ranges:**
   - [Binary Search](binary_search_pattern.md) - Fundamental technique
   - [Intervals Merging](intervals_merging_pattern.md) - Common in scheduling

4. **Understand priority-based:**
   - [Top K Elements](top_k_elements_pattern.md) - K largest/smallest problems
   - [Heap / Priority Queue](heaps_priority_queue_pattern.md) - Median, merging, scheduling

5. **Explore graphs & trees:**
   - [Graph Traversal (BFS/DFS)](graph_traversal_pattern.md) - Essential for connectivity

6. **Master generation & optimization:**
   - [Backtracking](backtracking_pattern.md) - Generate all solutions
   - [Greedy](greedy_pattern.md) - Local optimal choices
   - [Dynamic Programming](dynamic_programming_pattern.md) - Hardest but most powerful

---

## 🚀 Quick Problem Lookup

### By Keyword

| If Problem Mentions... | Check These Patterns |
|------------------------|----------------------|
| **"contiguous subarray/substring"** | [Sliding Window](sliding_window_pattern.md), [Prefix Sum](prefix_sum_running_sum_pattern.md) |
| **"sorted array"** | [Binary Search](binary_search_pattern.md), [Two Pointers](two_pointers_pattern.md) |
| **"cycle", "linked list middle"** | [Fast & Slow Pointers](fast_slow_pointers_pattern.md) |
| **"all combinations/permutations"** | [Backtracking](backtracking_pattern.md) |
| **"K largest/smallest/frequent"** | [Top K Elements](top_k_elements_pattern.md) |
| **"median", "merge K lists"** | [Heap / Priority Queue](heaps_priority_queue_pattern.md) |
| **"next greater/smaller"** | [Monotonic Stack](monotonic_stack_pattern.md) |
| **"intervals/ranges"** | [Intervals Merging](intervals_merging_pattern.md) |
| **"frequency/count/group"** | [Hash Map](hash_map_frequency_counting_pattern.md) |
| **"islands/connected/path"** | [Graph Traversal](graph_traversal_pattern.md) |
| **"count ways to"** | [Dynamic Programming](dynamic_programming_pattern.md) |
| **"scheduling/selection"** | [Greedy](greedy_pattern.md), [Intervals](intervals_merging_pattern.md) |

---

## 📊 Pattern Comparison Matrix

| Pattern | Time Complexity | Space | When to Use |
|---------|----------------|-------|-------------|
| Sliding Window | O(n) | O(1)-O(k) | Contiguous sequences with constraint |
| Two Pointers | O(n) | O(1) | Sorted arrays, palindromes, partitioning |
| Fast & Slow Pointers | O(n) | O(1) | Cycle detection, linked list problems |
| Hash Map | O(n) | O(n) | Need O(1) lookup, counting, grouping |
| Prefix Sum | O(n) prep, O(1) query | O(n) | Multiple range sum queries |
| Binary Search | O(log n) | O(1) | Sorted data, monotonic search space |
| Monotonic Stack | O(n) | O(n) | Next/previous greater/smaller |
| Top K Elements | O(n log k) | O(k) | K largest/smallest/frequent, Kth element |
| Heap / Priority Queue | O(n log n) | O(n) | Median, merging streams, scheduling |
| Intervals | O(n log n) | O(1)-O(n) | Overlapping ranges |
| Graph Traversal | O(V+E) | O(V) | Connectivity, paths, components |
| Backtracking | O(2ⁿ)-O(n!) | O(n) | Generate all valid solutions |
| Greedy | O(n)-O(n log n) | O(1) | Local optimal → global optimal |
| Dynamic Programming | O(n)-O(n²) | O(n) | Overlapping subproblems, counting |

---

## 🎯 Problem-Solving Workflow

```
1. Read problem → Identify data structure
   ↓
2. Find signal words → Match to pattern(s)
   ↓
3. Check constraints → Verify pattern fit
   ↓
4. Choose pattern → Reference documentation
   ↓
5. Apply canonical skeleton → Customize to problem
   ↓
6. Trace example → Verify correctness
   ↓
7. Analyze complexity → Optimize if needed
```

---

## 📖 Documentation Format

Each pattern file includes:

- ✅ **Pattern Description** - What it is and when to use it
- ✅ **Canonical Skeleton** - Template code structure
- ✅ **8 Variants** - From simplest to complex (in collapsible sections)
- ✅ **Full State Space** - Exhaustive brute force approach
- ✅ **Pruned State Space** - Optimized approach
- ✅ **Brute Force Solution** - Naive implementation
- ✅ **Optimized Solution** - Efficient implementation
- ✅ **Code Walkthroughs** - Step-by-step variable traces
- ✅ **Big-O Analysis** - Time and space complexity
- ✅ **Key Takeaways** - Summary and mental models
- ✅ **Mental Checklist** - When to apply the pattern

---

## 🔥 Most Common Interview Patterns

**Top 5 Most Frequent:**

1. [**Hash Map / Frequency Counting**](hash_map_frequency_counting_pattern.md) - Appears in ~40% of problems
2. [**Two Pointers**](two_pointers_pattern.md) - Fundamental technique
3. [**Sliding Window**](sliding_window_pattern.md) - Very common for arrays/strings
4. [**Binary Search**](binary_search_pattern.md) - Classic search problems
5. [**Graph Traversal (BFS/DFS)**](graph_traversal_pattern.md) - Tree and graph problems

**Growing in Popularity:**

- [**Monotonic Stack**](monotonic_stack_pattern.md) - Increasingly common in interviews
- [**Heap / Priority Queue**](heaps_priority_queue_pattern.md) - Top K problems everywhere
- [**Dynamic Programming**](dynamic_programming_pattern.md) - Senior level interviews

---

## 💡 Tips for Success

### Before You Start:
1. ✅ Read [Pattern Recognition Guide](pattern_recognition_guide.md) first
2. ✅ Don't memorize solutions - understand the pattern
3. ✅ Practice identifying patterns before coding
4. ✅ Start with easier variants, build up complexity

### During Problem Solving:
1. ✅ Spend 30 seconds identifying the pattern
2. ✅ Reference the canonical skeleton
3. ✅ Draw the state space diagram
4. ✅ Trace a small example by hand
5. ✅ Code from skeleton, customize for problem

### After Solving:
1. ✅ Verify with different test cases
2. ✅ Analyze time/space complexity
3. ✅ Check if pattern combination improves solution
4. ✅ Review Key Takeaways in pattern file

---

## 🛠️ Additional Resources

- **Pattern Recognition:** [pattern_recognition_guide.md](pattern_recognition_guide.md)
- **Quick Reference:** [checklist.md](checklist.md)

---

## 📝 Pattern Files Index

### All Patterns (Alphabetical)

1. [Backtracking Pattern](backtracking_pattern.md)
2. [Binary Search Pattern](binary_search_pattern.md)
3. [Dynamic Programming Pattern (1D)](dynamic_programming_pattern.md)
4. [Fast & Slow Pointers Pattern](fast_slow_pointers_pattern.md)
5. [Graph Traversal Pattern (BFS/DFS)](graph_traversal_pattern.md)
6. [Greedy Pattern](greedy_pattern.md)
7. [Hash Map / Frequency Counting Pattern](hash_map_frequency_counting_pattern.md)
8. [Heap / Priority Queue Pattern](heaps_priority_queue_pattern.md)
9. [Intervals / Merging Pattern](intervals_merging_pattern.md)
10. [Monotonic Stack Pattern](monotonic_stack_pattern.md)
11. [Prefix Sum / Running Sum Pattern](prefix_sum_running_sum_pattern.md)
12. [Sliding Window Pattern](sliding_window_pattern.md)
13. [Top K Elements Pattern](top_k_elements_pattern.md)
14. [Two Pointers Pattern](two_pointers_pattern.md)

---

## 🎉 Getting Started

**New to algorithm patterns?**

1. Start with [Pattern Recognition Guide](pattern_recognition_guide.md)
2. Pick an easy pattern: [Two Pointers](two_pointers_pattern.md) or [Hash Map](hash_map_frequency_counting_pattern.md)
3. Read Variant #1 (simplest problem)
4. Try solving it yourself
5. Compare with the solution
6. Move to Variant #2

**Preparing for interviews?**

1. Review [Pattern Recognition Guide](pattern_recognition_guide.md) for quick identification
2. Focus on "Most Common Interview Patterns" section above
3. Practice one pattern per day (all 8 variants)
4. Mix problems from different patterns after week 1
5. Time yourself and optimize solutions

---

**Happy Coding! 🚀**

*Remember: Pattern recognition is more important than memorizing solutions. Once you identify the pattern, the solution follows naturally.*
