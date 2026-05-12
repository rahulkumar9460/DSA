# Disjoint Set Union


> DSU = Disjoint Set Union
>
> Used when you need to maintain groups/components dynamically.

Supports:

- find(x) → which group does x belong to?
- union(x,y) → merge groups of x and y

Optimized with:

- Path Compression
- Union by Rank / Size

> Time complexity ≈ O(α(n)) (almost constant)

---

## How to IDENTIFY DSU Pattern in Interviews

A.  Connectivity Problems
-   Are A and B connected?
-   Number of connected components
-   Merge nodes after operations

Example:
1. Number of Provinces
2. Redundant Connection
3. Graph Valid Tree

B. Dynamic Grouping
-   People become friends
-   Accounts merged
-   Emails connected
-   Islands formed after adding land

C. Grid Problems with Components
- Cells connected up/down/left/right.
>   Convert (r,c) to node id and union neighbors.

---

## template
```cpp
class DSU {
public:
    vector<int> parent, size, rankv;

    DSU(int n) {
        parent.resize(n);
        size.resize(n, 1);
        rankv.resize(n, 0);

        for (int i = 0; i < n; i++)
            parent[i] = i;
    }

    int find(int x) {
        if (parent[x] == x) return x;
        return parent[x] = find(parent[x]); // Path compression
    }

    // Union by Size
    bool uniteBySize(int a, int b) {
        a = find(a);
        b = find(b);

        if (a == b) return false;

        if (size[a] < size[b]) swap(a, b);

        parent[b] = a;
        size[a] += size[b];

        return true;
    }

    // Union by Rank
    bool uniteByRank(int a, int b) {
        a = find(a);
        b = find(b);

        if (a == b) return false;

        if (rankv[a] < rankv[b]) {
            parent[a] = b;
        }
        else if (rankv[b] < rankv[a]) {
            parent[b] = a;
        }
        else {
            parent[b] = a;
            rankv[a]++;
        }

        return true;
    }
};
```
> [!NOTE]
> Union by size
>
>           Use the number of nodes in the component.
>           Attach smaller component under larger component.

```cpp
if(size[a] < size[b]) swap(a,b);
parent[b] = a;
size[a] += size[b];
```

### Meaning:
If:
-   component A has 10 nodes
-   component B has 3 nodes

>   Then attach B under A.
Benefit:
-   You always know component sizes.

---

> [!NOTE]
> Union by Rank
>
>           Use an estimate of tree height/depth.
>           Attach shallower tree under deeper tree.

```cpp
if(rank[a] < rank[b]) parent[a] = b;
else if(rank[b] < rank[a]) parent[b] = a;
else {
    parent[b] = a;
    rank[a]++;
}
```

### Meaning:
If:
-   Tree A height = 3
-   Tree B height = 1

>   Attach B under A.
-   Rank is not node count.
-   It approximates maximum depth.

---

## Problems:
1. LeetCode 547 — Number of Provinces
2. 684 — Redundant Connection
3. 323 — Number of Connected Components in Undirected Graph
4. 1319 — Number of Operations to Make Network Connected
5. 721 — Accounts Merge

6. 947 — Most Stones Removed with Same Row or Column
7. 1202 — Smallest String With Swaps
8. 1061 — Lexicographically Smallest Equivalent String
9. 990 — Satisfiability of Equality Equatio

10. 305 — Number of Islands II
11. 1579 — Remove Max Number of Edges to Keep Graph Fully Traversable
12. 1489 — Find Critical and Pseudo-Critical Edges in MST

13. 952 — Largest Component Size by Common Factor
14. 839 — Similar String Groups
15. 1627 — Graph Connectivity With Threshold

---

