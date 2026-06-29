29.06.2026
Sebastian Kirmayer

# Bitmask DPs

## Travelling Salesman

Consider the following classic NP-complete problem:

> Given $n$ cities with pairwise distances, find the shortest path which visits
> every city exactly once.

There is an easy solution with runtime $O(n! \cdot n)$: Just try out all
permutations of cities and compute the length of the path. It can be optimised
to $O(n!)$, but $13! \approx 6.2 \cdot 10^9$.

Instead, we will use the following idea: The remainder of the path depends only
on the set of cities we have already visited and the current city. So let us
build a DP:

$$
    DP[S, u] = \text{shortest path visiting all cities in $S$ ending in $u$}
    \space\space (u \not\in S)
$$

The transitions should be fairly easy to see:

$$
\begin{align}
    DP[\emptyset, u] &= 0 \\
    DP[S, u] &= \min_{v \in S} DP[S \setminus \\{v\\}, v] + d(v, u)
\end{align}
$$

The final answer is then $\min_{v \in V} DP[C \setminus \\{v\\}, v]$ where
$V$ is the set of cities.

The number of DP states is $O(2^n \cdot n)$, which is much less than $n!$.

One of the parameters of the DP is a subset of the cities. If we represent
these sets as `std::set` everything is slow and complicated. Instead,
represent each set as an $n$-bit number, where the $i$-th bit indicates
whether the $i$-th city is in the set.

- Sets can be used as indices into a vector
- Set operations become bitwise operations $\rightarrow O(1)$

With this representation, the transitions can be implemented in $O(n)$ to
get a runtime of $O(2^n \cdot n^2)$. For $n=20$, this is approximately
$4.2 \cdot 10^8$.

$DP[S, u]$ depends only on $DP[T, v]$ where $T \subset S$. So for a nice
iterative DP implementation we need to iterate over sets in an order such that
we see $T$ before $S$ whenever $T \subset S$. There are many ways to do this,
but the simplest is to iterate over the integers in the usual order and treat
them as bitmasks.

## Other bitmask DPs

There are two main kinds of bitmask DPs:

- For each $S$, transitions to $S \ {x}$ for every $x \in S$
  - $O(2^n \cdot n)$ transitions
- For each $S$, transitions to $T$ for every $T \subset S$
  - naively $O(4^n)$ transitions
  - actually $O(3^n)$ transitions
  - TCR has code for quickly enumerating subsets (needed for $O(3^n)$ )

Bitmasks DPs which optimise over permutations typically fall into the first
kind. Bitmasks DPs which optimise over set partitions often fall into the
second kind, but can sometimes be optimised into the first kind. One technique
here is to translate the problem into a problem about permutations.

## Sum over subsets (SoS)

Consider the following problem:

> Given a set $S$ and a weight function $w: 2^S \rightarrow \mathbb{Z}$,
> compute for each subset $T \subseteq S$:
>
> $$\sum_{T' \subseteq T} w(T')$$

This can be done naively in $O(3^n)$.

We might try to use a bitmask DP which just looks at subsets excluding one
element, but this will massively overcount: There are many paths from a subset
to a superset. It might be useful to think about this in terms of a hypercube.

So, allow only paths which add the elements in increasing order. We look at
each element in order, and allow weight to move along edges which add that
element. (Imagine a pretty picture here. I was too lazy to draw something.)

This problem (and its inverse) are often a useful building block. More on this
in a future bitwise convolutions talk.
