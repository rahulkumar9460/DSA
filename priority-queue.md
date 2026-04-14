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

---

> [!IMPORTANT]
> Pattern 1: Keep K Best
- Fixed size heap
- Remove worst when size > k

> Pattern 2: Expand from Best (Best-first search)
- Always process smallest/largest next
- Example: Dijkstra, K sorted lists merge

> Pattern 3: Two Heaps (Median / balancing)
- Maintain left + right halves

> Pattern 4: Lazy deletion
- When removal is expensive
- Mark invalid and skip later

---

> [!TIP]
> **Do I need the best element repeatedly while data keeps changing? --> if YES --> use priority_queue**

---

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

**Intuition**
```
To find the median we need:
    1. Highest element from left half
    2. And smallest element from right half

we can maintain to heaps each giving us above

strategy:
    -- maxHeap, minHeap
    -- maxHeap stores the extra one element if total number of elements is odd
    -- Do this while inserting:
        -- push element to maxheap
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

## 5. Task Scheduler
```diff
You are given an array of CPU tasks, each labeled with a letter from A to Z, and a number n. 
Each CPU interval can be idle or allow the completion of one task. Tasks can be 
completed in any order, but there's a constraint: there has to be a gap of at 
least n intervals between two tasks with the same label.

+ Return the minimum number of CPU intervals required to complete all tasks.

 

+ Example 1:
    Input: tasks = ["A","A","A","B","B","B"], n = 2
    Output: 8

    Explanation: A possible sequence is: A -> B -> idle -> A -> B -> idle -> A -> B.

    After completing task A, you must wait two intervals before doing A again. 
    The same applies to task B. In the 3rd interval, neither A nor B can be done, 
    so you idle. By the 4th interval, you can do A again as 2 intervals have passed.

+ Example 2:
    Input: tasks = ["A","C","A","B","D","B"], n = 1
    Output: 6

    Explanation: A possible sequence is: A -> B -> C -> D -> A -> B.

    With a cooling interval of 1, you can repeat a task after just one other task.
```

**Intuition**
```diff
So in one cycle of tasks there has to be n+1 distinct tasks,
if not then for remaining time the cpu will be idle

+ we will make each cycle one by one greedely
each cycle will have (n+1) most frequent distinct tasks, if there are no (n+1)
distinct tasks then add idle task

            A -> B -> idle -> A -> B -> idle -> A -> B.
            <------------->.  <------------->.  <----->

+ CPU Cycle size = n+1

+ Each cycle:
    pick top (n+1) tasks
    run them
    if not enough → idle fills

+ Greedy + Max Heap + Process in cycles of size (n+1)
```

```cpp
int leastInterval(vector<char>& tasks, int n) {
    unordered_map<char, int> mp;
    for(char &t: tasks) mp[t]++;

    priority_queue<pair<int, char>> pq;
    for(auto &[task, freq]: mp) pq.push({freq, task});

    int ans = 0;
    while(!pq.empty()) {
        vector<pair<int, char>> v;

        int count = 0;
        // CPU cycle of size n+1
        while(!pq.empty() && count <= n) {
            auto [freq, task] = pq.top();
            pq.pop();
            count++;

            if(freq > 1) v.push_back({freq-1, task});
        }

        if(v.size() == 0) ans += count; // last cycle case
        else ans += (n+1);

        for(auto t : v) pq.push(t);
    }

    return ans;
}
```

## 6. Reorganize String
```diff
Given a string s, rearrange the characters of s so that any two adjacent characters are not the same.

Return any possible rearrangement of s or return "" if not possible.

+ Example 1:
    Input: s = "aab"
    Output: "aba"

+ Example 2:
    Input: s = "aaab"
    Output: ""
