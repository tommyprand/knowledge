# Algorithm or effective procedure
An **algorithm** is a description of a sequence of finite and elementary steps which allows to reach some objectives. Elementary means something that is achievable "mechanically" (i.e. without intelligence).

We can think of an algorithm as a "black box" that transforms an input into an output, this transformation is performed by the sequence of elementary instructions specified by the algorithm.

```mermaid
graph LR;
	input-->algo;
	algo-->output;
```

The algorithm is **deterministic** if at any point in execution it is possible to determine the next step without ambiguity, this implies that each possible input will uniquely determine the corresponding output. Note that the procedure might not terminate so no output will be available.

This means that the algorithm determines a [[Definitions#^ff8ff7 | (partial) function]].

$$
f: input \rightarrow output
$$

We then say that the function $f$ is _computed_ by the algorithm and that $f$ is [[Definitions#^b2264c | computable]].
Notice that computability is defined on existence of the algorithm not on actually knowing what the algorithm is. 

There follows two functions to show examples about computability analysis.

### Example 1.1
Let
$$
g(n) = \begin{cases}
	1 & \text{There is a sequence of exactly } n \text{ 5 in } \pi\\
	0 & \text{otherwise}
\end{cases}
$$

A naive algorithm that we could try to use to compute this would be to:
1. Generate all the digits of $\pi$
2. If there is a sequence of $5$ long $n$ then return $1$
3. Otherwise return $0$

This is obviously not sound because the step 1 would require to generate an infinite sequence which is not possible on a computational machine.

Note that ***this doesn't mean that $g$ is not computable!***, we don't know if an actual algorithm exists to solve this problem in finite amount of steps, so we cannot conclude anything about the computability of $g$.

### Example 1.2
Let now be
$$
h(n) = \begin{cases}
	1 & \text{There is a sequence of \textit{at least} } n \text{ consecutive } 5 \text{ in } \pi \\
	0 & \text{otherwise}
\end{cases}
$$

The function is very similar to the $g$ of the [[#Example 1.2]] but we can make a very important observation which was not true before: if we find that for instance $h(3) = 1 \implies h(2) = 1 \text{ and } h(1) = 1$.

More precisely let $K = sup\{n \mid \pi \text{ contains } n \text{ consecutive digits } 5\}$, then either:
1. $K$ is finite, and thus: 
	$$ h(n) = \begin{cases}
		 1 & n \leq K\\
		 0 & \text{otherwise}
	\end{cases}
	$$
2.  $K$ is infinite, and thus  $$h(n) = 0 \quad \forall n \in \mathbb{N}$$

This implies that $h$ is actually computable because it is either a step function or a constant one and these are both evidently easy to compute with a simple program. We are not interested in the nature of $K$: we care about computability which means proving the existence of an algorithm for the computation, not on finding the actual solution.

# Algorithms and existence of non-computable functions
In the following section we will prove the existence of non-computable functions by algorithms.
### Mathematical notation
- _Natural numbers_: $\mathbb{N} = \{0, 1, 2, ...\}$
- Given the sets $A, B$ their _cartesian product_ is the set $A \times B = \{ (a, b) \mid a \in A, b \in B\}$, it follows that $A^n = A \times A \times ... \times A$ n times.
- A _relation_ $R$ is the subset of the cartesian product between sets: Given the sets $A, B, ..., N$ then the relation $R \subseteq A \times B \times ... \times N$
	- We can see a [[Definitions#^ff8ff7|(partial) function]] $f: A \rightarrow B$ as a special kind of relation where: $f \subset \{ (a, b) \mid a \in A, b \in B\}$ and for each pair of tuples if $(a, b_1), (a, b_2) \in f \implies b_1 = b_2$.
		- The domain of $f$ is $dom(f) = \{a \in A \mid \exists b \in B \text{ where } f(a) = b\}$
		- We indicate that the function $f$ is defined in $a$ with $f(a)\downarrow$, we indicate that it is undefined with $f(a)\uparrow$.
- Given a set $A$ we indicate its cardinality with $|A|$ (for finite sets it is the number of elements in the set).
- Given the sets $A, B$ we say that:
	- $|A| = |B|$ if there exist a bijective mapping between $A$ and $B$
	- $|A| \leq |B|$ if there exist an injective function between $A$ and $B$ (all the elements of $A$ are mapped into  _unique_ elements of $B$). <span style="display: block; margin:10px auto; width: 50%">![[injective-function-svg.svg]]</span> Equivalently there exist a surjective function between $B$ and $A$ (all the elements in $A$ are part of the image). <span style="display: block; width: 50%; margin: 10px auto">![[surjective-function-svg.svg]]</span>
	-  $A \subseteq B \implies |A| \leq |B|$
	- We say that a set $A$ is _countable_ or _denumerable_ if $|A| \leq |\mathbb{N}|$, so if there is a surjective function $f: \mathbb{N} \rightarrow A$, note that when this is the case we can _enumerate_ the elements of $A$, which correspond to the image of the function $f$.
	- When $A, B$ are countable then $A \times B$ is also countable.
		- _Proof_: we can place the elements of $A \times B$ in a matrix: $$ 
			A \times B = \begin{pmatrix}  (a_1, b_1) & (a_1, b_2) &  (a_1, b_3) & (a_1, b_4) &  (a_1, b_5) \\ (a_2, b_1) &  (a_2, b_2) & (a_2, b_3) &  (a_2, b_4) & (a_2, b_5) \\  (a_3, b_1) & (a_3, b_2) &  (a_3, b_3) & (a_3, b_4) &  (a_3, b_5) \\ (a_4, b_1) &  (a_4, b_2) & (a_4, b_3) &  (a_4, b_4) & (a_4, b_5) \\  (a_5, b_1) & (a_5, b_2) &  (a_5, b_3) & (a_5, b_4) &  (a_5, b_5) \end{pmatrix}$$
			They can be then enumerated along the diagonals as: $(a_1, b_1)$, $(a_1, b_2)$, $(a_2, b_1)$, $(a_1, b_3)$ and so on (_dove tail_ enumeration).
	- A countable union of countable sets is countable. ^b0965c

### Existence of non-computable functions

We now will show that, given an arbitrarily-fixed computational model following the constraints listed [[Definitions#^1acc78|here]], there are functions not computable in such a model.

Let $$\mathcal{F} = \{f \mid f: \mathbb{N} \rightarrow \mathbb{N}\}$$
be the set of all (partial) _unary functions_ over $\mathbb{N}$.

Then let $\mathcal{A}$ be the set of all the algorithms in our computational model. All algorithms $A \in \mathcal{A}$ computes a function $f_A: \mathbb{N} \rightarrow \mathbb{N}$. Recall that a function is said to be [[Definitions#^b2264c|computable]] in our model if there is an algorithm that computes it.

We can then define the set of computable unary functions as $$\mathcal{F}_\mathcal{A} = \{ f_A \in \mathcal{F} \mid A \in \mathcal{A}\}$$.
Obviously this implies that $\mathcal{F}_\mathcal{A} \subseteq \mathcal{F}$

Now, by the assumptions stated [[Definitions#^1acc78|here]], we can define an algorithm $A$ as a finite sequence of instructions and thus the set of all the algorithms is $$\mathcal{A} \subseteq \bigcup_{n \in \mathbb{N}} I^{n}$$

The set of all instructions is finite, thus countable. As stated [[#^b0965c|above]] a union of countable sets is countable which implies that $\mathcal{A}$ is countable: $|\mathcal{A}| \leq |\mathbb{N}|$.

Since the mapping 
$$\mathcal{A} \rightarrow \mathcal{F}_\mathcal{A}$$
$$A \mapsto f_{A}$$
is by definition _surjective_ this means that also $\mathcal{F}_\mathcal{A}$ is countable: $|\mathcal{F}_\mathcal{A}| \leq |\mathbb{N}|$.

The problem is that the set of all functions $\mathcal{F}$ is not countable.
To prove this let $\mathcal{T} \subset \mathcal{F}$ the set of all the _total_ functions ($\forall f \in \mathcal{T}, \ dom(f) = \mathbb{N}$).
Suppose that $\mathcal{T}$ is countable: this means we can enumerate all the functions in it as $f_1, f_2,...$ and write them systematically in a matrix:
$$
\begin{bmatrix}
f_0(0) & f_1(0) & f_2(0) & \dots \\
f_0(1) & f_1(1) & f_2(1) & \dots \\
f_0(2) & f_1(2) & f_2(2) & \dots \\
\vdots & \vdots & \vdots & \ddots \\
\end{bmatrix}
$$

Then we can define a function $d$ that operates on the diagonal of the matrix so that $d(n) = f_n(n) + 1$. This function is by definition total (defined on all naturals).

By definition $\forall n \in \mathbb{N} \ d \neq f_n$ but this is absurd since by hypothesis $\mathcal{T}$ contained all the total functions on $\mathbb{N}$ and we just crafted a new one.
Hence we have proved that the set of all total functions $\mathcal{T}$ is _not_ countable, thus:
$$
|\mathcal{F}_{A}| \leq |\mathbb{N}| \lt |\mathcal{T}| \lt |\mathcal{F}|
$$

This shows that **not all functions are computable**.

# URM Computability

## Introduction to Computational Models

There are various models of computation that have been proposed to formalize the notion of computability:

1. Turing machine (Turing, 1936)
2. λ-calculus (Church, 1930)
3. Partial recursive functions (Gödel-Kleene 1930)
4. Canonical deductive systems (Post, 1943)
5. Markov systems (Markov, 1951)
6. Unlimited register machine (URM) (Shepherdson - Sturgis, 1963)

Despite the variety, all these "sufficiently expressive" models define the same class of computable functions. This observation leads to the Church-Turing thesis.

### Church-Turing Thesis

> A function is computable by an effective procedure (i.e., in a finitary computational model, obeying the [[Definitions#^1acc78|conditions from the previous chapter]], if and only if it is computable by a Turing machine.

**Note:** The Church-Turning thesis is not a formal theorem due to its reliance on the informal notion of an "effective procedure". It's supported by extensive evidence but cannot be formally proven.

# URM Computability

## Introduction to Computational Models

There are various models of computation that have been proposed to formalize the notion of computability:

1. Turing machine (Turing, 1936)
2. λ-calculus (Church, 1930)
3. Partial recursive functions (Gödel-Kleene 1930)
4. Canonical deductive systems (Post, 1943)
5. Markov systems (Markov, 1951)
6. Unlimited Register Machine (URM) (Shepherdson - Sturgis, 1963)

Despite the variety, all these "sufficiently expressive" models define the same class of computable functions. This observation leads to the Church-Turing thesis.

### 1.1 Church-Turing Thesis

> A function is computable by an effective procedure (i.e., in a finitary computational model, obeying the [[Definitions#^1acc78|conditions for an effective procedure]]) if and only if it is computable by a Turing machine.

**Note:** The Church-Turning thesis is not a formal theorem due to its reliance on the informal notion of an "effective procedure". It's supported by extensive evidence but cannot be formally proven.

## Unlimited Register Machine (URM)

We'll use the URM model to formalise the notion of computable functions. A URM consists of:

1. **Unbounded memory:** An infinite sequence of registers $(R_1, R_2, \ldots, R_n, \ldots)$, each storing a natural number. This might sound contradicting the fact that algorithm should use a _bounded_ amount of memory, but this is made so the machine will not limit the notion of computability no matter how big the necessary memory is. 
2. **Computing agent:** Capable of executing a URM program.
3. **URM program:** A finite sequence of instructions that can alter the machine's configuration.

### URM Instructions

1. **Zero:** $Z(n)$ - Sets the content of register $R_n$ to zero.
2. **Successor:** $S(n)$ - Increments the content of register $R_n$ by 1.
3. **Transfer:** $T(m,n)$ - Copies the content of register $R_m$ to $R_n$.
4. **Conditional Jump:** $J(m,n,t)$ - If the contents of $R_m$ and $R_n$ are equal, jump to instruction $t$; otherwise, continue to the next instruction.

### URM Configuration and Execution

- **Configuration:** A sequence $(r_1, r_2, \ldots, r_n, \ldots) \in \mathbb{N}^\omega$ representing the contents of all registers.
- **State:** A pair $\langle c, t \rangle$ where $c$ is the configuration and $t$ is the program counter (index of the next instruction).

### URM Computation Notation

For a URM program $P$ and input $(a_1, a_2, a_3, \ldots) \in \mathbb{N}^\omega$:

- $P(a_1, a_2, \ldots) \downarrow$ : The computation halts.
- $P(a_1, a_2, \ldots) \uparrow$ : The computation diverges (never halts).
- $P(a_1, \ldots, a_k)$ : Shorthand for $P(a_1, \ldots, a_k, 0, \ldots, 0)$ when only finitely many inputs are non-zero.

## URM-Computable Functions

### Definition

A function $f: \mathbb{N}^k \to \mathbb{N}$ is URM-computable if there exists a URM program $P$ such that for all $(a_1, \ldots, a_k) \in \mathbb{N}^k$ and $a \in \mathbb{N}$:

$P(a_1, \ldots, a_k) \downarrow a$ if and only if $(a_1, \ldots, a_k) \in dom(f)$ and $f(a_1, \ldots, a_k) = a$.

The output is conventionally stored in register $R_1$.

### Classes of URM-Computable Functions

- $\mathcal{C}$: The class of all URM-computable functions
- $\mathcal{C}^{(k)}$: The class of k-ary URM-computable functions

This implies: 
$$
\mathcal{C} = \bigcup_{k\geq1} \mathcal{C}^{(k)}
$$

## Examples of URM-Computable Functions

**Addition**: $f(x,y) = x + y$

>_Idea_: Increment $R_1$ and $R_3$ until $R_2$ and $R_3$ contain the same value. This results in adding the content of $R_2$ to $R_1$.

Initial configuration: 
$$
\begin{array}{ | c | c | c |}
\hline
R_1 & R_2 & R_3 & \dots \\
\hline
x & y & 0 & \dots \\
\hline
\end{array}
$$

```
I₁: J(2,3,5)
I₂: S(1)
I₃: S(3)
I₄: J(1,1,1) // unconditional jump
```

**Predecessor**: $f(x) = x \dot{-} 1 = \begin{cases} 0 & \text{if } x = 0 \\ x - 1 & \text{if } x > 0 \end{cases}$

>_Idea_: If $x = 0$, it trivially terminates. If $x > 0$, it keeps a value $k-1$ in $R_2$ and $k$ in $R_3$, with $k > 1$ ascending until $R_3 = x$, at that point $R_2 = x - 1$.

Initial configuration: 
$$
\begin{array}{ | c | c | c |}
\hline
R_1 & R_2 & R_3 & \dots \\
\hline
x & 0 & 0 & \dots \\
\hline
\end{array}
$$

```
I₁: J(1,3,8)
I₂: S(3)
I₃: J(1,3,7)
I₄: S(2)
I₅: S(3)
I₆: J(1,1,3)
I₇: T(2,1)
```

**Half (for even numbers)**: $f(x) = \begin{cases} \frac{x}{2} & \text{if } x \text{ is even} \\ \uparrow & \text{otherwise} \end{cases}$

>_Idea_: Store an increasing even number in $R_2$ and store its half in $R_3$.
Initial configuration:

$$
\begin{array}{ | c | c | c |}
\hline
R_1 & R_2 & R_3 & \dots \\
\hline
x & 2k & k & \dots \\
\hline
\end{array}
$$

```
I₁: J(1,2,6)
I₂: S(2)
I₃: S(2)
I₄: S(3)
I₅: J(1,1,1)
I₆: T(3,1)
```

## Function Computed by a Program

For a program $P$ with $k$ parameters, there exists a unique function $f_P^{(k)}: \mathbb{N}^k \to \mathbb{N}$ defined as:

$f_P^{(k)}(a_1, \ldots, a_k) = \begin{cases} a & \text{if } P(a_1, \ldots, a_k) \downarrow a \\ \uparrow & \text{if } P(a_1, \ldots, a_k) \uparrow \end{cases}$

**Note:** The same function can be computed by different programs due to:
1. Addition of useless instructions
2. Different algorithms computing the same function

## Variations of URM and Their Computational Power

### Reduced URM ($URM^-$)

URM without transfer instructions.

**Theorem:** $\mathcal{C}^- = \mathcal{C}$

**Proof:** 
1. Obviously, $\mathcal{C}^- \subseteq \mathcal{C}$.
2. To prove $\mathcal{C} \subseteq \mathcal{C}^-$, we show that a transfer instruction $T(m,n)$ at step $t$ can be replaced with the following subroutine:

```
Z(n)
LOOP: J(m,n,END)
S(n)
J(1,1,LOOP)
END:
```

We proceed by induction on the number $h$ of transfer instructions in $P$:

- Base case ($h=0$): Trivial, $P = P'$ since $P$ is already a $URM^-$ program.
- Inductive step ($h \to h+1$): Replace one transfer instruction with the subroutine above. The resulting program $P^{''}$ has $h$ transfer instructions. By the inductive hypothesis, there exists a $URM^-$ program $P'$ such that $f_P^{(k)} = f_{P_2}^{(k)} = f_{P'}^{(k)}$.

### URM with Swap Instructions (URM_S)

Replace transfer with swap instruction $T_S(m,n)$ that exchanges contents of registers $m$ and $n$.

**Theorem:** $\mathcal{C}_S = \mathcal{C}$

**Proof:** 
1. $\mathcal{C} \subseteq \mathcal{C}_S$: We know $\mathcal{C} \subseteq \mathcal{C}^-$ from the previous proof, and $\mathcal{C}^- \subseteq \mathcal{C}_S$, so $\mathcal{C} \subseteq \mathcal{C}_S$.
2. $\mathcal{C}_S \subseteq \mathcal{C}$: The swap instruction $T_S(m,n)$ can be encoded in URM as:

```
T(n,i)
T(m,n)
T(i,m)
```

where $i$ is a "new" register not used by the program.

We prove by induction on the number of swap instructions $h$ that any URM_S program can be transformed into an equivalent URM program.

### URM without Jump Instructions ($URM^{=}$)

**Theorem:** $\mathcal{C}^{=} \subset \mathcal{C}$

**Proof:** 
1. Clearly, $\mathcal{C}^{=} \subseteq \mathcal{C}$.
2. The inclusion is strict because $f: \mathbb{N} \to \mathbb{N}$ with $f(x) \uparrow \forall x$ is computable in URM but not in $URM^{=}$. All functions in $\mathcal{C}^{=}$ are total since programs without jump instructions always terminate.

**Characterization of unary functions in $\mathcal{C}_{nj}$:**
- $f(x) = c$
- $f(x) = x + c$

where $c$ is a constant in $\mathbb{N}$.

**Proof of characterization:**
Let $r_1(h,x)$ be the content of register $R_1$ after $h$ steps, starting from an initial configuration where $R_1$ contains $x$ and other registers contain 0.

We prove by induction on $h$ that after $h$ execution steps, $r_1(h,x)$ is equal to $x+c$ or $c$ for some suitable constant $c \in \mathbb{N}$.

- Base case ($h=0$): $r_1(0,x) = x$, which satisfies the claim with $c=0$.
- Inductive step ($h \to h+1$): By the inductive hypothesis, $r_1(h,x) = x+c$ or $r_1(h,x) = c$. The next instruction can be:
  - $Z(n)$: If $n=1$, $r_1(h+1,x) = 0$; otherwise, $r_1(h+1,x) = r_1(h,x)$.
  - $S(n)$: If $n=1$, $r_1(h+1,x) = r_1(h,x) + 1$; otherwise, $r_1(h+1,x) = r_1(h,x)$.
  - $T(m,n)$: If $n>1$ or $n=m=1$, then $r_1(h+1,x) = r_1(h,x)$. If $n=1, m>1$, we don't know the content of $r_1(h+1,x)$.

To resolve this issue, we can extend the proof to all the registers $r_j$ simultaneously, showing that $\forall j \in \mathbb{N} \ r_j(h,x)$ contains either $c$ or $x+c$ for a suitable constant $c$. This solves the problem and makes the last step working because now we do know the content of any register $r_j$.