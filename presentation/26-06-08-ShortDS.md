08.06.2026
Lucas Schwebler

# Datastructures with short code
## Fenwick Trees

Goal:
- maintain array $a$ of length $n$
- support point addition updates in $O(\log n)$
- query prefix sums in $O(\log n)$ (use 2 queries for arbitrary subarray sums)

Segtree can do this (and more!), but Fenwick tree has shorter code and better constant factor.

Idea:
- Similar to segment trees, store sums of some subarrays
- For each $i \in \{1, \ldots, n\}$ define $pre(i)$ as the number obtained by removing the lowest $1$-bit from $i$.
- Example: $pre(12) = pre(1100_2) = 1000_2 = 8$
- For each $i \in \{1, \ldots, n\}$, we store the sum of the subarray $[pre(i), i)$
- To answer a prefix sum query $[0, i)$, we sum up $O(\log n)$ while iteratively replacing $i \leftarrow pre(i)$
- For updates, notice that each index is only contained in $O(\log n)$ intervals of type $[pre(i), i)$.

For implementation, use bitmagic to get lowest $1$-bit: `x&-x` (Intuition: `-x = ~x + 1`)
Example:
```
  x = 00010100
 -x = 11101100
----------------
x&-x= 00000100
```

Full implementation:
```C++
vector<ll> tree;

void init(int n) {
	tree.assign(n + 1, 0);
}

// i exclusive!
ll prefix_sum(int i) {
	ll sum = 0;
	for (i; i > 0; i -= i & -i) sum += tree[i];
	return sum;
}

void pointAdd(int i, ll val) {
	for (i++; i < sz(tree); i += i & -i) tree[i] += val;
}

```


## Policy Based Datastructures
There are some extension headers (available on gcc) with useful stuff. Most useful one: set which allows indexing.
```C++
#include <ext/pb_ds/assoc_container.hpp>
#include <ext/pb_ds/tree_policy.hpp>
using namespace std; using namespace __gnu_pbds;
template<typename T>
using Tree = tree<T, null_type, less<T>, rb_tree_tag,
                  tree_order_statistics_node_update>;

int main() {
	Tree<int> X;
	for (int i : {1, 2, 4, 8, 16}) X.insert(i);
	*X.find_by_order(3); // => 8
	X.order_of_key(10);  // => 4 = min i, mit X[i] >= 10
}

```

Other stuff (see tcr):
- faster hashmap
- priority_queue (also supports join + modify)
- rope (similar to treap, allows cutting and merging subarrays in $O(log n)$)