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
> last = ans.back()
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