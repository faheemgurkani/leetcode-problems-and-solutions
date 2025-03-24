Below are three approaches to solve the **Longest Consecutive Sequence** problem, each documented with its code, an example walkthrough using the input  
```python
nums = [100,4,200,1,3,2]
```  
and analysis of its time complexity.

<!-- --- -->

<br>

## 1. Brute Force Approach

**Idea:**  
Check every number in the array and determine the longest consecutive sequence that starts from it. We do this by checking if `num + 1`, `num + 2`, ... exist in the array. This results in a high time complexity due to repeated lookups.

### **Code:**
```python
class Solution:

    def longestConsecutive(self, nums: List[int]) -> int:
    
        if not nums:
            return 0
        
        max_length = 0
    
        for num in nums:
            current_length = 1
    
            while num + 1 in nums:
                num += 1
                current_length += 1
    
            max_length = max(max_length, current_length)
        
        return max_length
```

### **Example Walkthrough:**
1. **For `num = 100`:** No `101` exists → Length `1`.
2. **For `num = 4`:** No `5` exists → Length `1`.
3. **For `num = 200`:** No `201` exists → Length `1`.
4. **For `num = 1`:** Sequence `1 → 2 → 3 → 4` → Length `4`.

**Final Output:**
```python
4
```

### **Time Complexity:**
- **O(n²)** (For each number, we perform up to **O(n)** lookups).

<!-- --- -->

<br>

## 2. Sorting-Based Approach

**Idea:**  
Sort the numbers and iterate through them, counting the length of consecutive sequences.

### **Code:**
```python
class Solution:
    
    def longestConsecutive(self, nums: List[int]) -> int:
    
        if not nums:
            return 0
        
        nums.sort()
        max_length = 1
        current_length = 1
        
        for i in range(1, len(nums)):
    
            if nums[i] == nums[i-1]:
                continue  # Skip duplicates
    
            if nums[i] == nums[i-1] + 1:
                current_length += 1
            else:
                max_length = max(max_length, current_length)
                current_length = 1
        
        return max(max_length, current_length)
```

### **Example Walkthrough:**
- **Sorted `nums` = `[1, 2, 3, 4, 100, 200]`**
- Sequence `1 → 2 → 3 → 4` → Length `4`.
- No further consecutive numbers found.

**Final Output:**
```python
4
```

### **Time Complexity:**
- **O(n log n)** (Due to sorting, then **O(n)** iteration).

<!-- --- -->

<br>

## 3. Hash Set Approach (Optimal Solution)

**Idea:**  
Use a **hash set** to store numbers and iterate only over numbers that might start a sequence.

### **Code:**
```python
class Solution:
    
    def longestConsecutive(self, nums: List[int]) -> int:
    
        if not nums:
            return 0
        
        num_set = set(nums)
        max_length = 0
        
        for num in num_set:
    
            if num - 1 not in num_set:  # Check only starting numbers
                current_length = 1
    
                while num + 1 in num_set:
                    num += 1
                    current_length += 1
    
                max_length = max(max_length, current_length)
        
        return max_length
```

### **Example Walkthrough:**
- **Using HashSet `{100,4,200,1,3,2}`:**
- Start at `100`: Length `1`.
- Start at `4`: Length `1`.
- Start at `200`: Length `1`.
- Start at `1`: Sequence `1 → 2 → 3 → 4` → Length `4`.

**Final Output:**
```python
4
```

### **Time Complexity:**
- **O(n)** (Each number is processed at most twice: once in set creation and once in iteration).

<!-- --- -->

<br>

## Summary Comparison

| Approach                  | Time Complexity  | Space Complexity | Comments |
|--------------------------|----------------|----------------|----------|
| **Brute Force**          | O(n²)         | O(1)           | Too slow for large `n`. |
| **Sorting-Based**        | O(n log n)    | O(1)           | Faster but not optimal. |
| **Hash Set Approach**    | O(n)          | O(n)           | Best choice; runs in linear time. |

Each method correctly finds the longest consecutive sequence, but the **hash set approach** is the most efficient one.
