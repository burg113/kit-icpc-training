27.07.2026
Sebastian Kirmayer

# Dynamic Connectivity

Problem (<https://cses.fi/problemset/task/2133>):

> We have a graph on $n$ vertices, initially empty. We receive $q$ updates:
>
> - add an edge between $u$ and $v$
> - remove an edge between $u$ and $v$
>
> After every update, report the number of connected components.
>
> We have $n, q \leq 2 \cdot 10^5$.

If we only had edge additions, this would be easy in $O(q \alpha(n))$ with
Union-Find.

How can we support removals as well? Consider a restricted version of the
problem, where additions and removals happen in a stack-like fashion: We are
only allowed to remove the most recently added edge that has not been removed
yet. This is much easier: We just need to be able to undo the most recent
operation. One way to do this with Union-Find is to record any updates to the
union vector, and undo these.

The runtime should still be $O(q \alpha(n))$: Every removal does just as
much work as the associated addition, so is basically free. Except that
Union-Find is only $O(\alpha(n))$ amortized, and we break amortization by
undoing. Each operation is in fact $O(\log n)$ (path compression breaks, but
smaller-into-larger still works). So we get a runtime of $O(q \log n)$.

Back to the original version.

We will solve this offline, i.e. we will first read all the updates before
producing any output. We assume wlog that after performing all updates, the
graph is empty again (otherwise add removal updates to the end). Now we can
match additions and removals, computing for every edge some time interval
where it exists. There are at most $q$ such edges/intervals.

Now, remember how segment trees work. We have a tree of intervals, i.e. a
(not too large) set of intervals such that every pair of intervals is
either disjoint or one interval is contained in the other. When we make a
query, we split the query intervals into $O(\log n)$ intervals in the tree.

We build a segment tree over the time-steps. We split each edge interval
into $O(\log q)$ intervals from this tree, and collect the edges in each
tree node. The tree has $O(q \log q)$ vertices, and the total number of
intervals summed over all vertices is also $O(q \log q)$.

Now, traverse the segment tree recursively. Whenever we enter a node, add
all the edges in this node. Whenever we backtrack out of a node, remove all
edges in this node. Note that these edge additions and removals now occur in
a stack-like fashion, so we can use the idea from before. At every leaf of the
tree, we have the full state of the Union-Find at that point in time.

We have $O(q \log q)$ updates now, so the total runtime is $O(q (\log q)^2)$.

## More generally

We have access to the full state of the Union-Find at every time step, so
we can also do more complicated stuff, e.g. answer connectivity queries,
or more special operations like size of largest component.

There's nothing special about Union-Find in the above. Any data structure
which supports insertions can be made to support removals (if we can work
offline, and updates are not just amortized fast).

## What if we are online?

Probably not useful for competitive programming, but still possible:

- Polylogarithmic operations: <https://dl.acm.org/doi/pdf/10.1145/320211.320215>
- Even faster: <https://dl.acm.org/doi/pdf/10.1145/335305.335345>
