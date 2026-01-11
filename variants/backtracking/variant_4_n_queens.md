# N-Queens

**Difficulty:** Hard  
**LeetCode Problem:** #51  
**Tags:** `Backtracking`, `Constraint Satisfaction`, `Board Games`, `Classical Problem`

---

## Problem Statement

The **n-queens** puzzle is the problem of placing `n` queens on an `n x n` chessboard such that no two queens attack each other.

Given an integer `n`, return all distinct solutions to the **n-queens puzzle**. You may return the answer in **any order**.

Each solution contains a distinct board configuration of the n-queens' placement, where `'Q'` and `'.'` both indicate a queen and an empty space, respectively.

**Constraints:**
- `1 <= n <= 9`

**Example 1:**
```
Input: n = 4
Output: [[".Q..","...Q","Q...","..Q."],["..Q.","Q...","...Q",".Q.."]]
Explanation: There exist two distinct solutions to the 4-queens puzzle

Solution 1:          Solution 2:
. Q . .              . . Q .
. . . Q              Q . . .
Q . . .              . . . Q
. . Q .              . Q . .
```

**Example 2:**
```
Input: n = 1
Output: [["Q"]]
```

---

## Step 1: State Space

### Problem Visualization

**Constraint:** No two queens can attack each other.

```
Queen attacks:
  - Same row (horizontal)
  - Same column (vertical)
  - Same diagonal (2 diagonals: / and \)

Example: Queen at (2,1) attacks:
  Row 2: . Q . . (all of row 2)
  Col 1: vertical line through column 1
  Diag \: (0,3), (1,2), (2,1), (3,0)
  Diag /: (0,0), (1,1), (2,2), (3,3)... wait, that's wrong
  
Let me recalculate:
  Queen at (row=2, col=1):
    Same row: (2, any col)
    Same col: (any row, 1)
    Diag \: row - col = 2 - 1 = 1
            All (r,c) where r - c = 1: (1,0), (2,1), (3,2), (4,3)...
    Diag /: row + col = 2 + 1 = 3
            All (r,c) where r + c = 3: (0,3), (1,2), (2,1), (3,0)
```

**N=4 solution example:**

```
. Q . .     Row 0: Queen at column 1
. . . Q     Row 1: Queen at column 3
Q . . .     Row 2: Queen at column 0
. . Q .     Row 3: Queen at column 2

Check attacks:
  (0,1): attacks row 0, col 1, diag1 = -1, diag2 = 1
  (1,3): attacks row 1, col 3, diag1 = -2, diag2 = 4
  (2,0): attacks row 2, col 0, diag1 = 2, diag2 = 2
  (3,2): attacks row 3, col 2, diag1 = 1, diag2 = 5

No overlaps? ✓
  Rows: 0,1,2,3 all different
  Cols: 1,3,0,2 all different
  Diag1: -1,-2,2,1 all different
  Diag2: 1,4,2,5 all different
  
Valid solution!
```

**Decision tree (backtracking):**

```
Row-by-row placement:

Row 0: Try each column [0,1,2,3]
  ├─ Col 0: Place queen, mark attacks
  │   Row 1: Try valid columns (not attacked)
  │     ├─ Col 2: Place queen
  │     │   Row 2: No valid columns (all attacked)
  │     │   ✗ Dead end, backtrack
  │     ├─ Col 3: Place queen
  │     │   Row 2: Try Col 1
  │     │     Row 3: No valid columns
  │     │     ✗ Backtrack
  │     ✗ Backtrack to Row 0
  │
  ├─ Col 1: Place queen at (0,1)
  │   Row 1: Try Col 3
  │     Row 2: Try Col 0
  │       Row 3: Try Col 2
  │         ✓ Valid solution: [.Q.., ...Q, Q..., ..Q.]
  │
  ... continue for all positions
```

### Core Question

**How do we place N queens on an N×N board such that no two attack each other?**

Backtracking with:
1. **Row-by-row placement** (one queen per row)
2. **Column tracking** (no two in same column)
3. **Diagonal tracking** (no two on same diagonal)

### Deriving from First Principles

**Observation 1: One queen per row**
```
Must place exactly N queens on N×N board
If two queens in same row, they attack each other
Solution: Place one queen per row (guaranteed)

This reduces search space:
  Instead of n² positions (exponential)
  Try n positions per row, n rows
  → n^n without pruning (still exponential but structured)
```

**Observation 2: Track columns**
```
No two queens in same column
Use a Set or boolean array: cols[c] = true if column c occupied

When placing queen at (row, col):
  Check: cols[col] == false?
  If yes, mark cols[col] = true, recurse
  Backtrack: cols[col] = false
```

**Observation 3: Track diagonals**
```
Two types of diagonals:
  
  1. Main diagonals (top-left to bottom-right) \
     Property: row - col is constant
     Range: -(n-1) to (n-1)
     Example: (0,1), (1,2), (2,3) → row-col = -1
  
  2. Anti-diagonals (top-right to bottom-left) /
     Property: row + col is constant
     Range: 0 to 2(n-1)
     Example: (0,3), (1,2), (2,1) → row+col = 3

Store as: 
  diag1[row - col] = occupied
  diag2[row + col] = occupied

Handle negative indices:
  diag1[row - col + (n-1)] to make non-negative
  Or use HashSet<int>
```

