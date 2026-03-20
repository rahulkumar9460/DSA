# Prefix–Suffix DP Pattern

## 1. Overview

**Prefix–Suffix DP** is a common algorithmic pattern where we precompute information from:

* **Left side → Prefix**
* **Right side → Suffix**

This helps solve problems where the answer for index `i` depends on **elements before and after `i`**.

Instead of recomputing the left and right parts repeatedly (which leads to **O(n²)**), we precompute both sides once and combine them in **O(n)**.

---

# 2. Core Idea

For an array `a` of size `n`:

```
prefix[i]  = information from a[0] to a[i]
suffix[i]  = information from a[i] to a[n-1]
```

Then for each index:

```
answer[i] = combine(prefix[i-1], suffix[i+1])
```

This allows solving many problems efficiently in **linear time**.

---

# 3. When to Recognize Prefix–Suffix Pattern

Look for these signals in a problem.

---

## Case 1 — Removing One Element

Problem statement may say:

```
What happens if we remove index i?
```

Then:

```
left  part = prefix[i-1]
right part = suffix[i+1]
```

Examples:

* Maximum subarray after deleting one element
* Longest increasing subarray after removing one element
* Arithmetic sequence after changing/removing an element

---

## Case 2 — Need Information From Both Sides

Typical wording:

* elements before and after
* excluding the current element
* split the array
* best partition

Example idea:

```
left[i]  = best answer up to i
right[i] = best answer from i
```

Then combine.

---

## Case 3 — Range Query Excluding Current Index

Example problem:

```
Product of array except self
```

Solution:

```
result[i] = prefix_product[i-1] * suffix_product[i+1]
```

---

## Case 4 — Joining Two Segments

Sometimes we want to **merge left and right segments**.

Example:

```
can left segment ending at i-1 join
with right segment starting at i+1 ?
```

---

# 4. Standard Prefix–Suffix Template

## Step 1 — Build Prefix Array

```
prefix[0] = a[0]

for i = 1 → n-1
    prefix[i] = combine(prefix[i-1], a[i])
```

---

## Step 2 — Build Suffix Array

```
suffix[n-1] = a[n-1]

for i = n-2 → 0
    suffix[i] = combine(suffix[i+1], a[i])
```

---

## Step 3 — Combine Results

```
for i = 0 → n-1

    if i == 0
        answer = suffix[1]

    else if i == n-1
        answer = prefix[n-2]

    else
        answer = combine(prefix[i-1], suffix[i+1])
```

Time complexity:

```
O(n)
```

Space complexity:

```
O(n)
```

---

# 5. Space Optimization Trick

You do not always need two arrays.

Sometimes you can compute:

```
prefix pass → store result
suffix pass → combine while iterating
```

Example: optimized solution for product except self.

---

# 6. Mental Checklist (Interview Trick)

When reading a problem ask:

```
1. Does answer for index i depend on elements before it?
2. Does it also depend on elements after it?
3. Are we removing or modifying an element?
4. Can I precompute left and right information?
```

If **YES**, consider **Prefix–Suffix DP**.

---

# 7. Common Interview Problems Using This Pattern

Very frequently asked in interviews.

```
1. Product of Array Except Self
2. Trapping Rain Water
3. Maximum Subarray Sum with One Deletion
4. Longest Increasing Subarray After Removing One Element
5. Arithmetic Subarray Modification
6. Maximum Prefix + Suffix Split
7. Best Sightseeing Pair
```

---

# 8. Key Takeaway

Golden rule:

```
If the solution requires information
from BOTH SIDES of an index,
think PREFIX + SUFFIX.
```

Prefix–Suffix DP converts many **O(n²)** ideas into **O(n)** solutions.

---

## 1. Product of array except self

```
Given an integer array nums, return an array answer such that answer[i] is equal to the product 
of all the elements of nums except nums[i].

The product of any prefix or suffix of nums is guaranteed to fit in a 32-bit integer.

You must write an algorithm that runs in O(n) time and without using the division operation.

Example 1:

Input: nums = [1,2,3,4]
Output: [24,12,8,6]
Example 2:

Input: nums = [-1,1,0,-3,3]
Output: [0,0,9,0,0]
```

2 <= nums.length <= 105
-30 <= nums[i] <= 30
The input is generated such that answer[i] is guaranteed to fit in a 32-bit integer.

**Can you solve the problem in O(1) extra space complexity?** (The output array does not count as 
extra space for space complexity analysis.)

```cpp
vector<int> productExceptSelf(vector<int>& nums) {
    int n = nums.size();
    vector<int> ans(n, 1);

    int curr = 1;
    for(int i=0; i<n; i++) {
        ans[i] *= curr;
        curr *= nums[i];
    }

    curr = 1;
    for(int i=n-1; i>=0; i--) {
        ans[i] *= curr;
        curr *= nums[i];
    }

    return ans;
}
```

---

