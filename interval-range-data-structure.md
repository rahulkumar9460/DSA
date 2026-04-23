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