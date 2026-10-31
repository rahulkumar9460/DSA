# Google DSA Interview Questions (Jan 2026 – Oct 2026)
Source: LeetCode Discuss (tags: Google, L3 Google, L4 Google). Base URL for all links: https://leetcode.com

---

## January 2026
- SDE-3, India, rejected (Jan 25): special-string question (covered in an earlier answer).
- L4 offer post (Jan 24): compensation only, no question. https://leetcode.com/discuss/post/7520006/google-l4-swe-3-india-35-yoe-by-anonymou-8pvq/

## February 2026
1. **L3 onsite – GPS interpolation (Feb 2)** – https://leetcode.com/discuss/post/7544550/google-l3-onsite-delayed-interview-once-592ok/
   - Statement: Given sorted ground-truth checkpoints (time, position) and sorted noisy samples, for each sample find the surrounding checkpoints, interpolate the expected position, take the distance error and sum the errors.
   - Example: checkpoints (t=0,x=0) and (t=10,x=10); sample (t=5,x=6) -> expected 5, error 1.
   - Approach: two pointers, O(n+m). Edge cases: sample on a checkpoint, zero time gap, sample outside range.
   - Similar: 986 Interval List Intersections, 88 Merge Sorted Array.
2. **In-person onsite (Feb 10)** – https://leetcode.com/discuss/post/7567827/interview-experience-sharing-first-in-pe-oyg1/
   - R1: variant of 60 Permutation Sequence – https://leetcode.com/problems/permutation-sequence/
   - R2: sorted string; return first and last position of characters appearing more than twice (e.g. "aaabccc" -> a:(0,2), c:(4,6)). Follow-up: dictionary matching after removing repeats.
   - Similar: 80 Remove Duplicates from Sorted Array II, 269 Alien Dictionary (commenter's guess).
3. **Web Solutions Engineer L3 (Feb 26)** – https://leetcode.com/discuss/post/7611418/google-web-solutions-engineer-l3-by-anon-fo7v/
   - CSV parser (quotes, embedded commas); no LC match. Example: `a,"b,c",d` -> [a, "b,c", d].
   - Aliens on planets, pairs from different planets -> 2316 Count Unreachable Pairs of Nodes.
   - Valid tree from parent array -> 1361 Validate Binary Tree Nodes / 261 Graph Valid Tree.
   - Also SQL and a product-design question.
4. **L5 Bangalore onsite (Feb 26)** – https://leetcode.com/discuss/post/7610148/google-l5-interview-experience-onsite-ba-mk1z/
   - Router broadcast reachability -> 1971 Find if Path Exists in Graph
   - First bad commit with overflow-safe midpoint -> 278 First Bad Version
   - Number of digit-string decodings -> 91 Decode Ways
   - Logger drops repeats within 10s -> 359 Logger Rate Limiter
   - System design: Inshorts.
5. Feb 8 L3 Bangalore (covered earlier).

## March 2026
1. **L4 phone screen + onsite (Mar 24)** – https://leetcode.com/discuss/post/7685567/google-interview-l4-by-anonymous_user-6s1j/
   - Prefix partition with `isValid(s)` API, minimize moves, two pointers -> 905 Sort Array By Parity, 283 Move Zeroes.
   - URL-shortener generator: `getNext()` returns unique lowercase strings, shortest first, no char frequency above T -> 1415 K-th Happy String, 1079 Letter Tile Possibilities.
   - LRU cache, then add expiry -> 146 LRU Cache, 981 Time Based Key-Value Store.
2. Mar 26 (L4 rejected) and Mar 27 (about 45-question compilation) were covered earlier.

## April 2026
- **L4 SDE-III (Apr 25)** – https://leetcode.com/discuss/post/8096071/google-sdeiii-interview-experience-by-an-67tm/
  - Split array into groups of 5 consecutive numbers -> 846 Hand of Straights (k=5)
  - Max-area rectangle from points -> 939 Minimum Area Rectangle (maximum variant)
  - Equal-time tasks on machines: earliest finish, then minimum CPUs (binary search on answer) -> 1011 Capacity to Ship Packages
- Apr 5 and Apr 22 posts contain no questions.

## May 2026
1. **L3 India (May 9)** – https://leetcode.com/discuss/post/8173874/google-interview-experience-2-rounds-swe-ku5d/
   - Statement: weighted graph; delete a node and its edges; return the max remaining edge weight.
   - Example: edges A-B=5, B-C=9, C-D=7; remove C -> answer 5.
   - Approach: keep top edges or precompute per node.
   - Similar: no exact match; 743 Network Delay Time for edge-list handling.
2. **L3 Bangalore (May 18)** – https://leetcode.com/discuss/post/8268786/google-l3-bangalore-india-interview-expe-dt1h/
   - Flights graph with arrival/departure times (BFS with visited states) -> 787 Cheapest Flights Within K Stops
   - Hidden-board rook problem: n x n board with n-1 rooks, API `countRook(a,b,c,d)`; find the empty row and column in few calls via binary search. Example: n=3, rooks at (0,1) and (1,0) -> empty row 2, empty column 2.
   - Similar: 1428 Leftmost Column with at Least a One.
3. **L4 Bangalore (May 19)** – https://leetcode.com/discuss/post/8276966/google-l4-banglore-by-anonymous_user-97gh/
   - Top K similar movies from a similarity graph -> 692 Top K Frequent Words
   - Ad rotator: `insertAd`, `getAd`, no consecutive repeats, then cooldown -> 358 Rearrange String k Distance Apart, 621 Task Scheduler
   - Lakes in a grid from a land cell (land inside lake twist) -> 1254 Number of Closed Islands
4. May 18 L4 (topological sort + LFU) covered earlier.

## June 2026
1. **L4 (Jun 7)** – https://leetcode.com/discuss/post/8319665/google-l4-interview-experience-bangalore-mwp1/
   - Kadane + hash-map follow-up -> 53 Maximum Subarray, 560 Subarray Sum Equals K
   - Hard geometry, N^3 -> N^2 with sets -> 939 Minimum Area Rectangle, 149 Max Points on a Line
   - Hard sliding window with precomputation follow-up (unnamed; 76 Minimum Window Substring is a guess)
2. **L4 (Jun 10)** – https://leetcode.com/discuss/post/8325811/google-l4-interview-experience-chances-b-3qmq/
   - Longest path in a BST with equal node values -> 687 Longest Univalue Path
   - CPU task scheduling plus number of CPUs for best time. Example: 4 tasks of length 2 on 2 CPUs -> time 4. Similar: 253 Meeting Rooms II, 621 Task Scheduler.
   - Max rectangle from points -> 939 / 963 Minimum Area Rectangle (I and II)
3. **L4 Bangalore onsite (Jun 24)** – https://leetcode.com/discuss/post/8355549/l4-google-banglore-experience-by-sonusin-z09y/
   - Topological sort on dependencies -> 210 Course Schedule II
   - Minimum flight travel time (Dijkstra) -> 743 Network Delay Time
   - LIS with difference 1 -> 1218; difference at most d -> 2407 Longest Increasing Subsequence II

## July 2026
No posts with coding questions found (team-match posts, a recruiter screen, requests for question lists).

## August 2026
- **L4 onsite (Aug 21)** – https://leetcode.com/discuss/post/8473279/google-swe-iii-onsite-experience-team-ma-9r4h/
  - An array question (unnamed)
  - Scenario-based multi-source BFS -> 542 01 Matrix, 994 Rotting Oranges

## September 2026 (new items)
- **L3 coding round (Sep 13)** – https://leetcode.com/discuss/post/8519361/l3-google-interview-experience-by-anonym-y2b5/
  - Statement: string of parentheses and digits; each digit deletes that many characters to its left; decide if some valid order of deletions gives a balanced string.
  - Approach: backtracking with memoization.
  - Similar: 301 Remove Invalid Parentheses, 1249 Minimum Remove to Make Valid Parentheses.
- **L3 India (Sep 18)** – https://leetcode.com/discuss/post/8527382/google-swe-ii-l3-interview-experience-in-hkeb/
  - LIS with difference 1 -> 1218
  - Minimum security level to reach a node -> 1631 Path With Minimum Effort
  - Minimum cost to cut all leaves of a tree

## October 2026
Nothing new beyond the hardware-timer post (Oct 24), covered earlier.

---

## Coverage gaps
- The Google tag feed stops loading at about 22 Sep 2026, so the L3 and L4 tags were used for earlier months. Posts without those tags may be missing.
- Some questions were described only in general terms (marked as unnamed or guessed).
- The last third of the Arshala Khan compilation and round 4 of the Sep 15 L4 post were not read.