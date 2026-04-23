# 1. Range updates
- add(a, b)
- remove(a, b)
- insert interval


# 2. Queries on points or ranges
- isExists(x)
- query(a, b)
- count / coverage


# 3. Large constraints
- Cannot iterate over all numbers

👉 Then think:
- “I should NOT store elements individually — I should store intervals.”

> [!NOTE]
>
>       Core idea is to maintain Sorted, non-overlapping intervals
>
> Operations:
>
> - Merge on insert
> - Split on delete
> - Binary search for queries

---

> [!IMPORTANT]
>
>       Compress continuous data into intervals

```
Edge cases:

    1. Adjacent intervals [1,2] and [3,5]
    2. Fully overlapping ranges
    3. Queries at boundaries
```

---

```
problems:

Merge Intervals
Insert Interval
Non-overlapping Intervals

My Calendar I
My Calendar II
Data Stream as Disjoint Intervals

Range Module ⭐ MOST IMPORTANT
Falling Squares
Count Integers in Intervals
```

---

## 1. Merge Intervals
[Leetcode link](https://leetcode.com/problems/merge-intervals/description/)

Given an array of intervals where intervals[i] = [starti, endi], merge all overlapping intervals,
and return an array of the non-overlapping intervals that cover all the intervals in the input.

 

Example 1:
- Input: intervals = [[1,3],[2,6],[8,10],[15,18]]
- Output: [[1,6],[8,10],[15,18]]
- Explanation: Since intervals [1,3] and [2,6] overlap, merge them into [1,6].

Example 2:
- Input: intervals = [[1,4],[4,5]]
- Output: [[1,5]]
- Explanation: Intervals [1,4] and [4,5] are considered overlapping.

```
Constraints:

    1 <= intervals.length <= 10^4
    intervals[i].length == 2
    0 <= starti <= endi <= 10^4
```

### Intuition
> [!NOTE]
>
> Given two intervals [a, b] and [c, d] both are given in sorted order which mean a <= c
>
> these intervals can overlap if c <= b
>
> Ex> [2, 5] and [4, 8]
>
> since 4 < 5 --> overlap
>
>       combined interval = [2, max(5, 8)] == [2, 8]

> [!IMPORTANT]
>
> sort intervals in non-descending order
>
> take ans = {} and ans.push(intervals[0])
>
> iterate over i = 1 to n
>
>       last = ans.back()
>
>       if last[1] >= intervals[i][0] --> intervals[i] overlaps with last
>           last[1] = max(last[1], intervals[i][1]) --> merge both intervals
>
>       else 
>           ans.push_back(intervals[i])

```cpp
vector<vector<int>> merge(vector<vector<int>>& intervals) {
    sort(intervals.begin(), intervals.end());

    vector<vector<int>> ans;
    ans.push_back(intervals[0]);

    for(int i=1; i<intervals.size(); i++) {
        vector<int> &last = ans.back();
        if(intervals[i][0] <= last[1]) { // overlaps
            last[1] = max(last[1], intervals[i][1]); // merge
        } else { 
            ans.push_back(intervals[i]);
        }
    }

    return ans;
    }
```
---

## 2. Insert Interval
[Leetcode link](https://leetcode.com/problems/insert-interval/description/)

You are given an array of non-overlapping intervals intervals where intervals[i] = [starti, endi] 
represent the start and the end of the ith interval and intervals is sorted in ascending order by starti

Insert newInterval into intervals such that intervals is still sorted in ascending order by starti 
and intervals still does not have any overlapping intervals (merge overlapping intervals if necessary).

> Return intervals after the insertion.

```
Input: intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
    Output: [[1,2],[3,10],[12,16]]
    Explanation: Because the new interval [4,8] overlaps with [3,5],[6,7],[8,10].
```

### Intuition
> [!IMPORTANT]
>
> - int left = newInterval[0];
> - int right = newInterval[1];
>
> upper_bound will give us iterator 'it' such that it->first > left
>
> overlap may start from it-1
> 
>           if(it != ans.begin()) {
>               it--;
>               if(it->second < left) it++; // if it doesnt overlap, move further
>           }
>
> if a interval overlaps with {left, right}, partially or fully
>
>           left = min(left, it->first)
>           right = max(rightm it->second)
>           and.erase(it); // erase it since it is included in {left, right}
>
> Now insert {left, right}

```cpp
vector<vector<int>> insert(vector<vector<int>>& intervals, vector<int>& newInterval) {
    vector<vector<int>> ans(intervals.begin(), intervals.end());

    auto it = upper_bound(ans.begin(), ans.end(), newInterval);
    int left = newInterval[0];
    int right = newInterval[1];

    if(it != ans.begin()) {
        it--;
        if((*it)[1] < left) it++; //  it doesnt overlap, move further
    } 

    while(it != ans.end() && (*it)[0] <= right) { // ovelapping intervals
        left = min(left, (*it)[0]);
        right = max(right, (*it)[1]);
        ans.erase(it);
    }

    ans.insert(it, {left, right});

    return ans;
}
```

---

## 3. Non-overlapping Intervals
[Leetcode link](https://leetcode.com/problems/non-overlapping-intervals/description/)

Given an array of intervals intervals where intervals[i] = [starti, endi], 
return the minimum number of intervals you need to remove to make the rest of the intervals non-overlapping.

Note that intervals which only touch at a point are non-overlapping. 
For example, [1, 2] and [2, 3] are non-overlapping.

 

Example 1:
- Input: intervals = [[1,2],[2,3],[3,4],[1,3]]
- Output: 1
- Explanation: [1,3] can be removed and the rest of the intervals are non-overlapping.

Example 2:
- Input: intervals = [[1,2],[1,2],[1,2]]
- Output: 2
- Explanation: You need to remove two [1,2] to make the rest of the intervals non-overlapping.

### Intuition
> [!IMPORTANT]
> - Need to remove minimum interval <==> Need to keep maximum intervals
> - always pick smalles ending interval first, so we have larger sapce for other intervals
> 
>           For Example [[1, 2], [1, 10], [2, 3]]
>           if we pick [1, 10] first then we cant pick [1, 2] and [2, 3] ==> ans = 2
>           But if we pick [1, 2] first then we can pick [2, 3] ==> ans = 1
>
> - sort the intervals with increasing end value

```cpp
bool compare(vector<int> &a, vector<int> &b) {
    if(a[1] == b[1]) return a[0] < b[0];
    return a[1] < b[1];
}
class Solution {
public:
    int eraseOverlapIntervals(vector<vector<int>>& intervals) {
         /*
            (1, 2), (1, 10), (6, 7), (2, 3), (4, 5)

            first sort in ascending order of end interval:
            (1, 2), (2, 3), (4, 5), (6, 7), (1, 10)
        */
        sort(intervals.begin(), intervals.end(), compare);

        vector<int> prev = intervals[0];
        int ans = 0;

        for(int i=1; i<intervals.size(); i++) {
            if(intervals[i][0] < prev[1]) {
                ans++;
            } else {
                prev = intervals[i];
            }
        }

        return ans;
    }
};
```
