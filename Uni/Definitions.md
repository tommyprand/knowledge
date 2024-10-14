 **Partial function**: A partial function $f$ from a set $X$ to a set $Y$ is a function from a subset $S \subseteq X$ to $Y$. The subset $S$ is called the domain of $f$: $S = dom(f)$. ^ff8ff7

**Total function**: A function $f$ where $dom(f) = X$ is said to be a total function.

**Computable function**: A function $f$ is _computable_ $\iff \exists$  an algorithm that computes $f$, so that given the input $a \in dom(f)$ , the algorithm gives as output $f(a)$. ^b2264c

**Algorithm**: An algorithm is a finite sequence of elementary instructions that are computable by a machine implementing some computational model. The following properties are required to be satisfied by an algorithm: ^1acc78
1. _Finite length_
2. There exist a _computing agent_ able to compute its instructions
3. The agent has _memory_
4. Computation is in _discrete steps_
5. Computation is _deterministic_
6. Input size is _unbounded_
7. The computing agent has unbounded memory (so that we are not limited by the resources of the machine when evaluating computability)
8. Instructions are _limited_ in number and complexity (or no machine would be possible)

**URM-Computable function**: A (partial) function $f: \mathbb{N}^k \to \mathbb{N}$ is URM-computable if there exists a URM program $P$ such that for all $(a_1, \ldots, a_k) \in \mathbb{N}^k$ and $a \in \mathbb{N}$:

$P(a_1, \ldots, a_k) \downarrow a$ if and only if $(a_1, \ldots, a_k) \in dom(f)$ and $f(a_1, \ldots, a_k) = a$.

The output is conventionally stored in register $R_1$.