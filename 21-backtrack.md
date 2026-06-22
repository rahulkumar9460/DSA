# CASE 1: Subsets and permutations

## 1. Subsets
[Leetcode link](https://leetcode.com/problems/subsets/description/)

Given an integer array nums of unique elements, return all possible subsets (the power set).

The solution set must not contain duplicate subsets. Return the solution in any order.

 
- Input: nums = [1,2,3]
- Output: [[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]

```
                             {}
                /            |          \
              {1}           {2}         {3}
            /   \            |            
        {1, 2} {1, 3}       {2, 3}       
          |
        {1, 2, 3}
                
```

### Intuition
> [!IMPORTANT]
> A subset can start from any index
>
> if a subset starts from index 'i'
>
>           then we may include i+1 th element
>           or we may not include
>
> So the recursion becomes like:
>
>           include
>           solve
>           remove

```cpp
class Solution {
public:
    vector<vector<int>> ans;
    vector<int> temp;

    void solve(vector<int>& nums, int idx) {
        ans.push_back(temp);
        if(idx >= nums.size()) return;

        for(int i=idx; i<nums.size(); i++) {
            temp.push_back(nums[i]);        // include
            solve(nums, i+1);               // solve
            temp.pop_back();                // remove
        }
    }

    vector<vector<int>> subsets(vector<int>& nums) {
        solve(nums, 0);

        return ans;
    }
};
/*
    total recursion calls = 2^n
    work done inside one recursion call = O(n)

    time = O(n* 2^n)

    space will be height of recursion tree = O(n)
*/
```

---

## 2. Subsets II
[Leetcode link](https://leetcode.com/problems/subsets-ii/)

Given an integer array nums that may contain duplicates, return all possible subsets (the power set).

The solution set must not contain duplicate subsets. Return the solution in any order.

- Input: nums = [1,2,2]
- Output: [[],[1],[1,2],[1,2,2],[2],[2,2]]

### Intuition
> [!IMPORTANT]
> Subsets that starts from 1
>
>           If we include 2 of index 1 the subset will be (1, 2)
>           If we include 2 of index 2 the subset will be (1, 2)
>               We get duplicate subset
>
> So While choosing the next element in a subset, dont choose the same as previously choosen

```cpp
class Solution {
public:
    vector<vector<int>> ans;
    vector<int> temp;

    void solve(vector<int>& nums, int idx) {
        ans.push_back(temp);
        if(idx >= nums.size()) return;

        for(int i=idx; i<nums.size(); i++) {
            if(i > idx && nums[i] == nums[i-1]) continue; // dont choose duplicates

            temp.push_back(nums[i]);
            solve(nums, i+1);
            temp.pop_back();
        }
    }

    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        ans = {};
        temp = {};
        sort(nums.begin(), nums.end());

        solve(nums, 0);

        return ans;    
    }
};
```

---

## 3. Permutations
[Leetcode link](https://leetcode.com/problems/permutations/description/)

Given an array nums of distinct integers, return all the possible permutations. You can return the answer in any order.


- Input: nums = [1,2,3]
- Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]

```
                                            t = {}, avail = {1, 2, 3}
                            /                           |                                    \
                t = {1}, avail = {2, 3}         t = {2}, avail={1, 3}                    t = {3}, avail={1, 2}
                /                  \            /                   \                           /           \
t={1, 2}, avail={3} t={1, 3}, avail={2}    t={2, 1}, av={3}     t={2, 3}, av={1}        t={3, 1}, av={2}    t={3, 2}, av={1}
      |                   |                         |                   |                       |                    |
t={1,2,3}, av={}    t={1,3,2}, av={2}        t={2,1,3}, av={}      t={2,3,1}, av={}        t={3,1,2}, av={}     t={3,2,1}, av={}


```

### Intuition
> [!IMPORTANT]
> We keep two vectors:
>
>               temp and available
>               Keep pushing into temp and deleting from available
>               Once the solution is done:
>                   delete from temp and insert back into available
>
> If we use available vector:
>
>               We need to insert and delete elements
>               which is O(n)
>
> Instead we use vector<bool> used
>
>               We pick elements which are not used
>               When we use some element: used[val] = true
>               Once recursion is done
>                       used[val] = false;

```cpp
class Solution {
public:
    vector<vector<int>> ans;
    vector<int> temp;

    void solve(vector<int> &nums, vector<bool> &used) {
        if(temp.size() == nums.size()) {
            ans.push_back(temp);
            return;
        }

        for(int i=0; i<nums.size(); i++) {
            if(used[i]) continue;       // If already used in temp

            temp.push_back(nums[i]);
            used[i] = true;

            solve(nums, used);

            temp.pop_back();
            used[i] = false;
        }
    }

    vector<vector<int>> permute(vector<int>& nums) {
        ans = {};
        temp = {};
        vector<bool> used(nums.size(), false);

        solve(nums, used);
        return ans;
    }
};
/*
    total recursion calls:  1 + n + n*(n-1) + n*(n-1)*(n-2)... = n!
    work done inside one recursion call = O(n)

    time = O(n * n!)
    space = height of recursion tree = O(n)
*/
```

---

## 4. Permutations II
[Leetcode link](https://leetcode.com/problems/permutations-ii/description/)

Given a collection of numbers, nums, that might contain duplicates, return all possible unique permutations in any order.

 
- Input: nums = [1,1,2]
- Output:
        [[1,1,2],
        [1,2,1],
        [2,1,1]]

### Intuition
> [!IMPORTANT]
> If we sort the nums: [1, 1, 2]
> 
>               All permutations start starts from index 0 or index 1 will be same
>               so they will be duplicate
>
> So we need to ignore indicies whose value was already included in permutations

```cpp
class Solution {
public:
    vector<vector<int>> ans;
    vector<int> temp;
    int n;

    void solve(vector<int> &nums, vector<bool> &used) {
        if(nums.size() == temp.size()) {
            ans.push_back(temp);
            return;
        }

        for(int i=0; i<nums.size(); i++) {
            if(used[i]) continue;
            if(i > 0 && nums[i] == nums[i-1] && !used[i-1]) continue; // duplicate handling

            temp.push_back(nums[i]);
            used[i] = true;

            solve(nums, used);

            temp.pop_back();
            used[i] = false;
        }
    }
    vector<vector<int>> permuteUnique(vector<int>& nums) {
        ans = {};
        temp = {};
        sort(nums.begin(), nums.end());
        vector<bool> used(nums.size(), false);

        solve(nums, used);
        return ans;
    }
};
```