## 1. Number of Provinces
[Leetcode link](https://leetcode.com/problems/number-of-provinces/description/)

You are given an n x n matrix isConnected where isConnected[i][j] = 1 if the ith city 
and the jth city are directly connected, and isConnected[i][j] = 0 otherwise.

> Return the total number of provinces.

### Intuition
> [!IMPORTANT]
> After forming DSU
>
> Nodes in same group have same ultimate-parent
>
> for every node get its ultimate-parent
> 
> Number of ultimate-parents is the answer

```cpp
class DSU {
    vector<int> parent, size;

public:
    DSU(int n) {
        parent.resize(n);
        size.resize(n,1);
        for(int i=0;i<n;i++) parent[i]=i;
    }

    int find(int x) {
        if(parent[x]==x) return x;
        return parent[x]=find(parent[x]);
    }

    void unite(int a,int b) {
        a=find(a);
        b=find(b);

        if(a==b) return;

        if(size[a]<size[b]) swap(a,b);

        parent[b]=a;
        size[a]+=size[b];
    }
};

class Solution {
public:
    int findCircleNum(vector<vector<int>>& isConnected) {
        int n=isConnected.size();
        DSU dsu(n);

        for(int i=0;i<n;i++) {
            for(int j=0;j<n;j++) {
                if(isConnected[i][j]) dsu.unite(i,j);
            }
        }

        unordered_set<int> st;
        for(int i=0;i<n;i++) st.insert(dsu.find(i));

        return st.size();
    }
};
```

---

## 2. Redundant Connection
[Leetcode link](https://leetcode.com/problems/redundant-connection/description/)

A graph started as a tree, and now there is extra one edge added which is different
from existing edges

>   return the redundant edge that can be removed and we get the tree again.

### Intuition
> [!IMPORTANT]
> Form the DSU
>
> If while forming dsu, for some edge(x, y), find(x) == find(y)
> - which means x and y are already connected
> - and this edge is redundant

```cpp
vector<int> findRedundantConnection(vector<vector<int>>& edges) {
    int n = edges.size();
    DSU* dsu = new DSU(n);

    for(int i=0; i<n; i++) {
        int x = edges[i][0]-1;
        int y = edges[i][1]-1;

        if(dsu->find(x) == dsu->find(y)) return edges[i];
        else dsu->unite(x, y);
    }

    return {};
}
```

---

## 3. Number of Operations to Make Network Connected
[Leetcode link](https://leetcode.com/problems/number-of-operations-to-make-network-connected/description/)

You are given an initial computer network connections. You can extract certain cables between two 
directly connected computers, and place them between any pair of disconnected computers to make them directly connected.

### Intuition
> [!IMPORTANT]
> We need (connected_components - 1) number of cabels
>   - While forming DSU
>       -   If find(i) == find(j) ==> we have redundant edge
>       -   else connComp--
>
>
>               if redundantEdges < (connected_components - 1) ==> return -1 ==> not possible
>
>               else return (connected_components - 1)

```cpp
int makeConnected(int n, vector<vector<int>>& connections) {
    DSU* dsu = new DSU(n);

    int connComp = n;
    int redundantEdges = 0;

    for(auto &conn: connections) {
        int i = conn[0], j = conn[1];

        if(dsu->find(i) == dsu->find(j)) redundantEdges++;
        else {
            dsu->unite(i, j);
            connComp--;
        }
    }

    int required = connComp-1;
    if(redundantEdges < required) return -1;
    return required;
}
```

---

## 4. Accounts Merge
[Leetcode link](https://leetcode.com/problems/accounts-merge/description/)
Given a list of accounts where each element accounts[i] is a list of strings, where the first element 
accounts[i][0] is a name, and the rest of the elements are emails representing emails of the account.

Now, we would like to merge these accounts. Two accounts definitely belong to the same person if there 
is some common email to both accounts. Note that even if two accounts have the same name, they may belong 
to different people as people could have the same name. A person can have any number of accounts initially, 
but all of their accounts definitely have the same name.

Input: accounts = 
[
    ["John","johnsmith@mail.com","john_newyork@mail.com"],
    ["John","johnsmith@mail.com","john00@mail.com"],
    ["Mary","mary@mail.com"],
    ["John","johnnybravo@mail.com"]
]

Output: 
[
    ["John","john00@mail.com","john_newyork@mail.com","johnsmith@mail.com"],
    ["Mary","mary@mail.com"],["John","johnnybravo@mail.com"]
]

### Intuition
> [!IMPORTANT]
> We need to unite indicies that have common emails
>
> Use DSU to unite the indicies
>
> maintain a map<email, ultimate_parent_index> ulp
>
>               IF a email 'e' is already seen in ulp map that means mp['e'] and current index need to be united

```cpp
class DSU {
public:
    vector<int> parent, size;
    DSU(int n) {
        parent.resize(n);
        size.resize(n, 1);
        for(int i=0; i<n; i++) parent[i] = i;
    }

    int find(int x) {
        if(parent[x] == x)return x;
        return parent[x] = find(parent[x]);
    }

    void unite(int a, int b) {
        a = find(a);
        b = find(b);
        if(a == b) return;

        if(size[a] < size[b]) swap(a, b);
        parent[b] = a;
        size[a] += size[b];
    }
};

class Solution {
public:
    vector<vector<string>> accountsMerge(vector<vector<string>>& accounts) {
        int n = accounts.size();
        DSU* dsu = new DSU(n);

        unordered_map<string, int> ulp; // stores the parent index for email

        for(int i=0; i<accounts.size(); i++) {
            for(int j=1; j<accounts[i].size(); j++) {
                if(ulp.find(accounts[i][j]) != ulp.end()) { // email is already seen
                    int p = ulp[accounts[i][j]];
                    dsu->unite(p, i); // unite current account and parent account
                }
            }

            for(int j=1; j<accounts[i].size(); j++) {
                ulp[accounts[i][j]] = dsu->find(i); // insert emails in map
            }
        }

        unordered_map<int, set<string>> mp;
        for(int i=0; i<n; i++) {
            int p = dsu->find(i);
            for(int j=1; j<accounts[i].size(); j++)
                mp[p].insert(accounts[i][j]);
        }

        vector<vector<string>> ans;
        for(auto &[idx, emails] : mp) {
            string name = accounts[idx][0];
            vector<string> v;
            v.push_back(name);

            for(string email : emails) v.push_back(email);
            ans.push_back(v);
        }

        return ans;
    }
};
```

---

## 5. Most Stones Removed with Same Row or Column
[Leetcode link](https://leetcode.com/problems/most-stones-removed-with-same-row-or-column/description/)

On a 2D plane, we place n stones at some integer coordinate points. Each coordinate point may have at most one stone.

A stone can be removed if it shares either the same row or the same column as another stone that has not been removed.

Given an array stones of length n where stones[i] = [xi, yi] represents the location of the ith stone, 
return the largest possible number of stones that can be removed.

### Intuition
> [!IMPORTANT]
>
>               All stones that shares column or row, make one connected-component
>               In each component we can remove (k-1) stones where k is the size of component
>
> We just need to make a graph and find number of stones in each component
>
> We are Given location of stones: [[0,0],[0,2],[1,1],[2,0],[2,2]]
> - we need to unite indicies in above stones array
>
>               Have two maps -- rowMp and colMp which stores the ultimate-parent-index of a row or col
>               for a stone at index i = (a, b) find there ultimate parent from rowMp[a] and rowMp[b]
>               - Now unite:
>                   - unite(i, rowMp[a])
>                   - unite(i, colMp[b])

```cpp
int removeStones(vector<vector<int>>& stones) {
    // stones make a connected componenet
    // in a connected componenet of size k, we can remove k-1 stones
    
    int n = stones.size();
    DSU* dsu = new DSU(n);

    unordered_map<int, int> rowMp, colMp;
    
    for(int i=0; i<n; i++) {
        if(rowMp.find(stones[i][0]) != rowMp.end()) {
            int p = rowMp[stones[i][0]];
            dsu->unite(i, p);
        }

        if(colMp.find(stones[i][1]) != colMp.end()) {
            int p = colMp[stones[i][1]];
            dsu->unite(i, p);
        }

        rowMp[stones[i][0]] = dsu->find(i);
        colMp[stones[i][1]] = dsu->find(i);
    }

    unordered_set<int> comp;
    for(int i=0; i<n; i++) comp.insert(dsu->find(i));

    int ans = 0;
    for(int c : comp) ans += dsu->size[c]-1;

    return ans;
}
```

--- 

## 6. Smallest String With Swaps
[Leetcode link](https://leetcode.com/problems/smallest-string-with-swaps/description/)

You are given a string s, and an array of pairs of indices in the string pairs 
where pairs[i] = [a, b] indicates 2 indices(0-indexed) of the string.

> You can swap the characters at any pair of indices in the given pairs any number of times.
> Return the lexicographically smallest string that s can be changed to after using the swaps.

Input: s = "dcab", pairs = [[0,3],[1,2],[0,2]]
Output: "abcd"

Explaination: 
- Swap s[0] and s[3], s = "bcad"
- Swap s[0] and s[2], s = "acbd"
- Swap s[1] and s[2], s = "abcd"

### Intuition
> [!IMPORTANT]
> - All pairs that shares index can become connected component
> - For Example (0, 1), (1, 3)
> - 0 can be swapped with 1 and 1 can be swapped with 3
> - so (0, 1, 3) can be swapped with each other 
>
> so we can become greedy and sort all the charecters in given component
> 
>                   Group together all pairs that shares same index
>                   For each components get all indicies in sorted order (a, b, c, d, ...)
>                   get all chars at thoses indicies in sorted order
>                   Assign smallest character to smallest index and so on
>                   Keep doing it for all components

```cpp
string smallestStringWithSwaps(string s, vector<vector<int>>& pairs) {
    int n = pairs.size();
    DSU dsu(n);

    unordered_map<int, int> mp;
    for(int i=0; i<n; i++) {
        if(mp.count(pairs[i][0])) dsu.unite(i, mp[pairs[i][0]]);
        if(mp.count(pairs[i][1])) dsu.unite(i, mp[pairs[i][1]]);

        mp[pairs[i][0]] = dsu.find(i);
        mp[pairs[i][1]] = dsu.find(i);
    }

    unordered_map<int, set<int>> comps; // find all indicies in each components in sorted order
    for(int i=0; i<n; i++) {
        int p = dsu.find(i);
        comps[p].insert(pairs[i][0]);
        comps[p].insert(pairs[i][1]);
    }

    for(auto &[idx, comp] : comps) {
        vector<char> v;
        for(int i: comp) v.push_back(s[i]); // find all characters at each indicies in components
        sort(v.begin(), v.end()); // sort the characters

        int j = 0;
        for(int i: comp) s[i] = v[j++];
    }

    return s;
}
```

---

## 7. Lexicographically Smallest Equivalent String
[Leetcode link](https://leetcode.com/problems/lexicographically-smallest-equivalent-string/description/)

We say s1[i] and s2[i] are equivalent characters.

For example, if s1 = "abc" and s2 = "cde", 
> then we have 'a' == 'c', 'b' == 'd', and 'c' == 'e'.
>
> Find lexicographically smallest equivalent string of baseStr

```

```
Input: 
-   s1 = "parker", s2 = "morris", baseStr = "parser"
    
Output: 
-   "makkek"

Explanation: 
> Based on the equivalency information in s1 and s2, 
>
> we can group their characters as [m,p], [a,o], [k,r,s], [e,i].

The characters in each group are equivalent and sorted in lexicographical order.
- So the answer is "makkek".

### Intuition
> [!IMPORTANT]
> We need to unite characters from strings s1 and s2 at the same indicies
>  - But need to make sure we have smallest characters as the parent
>  - Now for each char of baseStr just do 
>
>                   baseStr[i] = dsu->find(baseStr[i])

```cpp
class DSU {
public:
    unordered_map<char, char> parent;
    DSU() {
        for(char c='a'; c<='z'; c++) {
            parent[c] = c;
        }
    }

    char find(char x) {
        if(parent[x] == x) return x;
        return parent[x] = find(parent[x]);
    }

    void unite(char a, char b) {
        a = find(a);
        b = find(b);
        if(a == b)return;

        if(a > b) swap(a, b); // Keep the smallest char as parent
        parent[b] = a;
    }
};
class Solution {
public:
    string smallestEquivalentString(string s1, string s2, string baseStr) {
        DSU dsu;

        int n = s1.size(), m = baseStr.size();
        for(int i=0; i<n; i++) dsu.unite(s1[i], s2[i]);

        for(int i=0; i<m; i++) baseStr[i] = dsu.find(baseStr[i]); // find smallest equivalent character

        return baseStr;
    }
};
```

---

## 8. Satisfiability of Equality Equations
[Leetcode link](https://leetcode.com/problems/satisfiability-of-equality-equations/description/)

You are given an array of strings equations that represent relationships between variables where
each string equations[i] is of length 4 and takes one of two different forms: 
> "xi==yi" or "xi!=yi".
> Here, xi and yi are lowercase letters (not necessarily different) that represent one-letter variable names.
> 
> Return true if it is possible to assign integers to variable names so as to satisfy all the given equations, or false otherwise.

 
Input: 
- equations = ["a==b","b!=a"]

Output: 
- false

> Explanation: If we assign say, a = 1 and b = 1, then the first equation is satisfied, but not the second.
>
> There is no way to assign the variables to satisfy both equations.

### Intuition
> [!IMPORTANT]
> Equations containing '==' belongs to same component
>
> Elemenets present in '!=" equation must not be in same conponent
>
>               First make componenet with all '==' equation
>               Now for all '!=' equations check elements are not in same component
>

```cpp
bool equationsPossible(vector<string>& equations) {
    DSU dsu;

    // First pass: all == equations
    for(string & eq: equations) {
        int a = eq[0] - 'a';
        int b = eq[3] - 'a';

        if(eq[1] == '=') {
            dsu.unite(a, b); // unite them into same component
        }
    }

    // Second pass: all != equations
    for(string &eq : equations) {
        int a = eq[0] - 'a';
        int b = eq[3] - 'a';

        if(eq[1] == '!') {
            if(dsu.find(a) == dsu.find(b)) // a and b must not be in same component
                return false;
        }
    }
    
    return true;
}
```

---

## 9. Number Of Islands II
[GFG link](https://www.geeksforgeeks.org/problems/number-of-islands/1)

You are given a n,m which means the row and column of the 2D matrix and an array of  size k 
denoting the number of operations. Matrix elements is 0 if there is water or 1 if there is land. 

> Originally, the 2D matrix is all 0 which means there is no land in the matrix. 

The array has k operator(s) and each operator has two integer A[i][0], A[i][1] means that you can 
> change the cell matrix[A[i][0]][A[i][1]] from sea to island. 

Return how many island are there in 
the matrix after each operation.You need to return an array of size k.

```
Input: n = 4
m = 5
k = 4
A = {{1,1},{0,1},{3,3},{3,4}}

Output: 1 1 2 2
Explanation:
0.  00000
    00000
    00000
    00000
1.  00000
    01000
    00000
    00000
2.  01000
    01000
    00000
    00000
3.  01000
    01000
    00000
    00010
4.  01000
    01000
    00000
    00011
```

### Intuition
> [!IMPORTANT]
> Intially number of connected-components is count=0
>
> At each operation count++
>
> At each operation, check in all directions if there is land, 
>   - if yes connect them and count--
>
> We have 2D grid, to maintain parent information 
>   1. Use i + "-" + j
>   2. Use i*m + j, where m = number of columns
>
> Option 2 is much simpler, we can just make parent and size as 1D vector

```cpp
vector<vector<int>> dir = {{1, 0}, {0, 1}, {-1, 0}, {0, -1}};

vector<int> numOfIslands(int n, int m, vector<vector<int>> &operations){
    vector<vector<int>> island(n, vector<int>(m, 0));
    DSU dsu(n*m + m);
    
    vector<int> ans;
    int count = 0;

    for(auto &op: operations) {
        int a = op[0], b = op[1];
        if(island[a][b] == 1) {
            ans.push_back(count);
            continue;
        }

        island[a][b] = 1;
        count++;  // increase number of component
        int node = a*m + b;

        for(auto &d : dir) {
            int x = a + d[0];
            int y = b + d[1];
            if(x<0 || y <0 || x>=n || y>=m || island[x][y] == 0) continue;
            
            int node2 = x*m + y;
            if(dsu.find(node) != dsu.find(node2)) { 
                dsu.unite(node, node2);
                count--; // meaning adding this land does not lead to new component
            }
        }

        ans.push_back(count);
    }

    return ans;
}
```

---

## 10. Remove Max Number of Edges to Keep Graph Fully Traversable
[Leetcode link](https://leetcode.com/problems/remove-max-number-of-edges-to-keep-graph-fully-traversable/description/)

Alice and Bob have an undirected graph of n nodes and three types of edges:

1. Type 1: Can be traversed by Alice only.
2. Type 2: Can be traversed by Bob only.
3. Type 3: Can be traversed by both Alice and Bob.

> Return the maximum number of edges you can remove, or return -1 if Alice and Bob cannot fully traverse the graph

### Intuition
> [!IMPORTANT]
> Start making graph from given edges
> 
> First check the type3 edges if they can be removed = redundantEdges
>
> Now Build graph for Alice, and count the extra edges = redundantEdges1
> - check if connected-components == 1, otherwise return -1
>
> Node build graph for Bob, and count extra edges = redundantEdges2
> - check if connected-components == 1, otherwise return -1
>
>                       Use DSU and first traverse type-3 edges and get extra edges = redundantEdges
>                       Clone DSU to two DSUs dsu1 and dsu2
>                       Get redundant edges for both Alice and Bob using clone DSUs

```cpp
int helper(vector<vector<int>>& edges, DSU &dsu, int comp, int player) {
    int count = 0;
    for(auto &edge: edges) {
        if(edge[0] == player) {
            int a = edge[1]-1, b = edge[2]-1;
            if(dsu.find(a) == dsu.find(b)) count++;
            else {
                dsu.unite(a, b);
                comp--;
            }
        }
    }

    if(comp != 1) return -1;
    return count;
}

int maxNumEdgesToRemove(int n, vector<vector<int>>& edges) {
    DSU dsu(n);

    int comp = n;
    int redundantEdges = 0;
    for(auto &edge : edges) {
        if(edge[0] == 3) {
            int a = edge[1]-1, b = edge[2]-1;
            if(dsu.find(a) == dsu.find(b)) redundantEdges++;
            else {
                dsu.unite(a, b);
                comp--;
            }
        }
    }

    DSU dsu1 = dsu;
    int redundantEdges1 = helper(edges, dsu1, comp, 1);
    if(redundantEdges1 == -1) return -1;

    DSU dsu2 = dsu;
    int redundantEdges2 = helper(edges, dsu2, comp, 2);
    if(redundantEdges2 == -1) return -1;

    return redundantEdges + redundantEdges1 + redundantEdges2;
}
```

---

## 11. Find Critical and Pseudo-Critical Edges in Minimum Spanning Tree
[Leetcode link](https://leetcode.com/problems/find-critical-and-pseudo-critical-edges-in-minimum-spanning-tree/description/)


---

## 12. Largest Component Size by Common Factor
[Leetcode link](https://leetcode.com/problems/largest-component-size-by-common-factor/description/)

You are given an integer array of unique positive integers nums. Consider the following graph:

> There are nums.length nodes, labeled nums[0] to nums[nums.length - 1],
> There is an undirected edge between nums[i] and nums[j] if nums[i] and nums[j] share a common factor greater than 1.
> Return the size of the largest connected component in the graph.

### Intuition
> [!IMPORTANT]
> Two pairs have common factor if their gcd is > 1
>
> If we find gcd for all pairs, this solution will give TLE
>
> Instead compute all prime-fators for all numbers
> - and store <prime-factor, index> into a map
> - This next time if we see same prime factor then get it's parent index from map and unite them

```cpp
vector<int> primeFactors(int n) {
    vector<int> ans;

    for(int x=2; x*x<=n; x++) {
        if(n%x == 0) {
            ans.push_back(x);

            while(n%x == 0) n = n/x;
        }
    }
    if(n>1) ans.push_back(n); // important, DON't FORGET

    return ans;
}

int largestComponentSize(vector<int>& nums) {
    int n = nums.size();
    DSU dsu(n);
    unordered_map<int, int> parent;

    for(int i=0; i<n; i++) {
        vector<int> factors = primeFactors(nums[i]);

        for(int &f : factors) {
            if(parent.count(f)) dsu.unite(i, parent[f]); // unite current index and factor's parent index
            else parent[f] = i; // else make current index as parent
        }
    }

    int ans = 0;
    for(int i=0; i<n; i++)
        ans = max(ans, dsu.size[dsu.find(i)]); // get max size
    
    return ans;
}
```

---

## 13. Similar String Groups
[Leetcode link](https://leetcode.com/problems/similar-string-groups/description/)

Two strings, X and Y, are considered similar if either they are identical or we can make 
them equivalent by swapping at most two letters (in distinct positions) within the string X.

> We are given a list strs of strings where every string in strs is an anagram of every other string in strs.
> How many groups are there?

Input: 
- strs = ["tars","rats","arts","star"]

Output: 
- 2

Explanation:
- Group1 = ["tars","rats","arts"], Group2 = ["star"]

```
Constraints:

    1 <= strs.length <= 300
    1 <= strs[i].length <= 300
    strs[i] consists of lowercase letters only.
    All words in strs have the same length and are anagrams of each other.
```

### Intuition
> [!IMPORTANT]
> Since given constraints are small: 
> - So compare each string with every other string and see if they are similar
> - If two strings are similar group them together using DSU data structure
> 
>               Finally return the number of connected components

```cpp
bool areSimilar(string &s1, string &s2) {
    int count = 0;
    for(int i=0; i<s1.size(); i++) {
        if(s1[i] != s2[i]) count++;
        if(count > 2) return false;
    }

    return true;
}

int numSimilarGroups(vector<string>& strs) {
    int n = strs.size();
    DSU dsu(n);

    int count = n;
    for(int i=0; i<n; i++) {
        for(int j=i+1; j<n; j++) {
            if(!areSimilar(strs[i], strs[j])) continue;

            if(dsu.find(i) == dsu.find(j)) continue;
            else {
                dsu.unite(i, j);
                count--;
            }
        }
    }

    return count;
}
```

---

## 14. Graph Connectivity With Threshold
[Leetcode link](https://leetcode.com/problems/graph-connectivity-with-threshold/description/)

Two numbers are connected if they share a common factor > Threshold

Given list of queries(a, b), return true if a and b are connected

Input: 
- n = 6, threshold = 2, queries = [[1,4],[2,5],[3,6]]

Output: 
- [false,false,true]

Explanation: 
- The divisors for each number:
        1:   1
        2:   1, 2
        3:   1, 3
        4:   1, 2, 4
        5:   1, 5
        6:   1, 2, 3, 6

Using the underlined divisors above the threshold, only cities 3 and 6 share a common divisor, so they are the
only ones directly connected. The result of each query:

- [1,4]   1 is not connected to 4
- [2,5]   2 is not connected to 5
- [3,6]   3 is connected to 6 through path 3--6

### Intuition
> [!IMPORTANT]
> If we try to solve it by using all prime factors and see if two numbers share common prime factors
> - We might miss higher valid common factor(might not be prime factor) which is greater than Threshold
> - Example
>   
>                   9 and 18, threshold is 5
>                   30 and 18 share common prime factor 2, 3 but since it is less than threshold
>                       we do not consider it 
>                       And we dont create a Edge between them
>
>                   But they share 6 as common factor > threshold
>                   So there must be a edge between them
>
> - instead of using prime factors
>                   Unite d and all multiple of d's (2d, 3d, 4d, 5d....) together
>                   Keep doing it for all (threshold < d <= n)


```cpp
vector<bool> areConnected(int n, int threshold, vector<vector<int>>& queries) {
    DSU dsu(n);
    unordered_map<int, int> parent;

    for(int d=threshold+1; d<=n; d++) {
        // get multiple of d => 2d, 3d, 4d, 5d
        for(int i=2; i*d<=n; i++) {
            int multiple = i*d;
            dsu.unite(d-1, multiple-1);
        }
    }
    
    vector<bool> ans;
    for(auto &q : queries) {
        ans.push_back(dsu.find(q[0]-1) == dsu.find(q[1]-1));
    }

    return ans;
}
```

