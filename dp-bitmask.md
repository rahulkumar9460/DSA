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
> - Check if i is in mask ----> mask & (1 << i)
>
> - Add i to mask ----> mask | (1 << i)
>
> - Remove i from mask ----> mask ^ (1 << i)
>
> - Check if mask is full ---- > mask == (1 << n) - 1
>
> - Iterate all masks ----> for (int mask = 0; mask < (1<<n); mask++)
>
> - Iterate elements in mask
>   - for (int i = 0; i < n; i++)
>       - if (mask & (1<<i))
> 
> - Iterate elements NOT in mask
>   - for (int i = 0; i < n; i++)
>       - if (!(mask & (1<<i)))
>
> - Remove lowest set bit ----> mask & (mask - 1)
> 
> - Get lowest set bit ----> mask & (-mask)


## 🎯 Full Mask vs Final Mask
> [!NOTE]
> - Total masks ----> totalMasks = 1 << n → number of states
> - Final mask (all visited) ----> finalMask = (1 << n) - 1
>
> - Example (`n = 4`):
>   - 1 << 4     = 10000
>   - (1<<4) - 1 = 01111 → 1111

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

## 1. Travelling Salesman Problem
[GFG link](https://www.geeksforgeeks.org/problems/travelling-salesman-problem2732/1)

Given a 2d matrix cost[][] of size n where cost[i][j] denotes the cost of moving from city i to city j. 
Your task is to complete a tour from city 0 (0-based index) to all other cities such 
that you visit each city exactly once and then at the end come back to city 0 at minimum cost.

```
Input: cost[][] = [[0, 1000, 5000],
                [5000, 0, 1000],
                [1000, 5000, 0]]
Output: 3000
Explanation: We can visit 0->1->2->0 and cost = 1000 + 1000 + 1000 = 3000
```

Constraints:
- 1 ≤ cost.size() ≤ 15
- 0 ≤ cost[i][j] ≤ 104

### Intuition
> [!IMPORTANT]
> Brute-force: need to generate all permutaions --> time - 0(n!)
>
> In these permutaions many paths will be common and results for these can be stored
> 
> - States depends in which cities have been visited, and where is salesman now
>   - Example: two paths (0->1->3), (3->1->0) 
>   - mask for both path is: 1101, but both are different
>       
>           this is why we need the curr position also to determine the correct state
>
>           dp[mask][i] --> cost of visiting cities in mask while curr location is at 'i'
>           mask ---> visited cities
>           i ---> current city

> [!NOTE]
> mask is a subset not a permutation
> - mask1 = 100 --> city 2 is visited
> - mask2 = 0010010 --> cities 1 and 4 are visited

```cpp
int tsp(vector<vector<int>>& cost) {
    int n = cost.size();
    if (n == 1) return 0;
    
    int totalMasks = 1 << n;
    const int INF = 1e9;
    
    vector<vector<int>> dp(totalMasks, vector<int>(n, INF));
    dp[1 << 0][0] = 0; // visited city 0, currently at city 0 --> cost = 0
    
    for (int mask = 0; mask < totalMasks; mask++) { // all subsets -- 0(2^n)
        for (int i = 0; i < n; i++) { // curr positions
            
            // curr position i not in mask
            if (!(mask & (1 << i))) continue;
            
            // visit j from i
            for (int j = 0; j < n; j++) {
                // j is already visited
                if (mask & (1 << j)) continue;
                
                int newMask = mask | (1 << j);
                dp[newMask][j] = min(dp[newMask][j], dp[mask][i] + cost[i][j]);
            }
        }
    }
    
    int finalMask = (1 << n) - 1;
    int ans = INT_MAX;
    
    for (int i = 1; i < n; i++) {
        ans = min(ans, dp[finalMask][i] + cost[i][0]);
    }
    
    return ans;
}
```

```
Time - O(2^n * n^2)
Space - O(2^n * n)
```
---

## 2. Minimum XOR Sum of Two Arrays
[text](https://leetcode.com/problems/minimum-xor-sum-of-two-arrays/description/)

You are given two integer arrays nums1 and nums2 of length n.

The XOR sum of the two integer arrays is 
(nums1[0] XOR nums2[0]) + (nums1[1] XOR nums2[1]) + ... + (nums1[n - 1] XOR nums2[n - 1]) (0-indexed).

For example, the XOR sum of [1,2,3] and [3,2,1] is equal to 
(1 XOR 3) + (2 XOR 2) + (3 XOR 1) = 2 + 0 + 2 = 4.

> Rearrange the elements of nums2 such that the resulting XOR sum is minimized.
> Return the XOR sum after the rearrangement.


Input: nums1 = [1,0,3], nums2 = [5,3,4]
Output: 8

> Explanation: Rearrange nums2 so that it becomes [5,4,3]. 
> The XOR sum is (1 XOR 5) + (0 XOR 4) + (3 XOR 3) = 4 + 4 + 0 = 8.

```
Constraints:

n == nums1.length
n == nums2.length
1 <= n <= 14
```

### Intuition
> [!IMPORTANT]
> mask --> represents elements form nums2 which have been used already
>
> setbits in mask shows elements are used from nums2
>
> number of setbits in mask shows the index till elements are assigned form nums1
>
> dp[mask] = min XORSum achieved by elements present in mask from nums2

```cpp
int setbitsCount(int mask) {
    int count = 0;
    while (mask > 0) {
        count += (mask & 1);
        mask >>= 1;
    }
    return count;
}

int minimumXORSum(vector<int>& nums1, vector<int>& nums2) {
    // mask --> represents elements form nums2 which have been used already
    // setbits in mask shows elements are used from nums2
    // unset bits tell the indicies not used from nums2
    // number of setbits in mask shows the index till elements are assigned form nums1

    int n = nums1.size();
    int totalMasks = 1 << n;
    const int INF = 1e9;

    vector<int> dp(totalMasks, INT_MAX);
    dp[0] = 0; // 0 elements used from nums2

    for(int mask=0; mask<totalMasks; mask++) {
        if(dp[mask] == INT_MAX) continue;
        int setBits = setbitsCount(mask);
        
        int i = setBits; // next index from nums1
        if(i >= n) continue;

        for(int j=0; j<n; j++) {
            if(!(mask & (1<<j))) { // index already used from nums2
                int newMask = mask | (1<<j);

                dp[newMask] = min(dp[newMask], dp[mask] + (nums1[i]^nums2[j]));
            }
        }
    }

    int finalMask = (1<<n)-1;
    return dp[finalMask];
}
```
