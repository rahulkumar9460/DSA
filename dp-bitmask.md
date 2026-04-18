# Bitmask DP (Subset DP)
---

If you can say:

> “I need to consider all subsets, but efficiently”

👉 Use **Bitmask DP**

Bitmask DP is used when the **state depends on a subset of elements**.
- Instead of: dp[i]
- We use: dp[mask]

Where:
* `mask` = binary representation of a subset (Eg: mask = 101011, elements 0, 1, 3 and 5 are present in subset)
* `n elements → mask ∈ [0, (1<<n) - 1]`

---

## How to Identify This Pattern

### Strong signals
* `n ≤ 20` (small constraints)
* Keywords:

  * "visit all"
  * "each exactly once"
  * "minimum cost to cover all"
  * "assign tasks"
  * "subset / combination"

---

### Common problem types

| Type               | Typical State |
| ------------------ | ------------- |
| Visiting all nodes | `dp[mask][i]` |
| Assignment         | `dp[mask]`    |
| Pairing            | `dp[mask]`    |
| Partition          | `dp[mask]`    |

---

## When to Apply Bitmask DP
Use it when:
* You need to process **all subsets or permutations**
* Brute force is `O(n!)` or `O(2^n)`
* You can build answer **incrementally by adding elements**

---

##  How to Think About States

### 1. Subset only

> dp[mask]
> Used when order doesn’t matter

### 2. Subset + last element

> dp[mask][i]
> Used when order/path matters

Meaning:
> visited all in `mask`, currently at `i`

---

## Transition Pattern

Core idea:
```
for mask: // all possible subsets
    for i in mask: // currently at i
        for j not in mask: // move to j from i
            newMask = mask | (1<<j)
```

---

## ⚙️ Important Bit Operations

> [!IMPORTANT]
> Check if i is in mask ----> mask & (1 << i)
>
> Add i to mask ----> mask | (1 << i)
>
> Remove i from mask ----> mask ^ (1 << i)
>
> Check if mask is full ---- > mask == (1 << n) - 1
>
> Iterate all masks ----> for (int mask = 0; mask < (1<<n); mask++)
>
> Iterate elements in mask
> - for (int i = 0; i < n; i++)
>   - if (mask & (1<<i))
> 
> Iterate elements NOT in mask
> - for (int i = 0; i < n; i++)
>   - if (!(mask & (1<<i)))
>
> Remove lowest set bit ----> mask & (mask - 1)
> 
> Get lowest set bit ----> mask & (-mask)


## 🎯 Full Mask vs Final Mask
> [!NOTE]
> Total masks ----> totalMasks = 1 << n → number of states
> Final mask (all visited) ----> finalMask = (1 << n) - 1
>
> - Example (`n = 4`):
> - 1 << 4     = 10000
> - (1<<4) - 1 = 01111 → 1111

---

## How Iteration Works
Bottom-up order:
* Always go from **small mask → large mask**

```
for mask in [0 → (1<<n)-1]:
    for i in mask:
        for j not in mask:
            expand
```

### Why this works
Because:
> smaller subsets are already computed before larger ones

---

## Common Mistakes
> [!WARNING]
> * ❌ Using size `(1<<n) - 1` instead of `(1<<n)`
> * ❌ Forgetting to check `i ∈ mask`
> * ❌ Overflow when using `INT_MAX`
> * ❌ Wrong mask transitions
> * ❌ Using when `n > 22`

---

## Mental Model

Think:

> “I have already processed this subset (mask).
> How do I extend it by adding one more element?”

---

## 🔥 Classic Problems

* Travelling Salesman Problem (TSP)
* Assignment Problem
* Minimum XOR Sum
* Shortest Path Visiting All Nodes
* Partition to K Equal Sum Subsets
* Can I Win
* Beautiful Arrangement
* Minimum Cost to Connect Two Groups

---

## ⚡ Complexity

```
Time:  O(2^n * n^2)
Space: O(2^n * n)
```
---

