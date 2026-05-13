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

## 5.  Largest Rectangle in Histogram
[Leetcode link](https://leetcode.com/problems/largest-rectangle-in-histogram/description/)

> Given an array of integers heights representing the histogram's bar height where 
> the width of each bar is 1, return the area of the largest rectangle in the histogram

Input: 
- heights = [2,1,5,6,2,3]
Output: 
- 10

Explanation: 
- The above is a histogram where width of each bar is 1.
- The largest rectangle is shown in the red area, which has an area = 10 units.

```

         | |   
      | || |   
      | || |   
      | || |   | |
| |   | || || || |
| || || || || || |
 2. 1  5. 6. 2. 3
```

### Intuition
> [!IMPORTANT]
> For each histogram we need to check on left and right how far we can maintain its height
>
> We need nearest-smaller-to-left and nearest-smaeer-to-right
>
>                   then for ith histogram, area = (nsr[i] - nsl[i] - 1) * heights[i]
>
>                   For above Example:
>                   nsr = [1, 6, 4, 4, 6, 6]
>                   nsl = [-1, -1, 1, 2, 1, 4]
>                   nsr-nsl-1 = [1, 6, 2, 1, 4, 1]
>                   area = [2, 6, 10, 6, 8, 3]
>                   Max Areas = 10

```cpp
int largestRectangleArea(vector<int>& heights) {
    int n = heights.size();
    int ans = 0;

    stack<int> st;
    // Nearest smaller to right
    vector<int> nsr(n, n);
    for(int i=n-1; i>=0; i--) {
        while(!st.empty() && heights[st.top()] >= heights[i]) st.pop();
        
        if(!st.empty()) nsr[i] = st.top();
        st.push(i);
    }

    while(!st.empty()) st.pop();
    // Nearest smaller to left
    vector<int> nsl(n, -1);
    for(int i=0; i<n; i++) {
        while(!st.empty() && heights[st.top()] >= heights[i]) st.pop();
        
        if(!st.empty()) nsl[i] = st.top();
        st.push(i);
    }

    for(int i=0; i<n; i++) {
        ans = max(ans, (nsr[i]-nsl[i]-1)*heights[i]);
    }

    return ans;
}
```

---

## 6. Sum of Subarray Minimums
[Leetcode link](https://leetcode.com/problems/sum-of-subarray-minimums/description/)

> Given an array of integers arr, find the sum of min(b), where b ranges over every (contiguous) subarray of arr. 
> Since the answer may be large, return the answer modulo 109 + 7.

Input: 
- arr = [3,1,2,4]
Output: 
- 17

Explanation: 
Subarrays are [3], [1], [2], [4], [3,1], [1,2], [2,4], [3,1,2], [1,2,4], [3,1,2,4]. 
Minimums are 3, 1, 2, 4, 1, 1, 2, 1, 1, 1.
Sum is 17.

### Intuition
> [!IMPORTANT]
> For each element arr[i] just find how many subarrays are there which have arr[i] as minimum
> 
> Let call there are d subarrays that have arr[i] as minimum, so sum = arr[i]*d
>
> Keep doing it for all elements
>
> - How to find d?
>
> - For element arr[i], we can make boundary to its left and right till we find elements greater than arr[i]
> - For example [1, 4, 2, 6, 0], for element 2 left boundary is [4, 2] and right boundary is [2, 6]
> - so total subarray that have 2 as minimum are:
>
>                   [4, 2], [2], [2, 6], [4, 2, 6]
>
>                   total = 4 = length of left boundary * length of right boundary
>
>                   To calculate left and. right boundary we need to find NSL and NSR

```cpp
int sumSubarrayMins(vector<int>& arr) {
    int n = arr.size();
    int mod = 1e9+7;

    stack<int> st;
    // nearest smaller to right
    vector<int> nsr(n, n);
    for(int i=n-1; i>=0; i--) {
        while(!st.empty() && arr[st.top()] > arr[i]) st.pop();

        if(!st.empty()) nsr[i] = st.top();
        st.push(i);
    }

    while(!st.empty()) st.pop();
    // nearest smaller to left
    vector<int> nsl(n, -1);
    for(int i=0; i<n; i++) {
        while(!st.empty() && arr[st.top()] >= arr[i]) st.pop();

        if(!st.empty()) nsl[i] = st.top();
        st.push(i);
    }

    long ans = 0;
    for(int i=0; i<n; i++) {
        long res = 1LL * (nsr[i]-i) * (i-nsl[i]) * arr[i];

        ans += res;
        ans %= mod;
    }

    return (int)ans;
}
```

---

# 2. Parentheses / Matching

## Problems
1. LeetCode 20 Valid Parentheses
2. LeetCode 32 Longest Valid Parentheses
3. LeetCode 1249 Minimum Remove to Make Valid Parentheses

---

## 1. Valid Parentheses
[Leetcode link](https://leetcode.com/problems/valid-parentheses/description/)

Given a string s containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.

An input string is valid if:

1. Open brackets must be closed by the same type of brackets.
2. Open brackets must be closed in the correct order.
3. Every close bracket has a corresponding open bracket of the same type.

### Intuition
> [!IMPORTANT]
> Use a stack to keep stack of open brackets
>
> when a close bracket is encounterd check the corrosponding open bracket and pop
>
> else return false
>
> finally check if stack is empty or not

```cpp
bool isOpen(char &c) {
    return c == '(' || c == '[' || c == '{';
}

char openBracket(char &c) {
    if(c == ')') return '(';
    if(c == '}') return '{';
    return '[';
}

bool isValid(string s) {
    stack<char> st;

    for(char c : s) {
        if(isOpen(c)) st.push(c);
        else {
            if(st.empty()) return false;
            if(st.top() != openBracket(c)) return false;

            st.pop();
        }
    }

    return st.empty();
}
```

---

## 3.  Longest Valid Parentheses
[Leetcode link](https://leetcode.com/problems/longest-valid-parentheses/description/)

> Given a string containing just the characters '(' and ')', 
> return the length of the longest valid (well-formed) parentheses substring.

Input: s = ")()())"

Output: 4

Explanation: The longest valid parentheses substring is "()()".

### Intuition
> [!IMPORTANT]
> We need to find a continuous block of valid parentheses
>
> Lets use stack if we encounter '(' push it else if stack is not empty then pop 
>
> We need to keep track of lastInvalidIdx so that length of valid substring = i-lastValidIdx
>
>               Example: ((()())(, at start lastInvalidIdx = -1
>               ( ==> st.push(0)
>               ( ==> st.push(1)
>               ( ==> st.push(2)
>               ) ==> st.pop() ==> (st = {0, 1}) and length = i-st.top() = 3-1 = 2
>               ( ==> st.push(4)
>               ) ==> st.pop() ==> (st = {0, 1}) and length = i-st.top() = 5-1 = 4
>               ) ==> st.pop() ==> (st = {0}) and length = i-st.top() = 6-0 = 6
>
>
> Example 2;
>
>               ))(), at start lastInvalidIdx = -1
>               ) ==> st.pop not possible so lastInvalidIdx = 0
>               ) ==> st.pop not possible so lastInvalidIdX = 1
>               ( ==> st.push(2)
>               ) ==> st.pop() ==> (st = {}) length = i-lastInvalidIdx = 3-1 = 2

```cpp
int longestValidParentheses(string s) {
    stack<int> open;
    int ans = 0;
    int lastInvalidIdx = -1;

    for(int i=0; i<s.size(); i++) {
        if(s[i] =='(') {
            open.push(i);
            continue;
        }
        
        // ')' case
        if(open.empty()) {
            lastInvalidIdx = i;
        } else {
            open.pop(); // remove open bracket index

            if(open.empty()) ans = max(ans, i-lastInvalidIdx);
            else ans = max(ans, i-open.top());
        }
    }

    return ans;
}
```
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