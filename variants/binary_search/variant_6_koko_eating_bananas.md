# Koko Eating Bananas (Binary Search on Answer Space)

**Difficulty:** Medium  
**LeetCode Problem:** #875  
**Tags:** `Binary Search`, `Answer Space`, `Simulation`

---

## Problem Statement

Koko loves to eat bananas. There are `n` piles of bananas, the `i-th` pile has `piles[i]` bananas. The guards have gone and will come back in `h` hours.

Koko can decide her bananas-per-hour eating speed of `k`. Each hour, she chooses some pile of bananas and eats `k` bananas from that pile. If the pile has less than `k` bananas, she eats all of them instead and will not eat any more bananas during this hour.

Return the minimum integer `k` such that she can eat all the bananas within `h` hours.

**Constraints:**
- `1 <= piles.length <= 10^4`
- `piles.length <= h`
- `1 <= piles[i] <= 10^9`
- `1 <= h <= 10^9`

**Example 1:**
```
Input: piles = [3,6,7,11], h = 8
Output: 4
Explanation: 
At k=4: 3/4=1hr, 6/4=2hr, 7/4=2hr, 11/4=3hr → Total 8hrs ✓
At k=3: 3/3=1hr, 6/3=2hr, 7/3=3hr, 11/3=4hr → Total 10hrs ✗
```

**Example 2:**
```
Input: piles = [30,11,23,4,20], h = 5
Output: 30
```

---

## Step 1: State Space

### Problem Visualization

For `piles = [3,6,7,11]`, `h = 8`:

```
k=1:  3hrs + 6hrs + 7hrs + 11hrs = 27hrs  (too slow)
k=2:  2hrs + 3hrs + 4hrs + 6hrs = 15hrs   (too slow)
k=3:  1hr + 2hrs + 3hrs + 4hrs = 10hrs    (too slow)
k=4:  1hr + 2hrs + 2hrs + 3hrs = 8hrs     ✓ minimum!
k=5:  1hr + 2hrs + 2hrs + 3hrs = 8hrs     ✓
k=6:  1hr + 1hr + 2hrs + 2hrs = 6hrs      ✓
...
k=11: 1hr + 1hr + 1hr + 1hr = 4hrs        ✓

Answer: k=4 (minimum k that satisfies ≤ 8hrs)
```

Time calculation for each pile at speed k:
```
pile = 7, k = 4:
  hours = ceil(7/4) = ceil(1.75) = 2 hours
  
pile = 11, k = 4:
  hours = ceil(11/4) = ceil(2.75) = 3 hours
```

### Core Question

**What is the minimum eating speed k such that all bananas can be eaten within h hours?**

This is a "minimum value that satisfies condition" problem. We're searching for k in the range [1, max(piles)].

### Deriving from First Principles

**Observation 1: Feasibility function**
For a given speed k, we can check if it's possible to eat all bananas in h hours:
```
canEatAll(k) = (sum of ceil(pile/k) for each pile) <= h
```

**Observation 2: Monotonic property**
If k works, then any k' > k also works (faster speed means less time).
If k doesn't work, then any k' < k also doesn't work.

This creates a boundary:
```
k: 1  2  3  4  5  6  7  8  9  10 11
   ✗  ✗  ✗  ✓  ✓  ✓  ✓  ✓  ✓  ✓  ✓
            ↑
         answer (leftmost ✓)
```

**Observation 3: Binary search on answer space**
Instead of binary search on array indices, we binary search on possible values of k:
- Search space: [1, max(piles)]
- Condition: Can Koko finish in h hours at speed k?
- Goal: Find minimum k that satisfies condition

**Observation 4: Time calculation**
For each pile with p bananas and speed k:
```
hours = ceil(p / k) = (p + k - 1) / k  (using integer division)
```

**Observation 5: Why max(piles) is upper bound**
At k = max(piles), Koko can eat the largest pile in 1 hour, and all smaller piles in 1 hour each. This guarantees finishing within n hours, which is ≤ h (per constraints).

**Formula:**
```
canFinish(k):
  totalHours = sum of ceil(pile / k) for each pile
  return totalHours <= h

binarySearch():
  left = 1, right = max(piles)
  while left < right:
    mid = left + (right - left) / 2
    if canFinish(mid):
      right = mid     // Try slower speed
    else:
      left = mid + 1  // Need faster speed
  return left
```

### State Space Structure

**Type:** Binary search on answer space (possible values of k).

**Structure:**
- Range [left, right] where answer lies
- Feasibility function: Can finish with speed k?
- Find leftmost k where feasible

