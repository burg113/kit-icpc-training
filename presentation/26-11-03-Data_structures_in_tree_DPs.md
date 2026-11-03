03.11.2026
Sebastian Kirmayer

# Data structures in tree DPs

## Smaller-into-larger

Consider the following problem:

> You are given a rooted tree with coloured vertices. Compute for each vertex
> the number of distinct colours in its subtree. ($1 \leq n \leq 10^5$)

We solve this by a tree DP. For each vertex, compute the set of colours in its
subtree, then just output the size of this set.

Unfortunately, at every vertex, we need to merge the colour sets of all the
children, which might be $O(n \log n)$. So we get a runtime of $O(n^2 \log n)$
which is too slow.

But we can observe that merging two sets of size $n$ and $m$ is not actually
$O((n+m) \log (n+m))$ in the sum of the sizes: We can do it in $O(n \log m)$
by iterating over the set of size $n$ and inserting each element. So let's
make sure to always iterate over the smaller of the two sets, and insert into
the larger one.

This may seem like it doesn't save much (after all, $m$ and $n$ could be
equal). But actually, this will give us a runtime of $O(n (\log n)^2)$.

First, consider some examples: On a long path -- maybe with some leaves added
-- only one of the two sets in every merge is larger than one element, so we
actually run in $O(n \log n)$. On a balanced binary tree, we do $O(n \log n)$
work in every layer, but there are only $O(\log n)$ layers, so we run in
$O(n (\log n)^2)$.

The second example points the way to a general proof: Note that, whenever we
move an element into a different set, the resulting set will be at least
twice the size of the old set once the merge has finished. (Except it isn't,
because of duplicate elements, but we can instead consider the upper bound
given by the size of the subtree instead.) So each element can only be moved
$O(\log n)$ times.

In this case, the size of the result is bounded by the size of the subtree. In
some cases, the size of the result is instead bounded by the depth of the
subtree. Then, smaller-into-larger does not incur a log factor: Whenever an
element is moved into a larger set, it will never be considered again, since
the size of the larger set does not change.

### "DSU on tree"

The above problem can be optimized to run in $O(n \log n)$ time: The idea is
to replace the set by a vector and a bitmap: The vector will simply contain
the list of colours, the bitmap will contain for every colour whether it is
active (assume colours are at most $n$, use coordinate compression if
necessary).

Now, inserting an element into a set can be done in constant time: First check
if it is already in the set by looking into the bitmap. If not, append
it to the vector and activate it in the bitmap.

Of course, there is a problem: The bitmap must always have size $n$,
regardless of the size of the subtree. So we will only have **one** global
bitmap, shared between all sets. When we are done with a set, we can iterate
over it to clear the bitmap, and reuse it for some other set.

So, the plan is: Whenever we return from a child, clear its set from the
bitmap, and then in the end merge all children's sets into one set. But note
that for the last child, there is no need to clear it from the bitmap, since
we will immediately merge it. So let's process the largest child last! Now,
all the other children will be smaller, so it's OK to iterate over them by
the same argument as above, and for the largest child we will not iterate over
its set.

For some reason this technique is known as "DSU on tree" or "sack". It is also
related to heavy-light decompositions.

## Magic $O(n^2)$ DPs

Consider the following problem (<https://codeforces.com/problemset/problem/581/F>):

> You are given a tree with an even number of leaves. Colour the vertices in
> red and blue such that
>
> - exactly half the leaves are red
> - the number of "bad" edges with differently coloured endpoints is minimized
>
> $2 \leq n \leq 5000$

We shall have the following DP: For a vertex $u$, a count $k$ and a colour $c$,
what is the minimum number of bad edges in the subtree below $u$ given that
$u$ has colour $c$ and exactly $k$ of the leaves below $u$ are red.

Now, for a vertex with two children, we must consider all possible options of
dividing $k$ vertices among the children, i.e. $l + r = k$, and we want the
smallest of these options. This is a $(\min,+)$-convolution, and can be done
in time $O(L \cdot R)$, where $L$ and $R$ are the lengths of the two sequences.

So, whenever we combine two children, we perform $O(L \cdot R) = O(n^2)$ work,
getting a total runtime of $O(n^3)$.

Or so you might think. A more careful analysis gives a runtime of $O(n^2)$.
The idea is as follows: For each merge, we will perform constant work for
every pair of leaves where one leaf is on the left and the other is on the
right. But this means that for every pair of leaves, we will perform work in
exactly one vertex: The LCA of the two leaves. Since there are only $O(n^2)$
pairs of leaves, this runs in $O(n^2)$.

This can be somewhat combined with smaller-into-larger: Say we can merge in
time $O((L+R) \cdot \min(L, R))$. Then we have $(L + R) \cdot \min(L, R)
= L \cdot \min(L, R) + R \cdot \min(L, R) \leq LR + RL = O(LR)$.
