## Models of computation

We can distinguish between two models of computation: **Functional** and **Reactive**:
**Functional**: A program takes input(s) and produces output(s), this computation implements a function that realises our desired functionality. The canonical model for this is the _Turing machine_.
**Reactive**: The system operates in an ongoing manner, so it keeps operating producing sequences of input/output where the desired behaviour is represented by the acceptable pairs of input/output.
A further distinction can be made between **sequential** and **concurrent** computations:
**Sequential**: The computation in composed by a sequence of instructions executed once at a time.
**Concurrent**: There are multiple processes executing and evolving at the same time, there might be communication between these processes.

### Synchronous models

We will consider **synchronous models** (as opposed to asynchronous) which means that all components execute their code in a sequence of rounds in lock-step (discrete steps synced by a shared clock).
The benefit of this is making modelling and design simpler because it establishes a clear cause-effect relationship between instructions, but is poses the challenge of implementing synchronous execution on multi-core hardware.

The following assumptions are made:
1. The time needed to execute the update code at each time step is negligible, this implies that reception of inputs and production of outputs happens at the same time.
2. When multiple components are composed together they still execute synchronously and simultaneously during the ticking of the global clock.

In reality these assumptions must be enforced by the actual implementation.

## Synchronous Reactive Components

In order to describe the components of a system we will use a modelling language called **Synchronous Reactive Components**, with well defined _syntactic_ and _semantic_ definitions.

![[IMG_8A7FD5629A2D-1.jpeg]]

#### Example (Delay component)

![[IMG_5EF06708B9F9-1.jpeg]]

### Inputs

Each component has its set of (typed) input variables $I$, we define the _Input_ as the valuation of all the input variables at a certain time-step.
The set of all the possible inputs is denoted $Q_I$.