**Cardinality:** 
- Range: [1, max(piles)] ≤ 10^9
- O(log(max(piles))) iterations
- Each iteration: O(n) to check feasibility
- Total: O(n log(max(piles)))

**Key Property:** Monotonic feasibility—if k works, all larger k work; this enables binary search.

### Example Computation

Input: `piles = [3,6,7,11]`, `h = 8`

```
Initial: left=1, right=11

Iteration 1:
  mid = 6
  canFinish(6)?
    ceil(3/6)=1, ceil(6/6)=1, ceil(7/6)=2, ceil(11/6)=2
    Total = 6 <= 8 ✓
  right = 6

Iteration 2:
  left=1, right=6, mid=3
  canFinish(3)?
    ceil(3/3)=1, ceil(6/3)=2, ceil(7/3)=3, ceil(11/3)=4
    Total = 10 > 8 ✗
  left = 4

Iteration 3:
  left=4, right=6, mid=5
  canFinish(5)?
    ceil(3/5)=1, ceil(6/5)=2, ceil(7/5)=2, ceil(11/5)=3
    Total = 8 <= 8 ✓
  right = 5

Iteration 4:
  left=4, right=5, mid=4
  canFinish(4)?
    ceil(3/4)=1, ceil(6/4)=2, ceil(7/4)=2, ceil(11/4)=3
    Total = 8 <= 8 ✓
  right = 4

left=4, right=4 → Return 4
```

### Generation Pattern

**Brute force (linear search):**
```csharp
public int MinEatingSpeed(int[] piles, int h) {
    int maxPile = piles.Max();
    for (int k = 1; k <= maxPile; k++) {
        if (CanFinish(piles, k, h)) {
            return k;
        }
    }
    return maxPile;
}
// Time: O(n × max(piles))
```

---

## Step 2: Brute Force

Try every speed from 1 to max(piles), return first that works: O(n × max(piles)).

---

## Step 3: Optimization Ideas

### Key Insights
1. **Monotonic feasibility:** If k works, larger k works
2. **Binary search on k:** Search space is [1, max(piles)]
3. **Find leftmost feasible k:** Minimum speed that satisfies condition

---

## Step 4: Optimal Solution

```csharp
public int MinEatingSpeed(int[] piles, int h) {
    int left = 1;
    int right = piles.Max();
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        
        if (CanFinish(piles, mid, h)) {
            right = mid;  // Try slower speed
        } else {
            left = mid + 1;  // Need faster speed
        }
    }
    
    return left;
}

private bool CanFinish(int[] piles, int k, int h) {
    long hours = 0;
    
    foreach (int pile in piles) {
        // Ceiling division: (pile + k - 1) / k
        hours += (pile + k - 1) / k;
        if (hours > h) return false;  // Early termination
    }
    
    return hours <= h;
}
```

**Complexity:**
- Time: O(n log(max(piles)))
  - Binary search: O(log(max(piles)))
  - Each iteration checks feasibility: O(n)
- Space: O(1)

**Key Mechanics:**
1. **Binary search on answer:** Not on array, but on possible k values
2. **Feasibility check:** Simulate eating at speed k
3. **Ceiling division:** `(pile + k - 1) / k` computes hours without floats
4. **Find leftmost feasible:** Template 2 (left < right, right = mid)

---

## Edge Cases

1. **Each pile takes 1 hour:** `piles=[3,6,7,11], h=4` → `k=11` (max pile)
2. **Plenty of time:** `piles=[30,11,23,4,20], h=100` → `k=1`
3. **Single pile:** `piles=[1000], h=10` → `k=100`
4. **All piles equal:** `piles=[5,5,5,5], h=8` → `k=3`

---

## Related Problems

1. **Capacity To Ship Packages (LeetCode #1011)** - Same pattern
2. **Minimum Size Subarray Sum (LeetCode #209)** - Sliding window alternative
3. **Split Array Largest Sum (LeetCode #410)** - Binary search on answer

---

## Pattern Recognition

**Problem Asks For:**
- "Minimum value such that condition is satisfied"
- "Maximum value such that condition is satisfied"
- Answer space has monotonic property

**This Suggests:**
- Binary search on answer space (not on array)
- Need feasibility function
- Find boundary where condition changes

**Red Flags for Binary Search on Answer:**
- "Minimum speed/capacity/size such that..."
- "Maximum days/splits such that..."
- Checking feasibility is easier than finding optimal directly

---

## Tags

`#binary-search` `#answer-space` `#feasibility` `#simulation` `#monotonic-property` `#ceiling-division`
