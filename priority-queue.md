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

## 2. You need real-time best choice (greedy + dynamic updates)
```
If problem says:
    “at every step pick best candidate”
    BUT candidates keep changing

👉 Heap = dynamic greedy
```

## 3. You need processing in sorted order but cannot sort upfront
```
Example:
    Streaming data
    Sliding window
    Graph exploration

👉 Heap gives:
    O(log n) insert + extract
    avoids full sorting
```

## 4. “Earliest / smallest / highest priority first”
```
Huge signal:
    shortest time
    earliest finishing
    lowest cost

👉 Almost always heap
```

## 5. When brute force is:
```
repeatedly finding min/max → O(n²)

👉 Replace with heap → O(n log n)
```