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

```
Pattern 1: Keep K Best
    Fixed size heap
    Remove worst when size > k

Pattern 2: Expand from Best (Best-first search)
    Always process smallest/largest next
    Example: Dijkstra, K sorted lists merge

Pattern 3: Two Heaps (Median / balancing)
    Maintain left + right halves

Pattern 4: Lazy deletion
    When removal is expensive
    Mark invalid and skip later
```

# Problems:

## 1. Kth largest element
```
Given an integer array nums and an integer k, return the kth largest element in the array.
Note that it is the kth largest element in the sorted order, not the kth distinct element.
Can you solve it without sorting?


Example 1:
    Input: nums = [3,2,1,5,6,4], k = 2
    Output: 5

Example 2:
    Input: nums = [3,2,3,1,2,4,5,5,6], k = 4
    Output: 4
```

```
If we sort the entire array the time complexity will be O(nlogn)
But if we keep track of only top K element the time will be O(nlogk)

Use min heap --> min element at top
if heap's size > k then we can safely erase the top element (current smallest)
```

```cpp
int findKthLargest(vector<int>& nums, int k) {
    priority_queue<int, vector<int>, greater<int>> pq;

    for(int n : nums) {
        pq.push(n);
        if(pq.size() > k) pq.pop();
    }

    return pq.top();
    // Use max heap if need to find the kth smallestTop K Frequent Elements
}
```

## 2. Top K Frequent Elements
```
Given an integer array nums and an integer k, return the k most frequent elements. You may return the answer in any order.

 
Example 1:
    Input: nums = [1,1,1,2,2,3], k = 2
    Output: [1,2]

Example 2:
    Input: nums = [1], k = 1
    Output: [1]

Example 3:
    Input: nums = [1,2,1,2,1,2,3,1,3,2], k = 2
    Output: [1,2]
```

```cpp
vector<int> topKFrequent(vector<int>& nums, int k) {
    unordered_map<int, int> mp;
    for(int n : nums) mp[n]++;

    priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> pq; // min heap

    for(auto [num, freq] : mp) {
        pq.push({freq, num});
        if(pq.size() > k) pq.pop();
    }

    vector<int> ans;
    while(!pq.empty()) {
        ans.push_back(pq.top().second);
        pq.pop();
    }
    
    return ans;
}
```

## 3. Find K Closest Elements
```
Given a sorted integer array arr, two integers k and x, return the k closest integers 
to x in the array. The result should also be sorted in ascending order.

An integer a is closer to x than an integer b if:
|a - x| < |b - x|, or
|a - x| == |b - x| and a < b
 

Example 1:
    Input: arr = [1,2,3,4,5], k = 4, x = 3
    Output: [1,2,3,4]

Example 2:
    Input: arr = [1,1,2,3,4,5], k = 4, x = -1
    Output: [1,1,2,3]
```

**Intuition**
```
Subtract x from arr[i]:
arr = [1,2,3,4,5], k = 4, x = 3
arr - x = [2, 1, 0, 1, 2]

now find kth smallest in (arr-x)
```

```cpp
vector<int> findClosestElements(vector<int>& arr, int k, int x) {

    priority_queue<pair<int,int>> pq;
    for(int n : arr) {
        pq.push({abs(n-x), n});
        if(pq.size() > k) pq.pop();
    }

    vector<int> ans;
    while(!pq.empty()) {
        ans.push_back(pq.top().second);
        pq.pop();
    }

    sort(ans.begin(), ans.end());
    return ans;
}
```