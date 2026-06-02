
1D DP           	    Very High
Grid DP         	    High
Knapsack / Take Skip	Very High
String DP	            High
LIS style           	High
Interval DP	            Medium
Tree DP	                Medium
Digit DP	            Medium
Bitmask DP	            Medium
State Machine DP	    Very High


1. Take / Skip DP (Knapsack Family)

State:

dp(i, remain) or dp(i)

Used when each item can be chosen or skipped.

Must Solve:
LeetCode 198 House Robber
213 House Robber II
740 Delete and Earn
416 Partition Equal Subset Sum
494 Target Sum
322 Coin Change
518 Coin Change II
1049 Last Stone Weight II
Interview Variants:
Pick non-adjacent max reward
Partition array into equal sum
Min coins to make X



2. Prefix / 1D Linear DP

State:

dp[i] = answer till i

Must Solve:
70 Climbing Stairs
746 Min Cost Climbing Stairs
91 Decode Ways
983 Minimum Cost For Tickets
139 Word Break
Asked as:
Ways to reach nth step
Min cost to cover travel days
Decode strings


3. String DP

Very common in Google.

State often:

dp(i,j) substring
or
dp(i1,i2) compare two strings

Must Solve:
1143 Longest Common Subsequence
72 Edit Distance
115 Distinct Subsequences
516 Longest Palindromic Subsequence
647 Palindromic Substrings
131 Palindrome Partitioning
Recognition:

Two strings / transformations / subsequences / palindromes.



4. Grid DP

State = row,col

Must Solve:
62 Unique Paths
63 Unique Paths II
64 Minimum Path Sum
221 Maximal Square
931 Minimum Falling Path Sum
Variants:
robot moves
min cost route
count ways with obstacles


5. LIS / Sequence DP

State = ending at i

Must Solve:
300 Longest Increasing Subsequence
673 Number of LIS
354 Russian Doll Envelopes
368 Largest Divisible Subset

Huge in L4 interviews.


6. Interval DP

When answer depends on subarray [l...r]

Must Solve:
312 Burst Balloons
516 Longest Palindromic Subsequence
1039 Minimum Score Triangulation
1547 Minimum Cost to Cut Stick
Recognition:

Need to choose last split / first split inside range.


7. State Machine DP (VERY IMPORTANT)

Used in stocks, cooldown, transactions.

Must Solve:
121 Best Time to Buy/Sell Stock
122 Stock II
309 Stock with Cooldown
714 Stock with Fee
188 Stock IV
Interview Gold:

Explain as:

hold / sold / cooldown states.

8. Tree DP

DFS + return values.

Must Solve:
337 House Robber III
124 Binary Tree Max Path Sum
543 Diameter of Binary Tree
968 Binary Tree Cameras

9. Digit DP (Google Likes)

When asked count numbers in range satisfying property.

Must Solve:
Count numbers with no repeated digits
Count numbers with digit sum K
Numbers <= N without consecutive ones

If you want I can give exact LC list.


LIS ⭐⭐⭐⭐⭐
Variants
Number of LIS
Largest Divisible Subset
Longest String Chain
Longest Arithmetic Subsequence
Minimum Removals to Make Mountain Array

LCS
Longest Common Subsequence ⭐⭐⭐⭐⭐
Direct Variants
Uncrossed Lines
Delete Operation for Two Strings
Minimum ASCII Delete Sum
Shortest Common Supersequence

Edit Distance ⭐⭐⭐⭐⭐
Related
Delete Operation
Minimum ASCII Delete Sum

take or skip
Distinct Subsequences ⭐⭐⭐⭐⭐
Related
Arithmetic Slices II

palindrome
Longest Palindromic Subsequence ⭐⭐⭐⭐⭐
Variants
Minimum Insertions to Make Palindrome
Maximize Palindrome Length

Counting Subsequences Family (Advanced)
Number of LIS
Arithmetic Slices II

