# Dynamic Programming With State Expansion
---

> [!IMPORTANT]
>
>       It appears when normal DP on position is NOT enough.
>
>       because your answer depends on extra information/state besides just where you are.

---

## 1. Core Idea of State Expansion

Normal DP:
```cpp
dp[i][j] = answer at cell (i,j)
```

> [!WARNING]
> But sometimes this fails because:
>
>      **Two paths** reaching same cell may **NOT** be equivalent.

> [!IMPORTANT]
> Because they may carry different:
> - XOR values
> - remaining budget
> - number of stops used
> - collected score
> - time spent
> - keys/masks/resources

So we expand state:
```cpp
dp[i][j][extra_state]
```

## 2. Mental Model

> Think: **"My position alone does not determine future."**

> Need: **"Position + current condition determines future."**

**That condition becomes extra DP dimension.**

## 3. How to Identify This Pattern


> [!NOTE]
> **Ask yourself:**
>
> If two ways reach same cell/node:
> - Can I safely keep only one?
>
>       If YES → normal DP.
>       If NO → state expansion.

### Example:
```
Minimum XOR Grid:
At cell (2,2):

1. Path A reached with XOR = 5
2. Path B reached with XOR = 9

Can discard one?

NO.

Because future XOR depends on previous XOR.

Thus:

dp[i][j][xor]
```

## 4. Generic Recognition Template

You need state expansion when problem has:

"Reach destination with constraint"
### Example
> [!IMPORTANT]
> - with K stops
> - within T time
> - with energy left
> - with fuel remaining
> - "Path value accumulates"

> [!IMPORTANT]
> - XOR
> - sum
> - score
> - parity
> - "Choices affect future"

> [!IMPORTANT]
> - cooldown
> - previous color
> - last move
> - remaining skips

## 5. Generic Transition Formula

```cpp
// usually
dp[position][state] =
    best from previous positions/states
//
// transition
new_state = update(old_state)
```