For a [[#Example (Delay component) | delay]] component
$$I = \{ \text{in of type bool} \}$$
$$Q_I = \{ 0, 1\}$$

### Outputs

Each component has its set of (typed) output variables $O$, we define the _Output_ as the valuation of all the output variables at a certain time-step.
The set of all the possible inputs is denoted $Q_O$.

For a [[#Example (Delay component) | delay]] component
$$O = \{ \text{out of type bool} \}$$
$$Q_O = \{ 0, 1\}$$

### State

Each component has its set of (typed) state variables $S$, we define the _State_ as the valuation of all the state variables at a certain time-step.
The set of all the possible states is denoted $Q_S$.

Note that the state is internal to the component (hidden to the outside), and preserved across rounds.

For a [[#Example (Delay component) | delay]] component
$$S = \{ \text{x of type bool} \}$$
$$Q_S = \{ 0, 1\}$$

### Initialisation

The state of each component is initialised with some value **before** the first update executes.
The set $Init$ contains the sequence of assignments made to initialise the state variables.
From the set $Init$ and the state $S$ we can derive the set $[\![Init]\!]$ that contains the possible initial states for the component. Note that $[\![Init]\!] \subseteq Q_S$

For a [[#Example (Delay component) | delay]] component
$$Init = \{ x := 0 \}$$
$$[\![Init]\!] = \{ 0 \}$$

### Reactions

The execution in every round is determined by the set $React$ that contains the sequence of instructions (assignments and conditionals) representing the code that updates internal state and produces output variables.

The set of _Reactions_ $[\![React]\!]$ contains tuples mapping all the possible states and inputs to all the possible new states and outputs; in fact $[\![React]\!] \subseteq Q_S \times Q_I \times Q_O \times Q_S$ 

For a [[#Example (Delay component) | delay]] component
$$React = \{ out := x; x := in \}$$
$$[\![React]\!] = \{ (0, 0, 0, 0), (0, 1, 0, 1), (1, 0, 1, 0), (1, 1, 1, 1) \}$$
Which correspond to this table:
$$
\begin{array}{ |cccc| }
	\hline
	x & in & out & x' \\ \hline
	0 & 0 & 0 & 0 \\ \hline
	0 & 1 & 0 & 1 \\ \hline
	1 & 0 & 1 & 0 \\ \hline
	1 & 1 & 1 & 1 \\ \hline
\end{array}
$$

To create this table one should first write down all the possible input and old state combinations and from that derive all the possible output and state transitions.

#### Multiple reactions

If we replace $React$ with this new fragment of code: $React = \{ out := x; x := \text{choose}\{in, x\}\}$ then we create a non-deterministic reaction, which means that, given old _State_ and _Input_, the _Output_ and new _State_ **are not unique!**. This increase the number of reactions from 4 to 6.
$$
\begin{array}{ |cccc| }
	\hline
	x & in & out & x' \\ \hline
	0 & 0 & 0 & 0 \\ \hline
	0 & 1 & 0 & 1 \\ \hline
	0 & 1 & 0 & 0 \\ \hline
	1 & 0 & 1 & 0 \\ \hline
	1 & 0 & 1 & 1 \\ \hline
	1 & 1 & 1 & 1 \\ \hline
\end{array}
$$

Instead by replacing $React$ with $React = \{ \texttt{if } x \neq 0 \texttt{ then } \{ out := x; x := in \}\}$, we obtain that with some combinations of input and/or current state there will **not be any reaction**, thus now the set contains only 2 reactions.
$$
\begin{array}{ |cccc| }
	\hline
	x & in & out & x' \\ \hline
	1 & 0 & 1 & 0 \\ \hline
	1 & 1 & 1 & 1 \\ \hline
\end{array}
$$

### Local variables

Components may also contain local variables $L$ that are _not_ persisted across rounds and are use to help with update code execution.

### Update code constraints

The update code must follow these rules:
1. Types of variables and expressions should _match_
2. Output variables _must_ be written before being read
3. Output variables _must_ be explicitly assigned a value by the component
4. Input values _cannot_ be written

In case the update code violates those rules then $[\![React]\!] = \emptyset$.

### Formal notation

- Set $I$ of typed input variables $\rightarrow$ set $Q_I$ of possible input values
- Set $O$ of typed output variables $\rightarrow$ set $Q_O$ of possible output values
- Set $S$ of typed state variables $\rightarrow$ set $Q_S$ of possible state values
- Set $L$ of typed local variables
- Initialisation code $Init$ $\rightarrow$ set $[\![Init]\!]$ of possible initial states
- Update code $React$ $\rightarrow$ set $[\![React]\!]$ of possible reactions in the form $s \xrightarrow{\text{i/o}} t$.

- A component is formally annotated as $C = (I, O, S, Init, React)$ with the meaning as above
- The **executions** of a component is a sequence of states in which the component finds itself by arbitrarily choosing the initial state and the inputs at each round. A sample execution looks like this (for deterministic code...): $$s_0 \xrightarrow{i_1/o_1} s_1 \xrightarrow{i_2/o_2} s_2 \xrightarrow{i_3/o_3} \quad ...$$

### Events

A special type an input/output variable can have is the type _event_. An _event_ can be _absent_ or _present_, in the latter case the event holds a value.

- $\texttt{event } x$ means $x$ ranges over the values $\{\top \text{ (present) }, \bot \text{ (absent) }\}$
- $\texttt{event(bool) } x$ means $x$ ranges over the values $\{\bot, 0, 1\}$
- $\texttt{event(nat) } x$ means $x$ ranges over the values $\{\bot, 0, 1, ...\}$
-  $x?$ returns $1$ if the value present $0$ otherwise ($x \neq \bot$)
- $x!v$ is the assignment $x := v$
- By default an event is absent
- Event-triggered components only executes in those rounds where an actual input event is present

![[IMG_7F860A92599F-1.jpeg]]

### Extended State Machines

State machines are a common way of describing the behaviour of a component in model-based design. In this notation we there is an implicit state variable ranging over an _enumeration_, called the _mode_ of the state machine.
The different modes are represented with circles with the _transitions_ between them marked with arrows.

In _extended_ state machines the description is augmented with additional state variables. Edges in the graph contain code that _guards_ the transition between modes and eventually updates the augmented state.

![[IMG_2AF04793A09E-1.jpeg]]

### Tasks

Reaction code inside a component may be split into _tasks_ to improve readability or composability.
Tasks are atomic blocks of code where each one specifies the variables it reads and writes.
Tasks inside a component form a _task graph_ where edges are precedence relations and nodes are tasks, this graph **must be acyclic** (otherwise there would be an unresolvable circular dependency).

![[IMG_A38A66FD5091-1 3.jpeg]]

#### Formal notation
More formally for an SRC $C = (I, O, S, Init, React)$, reaction description is given by a _set of tasks_ and precedence edges over these tasks.
We denote precedence with $A \prec B$, which means "$A$ precedes $B$". With $A \prec^{+} B$ we indicate that $A$ precedes $B$ _transitively_ across other tasks (possibly none).
Each task $A$ is specified by:
- A read set $R \subseteq I \cup S \cup O \cup L$ of variables
- A write set $W \subseteq S \cup O \cup L$ of variables
- An $Update$ set containing the code instructions to write $W$ based on vars in $R$. The set $[\![Update]\!] \subseteq Q_R \times Q_W$ contains all the reactions of the task.

#### Task schedule

The **task schedule** of an SRC is the _total ordering_ of all the $A_1, ..., A_n$ tasks of the component _consistent_ with the task graph edges. (If $A' \prec A \implies A'$ appears before than $A$ in the ordering). Note that there might be multiple valid schedules.
The acyclic constraint implies that there is _always_ at least one valid scheduling.

#### Await dependencies

Given two variables $x \in I$ and $y \in O$ there exist an **await dependency relation** ($y \succ x$) between the two if either:
- There exist a task $A$ that reads $x$ and writes $y$.
- There are two tasks $A_1, A_2$ where $A_1$ reads $x$, $A_2$ writes $y$ and there is a precedence relation between the two: $A_1 \prec^+ A_2$

$y$ await $x$ means that _y cannot be produced before x is supplied_.

### Write conflicts

A _write-conflict_ between two tasks $A_1$ and $A_2$ happens when there is a variable written by $A_1$, and read or written by $A_2$, and there is **not** an enforced ordering between the tasks. This causes the result of the execution to be dependent on the random ordering of execution of the two task.
To solve this we have to explicitly set $A_1 \prec^+ A_2$ or viceversa.

The set of reactions given by a task graph without write-conflicts _do not depend on the task schedule_.

### Requirements on task graph

A task graph is valid only if:
1. The graph is _acyclic_ (ensure scheduling)
2. Each output variable is in the write set of **exactly one** task
3. Tasks with a write conflict must be _ordered_
4. Output and local variables are written before being read (no uninitialised variables)

## Component Composition

To describe complex systems we obviously desire to compose already existing components in order to save time, increase modularity and improve clarity.

#### DoubleDelay
![[IMG_4D072697F987-1.jpeg]]

In this example we have _composed_ two _instances_ of the [[#Example (Delay component) | Delay]] component to create a _DoubleDelay_ component (output = input two round before).
Three operations have been made to compose these components:
1. **Instantiation**: Similar to _classes_ and _objects_ in OOP, _Delay1_ and _Delay2_ are _instances_ of the _Delay_ component. These instances are obtained by renaming the input/output variables. Note that in a system there **cannot** be state variables with the same name to avoid conflicts (see [[#I/O Variable Renaming]]).
2. **Parallel Composition**: The two _instances_ run in parallel, this means they execute [[#Synchronous models|synchronously]] and _simultaneously_ with the output of _Delay1_ being the input of _Delay2_. At each round _Delay1_ reads _in_, updates its state and outputs _temp_. At the same round _Delay2_ reads _temp_, updates its state and outputs _out_. Communication between the two is also _synchronous_.
3. **Output Hiding**: The auxiliary variable _temp_ is hidden from the outside of the component since its just an "utility" variable.

Formally this component would be written as $$(\texttt{Delay}[out \mapsto temp] \ \| \ {Delay}[in \mapsto temp]) \setminus temp$$

### I/O Variable Renaming

Before composing components it is necessary to rename variables appropriately to avoid conflicts (state variables), and _explicitly_ determine connections between components.

In particular in a system there must not be components with state variables having the same name to prevent conflicts.
Input/output variables must be named to create connections between components (see $temp$ [[#DoubleDelay]] above).

Usually state variable rename is _implicit_, while i/o renaming must be _explicit_ (it is not possible to determine _automatically_ relations between components).

#### Notation
Let $C = (I, O, S, Init, React)$ be a SRC component, $x$ be an input or output variable, and $y$ a _fresh_ variable with the same type of $x$, so that the name of $y$ is not already taken by any other variable in $C$.
We denote the renaming of $x$ in $y$ with $C_R = C[x \mapsto y]$

### Parallel Composition

Parallel composition combines two components into a single one whose behaviour captures the synchronous interaction between the two composed components.

#### Variable Names Compatibility

Let $C_1 = (I_1, O_1, S_1, Init_1, React_1)$ and $C_2 = (I_2, O_2, S_2, Init_2, React_2)$ then it must be that:
- No naming conflict concerning state variables and variables in the other component: $$\begin{cases}
	S_1 \cap S_2 = \emptyset \\
	S_1 \cap I_2 = \emptyset \\
	S_1 \cap O_2 = \emptyset \\
	\end{cases} $$
	And viceversa 
- A variable can be an input variable of both components
- An output variable must be an output variable of exactly one component (only one component is responsible for every output)
- An output variable of one component can be an input variable of the other
