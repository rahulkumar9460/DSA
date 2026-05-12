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
LeetCode 739 Daily Temperatures
LeetCode 496 Next Greater Element I
LeetCode 503 Next Greater Element II
LeetCode 901 Online Stock Span
LeetCode 84 Largest Rectangle in Histogram
LeetCode 907 Sum of Subarray Minimums

---

# 2. Parentheses / Matching

## Problems
LeetCode 20 Valid Parentheses
LeetCode 32 Longest Valid Parentheses
LeetCode 1249 Minimum Remove to Make Valid Parentheses

---

# 3. Expression Evaluation

## Problems
LeetCode 150 Evaluate Reverse Polish Notation
LeetCode 224 Basic Calculator
LeetCode 227 Basic Calculator II


---

# 4. Design Stack

## Problems
LeetCode 155 Min Stack
LeetCode 716 Max Stack
LeetCode 232 Implement Queue using Stacks


---


# 5. Greedy + Stack

## Problems
LeetCode Remove K Digits


---

# 6. Simulation + Stack

## Problems
LeetCode Asteroid Collision
LeetCode Exclusive Time of Functions
LeetCode Car Fleet