```

**Greedely pick highest two frequent element**
```cpp
string reorganizeString(string s) {
    unordered_map<int, int> mp;
    for(char c : s) mp[c]++;

    priority_queue<pair<int, char>> pq;
    for(auto &[c, freq]: mp) pq.push({freq, c});

    string ans = "";
    while(!pq.empty()) {
        auto [f1, c1] = pq.top();
        pq.pop();
        ans += c1;

        if(pq.empty()) {
            if(f1 > 1) return "";
            else break;
        }

        auto [f2, c2] = pq.top();
        pq.pop();
        ans += c2;

        if(f2 > 1) pq.push({f2-1, c2});
        if(f1 > 1) pq.push({f1-1, c1});
    }

    return ans;
}
```

## 7. Single-Threaded CPU
```diff
You are given n​​​​​​ tasks labeled from 0 to n - 1 represented by a 2D integer array tasks, 
where tasks[i] = [enqueueTimei, processingTimei] means that the i​​​​​​th​​​​ task will be 
available to process at enqueueTimei and will take processingTimei to finish processing.

You have a single-threaded CPU that can process at most one task at a time and will act in the following way:

+ If the CPU is idle and there are no available tasks to process, the CPU remains idle.
+ If the CPU is idle and there are available tasks, the CPU will choose the one with 

the shortest processing time. If multiple tasks have the same shortest processing 
time, it will choose the task with the smallest index.

+ Once a task is started, the CPU will process the entire task without stopping.
+ The CPU can finish a task then start a new one instantly.
+ Return the order in which the CPU will process the tasks.

Example 1:
Input: tasks = [[1,2],[2,4],[3,2],[4,1]]
Output: [0,2,3,1]
Explanation: The events go as follows: 

- At time = 1, task 0 is available to process. Available tasks = {0}.
- Also at time = 1, the idle CPU starts processing task 0. Available tasks = {}.
- At time = 2, task 1 is available to process. Available tasks = {1}.
- At time = 3, task 2 is available to process. Available tasks = {1, 2}.
- Also at time = 3, the CPU finishes task 0 and starts processing task 2 as it is the shortest. Available tasks = {1}.
- At time = 4, task 3 is available to process. Available tasks = {1, 3}.
- At time = 5, the CPU finishes task 2 and starts processing task 3 as it is the shortest. Available tasks = {1}.
- At time = 6, the CPU finishes task 3 and starts processing task 1. Available tasks = {}.
- At time = 10, the CPU finishes task 1 and becomes idle.
```

**Intuition**
```diff
+ Keep track of time: currTime
+ Maintain a maxHeap to store procTime and index
+ Push all tasks if enqueueTime <= currTime
+ Pick top element from heap and process that task
+ Now currTime = currTime + pq.top().procTime
```

```cpp
vector<int> getOrder(vector<vector<int>>& tasks) {

    // We Need one more vector to store the original indicies
    vector<vector<int>> t;
    for(int i=0; i<tasks.size(); i++)
        t.push_back({tasks[i][0], tasks[i][1], i});
    
    sort(t.begin(), t.end()); // sort based on enque time

    priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int,int>>> pq;
    vector<int> ans;
    int i = 0;
    long long currTime = t[0][0];

    while(i < t.size()) {
        // ⛔ Key: jump time if nothing to process
        if(pq.empty() && currTime < t[i][0]) {
            currTime = t[i][0];
        }

        while(i < t.size() && t[i][0] <= currTime) {
            pq.push({t[i][1], t[i][2]});
            i++;
        }

        // pick task with minimum proc time
        auto [procTime, idx] = pq.top();
        pq.pop();

        ans.push_back(idx);
        currTime += procTime;
    }

    while(!pq.empty()) {
        auto [procTime, idx] = pq.top();
        pq.pop();
        ans.push_back(idx);
    }

    return ans;
}
```

## 8. Process Tasks Using Servers
```diff
You are given two 0-indexed integer arrays servers and tasks of lengths n​​​​​​ and m​​​​​​ respectively. 
servers[i] is the weight of the i​​​​​​th​​​​ server, and tasks[j] is the time needed to process the j​​​​​​th​​​​ task in seconds.

+ Tasks are assigned to the servers using a task queue. Initially, all servers are free, and the queue is empty.

+ At second j, the jth task is inserted into the queue (starting with the 0th task being inserted at second 0). 
As long as there are free servers and the queue is not empty, the task in the front of the queue will be 
assigned to a free server with the smallest weight, and in case of a tie, it is assigned to a 
free server with the smallest index.

If there are no free servers and the queue is not empty, we wait until a server becomes free and 
immediately assign the next task. If multiple servers become free at the same time, then multiple 
tasks from the queue will be assigned in order of insertion following the weight and index priorities above.

