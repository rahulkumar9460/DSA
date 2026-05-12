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