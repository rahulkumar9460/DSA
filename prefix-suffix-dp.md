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

2 <= nums.length <= 10^5
-30 <= nums[i] <= 30
The input is generated such that answer[i] is guaranteed to fit in a 32-bit integer.
```

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

## 2. Trapping Rain Water

```
Given n non-negative integers representing an elevation map where the width of each bar is 1, 
compute how much water it can trap after raining.

 
Example 1:

Input: height = [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
Explanation: The above elevation map (black section) is represented by array [0,1,0,2,1,0,1,3,2,1,2,1]. 
In this case, 6 units of rain water (blue section) are being trapped.

Example 2:

Input: height = [4,2,0,3,2,5]
Output: 9

n == height.length
1 <= n <= 2 * 10^4
0 <= height[i] <= 10^5
```

```cpp
// prefix suffix dp solution takes o(n) extra space to compute lMax[] and rMax[]
// water[i] = min(leftMax[i], rightMax[i]) - height[i]
// below is space efficient approch
int trap(vector<int>& height) {
    int n = height.size();

    int l=0, r = n-1;
    int lMax = height[0], rMax = height[n-1];
    int ans = 0;

    while(l <= r) {
        if(height[l] <= height[r]) { //left side becomes limiting factor, Even if right side has taller bars later.
            lMax = max(lMax, height[l]);
            ans += lMax-height[l];
            l++;
        } else { // right side becomes limiting factor, Even if left side has tallers bars later.
            rMax = max(rMax, height[r]); 
            ans += rMax-height[r];
            r--;
        }
    }

    return ans;
}
```

---

## 3. Maximum Subarray Sum with One Deletion

```
Given an array of integers, return the maximum sum for a non-empty subarray (contiguous elements) with at most one element deletion. In other words, you want to choose a subarray and optionally delete one element from it so that there is still at least one element left and the sum of the remaining elements is maximum possible.

Note that the subarray needs to be non-empty after deleting one element.

 

Example 1:

Input: arr = [1,-2,0,3]
Output: 4
Explanation: Because we can choose [1, -2, 0, 3] and drop -2, thus the subarray [1, 0, 3] becomes the maximum value.
Example 2:

Input: arr = [1,-2,-2,3]
Output: 3
Explanation: We just choose [3] and it's the maximum sum.
Example 3:

Input: arr = [-1,-1,-1,-1]
Output: -1
Explanation: The final subarray needs to be non-empty. You can't choose [-1] and delete -1 from it, then get an empty subarray to make the sum equals to 0.

1 <= arr.length <= 10^5
-10^4 <= arr[i] <= 10^4
```

```cpp
int maximumSum(vector<int>& arr) {
    // left[i], maxSum of subarray ending at i
    // right[i], maxSum of subarray starting at i

    // can we delete ith element
    // if we delete ith element then effective sum becomes left[i-1] + right[i+1]

    int n = arr.size();
    vector<int> left(n, 0), right(n, 0);
    int ans = INT_MIN;

    int sum = 0;
    for(int i=0; i<n; i++) {
        sum += arr[i];
        left[i] = sum;

        ans = max(ans, left[i]);
        if(sum < 0) sum = 0;
    }

    sum = 0;
    for(int i=n-1; i>=0; i--) {
        sum += arr[i];
        right[i] = sum;

        ans = max(ans, right[i]);
        if(sum < 0) sum = 0;
    }

    for(int i=1; i<n-1; i++) {
        ans = max(ans, left[i-1]+right[i+1]);
    }

    return ans;
}
```

---

## 4. Longest Subarray of 1's After Deleting One Element

```
Given a binary array nums, you should delete one element from it.

Return the size of the longest non-empty subarray containing only 1's in the resulting array. 
Return 0 if there is no such subarray.

 

Example 1:

Input: nums = [1,1,0,1]
Output: 3
Explanation: After deleting the number in position 2, [1,1,1] contains 3 numbers with value of 1's.
Example 2:

Input: nums = [0,1,1,1,0,1,1,0,1]
Output: 5
Explanation: After deleting the number in position 4, [0,1,1,1,1,1,0,1] longest subarray with value of 1's is [1,1,1,1,1].
Example 3:

Input: nums = [1,1,1]
Output: 2
Explanation: You must delete one element.

1 <= nums.length <= 10^5
nums[i] is either 0 or 1.
```

```cpp
// left[i] -- len of contiguous 1's ending at i
// right[i] -- len of contiguous 1's starting at i
// if we delete ith element, effective len = left[i-1] + right[i+1]
int longestSubarray(vector<int>& nums) {
    int n = nums.size();
    if(n == 1) return 0;

    vector<int> left(n, 0), right(n, 0);

    left[0] = (nums[0] == 1) ? 1 : 0;
    right[n-1] = (nums[n-1] == 1) ? 1 : 0;

    for(int i=1; i<n; i++) {
        if(nums[i] == 1) 
            left[i] = left[i-1] + 1;
    }

    for(int i=n-2; i>=0; i--) {
        if(nums[i] == 1)    
            right[i] = right[i+1] + 1;
    }

    int ans = 0;
    for(int i=0; i<n; i++) {
        if(i == 0) ans = max(ans, right[1]);
        else if(i == n-1) ans = max(ans, left[n-2]);
        else ans = max(ans, left[i-1] + right[i+1]);
    }

    return ans;
}
```

**It can be solved with sliding window as well with o(1) space**

```cpp
// keep track of number of zeros
// if number of zeros is greater than one, shrink window from left
int longestSubarray(vector<int>& nums) {
    int n = nums.size();
    if(n == 1) return 0;

    int i = 0, j = 0;
    int zeros = 0, ans = 0;

    while(j < n) {
        if(nums[j] == 0) zeros++;

        while(zeros > 1) {
            if(nums[i] == 0) zeros--;
            i++;
        }

        ans = max(ans, j-i);
        j++;
    }

    return ans;
}
```

---

## 5. Longest Arithmetic Sequence After Changing At Most One Element

```
You are given an integer array nums.

A subarray is arithmetic if the difference between consecutive elements in the subarray is constant.

You can replace at most one element in nums with any integer. Then, you select an arithmetic subarray from nums.
Return an integer denoting the maximum length of the arithmetic subarray you can select.

 

Example 1:
Input: nums = [9,7,5,10,1]
Output: 5

Explanation:
Replace nums[3] = 10 with 3. The array becomes [9, 7, 5, 3, 1].
Select the subarray [9, 7, 5, 3, 1], which is arithmetic because consecutive elements have a common difference of -2.

Example 2:
Input: nums = [1,2,6,7]
Output: 3

Explanation:
Replace nums[0] = 1 with -2. The array becomes [-2, 2, 6, 7].
Select the subarray [-2, 2, 6, 7], which is arithmetic because consecutive elements have a common difference of 4.

4 <= nums.length <= 10^5
1 <= nums[i] <= 10^5
```

```cpp
int longestArithmetic(vector<int>& nums) {
    /*
        left[i] -- longest arithmetic sequence ending at i
        right[i] -- longest arithmetic sequence starting at i

        we can change ith element:
            diff = (nums[i+1] - nums[i-1])/2
            if(diff %2 != 0) not possible

            else {
                if(nums[i-1]-nums[i-2] == diff) leftLen = left[i-1]
                if(nums[i+2]-nums[i+1] == diff) rightLen = right[i+1];

                ans = max(ans, leftLen + 1 + rightLen)
            }
    */

    int n = nums.size();
    vector<int> left(n, 2), right(n, 2);
    left[0] = 1;
    right[n-1] = 1;
    int ans = 2;

    for(int i=2; i<n; i++) {
        if(nums[i]-nums[i-1] == nums[i-1]-nums[i-2])
            left[i] = left[i-1] + 1;
        ans = max(ans, left[i]);
        if(i != n-1) ans = max(ans, 1+left[i]); // we can change i+1 th element
    }

    for(int i=n-3; i>=0; i--) {
        if(nums[i]-nums[i+1] == nums[i+1]-nums[i+2])
            right[i] = right[i+1] + 1;
        ans = max(ans, right[i]);
        if(i != 0) ans = max(ans, 1+right[i]); // we can change i-1 th element
    }

    for(int i = 1; i < n-1; i++) {

        int d = nums[i+1] - nums[i-1];
        if(d % 2) continue;
    
        int diff = d / 2;
        int leftLen = 1;
        int rightLen = 1;
    
        if(i-2 >= 0 && nums[i-1] - nums[i-2] == diff)
            leftLen = left[i-1];
    
        if(i+2 < n && nums[i+2] - nums[i+1] == diff)
            rightLen = right[i+1];
    
        ans = max(ans, leftLen + 1 + rightLen);
    }

    return ans;
}
```
