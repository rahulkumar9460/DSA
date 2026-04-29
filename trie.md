# What is a Trie

A Trie (prefix tree) is just a tree where:

- Each edge = a character
- Each path from root = a prefix or full word

Instead of storing:
> ["apple", "app", "bat"]

We store shared prefixes:

```mermaid
graph TD 
    root((root)) 

    root --> a[a] 
    root --> b[b]

    a --> p1[p] 
    p1 --> p2[p] 

    p2 --> l[l] 
    p2 --> end1((end)) 

    l --> e[e] 
    e --> end2((end)) 
    
    b --> a2[a] 
    a2 --> t[t] 
    t --> end3((end))
```