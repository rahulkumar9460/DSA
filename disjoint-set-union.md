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
>           if redundantEdges < (connected_components - 1) ==> return -1 ==> not possible
>           else return (connected_components - 1)

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

