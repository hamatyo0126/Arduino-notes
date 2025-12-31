# CAN-id-filtering
Notes on CAN ID filtering and bitmap techniques

# Comparison: List-Based Filtering vs Bitmap-Based Filtering

This document compares two common approaches for checking whether a CAN ID (or any integer key) is allowed:
1. **List-based filtering**
2. **Bitmap-based filtering (array-index lookup)**

---

## 1. List-Based Filtering

### Concept
Store allowed IDs in an array and check them by iterating through the list.

### Example
```cpp
const uint16_t allowedIds[] = {0x21, 0x30, 0x55};
bool isAllowedId(uint16_t id) {
    for (size_t i = 0; i < sizeof(allowedIds)/sizeof(allowedIds[0]); i++) {
        if (allowedIds[i] == id) return true;
    }
    return false;
}

```

### Characteristics
- **Time complexity:** O(n)  
  The system must *search* through the list.
- **Memory usage:** Small (only the number of allowed IDs)
- **Scalability:** Slows down as the list grows
- **Pros:**  
  - Easy to understand  
  - Minimal memory footprint  
- **Cons:**  
  - Requires iteration  
  - Worst-case performance increases with number of IDs  
  - Not ideal for real-time systems

---

## 2. Bitmap-Based Filtering (Array Lookup)

### Concept
Use an array where each index corresponds directly to an ID.  
The value at that index (true/false) indicates whether the ID is allowed.

### Example
```cpp
bool allowIdBitmap[2048] = { false };

void setup() {
    allowIdBitmap[0x21] = true;
    allowIdBitmap[0x30] = true;
    allowIdBitmap[0x55] = true;
}

inline bool isAllowedId(uint16_t id) {
    return allowIdBitmap[id];
}
```

### Characteristics
- **Time complexity:** O(1)  
  No searching. The CPU jumps directly to the correct index.
- **Memory usage:** Fixed (size = max ID + 1)  
  Example: 2048 bytes if using `bool[2048]`
- **Scalability:** Constant-time regardless of number of allowed IDs
- **Pros:**  
  - Extremely fast (direct index access)  
  - Perfect for real-time systems  
  - No iteration, no searching  
- **Cons:**  
  - Requires memory proportional to the maximum ID  
  - Not suitable if ID range is huge (e.g., millions)

---

## Summary Table

| Feature | List-Based Filtering | Bitmap-Based Filtering |
|--------|-----------------------|-------------------------|
| Time Complexity | O(n) | O(1) |
| Memory Usage | Small | Larger (depends on max ID) |
| Speed | Slower | Extremely fast |
| Scaling | Slows with more IDs | Constant-time |
| Best Use Case | Small sets of IDs | Real-time filtering, fixed ID ranges |

---

## Conclusion

The bitmap-based approach is ideal when:
- The ID range is known and not too large  
- Real-time performance is required  
- Memory usage is acceptable  

The list-based approach is better when:
- Memory is extremely limited  
- The number of allowed IDs is small  
- Performance is not critical
```
