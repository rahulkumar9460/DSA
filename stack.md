# STACK

---

# Patterns

# 1. Monotonic increasing/decreasing stack

> [!NOTE]
> How to identify
> 
> 1. Nearest greater or smaller to left or right
> 2. O(n²) brute force by scanning neighbors?
> 3. Once current arrives, old elements become useless? ==> pop from stack
> 4. Need range contribution? => Like subarray max/min

### Template for monotonic stack
```cpp
stack<int> st; // indices

for(int i=0;i<n;i++){
    while(!st.empty() && arr[st.top()] >= arr[i]){
        st.pop();
    }

    // previous smaller index = st.top() if exists

    st.push(i);
}
```

## Problems
1. LeetCode 739 Daily Temperatures
2. LeetCode 496 Next Greater Element I
3. LeetCode 503 Next Greater Element II
4. LeetCode 901 Online Stock Span
5. LeetCode 84 Largest Rectangle in Histogram
6. LeetCode 907 Sum of Subarray Minimums

## 1. Daily Temperatures
[Leetcode link](https://leetcode.com/problems/daily-temperatures/description/)

Given an array of integers temperatures represents the daily temperatures, 
return an array answer such that answer[i] is the number of days you have to wait after the 
ith day to get a warmer temperature. If there is no future day for which this is possible, 
keep answer[i] == 0 instead.

Example: 
Input: temperatures = [73,74,75,71,69,72,76,73]
Output: [1,1,4,2,1,1,0,0]

### Intuition
> [!IMPORTANT]
> - We need nearest greater to right element 


```cpp
vector<int> dailyTemperatures(vector<int>& temperatures) {
    // next greater to right
    int n = temperatures.size();
    stack<int> st;
    vector<int> ans(n, 0);

    for(int i=n-1; i>=0; i--) {
        while(!st.empty() && temperatures[st.top()] <= temperatures[i]) st.pop();

        if(!st.empty()) ans[i] = st.top()-i;
        st.push(i);
    }

    return ans;
}
```

---

## 2. Next Greater Element I
[Leetcode link](https://leetcode.com/problems/next-greater-element-i/description/)

Given two arrays nums1 and nums2
for each index i in nums1 find index j in nums2 such that nums1[i] == nums2[j] and then 
- find the greater to right of nums2[j]

Input: 
- nums1 = [4,1,2], nums2 = [1,3,4,2]
Output: 
- [-1,3,-1]

Explanation: The next greater element for each value of nums1 is as follows:
- 4 is underlined in nums2 = [1,3,4,2]. There is no next greater element, so the answer is -1.
- 1 is underlined in nums2 = [1,3,4,2]. The next greater element is 3.
- 2 is underlined in nums2 = [1,3,4,2]. There is no next greater element, so the answer is -1.

> [!IMPORTANT]
> Preprocess nums2 and find next greater to right for each index and store them in map
>
> now for each element in nums1 just return map[nums1[i]]

```cpp
vector<int> nextGreaterElement(vector<int>& nums1, vector<int>& nums2) {
    unordered_map<int,int> mp;
    int n = nums1.size(), m = nums2.size();

    stack<int> st;
    for(int i=m-1; i>=0; i--) {
        while(!st.empty() && st.top() <= nums2[i]) st.pop();

        if(!st.empty()) mp[nums2[i]] = st.top();
        else mp[nums2[i]] = -1;

        st.push(nums2[i]);
    }

    vector<int> res(n);
    for(int i=0; i<n; i++) res[i] = mp[nums1[i]];

    return res;
}
```

---

## 3. Next Greater Element II
[Leetcode link](https://leetcode.com/problems/next-greater-element-ii/description/)

> Given a circular integer array nums (i.e., the next element of nums[nums.length - 1] is nums[0]), 
> return the next greater number for every element in nums.

The next greater number of a number x is the first greater number to its traversing-order next in the array

Example:

Input: 
- nums = [1,2,3,4,3]
- Output: [2,3,4,-1,4]

### Intuition
> [!IMPORTANT]
> [1,2,3,4,3] ==> circular form will be 1,2,3,4,3 - 1,2,3,4
>
> Now consider the combined array for making the monotonic decreasing stack
>
>                   First traverse from n-2 to 0 and build stack
>                   Now again traverse from n-1 to 0 and build stack and update answer


```cpp
vector<int> nextGreaterElements(vector<int>& nums) {
    int n = nums.size();
    stack<int> st;

    for(int i=n-2; i>=0; i--) {
        while(!st.empty() && st.top() <= nums[i]) st.pop();
        st.push(nums[i]);
    }

    vector<int> ans(n, -1);
    for(int i=n-1; i>=0; i--) {
        while(!st.empty() && st.top() <= nums[i]) st.pop();

        if(!st.empty()) ans[i] = st.top();
        st.push(nums[i]);
    }

    return ans;
}
```

---

## 4. Online Stock Span
[Leetcode link](https://leetcode.com/problems/online-stock-span/description/)

Span of stock at day i is number of days it has been lesser or equal to current price 

Example:

- ["StockSpanner", "next", "next", "next", "next", "next", "next", "next"]
- [[], [100], [80], [60], [70], [60], [75], [85]]

Output:
- [null, 1, 1, 1, 2, 1, 4, 6]

### Intuition
> [!IMPORTANT]
> To count days where is has been lesser of equal to current price
>
>               We need to find the nearest greater to left = day
>
>               answer = currDay - day
>
>
> - monotonic decreasing stack

```cpp
class StockSpanner {
public:
    stack<pair<int, int>> st;
    int day;
    StockSpanner() {
        day = 0;
    }
    
    int next(int price) {
        // nearest greater to left
        while(!st.empty() && st.top().first <= price) st.pop();

        int ans = 1;
        if(st.empty()) ans = day+1;
        else ans = day-st.top().second;

        st.push({price, day});
        day++;

        return ans;
    }
};
```


---

# 2. Parentheses / Matching

## Problems
1. LeetCode 20 Valid Parentheses
2. LeetCode 32 Longest Valid Parentheses
3. LeetCode 1249 Minimum Remove to Make Valid Parentheses

---

# 3. Expression Evaluation

## Problems
1. LeetCode 150 Evaluate Reverse Polish Notation
2. LeetCode 224 Basic Calculator
3. LeetCode 227 Basic Calculator II


---

# 4. Design Stack

## Problems
1. LeetCode 155 Min Stack
2. LeetCode 716 Max Stack
3. LeetCode 232 Implement Queue using Stacks


---


# 5. Greedy + Stack

## Problems
1. LeetCode Remove K Digits


---

# 6. Simulation + Stack

## Problems
1. LeetCode Asteroid Collision
2. LeetCode Exclusive Time of Functions
3. LeetCode Car Fleet