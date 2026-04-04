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

## 4. Find Median from Data Stream
```
The median is the middle value in an ordered integer list. If the size 
of the list is even, there is no middle value, and the median is the 
mean of the two middle values.

For example, for arr = [2,3,4], the median is 3.
For example, for arr = [2,3], the median is (2 + 3) / 2 = 2.5.

Implement the MedianFinder class:

    MedianFinder() initializes the MedianFinder object.
    void addNum(int num) adds the integer num from the data stream to the data structure.
    double findMedian() returns the median of all elements so far. 
        Answers within 10^-5 of the actual answer will be accepted.

Constraints:
    -10^5 <= num <= 10^5
    There will be at least one element in the data structure before calling findMedian.
    At most 5 * 104 calls will be made to addNum and findMedian.
```

**Intuituion**
```
To find the median we need:
    1. Highest element from left half
    2. And smallest element from right half

we can maintain to heaps each giving us above

strategy:
    -- maxHeap, minHeap
    -- maxHeap stores the extra one element if total number of elements is odd
    -- Do this while inserting:
        -- if maxHeap.size() > minHeap.size() ==> push element to minHeap
        -- if minHeap.size() > maxHeap.size() ==> push element to maxHeap

example:
    arr = [2, 3, 4, 5, 6]
    maxHeap = {}, minHeap = {}

itr 1:
    maxHeap = {2}, minHeap = {} ==> median = 2
itr 2:
    maxHeap = {2, 3}, minHeap = {}
    maxHeap = {2}, minHeap = {3} ==> median = (2+3)/2 = 2.5
itr 3:
    maxheap = {2, 4}, minHeap = {3}
    maxHeap = {2}, minHeap = {3, 4}
    maxHeap = {2, 3}, minHeap = {4} ==> median = 3
itr 4:
    maxheap = {2, 3, 5}, minHeap = {4}
    maxHeap = {2, 3}, minHeap = {4, 5} ==> median = (3+4)/2 = 3.5
itr 5:
    maxheap = {2, 3, 6}, minHeap = {4, 5}
    maxHeap = {2, 3}, minHeap = {4, 5, 6}
    maxHeap = {2, 3, 4}, minHeap = {5, 6} ==> median = 4
```

```cpp
class MedianFinder {
public:
    priority_queue<int> maxHeap;
    priority_queue<int, vector<int>, greater<int>> minHeap;

    MedianFinder() {
        
    }
    
    void addNum(int num) {
        maxHeap.push(num);

        if(maxHeap.size() > minHeap.size()) {
            minHeap.push(maxHeap.top());
            maxHeap.pop();
        }

        if(minHeap.size() > maxHeap.size()) {
            maxHeap.push(minHeap.top());
            minHeap.pop();
        }
    }
    
    double findMedian() {
        if(maxHeap.size() == minHeap.size())
            return (maxHeap.top() + minHeap.top()) / double(2); 
        
        return maxHeap.top();
    }
};
```