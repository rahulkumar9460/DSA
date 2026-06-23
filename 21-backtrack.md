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

---

# CASE 2: Multiple choices: pick one and recurse

## 1. Combination Sum II
[Leetcode link](https://leetcode.com/problems/combination-sum-ii/description/)

Given a collection of candidate numbers (candidates) and a target number (target), find all unique
 combinations in candidates where the candidate numbers sum to target.

Each number in candidates may only be used once in the combination.

Note: The solution set must not contain duplicate combinations.

 
- Input: candidates = [10,1,2,7,6,1,5], target = 8
- Output: 
        [
            [1,1,6],
            [1,2,5],
            [1,7],
            [2,6]
        ]

### Intuition
> [!IMPORTANT]
> At each index we have two choices either pick it or skip it
>
> Both choices can give us a valid answer
>
> After sorting the candidates, number of choices we can effectivly call out 
>
> int idx --> tells what choices are ramianing
>
>           remaining choices are [idx....n-1]

```cpp
class Solution {
public:
    vector<vector<int>> ans;
    vector<int> temp;

    void solve(vector<int>& candidates, int idx, int target) {
        if(target == 0) {
            ans.push_back(temp);
            return;
        }

        for(int i=idx; i<candidates.size(); i++) { // all choices, pick one and recurse
            int cand = candidates[i];
            if(cand > target) break;
            if(i > idx && candidates[i] == candidates[i-1]) continue; // duplicate choice

            temp.push_back(cand);
            solve(candidates, i+1, target-cand);
            temp.pop_back();
        }
    } 

    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        sort(candidates.begin(), candidates.end());

        solve(candidates, 0, target);
        return ans;
    }
};
```

---

## 2. Palindrome Partitioning
[Leetcode link](https://leetcode.com/problems/palindrome-partitioning/description/)

Given a string s, partition s such that every substring of the partition is a palindrome. 
Return all possible palindrome partitioning of s.

- Input: s = "aab"
- Output: [["a","a","b"],["aa","b"]]

### Intuition
> [!IMPORTANT]
> We have multiple choices to break a string into multiple chuncks
>
> We pick the leftMost chunck if it is palindrome and solve for remaining part
>
> For example: "aab"
>
>               a + solve(ab)
>               and; "aa" + solve(b)
>
>               Now for "ab"
>                       a + solve(b)
>
> We have one idx which tells what part of string s is remaining that needs to be partitioned

```cpp
class Solution {
public:
    vector<vector<string>> ans;
    vector<string> temp;

    bool isPalindrome(string &s, int start, int end) {
        while(start < end) {
            if(s[start] != s[end]) return false;
            start++;
            end--;
        }

        return true;
    }

    void solve(string &s, int idx) {
        if(idx == s.size()) {
            ans.push_back(temp);
            return;
        }

        for(int i=idx; i<s.size(); i++) { // all choices, pick one and recurse
            if(isPalindrome(s, idx, i)) {
                temp.push_back(s.substr(idx, i-idx+1));
                solve(s, i+1);
                temp.pop_back();
            }
        }
    }

    vector<vector<string>> partition(string s) {
        solve(s, 0);

        return ans;
    }
};
```

---

## 3. Restore IP Addresses
[Leetcode link](https://leetcode.com/problems/restore-ip-addresses/description/)

A valid IP address consists of exactly four integers separated by single dots. Each integer is 
between 0 and 255 (inclusive) and cannot have leading zeros.

For example, "0.1.2.201" and "192.168.1.1" are valid IP addresses, but "0.011.255.245", "192.168.1.312" 
and "192.168@1.1" are invalid IP addresses.

Given a string s containing only digits, return all possible valid IP addresses that can be formed 
by inserting dots into s. You are not allowed to reorder or remove any digits in s. You may return 
the valid IP addresses in any order.

- Input: s = "25525511135"
- output: ["255.255.11.135","255.255.111.35"]

### Intuition
> [!IMPORTANT]
> We hae multiple choices the break the string
>
> We can break the string exactly into 4 parts only
>
> For example: "25525511135"
>
>               Choice1: "2" + solve(5525511135)
>               Choice2: "25" + solve(525511135)
>               Choice3: "255" + solve(25511135)
>
> We need special handling for '0'
>
> The solution below passes substring
>
>           But for optimal solution use 'idx' to mark the substring

```cpp
class Solution {
public:
    vector<string> ans;
    vector<string> temp;

    void solve(string s) {
        if(s == "") {
            if(temp.size() != 4) return;
            
            string ip = "";
            for(string t : temp) {      // build the ip string
                ip += t;
                ip += '.';
            }
            ip.pop_back();

            ans.push_back(ip);
            return;
        }

        if(s[0] == '0') {   // in case of '0' only one choice
            temp.push_back("0");
            solve(s.substr(1));
            temp.pop_back();
        } else {
            for(int i=0; i<3; i++) {    // ip chunk can have max len of 3, 3 choices
                if(s[i] < '0' || s[i] > '9') break;

                string segment = s.substr(0, i+1);
                
                if(stoi(segment) <= 255) {  // check if choice is valid
                    temp.push_back(segment);
                    solve(s.substr(i+1));
                    temp.pop_back();
                }
            }
        }

        
    }

    vector<string> restoreIpAddresses(string s) {
        solve(s);

        return ans;
    }
};
```

---

## 4. Letter Combinations of a Phone Number
[Leetcode link](https://leetcode.com/problems/letter-combinations-of-a-phone-number/description/)

Given a string containing digits from 2-9 inclusive, return all possible letter combinations that the 
number could represent. Return the answer in any order.

A mapping of digits to letters (just like on the telephone buttons) is given below.
Note that 1 does not map to any letters.

- Input: digits = "23"
- Output: ["ad","ae","af","bd","be","bf","cd","ce","cf"]

```cpp
class Solution {
public:
    unordered_map<char, string> mapping = {
        {'2', "abc"},
        {'3', "def"},
        {'4', "ghi"},
        {'5', "jkl"},
        {'6', "mno"},
        {'7', "pqrs"},
        {'8', "tuv"},
        {'9', "wxyz"}
    };
    vector<string> ans;
    string temp;

    void solve(string & digits, int idx) {
        if(idx == digits.size()) {
            ans.push_back(temp);
            return;
        }

        for(char c : mapping[digits[idx]]) { // all choices 
            temp += c;
            solve(digits, idx+1);
            temp.pop_back();
        }
    }

    vector<string> letterCombinations(string digits) {
        solve(digits, 0);

        return ans;
    }
};
```
