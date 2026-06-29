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

---

# CASE 3: Important problems:

## 1. Word Search
[Leetcode link](https://leetcode.com/problems/word-search/description/)

Given an m x n grid of characters board and a string word, return true if word exists in the grid.

The word can be constructed from letters of sequentially adjacent cells, where adjacent cells are 
horizontally or vertically neighboring. The same letter cell may not be used more than once.

- Input: board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
- Output: true

### Intuition
> [!IMPORTANT]
> Start from some node (i, j)
>
> Explore one neighbour, and mark it visited
>
> If not found then unmark it visited and explore remianing neighbours

```cpp
class Solution {
public:
    int n;
    int m;
    vector<vector<int>> dir;

    bool solve(vector<vector<char>>& board, string &word, int i, int j, int idx) {
        if(idx == word.size()) return true;
        
        for(auto &d : dir) {
            int x = i + d[0];
            int y = j + d[1];
            if(x<0 || y<0 || x>=n || y>=m) continue;
            if(board[x][y] != word[idx]) continue;

            char c = board[i][j];
            board[i][j] = '*';

            if(solve(board, word, x, y, idx+1))
                return true;

            board[i][j] = c;
        }

        return false;
    }

    bool exist(vector<vector<char>>& board, string word) {
        /*
            Start from some (i, j)
            mark (i, j) visited
            try to find solution
            if not found
                mark (i, j) un-visited
                try some other (i, j)
        */
        n = board.size();
        m = board[0].size();
        dir = {{1, 0}, {0, 1}, {-1, 0}, {0, -1}};

        for(int i=0; i<n; i++) {
            for(int j=0; j<m; j++) {
                if(board[i][j] == word[0]) {
                    char c = board[i][j];
                    board[i][j] = '*';
                    if(solve(board, word, i, j, 1))
                        return true;
                    
                    board[i][j] = c;
                }    
            }
        }
        
        return false;
    }
};
```

---

## 2. N-Queens
[Leetcode link](https://leetcode.com/problems/n-queens/description/)

The n-queens puzzle is the problem of placing n queens on an n x n chessboard such 
that no two queens attack each other.

Given an integer n, return all distinct solutions to the n-queens puzzle. 
You may return the answer in any order.

Each solution contains a distinct board configuration of the n-queens' placement, 
where 'Q' and '.' both indicate a queen and an empty space, respectively.

- Input: n = 4
- Output: [[".Q..","...Q","Q...","..Q."],["..Q.","Q...","...Q",".Q.."]]
- Explanation: There exist two distinct solutions to the 4-queens puzzle as shown above

### Intuition
> [!IMPORTANT]
> For each row we have n choices to place queeen
> 
> We place a queen in row and move to next row
> 
> Place a queen in a row at some location
>            Now check if valid placement:
>                1. that column should not have queen
>                2. those two digonal's should not have queen
>
> We can store column number in a set where queen is placed
>
>               There are two digonals:
>                   left to right 
>                   right to left
>
> to check if a diagonal has queen or not
>            left to right:
>            a digonal look like: {(0, 1), (1, 2), (2, 3)}
>                             or: {(1, 0), {2, 1}}
>                             or: {(2, 0), (3, 1)}
>           so this digonal can be identified with a-b  
>
>            right to left:
>            a digonal look like: {(0, 3), (1, 2), (2, 1), (3, 0)}
>                             or: {(1, 3), (2, 2), (3, 1)}
>           so this digonal can be identified with a+b

```cpp
class Solution {
public:
    vector<vector<string>> ans;
    vector<string> temp;
    unordered_set<int> col, leftDia, rightDia;
    string emptyRow;

    void solve(int &n, int idx) {
        if(idx == n) {
            ans.push_back(temp);
            return;
        }

        for(int j=0; j<n; j++) {
            if(col.count(j) || leftDia.count(idx-j) || rightDia.count(idx+j))
                continue;
            
            string row = this->emptyRow;
            row[j] = 'Q';

            temp.push_back(row);
            col.insert(j);
            leftDia.insert(idx-j);
            rightDia.insert(idx+j);

            solve(n, idx+1);

            temp.pop_back();
            col.erase(j);
            leftDia.erase(idx-j);
            rightDia.erase(idx+j);
        }

    }

    vector<vector<string>> solveNQueens(int n) {
        for(int i=0; i<n; i++)
            emptyRow += '.';
        
        solve(n, 0);

        return ans;
    }
};
```

---

## 3. Sudoku Solver
[Leetcode link](https://leetcode.com/problems/sudoku-solver/description/)

Write a program to solve a Sudoku puzzle by filling the empty cells.

A sudoku solution must satisfy all of the following rules:

Each of the digits 1-9 must occur exactly once in each row.
Each of the digits 1-9 must occur exactly once in each column.
Each of the digits 1-9 must occur exactly once in each of the 9 3x3 sub-boxes of the grid.
The '.' character indicates empty cells.

### Intuition
> [!IMPORTANT]
> solve sudoku row by row, one col at a time
>
>            to check a digit is already in column:
>                map<col_id, set<int>> colMp
>            
>            to check a digit is already in row:
>                map<row_id, set<int>> rowMp
>            
>            to check a digit is already in subgrid
>                for example: (0, 0) ==> (0, 0), (0, 1), (0, 2)...(2, 1), (2, 2)
>                             (0, 3) ==> (0, 3), (0, 4), (0, 5)...(2, 4), (2, 5)
>                             (6, 6) ==> (6, 6), (6, 7), (6, 8)...(8, 7), (8, 8)
>
>                    for (a, b) subgrid can be indentified with (a/3, b/3)
>                
>                use map<pair<int,int>, set<int>> subGridMp

```cpp
class Solution {
public:
    map<int, set<char>> colMp, rowMp;
    map<pair<int, int>, set<char>> subGridMp;

    bool solve(vector<vector<char>>& board, int i, int j) {
        if(i == 9) return true;

        if(j == 9) return solve(board, i+1, 0);

        if(board[i][j] != '.') return solve(board, i, j+1); // already a number is there

        for(char c='1'; c<='9'; c++) {      // at (i, j) we have nice choices to fill 
            if(rowMp[i].count(c) || colMp[j].count(c)) continue;

            int a = i/3, b = j/3;
            if(subGridMp[{a, b}].count(c)) continue;

            board[i][j] = c;
            rowMp[i].insert(c);
            colMp[j].insert(c);
            subGridMp[{a, b}].insert(c);

            if (solve(board, i, j+1))
                return true;
            
            board[i][j] = '.';
            rowMp[i].erase(c);
            colMp[j].erase(c);
            subGridMp[{a, b}].erase(c);
        }

        return false;
    }

    void solveSudoku(vector<vector<char>>& board) {

        for(int i=0; i<9; i++) {
            for(int j=0; j<9; j++) {
                if(board[i][j] == '.') continue;

                rowMp[i].insert(board[i][j]);
                colMp[j].insert(board[i][j]);

                int a = i/3, b = j/3;
                subGridMp[{a, b}].insert(board[i][j]);
            }
        }

        solve(board, 0, 0);

        return;
    }
};
```

---

## 4. Expression Add Operators
[Leetcode link](https://leetcode.com/problems/expression-add-operators/description/)

Given a string num that contains only digits and an integer target, return all possibilities to 
insert the binary operators '+', '-', and/or '*' between the digits of num so that the resultant 
expression evaluates to the target value.

Note that operands in the returned expressions should not contain leading zeros.

Note that a number can contain multiple digits.

 

Example 1:
- Input: num = "123", target = 6
- Output: ["1*2*3","1+2+3"]

Example 2:
- Input: num = "232", target = 8
- Output: ["2*3+2","2+3*2"]

### Intuition
> [!IMPORTANT]
> generate all Possible permutations
>
>           Check if it evaluates to target
>           time would be (n*n!)
>
> Can we compute the expression on the go?
>
>           If we just add '+' or '-', we can compute on go
>           If we add '*' then we need the previous operand as well
>
>           lets say currValue is evaluated value till index idx
>           and prevOperand is the number we used last time
>
>           Now we have currNum and before it we need to put one sign + - or *
>
>            for + : currValue + currNum, and prevOperand becomes currNum
>            for - : currValue - currNum, and prevOperand becomes -currNum
>
>            for * : currValue - prevOperand + (prevOperand*currNum), prevOperand becoms = prevOperand*currNum
>
>            this way time complexity reduces to O(n!)

```cpp
class Solution {
public:
    vector<string> ans;
    long long target;

    void solve(string &num, int idx, long long currValue, long long prevOperand, string temp) {
        if(idx == num.size()) {
            if(currValue == target)
                ans.push_back(temp);
            return;
        }

        long long currNum = 0;
        for(int i=idx; i<num.size(); i++) {
            if(i > idx && num[idx] == '0') break; // we dont want 09 -- leading zeros

            currNum = currNum*10 + (num[i]-'0');
            string currStr = num.substr(idx, i-idx+1);

            if(idx == 0) {
                solve(num, i+1, currNum, currNum, currStr);
            }

            else {
                solve(num, i+1, currValue+currNum, currNum, temp+"+"+currStr);

                solve(num, i+1, currValue-currNum, -currNum, temp+"-"+currStr);

                solve(num, i+1, currValue-prevOperand+(prevOperand*currNum), prevOperand*currNum, temp+"*"+currStr);
            }
        }
    }

    vector<string> addOperators(string num, int target) {
        /*
            target, prevDigit, idx
        */
        this->target = target;

        solve(num, 0, 0, 0, "");

        return ans;
    }
};
```




