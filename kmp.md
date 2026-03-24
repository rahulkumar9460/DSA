## 1. longest proper prefix suffix (KMP algorithm)

```
Given a string s, of lowercase english alphabets, find the length of the longest proper prefix which is also a suffix.
Note: Prefix and suffix can be overlapping but they should not be equal to the entire string.

Examples :

Input: s = "abab"
Output: 2
Explanation: The string "ab" is the longest prefix and suffix. 

Input: s = "aabcdaabc"
Output: 4
Explanation: The string "aabc" is the longest prefix and suffix.

Input: s = "aaaa"
Output: 3
Explanation: "aaa" is the longest prefix and suffix. 
```


***Don’t restart matching after mismatch → reuse previous matched info***

```
lps[i] = longest proper prefix == suffix for substring [0..i]
Intution: “How much can I reuse if mismatch happens here?”

When mismatch happens:
We don't go to 0
We go to "largest valid prefix that is also suffix"
```

```cpp
vector<int> buildLPS(string &p) {
    int n = p.size();
    vector<int> lps(n, 0);

    int len = 0; // len of proper prefix which is suffix
    int i = 1;

    while(i < n) {
        if(p[i] == p[len]) {
            len++;
            lps[i] = len;
            i++;
        } else {
            if(len != 0) {
                len = lps[len-1];
            } else {
                lps[i] = 0;
                i++;
            }
        }
    }
    return lps;
}
```

---

## 2. First Occurence of pattern string in text string

```
Given two strings txt and pat, return index of the first occurrence of string pat in txt. If pat is not found, return -1.

Examples :
Input: txt = "GeeksForGeeks", pat = "Fr"
Output: -1
Explanation: "Fr" is not present in the string "GeeksForGeeks" as substring.

Input: txt = "GeeksForGeeks", pat = "For"
Output: 5
Explanation: "For" is present as substring in "GeeksForGeeks" from index 5 (0 based indexing).
```

**Brute force:**
```
start i = 0, j = 0;

if txt[i] != pat[j] ==> make i = 1 and j = 0

instead of resetting i and j everytime mismatch occurs, can we do something better?
```

**Intuition:**
```
Make lps of pattern

Ex: 
txt = "abxabcabcaby"
pat = "abcaby"

lps of pat = {0, 0, 0, 1, 2, 0}

i → txt
j → pat

i=0, j=0 → a == a ✅
i=1, j=1 → b == b ✅
i=2, j=2 → x != c ❌

Mismatch at j = 2
Instead of restarting j = 0, we do: j = lps[j-1] = lps[1] = 0

We already matched "ab"
what part of "ab" we can reuse?

None... since for "ab" lps is 0
so j = 0;

i=2, j=0 → x != a → move i
i=3, j=0 → a == a ✅
i=4, j=1 → b == b ✅
i=5, j=2 → c == c ✅
i=6, j=3 → a == a ✅
i=7, j=4 → b == b ✅
i=8, j=5 → c != y ❌

We already matched "abcab"
lps = 2 which is "ab"

so no need to look for "ab" again in text
make j = lps[j-1] = 2
and starting matching from index = 2
.
.
.
```

```cpp
int firstOccurence(string& txt, string& pat) {
        vector<int> lps = buildLps(pat);
        
        int n = txt.size(), m = pat.size();
        int i = 0, j = 0;
        
        while(i < n) {
            if(txt[i] == pat[j]) {
                i++;
                j++;
            } else {
                if(j != 0) {
                    j = lps[j-1];
                } else {
                    i++;
                }
            }
            
            if(j == m) return i-m; 
        }
        
        return -1;
    }
```

---

## 3. Repeated Substring Pattern

```
Given a string s, check if it can be constructed by taking a substring of it and appending multiple copies of the substring together.

 
Example 1:
Input: s = "abab"
Output: true
Explanation: It is the substring "ab" twice.

Example 2:
Input: s = "aba"
Output: false

Example 3:
Input: s = "abcabcabcabc"
Output: true
Explanation: It is the substring "abc" four times or the substring "abcabc" twice.
```

**Approach:**
```
s = "abab"
doubled = s + s = "abababab"
remove first and last character from doubled

str = "bababa"

str should contain s now
```

```cpp
bool repeatedSubstringPattern(string s) {
    string doubled = s + s;
    string sub = doubled.substr(1, doubled.size()-2);

    return sub.find(s) != string::npos; // use KMP to find if s is substr of p
}
```

---

## 4.  Shortest Palindrome

```
You are given a string s. You can convert s to a palindrome by adding characters in front of it.
Return the shortest palindrome you can find by performing this transformation.

 
Example 1:
Input: s = "aacecaaa"
Output: "aaacecaaa"

Example 2:
Input: s = "abcd"
Output: "dcbabcd"
```

**Intuition:**
```
We need to find longest prefix which is palindrome -- lpp
then we can just add remaining part at the front of s

if length of longest prefix which is palindrome = m;
ans = rev_s.substr(0, rev_s.size()-m) + s

Ex: abcd
string lpp = "a"
m = lpp.size() = 1

rev_s.substr(0, rev_s.size()-m) = "dcb"

ans = "d" + s = dcbabcd
```

**How to find lpp in linear time?**
```
take string p = s + "#" + rev(s) = a b c d # d c b a
find lps in string P
```

```cpp
string shortestPalindrome(string s) {
    /*
        s = abcd
        // we need: Longest prefix of s which is already a palindrome
        // we can add rest of path to front and overall string becomes palindrome

        s + "#" + rev(s) = abcd#dcba
        lps = 0, 0, 0, 0, 0, 0, 0, 0, 0, 1

        remove last 1 characters from rev(s)
        rev(s) = dcb

        return rev(s) + s = dcbabcd
    */
    string revStr = s;
    reverse(revStr.begin(), revStr.end());

    string p = s + "#" + revStr;
    vector<int> lps = buildLps(p);
    int m = lps[p.size()-1];

    return revStr.substr(0, revStr.size()-m) + s;
}
```