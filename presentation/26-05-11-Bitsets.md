11.05.2026
Lucas Schwebler

# Bitsets

A bitset is like a boolean array storing one bit per boolean.
It supports bitwise operations like `~`, `&`, `|`, `^`, `<<`, `>>`.
These operations run in $O(\frac{n}{w})$ where $n$ is the length of the bitset and $w$ is the wordsize (i.e. 64).

Sometimes bitsets can be used to speed up $O(n^2)$ time algorithms to $O(\frac{n^2}{w})$ and make them pass for constraints like $n \leq 10^5$ which seem too large for quadratic time solutions.

## DAG reachability

Problem: given a DAG, answer queries of the form
> Can vertex $u$ reach vertex $v$?

A trivial $O(nm)$ solution precomputes all answers as
```C++
canReach[u][u] = 1;
for(int w : adj[u]){
	for(int v = 0; v < n; v++){
		if(canReach[w][v]) canReach[u][v] = true;
	}
}
```
I.e. the solution basically ORs the boolean arrays of all vertices to which $u$ has a directed edge.
This can be made faster with bitsets:
```C++
canReach[u][u] = 1;
for(int w : adj[u]){
  canReach[u] |= canReach[w];
}
```
For some problems, $n^2$ bits are too much memory usage.
In this case, we can still get $O(\frac{n^2}{w})$ time with linear memory usage by computing the DP $\frac{n}{w}$ times with bitsets (or unsigned long longs) of size $w$.

## Subsetsum

Problem: Given an array $a$ and a number $C$, is there a subset $S$ with $\sum_{i \in S} a_i = C$?

Classic dp: $dp[i][s] = dp[i-1][s] \lor dp[i-1][s-a_i]$

Using bitsets:
```C++
bitset<100000> dp;
dp[0] = 1;
for(int x : a){
  dp |= dp << x
}
```

## Linear Algebra over $\mathbb{F}_2$

Problem: There are $n$ lamps, some are turned on some are turned off.
There are also $m$ switches, each of which toggles some specified subset of lamps.
Can you turn all lamps off?

Solution: Encode the switches as vectors over $\mathbb{F}_2$ and use Gaussian elimination. Since the arithmetic is over $\mathbb{F}_2$, we can use bitsets and the XOR operation.

## Final Remarks
- The size of the bitsets needs to be known at compile time.
This can be problematic for input constraints like $n \cdot m \leq value$. As a workaround one can implement their own vector-based bitset or use some [template magic](https://codeforces.com/blog/entry/143059|test).


## Practice Problems:
- <https://cses.fi/problemset/task/2138>
- <https://cses.fi/problemset/task/2137> (You might need to add `#pragma GCC target("popcnt")` to your code...)
- <https://atcoder.jp/contests/agc020/tasks/agc020_c>
- <https://codeforces.com/contest/1854/problem/B>