+ A server that is assigned task j at second t will be free again at second t + tasks[j].

+ Build an array ans​​​​ of length m, where ans[j] is the index of the server the j​​​​​​th task will be assigned to.

Return the array ans​​​​.

Example 1:

Input: servers = [5,1,4,3,2], tasks = [2,1,2,4,5,2,1]
Output: [1,4,1,4,1,3,2]
Explanation: Events in chronological order go as follows: 
- At second 0, task 0 is added and processed using server 1 until second 2.
- At second 1, task 1 is added and processed using server 4 until second 2.
- At second 2, servers 1 and 4 become free. Task 2 is added and processed using server 1 until second 4. 
- At second 3, task 3 is added and processed using server 4 until second 7.
- At second 4, server 1 becomes free. Task 4 is added and processed using server 1 until second 9. 
- At second 5, task 5 is added and processed using server 3 until second 7.
- At second 6, task 6 is added and processed using server 2 until second 7.
```

**Approach**
```diff
+ Maintain two priority queues: one for available servers and one for busy servers
+ Initially all server are put into available queue
+ As time goes by, first check if any server from busy queue is free now
+ If Yes push that server back into available server
+ For the current task pick top server from available queue

This is a classic dual heap scheduling pattern:

Recognize this when:
    Tasks arrive over time
    Resources get busy and free later
    Need to simulate time
```

```cpp
class Compare {
public:
    bool operator()(pair<int,int>&a, pair<int,int>&b) {
        if(a.first == b.first) return a.second > b.second;
        else return a.first > b.first;
    }
};

class Solution {
public:
    vector<int> assignTasks(vector<int>& servers, vector<int>& tasks) {
        priority_queue<pair<int, int>, vector<pair<int,int>>, Compare> avaServers; // minHeap
        for(int i=0; i<servers.size(); i++) avaServers.push({servers[i], i});

        priority_queue<pair<int, int>, vector<pair<int,int>>, Compare> busyServers; // minHeap

        vector<int> ans;
        long long currTime = 0;
        int i = 0;
        while(i<tasks.size()) {
            int t = tasks[i];
            currTime = max(currTime, (long long)i);

            while(!busyServers.empty() && busyServers.top().first <= currTime) {
                auto [finishTime, idx] = busyServers.top();
                busyServers.pop();

                avaServers.push({servers[idx], idx});
            }

            // if no server available → jump time
            if(avaServers.empty()) {
                currTime = busyServers.top().first;
                continue;
            }

            auto [avs, idx] = avaServers.top();
            avaServers.pop();

            busyServers.push({currTime+t, idx});
            ans.push_back(idx);
            i++;
        }

        return ans;
    }
};
```

## 9. Merge k Sorted Lists
```diff
You are given an array of k linked-lists lists, each linked-list is sorted in ascending order.

Merge all the linked-lists into one sorted linked-list and return it.

 

+ Example 1:
    Input: lists = [[1,4,5],[1,3,4],[2,6]]
    Output: [1,1,2,3,4,4,5,6]

+Explanation: The linked-lists are:
    [
        1->4->5,
        1->3->4,
        2->6
    ]
    merging them into one sorted linked list:
    1->1->2->3->4->4->5->6
```

```cpp
ListNode* mergeKLists(vector<ListNode*>& lists) {
    // put first node of each list into minHeap
    // take min element and put next of it
    priority_queue<pair<int, ListNode*>, vector<pair<int, ListNode*>>, greater<pair<int, ListNode*>>>pq;

    for(ListNode* l : lists) {
        if(l) 
            pq.push({l->val, l});
    } 

    ListNode* ans = NULL;
    ListNode* tail = NULL;

    while(!pq.empty()) {
        auto [val, node] = pq.top();
        pq.pop();

        if(!ans) {
            ans = node;
            tail = ans;
        } else {
            tail->next = node;
            tail = tail->next;
        }

        if(node->next) pq.push({node->next->val, node->next});
    } 

    return ans;
}
```

## 9. Find K Pairs with Smallest Sums
```diff
You are given two integer arrays nums1 and nums2 sorted in non-decreasing order and an integer k.
Define a pair (u, v) which consists of one element from the first array and one element from the second array.

