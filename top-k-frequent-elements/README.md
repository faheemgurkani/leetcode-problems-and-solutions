Below are two approaches to solve the **Top K Frequent Elements** problem, each documented with its code, an example walkthrough using the input  
```python
nums = [1, 1, 1, 2, 2, 3]
k = 2
```  
and analysis of its time complexity.

<!-- --- -->

<br>

## 1. Min-Heap Approach (Using Hash Map + Priority Queue)

**Idea:**  
1. Use a hash map (`Counter`) to count the frequency of each element.
2. Use a **min-heap (priority queue)** to maintain the top `k` frequent elements.  
   - Push elements into the heap in the form `(frequency, number)`.
   - If the heap size exceeds `k`, remove the least frequent element.
3. Extract the `k` most frequent elements from the heap.

### **Code:**
```python
from typing import List
from collections import Counter
import heapq

class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        # Counting the frequency of each element
        freq_map = Counter(nums)
        
        # Using a min-heap to keep track of top k elements
        min_heap = []
        
        for num, freq in freq_map.items():
            heapq.heappush(min_heap, (freq, num))  # Pushing (frequency, number)

            if len(min_heap) > k:
                heapq.heappop(min_heap)  # Removing the smallest frequency element
        
        # Extracting elements from the heap
        return [num for freq, num in min_heap]
```

### **Example Walkthrough:**
#### Input:
```python
nums = [1, 1, 1, 2, 2, 3]
k = 2
```

1. **Frequency Count:**  
   - `{1: 3, 2: 2, 3: 1}`

2. **Heap Operations:**
   - Push `(3, 1)`: `[ (3,1) ]`
   - Push `(2, 2)`: `[ (2,2), (3,1) ]`
   - Push `(1, 3)`: `[ (1,3), (3,1), (2,2) ]`
   - Heap exceeds `k`, so pop the smallest → `[ (2,2), (3,1) ]`

3. **Extract Result:** `[1, 2]`

#### **Output:**
```python
[1, 2]
```

### **Time Complexity:**
- **O(N)** for counting frequencies.
- **O(N log k)** for heap operations.
- **O(k)** for extracting elements.
- **Total: O(N log k)** (efficient when `k << N`).

### **Space Complexity:**
- **O(N)** for hash map storage.
- **O(k)** for heap storage.
- **Total: O(N + k) ≈ O(N)**.

<!-- --- -->

<br>

## 2. Sorting-Based Approach (Using Hash Map + Sorting)

**Idea:**  
1. Use a hash map (`Counter`) to count the frequency of each element.
2. Convert the hash map into a list of `(element, frequency)` pairs.
3. Sort this list in descending order based on frequency.
4. Extract the first `k` elements.

### **Code:**
```python
from typing import List
from collections import Counter

class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        # Counting the frequency of each element
        freq_map = Counter(nums)
        
        # Sorting elements by frequency in descending order
        sorted_elements = sorted(freq_map.keys(), key=lambda num: -freq_map[num])
        
        # Returning the top k elements
        return sorted_elements[:k]
```

### **Example Walkthrough:**
#### Input:
```python
nums = [1, 1, 1, 2, 2, 3]
k = 2
```

1. **Frequency Count:**  
   - `{1: 3, 2: 2, 3: 1}`

2. **Sorting by Frequency:**
   - Sorting keys by frequency: `[1, 2, 3]`

3. **Extract First `k = 2` Elements:**  
   - `[1, 2]`

#### **Output:**
```python
[1, 2]
```

### **Time Complexity:**
- **O(N)** for frequency count.
- **O(N log N)** for sorting.
- **O(k)** for slicing the list.
- **Total: O(N log N)** (slower than heap approach when `k` is small).

### **Space Complexity:**
- **O(N)** for hash map storage.
- **O(N)** for sorted list.
- **Total: O(N)**.

<br>

<!-- --- -->

## Summary Comparison

| Approach                          | Time Complexity      | Space Complexity  | Comments                                            |
|-----------------------------------|----------------------|-------------------|-----------------------------------------------------|
| **Min-Heap (Priority Queue)**     | O(N log k)          | O(N + k) ≈ O(N)  | Efficient when `k` is small, avoids full sorting    |
| **Sorting-Based Approach**        | O(N log N)          | O(N)             | Simpler but slower for large `N`, due to sorting   |


Use the **Min-Heap approach** when `k` is small (`k << N`), as it avoids full sorting.
Use **Sorting-Based approach** if `k` is close to `N`, since sorting is more straightforward.
