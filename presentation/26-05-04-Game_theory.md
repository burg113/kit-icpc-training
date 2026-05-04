04.05.2026
Sebastian G. Kirmayer

# Game Theory

Abstract formulation of a (combinatorial) game:

- Set of states $S$
- Initial state $s_0$
- For each player, a transition relation
  $\rightarrow_1, \rightarrow_2 \subseteq S \times S$
  - For now, focus on **impartial games**:
    $\rightarrow_1 = \rightarrow_2 =:\ \rightarrow$
  - View it as a graph
- Players take turns
- The game ends when a player has no valid move on their turn
- Determining the winner:
  - **normal**: The player with no valid moves loses
  - **misère**: The player with no valid moves wins

We focus on games which terminate, i.e. the state graph is acyclic.

Standard problem type: Here is a game, determine winner under optimal play.

A state is **winning** if, starting in that state, the first player wins,
otherwise **losing**.

Under normal play:
- A state is winning if it has a losing successor state
- A state is losing if all its successor states are winning

Under misère play:
- A state is winning if it has a losing successor state *or no successor
  states at all*
- A state is losing if all its successor states are winning *and it has at
  least one successor state*

$\Rightarrow$ Intuition: Normal is simpler, sometimes we can convert
misère to normal by ending the game one step earlier

How to solve problems with this:
- DP over game states
- Find some criterion for winning states, and prove its correctness with these
  rules
  - How to find criterion?
  - Generally: Difficult
  - Implement DP and try to spot patterns
  - Run DP in your head and try to spot patterns (observing how the DP values
    are produced can make this simpler)

Example problem: <https://codeforces.com/problemset/problem/1537/D>

Start with a positive integer $n$. In every move, the player can subtract any
divisor except 1 and the current number. A player who cannot make a move loses.

- Game states: Positive integers
- Initial state is $n$
- Game is impartial, transition relation:
  $n \rightarrow n' \Leftrightarrow 0 < n' < n-1 \wedge (n - n') | n$
- Normal play
- Number gets smaller with every move, so the game terminates

For small $n$: DP in $O(n \log n)$

Spot patterns:
- most of the time odd is losing, even is winning
- powers of two are the only exception to the rule

Prove correctness:
- If $n$ is odd, all divisors are odd, so next number will be even
  and not a power of two
  - Those are all winning, so $n$ is losing
- If $n$ is even and not a power of two, there is an odd divisor, which makes
  next number odd
  - That next number is losing, so $n$ is winning
- Powers of two are left as an exercise to the reader
