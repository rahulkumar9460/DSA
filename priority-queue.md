# Priority queue

**How to Identify Priority Queue Pattern**

## 1. You need top K / smallest K / largest K
```
Classic trigger:
    “find k smallest/largest”
    “maintain k best elements so far”

👉 Use:
    1. min heap(minimum at top) → for k largest
    2. max heap(maximum at top) → for k smallest
```