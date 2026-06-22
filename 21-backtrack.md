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
