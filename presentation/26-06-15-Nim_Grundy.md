## Nim

- Several piles of tokens
- One move:
  - Select a pile
  - Take at least one token

First player wins iff bitwise XOR of pile sizes is nonzero.

Proof:
- If XOR is 0, taking anything will change the XOR.
- If XOR is $x \neq 0$, let $b$ be the highest set bit in $x$. There is at
  least one pile of height $p_i$ where $b$ is set. Then $p_i \oplus x < p_i$,
  so reduce that pile to $p_i \oplus x$. This changes the XOR to 0.

## Sum of games

Let's say we have several games $G_1, \cdots, G_n$. We will play all games in
parallel. When it's a player's turn, they can choose a game, and then make a
move in this game. The game ends when a player cannot make a move in any game.

Example: Nim is sum of several piles

For Nim, computing which player wins the sum was quite easy. It would be nice
if it was always this easy.

Trick: Grundy numbers

- Every (impartial, normal play) game has a Grundy number
- First player wins iff the Grundy number is nonzero
- Grundy number of a sum of games is XOR of Grundy numbers
- Formally: A game is "equivalent" to Nim with a single pile whose height
  is the Grundy number (for the right definition of equivalence)

How to compute Grundy numbers:

- The Grundy number of a state is the MEX (minimum excluded) of the Grundy
  numbers of its successor states
  - Smallest non-negative integer which is not the Grundy number of a successor
    state
- See how this corresponds to computing losing/winning states
- Can use similar DP

Typical problem type: Given several instances of a game, compute winner of
their sum.

Example problem: S-Nim <https://open.kattis.com/problems/snim>

Nim, but we are given a set $S$ and we can only take a number of tokens in $S$.
We have $|S| \le 100$, and we are given up to 100 positions with up to 100
piles of up to 10000 tokens. Decide which player wins each positions.

On a single pile: If we have $x$ tokens, successors are $\{x - y | y \in S\}$.
We can compute Grundy numbers by DP in $O(x \cdot |S|)$.

For multiple piles: Compute DP once, then XOR.