+ Return the k pairs (u1, v1), (u2, v2), ..., (uk, vk) with the smallest sums.

-Example 1:
    Input: nums1 = [1,7,11], nums2 = [2,4,6], k = 3
    Output: [[1,2],[1,4],[1,6]]
    Explanation: The first 3 pairs are returned from the sequence: 
                [1,2],[1,4],[1,6],[7,2],[7,4],[11,2],[7,6],[11,4],[11,6]

-Example 2:
    Input: nums1 = [1,1,2], nums2 = [1,2,3], k = 2
    Output: [[1,1],[1,1]]
    Explanation: The first 2 pairs are returned from the sequence: 
                [1,1],[1,1],[1,2],[2,1],[1,2],[2,2],[1,3],[1,3],[2,3]
```

**Intuition (Best-First Search Pattern)**
```diff
+ Use minHeap with {sum, {i, j}}
+ if we expand with (i+1, j) and (i, j+1)
- But there’s no visited tracking, so same pair can be pushed multiple times.

Example:
    From (0,0):
    push (1,0) and (0,1)

    Then:
        (1,0) pushes (1,1)
        (0,1) also pushes (1,1) ❌ duplicate

-👉 This leads to:
    repeated work
    incorrect ordering sometimes
    TLE in worst case

+Instead of expanding both directions, use this:
+👉 Fix i, only expand j

+Optimal Approach (Best-First Search Pattern)
Idea:
    Start with (i, 0) for all i
    Always expand (i, j+1)
```

```cpp
vector<vector<int>> kSmallestPairs(vector<int>& nums1, vector<int>& nums2, int k) {
    int n = nums1.size(), m = nums2.size();

    priority_queue<pair<int, pair<int,int>>, 
        vector<pair<int, pair<int,int>>>, 
        greater<pair<int, pair<int,int>>>> pq;

    // initialize with first column
    for(int i = 0; i < min(n, k); i++) {
        pq.push({nums1[i] + nums2[0], {i, 0}});
    }

    vector<vector<int>> ans;

    while(!pq.empty()) {
        auto [sum, idx] = pq.top(); pq.pop();
        int i = idx.first, j = idx.second;

        ans.push_back({nums1[i], nums2[j]});
        if(ans.size() == k) return ans;

        if(j + 1 < m) {
            pq.push({nums1[i] + nums2[j+1], {i, j+1}});
        }
    }

    return ans;
}
```

## 10. IPO
```diff
You have:
    capital[i] → minimum capital required to start project
    profit[i] → profit you gain after finishing it
    w → current capital
    k → max projects you can do

    👉 Goal: maximize final capital

+Example 1:
    Input: k = 2, w = 0, profits = [1,2,3], capital = [0,1,1]
    Output: 4
    Explanation: Since your initial capital is 0, you can only start the project indexed 0.
    After finishing it you will obtain profit 1 and your capital becomes 1.
    With capital 1, you can either start the project indexed 1 or the project indexed 2.
    Since you can choose at most 2 projects, you need to finish the project indexed 2 to get the maximum capital.
    Therefore, output the final maximized capital, which is 0 + 1 + 3 = 4.

+Example 2:
    Input: k = 3, w = 0, profits = [1,2,3], capital = [0,1,2]
    Output: 6

```

**Intuition**
```diff
+At any moment:
    You can only pick projects where capital[i] <= w

+So:
    Filter feasible projects
    From them → pick max profit

+👉 That’s two separate concerns → two heaps
```

```cpp
int findMaximizedCapital(int k, int w, vector<int>& profits, vector<int>& capital) {
    int n = profits.size();
    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>>> allProjects; //minHeap
    priority_queue<pair<int,int>> unlockedProjects; //maxHeap

    for(int i=0; i<n; i++)
        allProjects.push({capital[i], profits[i]});

    while(k--) {
        // check if we can unlock projects based on current capital
        while(!allProjects.empty() && allProjects.top().first <= w) {
            auto [c, p] = allProjects.top();
            allProjects.pop();

            unlockedProjects.push({p, c});
        }

        // pick max profit
        if(!unlockedProjects.empty()) {
            auto [p, c] = unlockedProjects.top();
            unlockedProjects.pop();

            w += p;
        }
    }

    return w;
}
```