**Observation 4: Backtracking structure**
```
Solve(row):
  if row == n:
    Found complete solution, add to result
    return
  
  for col in 0 to n-1:
    if safe to place at (row, col):
      Place queen
      Mark col, diag1, diag2 as occupied
      Solve(row + 1)
      Unmark (backtrack)
```

**Observation 5: Complexity**
```
Worst case: Try all positions
  Time: O(N!) - roughly, much better than N^N due to pruning
  Why N!? First row: N choices
           Second row: ~N-2 choices (one col, one diag occupied)
           Gets progressively smaller
  
Space: O(N) - recursion depth + tracking sets
```

**Formula (Backtracking):**
```
result = []
cols = Set()
diag1 = Set()  // row - col
diag2 = Set()  // row + col
board = n×n grid of '.'

Backtrack(row):
  if row == n:
    result.add(copy of board)
    return
  
  for col in 0 to n-1:
    d1 = row - col
    d2 = row + col
    
    if col in cols or d1 in diag1 or d2 in diag2:
      continue
    
    // Choose
    board[row][col] = 'Q'
    cols.add(col)
    diag1.add(d1)
    diag2.add(d2)
    
    // Explore
    Backtrack(row + 1)
    
    // Unchoose
    board[row][col] = '.'
    cols.remove(col)
    diag1.remove(d1)
    diag2.remove(d2)

Backtrack(0)
return result
```

### State Space Structure

**Type:** Constrained backtracking tree with heavy pruning.

**Structure:**
- Tree depth: n (one queen per row)
- Branching factor: varies (n at top, decreases due to conflicts)
- Total nodes: much less than n^n due to constraint pruning

**Cardinality:**
- Valid solutions: varies by n
  - n=1: 1 solution
  - n=4: 2 solutions
  - n=8: 92 solutions
- Time: O(N!) approximately
- Space: O(N) for tracking + recursion

**Key Property:** Row-by-row placement ensures one queen per row automatically.

### Example Computation

Input: `n = 4`

```
Backtrack(row=0):
  
  col=0:
    Place Q at (0,0)
    cols = {0}, diag1 = {0}, diag2 = {0}
    
    Backtrack(row=1):
      col=0: in cols, skip
      col=1: diag2 = 1, already used? No
             diag1 = 0, already used? Yes, skip
      col=2: Check: col=2 not in cols ✓
                    d1 = 1-2 = -1 not in diag1 ✓
                    d2 = 1+2 = 3 not in diag2 ✓
             Place Q at (1,2)
             cols = {0,2}, diag1 = {0,-1}, diag2 = {0,3}
             
             Backtrack(row=2):
               col=0: in cols, skip
               col=1: d1=1, d2=3 in diag2, skip
               col=2: in cols, skip
               col=3: d1=2-3=-1 in diag1, skip
               
               No valid columns → backtrack
             
             Restore (1,2)
      
      col=3: Check cols, diag1, diag2...
             Continue exploration...
    
    Eventually no solution from (0,0), backtrack
  
  col=1:
    Place Q at (0,1)
    cols = {1}, diag1 = {-1}, diag2 = {1}
    
    Backtrack(row=1):
      ... try columns ...
      col=3: Valid!
        Place Q at (1,3)
        
        Backtrack(row=2):
          col=0: Valid!
            Place Q at (2,0)
            
            Backtrack(row=3):
              col=2: Valid!
                Place Q at (3,2)
                
                Backtrack(row=4):
                  row == 4 == n, found solution!
                  Add [".Q..", "...Q", "Q...", "..Q."] ✓
                
                Restore (3,2)
              
              Continue col=3... (but not valid)
            
            Restore (2,0)
          
          Continue col=1,2,3...
        
        Restore (1,3)
      
      Continue other cols...
    
    Restore (0,1)
  
  col=2:
    ... explore all possibilities ...
    Eventually find second solution: ["..Q.", "Q...", "...Q", ".Q.."]
  
  col=3:
    ... explore ...

Result: 2 solutions found
```

---

## Step 2: Approaches

### Approach 1: Backtracking with Sets
Track columns and diagonals using HashSet

### Approach 2: Backtracking with Arrays
Use boolean arrays for columns/diagonals (faster)

### Approach 3: Bit Manipulation (Advanced)
Use bitmasks for tracking (space efficient)

---

## Step 3: Optimization Ideas

### Key Insights
1. **One queen per row:** Reduces search space dramatically
2. **Diagonal formula:** row-col and row+col identify diagonals
3. **Early pruning:** Skip invalid columns immediately
4. **No need for row tracking:** Placing row-by-row handles this

---

## Step 4: Optimal Solution (Backtracking with Sets)

