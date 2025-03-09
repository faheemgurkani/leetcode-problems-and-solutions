Below are three approaches to solve the **Product of Array Except Self** problem, each documented with its code, an example walkthrough using the input  
```python
nums = [1, 2, 3, 4]
```  
and analysis of its time complexity.

<!-- --- -->

<br>

## 1. Brute Force Approach

**Idea:**  
For each element in the array, compute the product of all other elements by iterating through the entire array (skipping the current element). This approach uses two nested loops and is not optimal for large inputs due to its quadratic time complexity.

### **Code:**
```python
class Solution:
    
    def productExceptSelf(self, nums: List[int]) -> List[int]:
        n = len(nums)
        answer = [1] * n
        
        # For each element, multiplying all other elements
        for i in range(n):
            prod = 1
            
            for j in range(n):
            
                if i != j:
                    prod *= nums[j]
            
            answer[i] = prod
        
        return answer
```

### **Example Walkthrough:**
For `nums = [1, 2, 3, 4]`:
1. **Index 0:**  
   - Compute product of `[2, 3, 4]` → `2 * 3 * 4 = 24`  
   - `answer[0] = 24`
2. **Index 1:**  
   - Compute product of `[1, 3, 4]` → `1 * 3 * 4 = 12`  
   - `answer[1] = 12`
3. **Index 2:**  
   - Compute product of `[1, 2, 4]` → `1 * 2 * 4 = 8`  
   - `answer[2] = 8`
4. **Index 3:**  
   - Compute product of `[1, 2, 3]` → `1 * 2 * 3 = 6`  
   - `answer[3] = 6`

**Final Output:**  
```python
[24, 12, 8, 6]
```

### **Time Complexity:**
- **O(n²):**  
  For each of the **n** elements, a nested loop iterates over **n-1** elements.

<!-- --- -->

<br>

## 2. Prefix and Suffix Arrays Approach

**Idea:**  
Compute two separate arrays:
- **Prefix Array:** Contains the product of all elements to the left of each index.
- **Suffix Array:** Contains the product of all elements to the right of each index.

The final answer for each index is the product of the corresponding prefix and suffix values.

### **Code:**
```python
class Solution:
    
    def productExceptSelf(self, nums: List[int]) -> List[int]:
        n = len(nums)
        prefix = [1] * n
        suffix = [1] * n
        
        # Building prefix product array
        for i in range(1, n):
            prefix[i] = prefix[i - 1] * nums[i - 1]
        
        # Building suffix product array
        for i in range(n - 2, -1, -1):
            suffix[i] = suffix[i + 1] * nums[i + 1]
        
        # Multiplying prefix and suffix arrays to get the answer
        answer = [prefix[i] * suffix[i] for i in range(n)]
        
        return answer
```

### **Example Walkthrough:**
For `nums = [1, 2, 3, 4]`:
- **Prefix Array Calculation:**
  - `prefix[0] = 1`
  - `prefix[1] = prefix[0] * 1 = 1`
  - `prefix[2] = prefix[1] * 2 = 2`
  - `prefix[3] = prefix[2] * 3 = 6`
  
  → `prefix = [1, 1, 2, 6]`

- **Suffix Array Calculation:**
  - `suffix[3] = 1`
  - `suffix[2] = suffix[3] * 4 = 4`
  - `suffix[1] = suffix[2] * 3 = 12`
  - `suffix[0] = suffix[1] * 2 = 24`
  
  → `suffix = [24, 12, 4, 1]`

- **Final Calculation:**
  - `answer[0] = 1 * 24 = 24`
  - `answer[1] = 1 * 12 = 12`
  - `answer[2] = 2 * 4 = 8`
  - `answer[3] = 6 * 1 = 6`

**Final Output:**  
```python
[24, 12, 8, 6]
```

### **Time Complexity:**
- **O(n):**  
  Two passes (one for the prefix and one for the suffix) and one final pass to compute the answer.

### **Space Complexity:**
- **O(n):**  
  Additional space is used for the prefix and suffix arrays.

<!-- --- -->

<br>

## 3. Optimized Pre and Post Variables Approach (Current Solution)

**Idea:**  
Use the output array to store the prefix products in the first pass. Then, in a second pass, traverse from right to left while maintaining a running suffix product (post variable) and multiply it with the corresponding element in the output array. This approach uses two passes and constant extra space (aside from the output array).

### **Code:**
```python
class Solution:
    
    def productExceptSelf(self, nums: List[int]) -> List[int]:
        n = len(nums)
        answer = [1] * n
        
        # Pre Pass: Building prefix product in the answer array
        for i in range(1, n):
            answer[i] = answer[i - 1] * nums[i - 1]
        
        # Post Pass: Multiplying with suffix product using a running variable
        post = 1
        
        for i in range(n - 1, -1, -1):
            answer[i] *= post
            post *= nums[i]
        
        return answer
```

### **Example Walkthrough:**
For `nums = [1, 2, 3, 4]`:
1. **Pre Pass (Prefix Products):**
   - **Initialize:** `answer = [1, 1, 1, 1]`
   - **i = 1:**  
     `answer[1] = answer[0] * nums[0] = 1 * 1 = 1`  
     → `answer = [1, 1, 1, 1]`
   - **i = 2:**  
     `answer[2] = answer[1] * nums[1] = 1 * 2 = 2`  
     → `answer = [1, 1, 2, 1]`
   - **i = 3:**  
     `answer[3] = answer[2] * nums[2] = 2 * 3 = 6`  
     → `answer = [1, 1, 2, 6]`
   
2. **Post Pass (Suffix Products):**
   - **Initialize:** `post = 1`
   - **i = 3:**  
     `answer[3] = answer[3] * post = 6 * 1 = 6`  
     Update `post = post * nums[3] = 1 * 4 = 4`
   - **i = 2:**  
     `answer[2] = answer[2] * post = 2 * 4 = 8`  
     Update `post = post * nums[2] = 4 * 3 = 12`
   - **i = 1:**  
     `answer[1] = answer[1] * post = 1 * 12 = 12`  
     Update `post = post * nums[1] = 12 * 2 = 24`
   - **i = 0:**  
     `answer[0] = answer[0] * post = 1 * 24 = 24`  
     Update `post = post * nums[0] = 24 * 1 = 24`
   
**Final Output:**  
```python
[24, 12, 8, 6]
```

### **Time Complexity:**
- **O(2n):**  
  Two passes over the array (pre and post), which simplifies to **O(n)**.

### **Space Complexity:**
- **O(1) Extra Space:**  
  Only constant extra space is used aside from the output array.

<br>

<!-- --- -->

## Summary Comparison

| Approach                          | Time Complexity      | Space Complexity  | Comments                                                         |
|-----------------------------------|----------------------|-------------------|------------------------------------------------------------------|
| **Brute Force**                   | O(n²)                | O(1)              | Not optimal; computes product for each element with nested loops |
| **Prefix and Suffix Arrays**      | O(n)                 | O(n)              | Uses extra arrays for prefix and suffix products                 |
| **Optimized Pre and Post**        | O(2n) → O(n)         | O(1)              | Two passes with constant extra space; most optimal solution         |

Each method correctly computes the product of all elements except self, but the **optimized pre and post variables approach** is generally the best choice when performance and space are critical.