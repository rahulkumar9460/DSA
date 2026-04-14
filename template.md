# 🚀 Problem Name / Topic Name

> [!NOTE]
> **Category:** Graph / DP / Binary Search / LLD / HLD  
> **Difficulty:** Medium  
> **Pattern:** Sliding Window / BFS / Topological Sort  
> **Last Revised:** DD-MM-YYYY  

---

# 📌 Problem Statement

<details>
<summary>Click to Expand</summary>

Write the full problem statement here.

</details>

---

# 🧠 Intuition

> [!TIP]
> Explain the core intuition in plain English.

Example:
- Brute force would check all subarrays.
- We need optimization because constraints are high.
- Prefix sum helps reduce repeated computation.

---

# 🐢 Brute Force Approach

<details>
<summary>Expand Brute Force</summary>

### Idea
Describe brute force here.

### Complexity
```cpp
Time: O(N^2)
Space: O(1)
```

</details>

---

# ⚡ Optimized Approach

> [!IMPORTANT]
> Key Insight:
> Convert repeated range computation into prefix values.

### Steps

1. Build prefix array  
2. Traverse once  
3. Maintain hashmap  

---

# 🔥 Code

```cpp
class Solution {
public:
    int solve(vector<int>& nums) {

    }
};
```

---

# 📊 Complexity Analysis

| Metric | Complexity |
|--------|-----------|
| Time   | O(N)      |
| Space  | O(N)      |

---

# ⚠️ Edge Cases

> [!WARNING]
> Always check these:

- Empty input  
- Duplicate values  
- Overflow possibility  
- Negative numbers  
- 1-element array  

---

# ❌ Mistakes / Pitfalls

> [!CAUTION]
> Common mistakes made while solving:

- Forgot to initialize hashmap  
- Off by one error  
- Overflow in multiplication  

---

# 🔄 Pattern Recognition

> [!TIP]
> How to identify this pattern next time?

Look for:
- "Subarray" + "sum" → Prefix Sum / Sliding Window  
- "Shortest path" → BFS / Dijkstra  
- "Dependencies" → Topological Sort  

---

# 🌍 Real World Analogy

<details>
<summary>Expand Analogy</summary>

Explain concept with analogy for memory retention.

</details>

---

# 🔗 Related Problems

- Two Sum  
- Subarray Sum Equals K  
- Longest Consecutive Sequence  

---

# ⭐ Revision Summary

```txt
Pattern:
Prefix Sum + Hashmap

Key Learning:
Store previous prefix to avoid recomputation.

Interview Trick:
Always think cumulative when asked repeated range queries.
```