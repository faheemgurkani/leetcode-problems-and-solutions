Below are three approaches to solve the **Group Anagrams** problem, each documented with its code, an example walkthrough using the input  
```python
strs = ["eat", "tea", "tan", "ate", "nat", "bat"]
```  
and analysis of its time complexity.

<!-- --- -->

<br>

## 1. Brute Force Approach

**Idea:**  
For every word, compare it with every other word to check if they are anagrams (using a helper function, e.g., by sorting the characters). Group words that are anagrams together. This approach does not use any auxiliary data structure for fast lookups and thus is not optimal for large inputs.

### **Code:**
```python
class Solution:

    def isAnagram(self, s: str, t: str) -> bool:
        # Checking anagram by comparing sorted versions of the strings
        return sorted(s) == sorted(t)
    
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        n = len(strs)
        used = [False] * n  # Tracking whether a word has been grouped
        groups = []
        
        # Comparing each word with every other word
        for i in range(n):
            
            if used[i]:
                continue
            
            current_group = [strs[i]]
            used[i] = True
            
            for j in range(i + 1, n):
            
                if not used[j] and self.isAnagram(strs[i], strs[j]):
                    current_group.append(strs[j])
                    used[j] = True
            
            groups.append(current_group)
        
        return groups
```

### **Example Walkthrough:**
1. **Iteration for `i = 0` ("eat"):**  
   - Compare with "tea": `isAnagram("eat", "tea")` → *True* → add "tea".  
   - Compare with "tan": → *False*.  
   - Compare with "ate": → *True* → add "ate".  
   - Compare with "nat" and "bat": → *False*.  
   - Group becomes: `["eat", "tea", "ate"]`.

2. **Iteration for `i = 2` ("tan"):**  
   - "tan" was not grouped yet.  
   - Compare with "nat": → *True* → add "nat".  
   - Group becomes: `["tan", "nat"]`.

3. **Iteration for remaining word "bat":**  
   - It remains on its own: `["bat"]`.

**Final Output:**  
```python
[["eat", "tea", "ate"], ["tan", "nat"], ["bat"]]
```

### **Time Complexity:**
- **O(n² * k):**  
  For each of the **n** words, we compare it with the remaining words. Each comparison (via sorting inside `isAnagram`) costs **O(k log k)** on average, or **O(k)** if using frequency count, but the nested loop still gives a quadratic factor.

<!-- --- -->

<br>

## 2. Sorting-Based Approach

**Idea:**  
For each word, sort its characters and use the sorted tuple as a key in a hash map (dictionary). All words that are anagrams will have the same sorted key and are grouped together.

### **Code:**
```python
from collections import defaultdict

class Solution:

    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        anagram_map = defaultdict(list)
        
        for word in strs:
            key = tuple(sorted(word))  # Sorted tuple of characters as key
            anagram_map[key].append(word)
        
        return list(anagram_map.values())
```

### **Example Walkthrough:**
- **For "eat":**  
  - Sorted: `("a", "e", "t")` → group becomes `["eat"]`.

- **For "tea":**  
  - Sorted: `("a", "e", "t")` → same key → group becomes `["eat", "tea"]`.

- **For "tan":**  
  - Sorted: `("a", "n", "t")` → new group: `["tan"]`.

- **For "ate":**  
  - Sorted: `("a", "e", "t")` → group becomes `["eat", "tea", "ate"]`.

- **For "nat":**  
  - Sorted: `("a", "n", "t")` → group becomes `["tan", "nat"]`.

- **For "bat":**  
  - Sorted: `("a", "b", "t")` → new group: `["bat"]`.

**Final Output:**  
```python
[["eat", "tea", "ate"], ["tan", "nat"], ["bat"]]
```

### **Time Complexity:**
- **O(n * k log k):**  
  Sorting each word of maximum length **k** and iterating over **n** words.

- **Space Complexity:**  
  **O(n * k)** for storing all words in the hash map.

<!-- --- -->

<br>

## 3. Optimized Hash Map with Frequency Count (Current Solution)

**Idea:**  
Instead of sorting, count the frequency of each letter (using an array of size 26 for lowercase English letters). Convert the frequency array into a tuple to use as a hashable key in a dictionary. This avoids the overhead of sorting and runs in linear time with respect to word length.

### **Code:**
```python
from collections import defaultdict

class Solution:
    
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        anagram_map = defaultdict(list)  
        
        for word in strs:
            freq = [0] * 26
            
            for char in word:
                freq[ord(char) - ord('a')] += 1  
            
            key = tuple(freq) 
            anagram_map[key].append(word)
        
        return list(anagram_map.values())
```

### **Example Walkthrough:**
- **For "eat":**  
  - Frequency array for `"eat"` becomes:  
    `[1 (for 'a'), 0, 0, 0, 1 (for 'e'), 0, …, 1 (for 't'), 0, …]` → key: `tuple([1,0,0,0,1,...,1,...,0])`  
  - Group becomes: `["eat"]`.

- **For "tea":**  
  - Same frequency key as `"eat"` → group becomes: `["eat", "tea"]`.

- **For "tan":**  
  - Frequency key for `"tan"` is different (e.g., count 1 for 'a', 1 for 'n', 1 for 't') → new group: `["tan"]`.

- **For "ate":**  
  - Same frequency key as `"eat"` → group becomes: `["eat", "tea", "ate"]`.

- **For "nat":**  
  - Same frequency key as `"tan"` → group becomes: `["tan", "nat"]`.

- **For "bat":**  
  - Unique frequency key (different counts for 'b', 'a', 't') → group becomes: `["bat"]`.

**Final Output:**  
```python
[["eat", "tea", "ate"], ["tan", "nat"], ["bat"]]
```

### **Time Complexity:**
- **O(n * k):**  
  For each of the **n** words, we count frequencies in **O(k)** time.

- **Space Complexity:**  
  **O(n * k)** for storing keys in the hash map and the grouped words.

<br>

---

**Note:** Within the both the sorting and frequency count approaches; we use hash maps as our primary data structure of choice, for storing the end results.

---

<br>

## Summary Comparison

| Approach                          | Time Complexity      | Space Complexity  | Comments                                                         |
|-----------------------------------|----------------------|-------------------|------------------------------------------------------------------|
| **Brute Force**                   | O(n² * k)            | O(n * k)          | Not scalable; compares each pair of words                        |
| **Sorting-Based**                 | O(n * k log k)       | O(n * k)          | Simpler; uses sorted word as key                                  |
| **Hash Map with Frequency Count** | O(n * k)             | O(n * k)          | Most efficient for long words; avoids sorting overhead             |

Each method correctly groups anagrams, but the **optimized hash map using frequency counts** is generally the best choice when performance matters.
