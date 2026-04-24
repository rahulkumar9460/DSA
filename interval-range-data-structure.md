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

---

## 4. My Calendar I
[Leetcode link](https://leetcode.com/problems/my-calendar-i/description/)

Implement the MyCalendar class:

> MyCalendar() Initializes the calendar object.
> boolean book(int startTime, int endTime) Returns true 
> if the event can be added to the calendar successfully without causing a double booking.
>  Otherwise, return false and do not add the event to the calendar.

```
Input
["MyCalendar", "book", "book", "book"]
[[], [10, 20], [15, 25], [20, 30]]
Output
[null, true, false, true]

Explanation
MyCalendar myCalendar = new MyCalendar();
myCalendar.book(10, 20); // return True
myCalendar.book(15, 25); // return False, It can not be booked because time 15 is already booked by another event.
myCalendar.book(20, 30); // return True, The event can be booked, as the first event takes every time less than 20, but not including 20.

Constraints:

    0 <= start < end <= 109
    At most 1000 calls will be made to book
```

### Intuition:
> [!IMPORTANT]
> - store all evenets {start, end} into set
>
> Find it = upper_bound({start, INT_MAX})
>
>       it--;
>       if(it->second <= start) it++
>
> - now for it we have it->first >= start
>
> check if (it->first < end) return false // intersection case
>
> else insert ({start, end})

```cpp
class MyCalendar {
public:
    set<pair<int,int>> bookings;
    MyCalendar() {
        
    }
    
    bool book(int startTime, int endTime) {
        auto it = bookings.upper_bound({startTime, INT_MAX});

        if(it != bookings.begin()) {
            it--;
            if(it->second <= startTime) it++;
        }

        if(it != bookings.end() && it->first < endTime) {
            return false;
        }

        bookings.insert({startTime, endTime});
        return true;
    }
};
```

---

## 5. My Calendar II
[Leetcode link](https://leetcode.com/problems/my-calendar-ii/description/)

Implement the MyCalendarTwo class:

- MyCalendarTwo() Initializes the calendar object.
- boolean book(int startTime, int endTime) Returns true if the event can be added to the calendar...
- successfully without causing a triple booking. Otherwise, return false and do not add the event to the calendar.

> at any time there should not be more than 2 meetings

### Intuition
> [!IMPORTANT]
>
> maintain a array of overlapping intervals
> - two bookings [1, 5] and [2, 9] --> overlapping interval = [2, 5]
>
> put [2, 5] into overlaping intervals
>
>       all time ranges in overlaping intervals have two bookings
>
> - if new bookings intersects with any of overlapping iterval
> - it will cause triple bookings

```cpp
class MyCalendarTwo {
public:
    vector<pair<int,int>> bookings;
    vector<pair<int, int>> overlaps;
    MyCalendarTwo() {
        
    }
    
    bool book(int startTime, int endTime) {
        // check for third booking
        for(auto &p : overlaps) {
            if(max(p.first, startTime) < min(p.second, endTime))
                return false;
        }

        for(auto &p: bookings) {
            int l = max(p.first, startTime);
            int r = min(p.second, endTime);
            if(l < r)
                overlaps.push_back({l, r}); // insert overlaping interval
        }

        bookings.push_back({startTime, endTime});
        return true;
    }
};
```

---

## 6. Range Module
[Leetcode link](https://leetcode.com/problems/range-module/description/)

RangeModule() Initializes the object of the data structure.

void addRange(int left, int right) Adds the half-open interval [left, right),
tracking every real number in that interval. Adding an interval that partially overlaps 
with currently tracked numbers should add any numbers in the interval [left, right) 
that are not already tracked.

boolean queryRange(int left, int right) Returns true if every real number in the interval
[left, right) is currently being tracked, and false otherwise.
void removeRange(int left, int right) Stops tracking every real number currently being 
tracked in the half-open interval [left, right).

```
Constraints:

1 <= left < right <= 10^9
At most 10^4 calls will be made to addRange, queryRange, and removeRange.
```

### Intuition
> [!IMPORTANT]
>
> - store these pairs into a set --> set<pair<int,int>>
> - Now all ranges are in sorted order
>
> - query range
>
>           To find a value 'x' use upper_bound(x) = itr
>           Now it is guaranteed that 'x' will before 'itr', not 'itr' itself or after it
>
> - add range (start, end)
>
>           find upper bound of 'start', let call it = itr
>           Now do itr-- since it might overlap with (start, end)
>        
>           
>           Now we need to merge intervals
>           If itr->first <= end
>               start = min(start, itr->first)
>               end = max(end, itr->second)
>               set.erase(itr) // we can earse itr since it has been included by range {start, end}
>
>           Now insert (start, end) --> set.insert({start, end})
>
> - delete range (start, end)
>
>    1. this range (start, end) might include multiple ranges that need to deleted from set
>    2. and boundary of start and end there can be partially overlapping range
>
>    3. for example: (1, 5) and we need to delete (3, 8) so  
>    4. (1, 3) we still have to keep and (3, 5) we need to delete
> 
>
>           find upper bound of 'start' call it 'itr'
>           Do itr-- since it might overlap with (start, end)
>   
>           declare one vector<pair<int,int>> toAdd, to add the ranges that gets cut off at boundaries
>       
>           Now iterate: while(itr->first < end)
>               if itr->first < start
>                   toAdd.push({itr->first, start})
>               if itr->second > end
>                   toAdd.push({end, itr->second})
>               itr = set.erase(itr)
>
>
>           Now push back these partially cut of ranges from toAdd
>           for(r : toAdd) set.insert(r)         


```cpp
class RangeModule {
public:
    set<pair<int,int>> ranges;
    RangeModule() {
        
    }
    
    void addRange(int left, int right) {
        auto it = ranges.upper_bound({left, INT_MAX});
        if(it != ranges.begin()) {
            it--;
            if(it->second < left) it++;
        }

        while(it != ranges.end() && it->first <= right) {
            left = min(left, it->first);
            right = max(right, it->second);
            it = ranges.erase(it);
        }

        ranges.insert({left, right});
    }
    
    bool queryRange(int left, int right) {
        auto it = ranges.upper_bound({left, INT_MAX});
        if(it != ranges.begin()) {
            it--;
        }

        return it->first <= left && it->second >= right;
    }
    
    void removeRange(int left, int right) {
        auto it = ranges.upper_bound({left, INT_MAX});
        if(it != ranges.begin()) {
            it--;
            if(it->second <= left) it++;
        }

        vector<pair<int,int>> toAdd;
        while(it != ranges.end() && it->first < right) {
            if(it->first < left)
                toAdd.push_back({it->first, left});

            if(it->second > right)
                toAdd.push_back({right, it->second});

            it = ranges.erase(it);
        }

        for(auto &p : toAdd)
            ranges.insert(p);
    }
};
```

---

## 7. Falling Squares
[Leetcode link](https://leetcode.com/problems/falling-squares/description/)

There are several squares being dropped onto the X-axis of a 2D plane.

You are given a 2D integer array positions where positions[i] = [lefti, sideLengthi] 
represents the ith square with a side length of sideLengthi that is dropped with its 
left edge aligned with X-coordinate lefti.

> After each square is dropped, you must record the height of the current tallest stack of squares.

> Return an integer array ans where ans[i] represents the height described above after dropping the ith square.

### Intuition
> [!IMPORTANT]
> - always maintain non-overlappig x-axis intervals
> - when a squre (start, end) falls on another square
>   - there can be three scenarios
>
>        1. Below square might get cut on left side
>
>               <--------->
>
>           <------>
>
>       2. Below square might get overlap
>
>               <-------->
>
>                <------>
>
>       3. Below suqre might get cur on right side
>
>               <-------->
>
>                      <----->
>
> In each cases we the height becomes = height of below + height of falling square
> 1. we need to insert left cut part into set
> 2. Compeletely remove the overlapped part
> 3. we need to insert right cut part into set


```cpp
class Range {
public:
    set<pair<int, pair<int, int>>> ranges; // left, right, height
    int add(int start, int end, int height) {
        auto it = ranges.upper_bound({start, {INT_MAX, INT_MAX}});
        if(it != ranges.begin()) {
            it--;
            if(it->second.first <= start) it++;
        }

        int maxHeight = 0;
        vector<pair<int, pair<int, int>>> toAdd;

        while(it != ranges.end() && it->first < end) { //iterating overlapping intervals
            if(it->first < start) { // left cut part
                toAdd.push_back({it->first, {start, it->second.second}});
            }

            if(it->second.first > end) { // right cut part
                toAdd.push_back({end, {it->second.first, it->second.second}});
            }
            
            maxHeight = max(maxHeight, it->second.second);
            it = ranges.erase(it);
        }

        for(auto a : toAdd) ranges.insert(a);
        ranges.insert({start, {end, maxHeight+height}});

        return maxHeight+height;
    }
};
class Solution {
public:
    vector<int> fallingSquares(vector<vector<int>>& positions) {
        Range *range = new Range();
        
        vector<int> heights;
        int maxHeight = 0;
        for(auto &p : positions) {
            int h = range->add(p[0], p[0]+p[1], p[1]);
            maxHeight = max(maxHeight, h);
            heights.push_back(maxHeight);
        }

        return heights;
    }
};
```

 


