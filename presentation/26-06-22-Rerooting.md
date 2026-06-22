# DP Rerooting

Easy problem:

> Given a rooted tree with $n$ vertices ($n \leq 2 \cdot 10^5$), compute the
> length of the longest path starting at the root.

Many possible solutions (DFS, BFS, ...). One possibility is DP:

$$
DP[u] = \max (\\{0\\} \cup \\{1 + DP[v] | v \in c(u)\\})
$$

Harder problem:

> Given a tree with $n$ vertices ($n \leq 2 \cdot 10^5$), compute the length
> of the longest path starting at **every vertex**.

One way to solve this would be to compute the DP with every vertex as root
$\rightarrow$ rerooting. But doing this naively is quadratic.

First, we will fix a root and compute the DP with this root.

The longest path starting at a vertex either goes "down" (away from the
root) or "up" (initially towards the root). The DP already computes "down"
paths. Idea: Traverse the tree again and push "up" paths down the tree.

```
dfs(u, up):
    answer[u] = max(up, DP[u])
    c = children[u]
    for i in 0..len(c):
        dfs(c[i], 1 + max(up, DP[c[0]], ..., DP[c[i-1]], DP[c[i+1]], ..., DP[c[len(c)-1]]))

dfs(root, 0)
```

Problem: This is still quadratic (consider a star).  
Solution: Compute prefix/suffix maxima. This gives linear time.

In a simple case like this, doing the rerooting manually is doable. In more
complicated DPs, doing it manually is error-prone.

## Rerooting template to the rescue

<https://github.com/mzuenni/ContestReference/blob/new-master/content/graph/reroot.cpp>

To use a template, we need some general formulation of tree DPs. There are a
few options, our template uses the formulation

$$
DP[u] = f(g(u, v_1, w_1, DP[v_1]) \diamond g(u, v_2, w_2, DP[v_2]) \diamond \cdots \diamond g(u, v_d, w_d, DP[v_d]))
$$

Where $v_i$ is the $i$-th child and $w_i$ is the weight on the edge between
$u$ and $v_i$.

You need to fill in:

- `T`: The DP type
- `W`: Edge weight type
- `fin`: The outer function $f$
- `takeChild`: The inner function $g$
- `comb`: The combine operator $\diamond$
- `E`: Identity of $\diamond$

$\diamond$ must form a commutative monoid with the given identity.

So for the above problem we can do:

- `T`: `int`
- `W`: doesn't matter (no weights)
- `fin(v) { return v; }`
- `takeChild(u, v, w, d) { return d+1; }`
- `comb(x, y) { return max(x, y); }`
- `E = 0`

## Problems

There are two kinds of rerooting problems:

- Obvious: Compute some value for every vertex
- Non-obvious: Compute a single result
  - Example: Compute diameter of a tree (there are much simpler solutions
    for this)

Someone has put together a [contest with rerooting problems](https://codeforces.com/contestInvitation/eb6f12aa75be484e2f00f86a9e73f7ec5e0aa376).