```csharp
public class Solution {
    public IList<IList<string>> SolveNQueens(int n) {
        var result = new List<IList<string>>();
        var board = new char[n][];
        
        // Initialize board with '.'
        for (int i = 0; i < n; i++) {
            board[i] = new char[n];
            Array.Fill(board[i], '.');
        }
        
        var cols = new HashSet<int>();
        var diag1 = new HashSet<int>();  // row - col
        var diag2 = new HashSet<int>();  // row + col
        
        Backtrack(board, 0, cols, diag1, diag2, result);
        return result;
    }
    
    private void Backtrack(char[][] board, int row, HashSet<int> cols, HashSet<int> diag1, HashSet<int> diag2, IList<IList<string>> result) {
        int n = board.Length;
        
        // Base case: placed all queens
        if (row == n) {
            result.Add(board.Select(r => new string(r)).ToList());
            return;
        }
        
        // Try placing queen in each column
        for (int col = 0; col < n; col++) {
            int d1 = row - col;
            int d2 = row + col;
            
            // Check if position is under attack
            if (cols.Contains(col) || diag1.Contains(d1) || diag2.Contains(d2)) {
                continue;
            }
            
            // Choose: place queen
            board[row][col] = 'Q';
            cols.Add(col);
            diag1.Add(d1);
            diag2.Add(d2);
            
            // Explore: place queens in next rows
            Backtrack(board, row + 1, cols, diag1, diag2, result);
            
            // Unchoose: remove queen (backtrack)
            board[row][col] = '.';
            cols.Remove(col);
            diag1.Remove(d1);
            diag2.Remove(d2);
        }
    }
}
```

**Complexity:**
- Time: O(N!) - roughly, with heavy pruning
  - For N=8, only 92 solutions checked vs billions without pruning
- Space: O(N) - recursion depth + 3 sets
  - Output space O(N² × solutions) not counted

---

## Alternative: Boolean Arrays (Slightly Faster)

```csharp
public IList<IList<string>> SolveNQueens(int n) {
    var result = new List<IList<string>>();
    var board = new char[n][];
    
    for (int i = 0; i < n; i++) {
        board[i] = new char[n];
        Array.Fill(board[i], '.');
    }
    
    bool[] cols = new bool[n];
    bool[] diag1 = new bool[2 * n - 1];  // row - col + (n-1)
    bool[] diag2 = new bool[2 * n - 1];  // row + col
    
    Backtrack(board, 0, cols, diag1, diag2, result);
    return result;
}

private void Backtrack(char[][] board, int row, bool[] cols, bool[] diag1, bool[] diag2, IList<IList<string>> result) {
    int n = board.Length;
    
    if (row == n) {
        result.Add(board.Select(r => new string(r)).ToList());
        return;
    }
    
    for (int col = 0; col < n; col++) {
        int d1 = row - col + (n - 1);  // Offset for non-negative index
        int d2 = row + col;
        
        if (cols[col] || diag1[d1] || diag2[d2]) {
            continue;
        }
        
        board[row][col] = 'Q';
        cols[col] = diag1[d1] = diag2[d2] = true;
        
        Backtrack(board, row + 1, cols, diag1, diag2, result);
        
        board[row][col] = '.';
        cols[col] = diag1[d1] = diag2[d2] = false;
    }
}
```

**Why arrays can be faster:** O(1) lookup vs HashSet overhead.

---

## Edge Cases

1. **n=1:** Single queen `[["Q"]]`
2. **n=2, n=3:** No solutions (impossible to place)
3. **n=4:** 2 solutions
4. **n=8:** 92 solutions (classic 8-queens)

---

## Visualization

```
N=4 solution process:

Row 0: Try col 1
  . Q . .

Row 1: Try col 3 (can't use 0,1,2 - attacked)
  . Q . .
  . . . Q

Row 2: Try col 0 (only valid position)
  . Q . .
  . . . Q
  Q . . .

Row 3: Try col 2 (only valid position)
  . Q . .
  . . . Q
  Q . . .
  . . Q .

✓ Valid solution found!
```

---

## Related Problems

1. **N-Queens II (LeetCode #52)** - Count solutions only
2. **Sudoku Solver (LeetCode #37)** - Similar constraint satisfaction
3. **Grid Illumination (LeetCode #1001)** - Similar diagonal tracking
4. **Valid Sudoku (LeetCode #36)** - Constraint checking

---

## Pattern Recognition

**Problem Asks For:**
- Place N items on board
- Multiple constraints (no attacks)
- Find all valid configurations

**This Suggests:**
- Backtracking with constraint checking
- Row-by-row or position-by-position placement
- Track occupied rows/cols/diagonals
- → **Constraint Satisfaction Backtracking Pattern**

**Key Indicators:**
- Board/grid placement problem
- Multiple mutual exclusion constraints
- "No two items can X"
- Find all valid configurations

---

## Tags

`#backtracking` `#constraint-satisfaction` `#n-queens` `#classical` `#board-games` `#diagonal-tracking`
