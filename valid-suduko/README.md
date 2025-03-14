Below are three approaches to solve the **Valid Sudoku** problem, each documented with its code, an example walkthrough using the given input, and an analysis of its time complexity.

<!-- --- -->

<br>

## 1. Using Hash Sets for Row, Column, and Sub-box Validation

### **Idea:**
- We iterate through the board while maintaining three hash sets:
  - **row_sets**: Stores seen numbers for each row.
  - **col_sets**: Stores seen numbers for each column.
  - **box_sets**: Stores seen numbers for each 3×3 sub-box.
- For each cell `(i, j)`, we check:
  - If `board[i][j]` is already in `row_sets[i]`, return `False`.
  - If `board[i][j]` is already in `col_sets[j]`, return `False`.
  - If `board[i][j]` is already in `box_sets[(i // 3, j // 3)]`, return `False`.
- If no duplicate is found, return `True`.

### **Code:**
```python
class Solution:

    def isValidSudoku(self, board: List[List[str]]) -> bool:
        row_sets = [set() for _ in range(9)]
        col_sets = [set() for _ in range(9)]
        box_sets = [[set() for _ in range(3)] for _ in range(3)]
        
        for i in range(9):

            for j in range(9):
                num = board[i][j]

                if num == ".":
                    continue
                
                if num in row_sets[i]:  
                    return False  
                if num in col_sets[j]:  
                    return False  
                if num in box_sets[i // 3][j // 3]:  
                    return False  
                
                row_sets[i].add(num)
                col_sets[j].add(num)
                box_sets[i // 3][j // 3].add(num)
        
        return True
```

---

### **Example Walkthrough:**
For the input:
```python
board = [["5","3",".",".","7",".",".",".","."]
        ,["6",".",".","1","9","5",".",".","."]
        ,[".","9","8",".",".",".",".","6","."]
        ,["8",".",".",".","6",".",".",".","3"]
        ,["4",".",".","8",".","3",".",".","1"]
        ,["7",".",".",".","2",".",".",".","6"]
        ,[".","6",".",".",".",".","2","8","."]
        ,[".",".",".","4","1","9",".",".","5"]
        ,[".",".",".",".","8",".",".","7","9"]]
```
- The function checks each cell row-wise.
- For each number, it ensures that it's not in the corresponding row, column, or 3×3 box.
- No duplicate numbers are found.
- Returns **`True`**.

For the invalid input (Example 2):
```python
board[0][0] = "8"
```
- The function detects a duplicate `"8"` in the top-left 3×3 box.
- Returns **`False`**.

---

### **Time Complexity:**
- **O(n2) -> O(9×9) = O(81) ≈ O(1)**  
  - We iterate through all 81 cells and check/add to sets, which take **O(1)** operations each.
  
### **Space Complexity:**
- **O(n) -> O(9×3) = O(27) ≈ O(1)**  
  - We use three lists of sets (one per row, column, and box), each storing at most 9 elements.

<!-- --- -->

<br>

## 2. Using a Single Set for Efficient Checking

### **Idea:**
- Instead of separate sets for rows, columns, and boxes, use a **single set** to track visited numbers.
- Store tuples in the set:
  - `(num, "row", row_index)`
  - `(num, "col", col_index)`
  - `(num, "box", box_index)`
- If a tuple exists, the board is invalid.

### **Code:**
```python
class Solution:

    def isValidSudoku(self, board: List[List[str]]) -> bool:
        seen = set()
        
        for i in range(9):

            for j in range(9):
                num = board[i][j]

                if num == ".":
                    continue
                
                if (num, "row", i) in seen or (num, "col", j) in seen or (num, "box", i // 3, j // 3) in seen:
                    return False
                
                seen.add((num, "row", i))
                seen.add((num, "col", j))
                seen.add((num, "box", i // 3, j // 3))
        
        return True
```

---

### **Time Complexity:**
- **O(1)** (Constant-time due to fixed board size).

### **Space Complexity:**
- **O(1)** (Single set storing at most 81 elements).

<!-- --- -->

<br>

## 3. Using Bitwise Operations for Optimal Space Complexity

### **Idea:**
- Instead of sets, use three **bit-masks** (integers) for rows, columns, and boxes.
- Use bitwise operations to check/set whether a number exists in a row, column, or box.

### **Code:**
```python
from typing import List

class Solution:
    def isValidSudoku(self, board: List[List[str]]) -> bool:
        row_bits = [0] * 9
        col_bits = [0] * 9
        box_bits = [0] * 9
        
        for i in range(9):
            for j in range(9):
                num = board[i][j]
                if num == ".":
                    continue
                
                bit = 1 << (ord(num) - ord('1'))
                box_index = (i // 3) * 3 + (j // 3)
                
                if (row_bits[i] & bit) or (col_bits[j] & bit) or (box_bits[box_index] & bit):
                    return False
                
                row_bits[i] |= bit
                col_bits[j] |= bit
                box_bits[box_index] |= bit
        
        return True
```

---

### **Time Complexity:**
- **O(1)** (Fixed operations for a 9×9 board).

### **Space Complexity:**
- **O(1)** (Uses only 27 integers instead of sets).

<!-- --- -->

<br>

## Summary Comparison

| Approach                         | Time Complexity | Space Complexity | Comments                                                   |
|----------------------------------|---------------|----------------|------------------------------------------------------------|
| **Hash Sets for Rows, Cols, Boxes** | O(1) (81 iterations) | O(1) (3×9 sets) | Simple, readable, and efficient.                          |
| **Single Set with Tuples**       | O(1) | O(1) | Optimized by reducing space complexity using a single set. |
| **Bitwise Masking**               | O(1) | O(1) (27 integers) | Highly optimized in terms of memory, fastest execution.   |

The **bitwise masking approach** is the most optimal in terms of space and performance, but it is harder to understand. The **hash-set-based approach** is easier to implement and is a good balance between clarity and efficiency.
